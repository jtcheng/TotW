## Tip of the Week #99: Nonmember Interface Etiquette

一个类的接口可以分为下面几个部分：

1. 公开的成员函数接口
2. 公开的基于 `ADL` 的非成员函数接口，包括模版特化，运算符重载，其他函数等
3. 类内部**直接定义**的友元函数接口

```c++
namespace space {
class Key { ... };
bool operator==(const Key& a, const Key& b);
bool operator<(const Key& a, const Key& b);
void swap(Key& a, Key& b);
// standard streaming
std::ostream& operator<<(std::ostream& os, const Key& x);
// gTest printing
void PrintTo(const Key& x, std::ostream* os);
// new-style flag extension:
bool ParseFlag(const string& text, Key* dst, string* err);
string UnparseFlag(const Key& v);
} // namespace space

HASH_NAMESPACE_BEGIN
template <>
struct hash<space::Key> {
  size_t operator()(const space::Key& x) const;
};
HASH_NAMESPACE_END
```

基于参数依赖查找 (`ADL`) 的函数接口，必须放到一个合适的名称空间下才行：

```c++
namespace library {
struct Letter {};
void good(Letter);
}  // namespace library

// bad is improperly placed in global namespace
void bad(library::Letter);

namespace client {
void good();
void bad();
void test(const library::Letter& x) {
  good(x);  // ok: 'library::good' is found by ADL
  bad(x);   // oops: '::bad' is hidden by 'client::bad'
}
} // namespace client
```

参考 [Tip of the Week #49: Argument-Dependent Lookup][TotW049]

对上面例子中的 `bad` 函数查找过程进行分析，下面的步骤 1 和 2 是并行进行的：

1. 参数依赖查找在名称空间 `library` 下面没有找到 `bad` 函数，查找停止
2. 名称查找从调用方开始往外层名称空间查找 `bad` ，找到了 `client::bad` ，停止查找
3. 由于 `client:bad()` 函数无法满足调用参数，于是触发编译错误

类内部**直接定义**的友元函数接口：

```c#
namespace library {
class Key {
 public:
  explicit Key(string s) : s_(std::move(s)) {}
  // friend means this is a non-member function, but can access private member
  friend bool operator<(const Key& a, const Key& b) { return a.s_ < b.s_; }
  friend bool operator==(const Key& a, const Key& b) { return a.s_ == b.s_; }
  friend void swap(Key& a, Key& b) {
    swap(a.s_, b.s_);
  }
 private:
  std::string s_;
};
}  // namespace library
```

类内部**直接定义**的友元函数比较特殊，尽管它们是定义在类内部，但是它们只能通过 `ADL` 查找到，名称查找无法找到它们，因此它们不会隐藏外部作用域中的同名函数。



[TotW049]: TotW049.md

