## Tip of the Week #49: Argument-Dependent Lookup

一个函数调用 `func(a, b, c)` 没有指定任何 `::` 限定符，这个叫做未限定函数调用。函数查找过程是下面两种方式的并行进行，最后由重载决议来匹配最佳函数：

1. 名称查找，从调用者的作用域开始往外层作用域查找，找到一个就停止
2. 参数依赖查找 `ADL`，从参数相关联的名称空间下查找

### 名称查找

只是查找函数名称，不考虑参数，从调用者的作用域一直往外查找，直到找到一个，然后停止。因此，内部作用域中的函数会隐藏外部作用域中的同名函数。

```c++
namespace b {
void func();
namespace internal {
void test() { func(); } // ok: finds b::func().
} // namespace internal
} // namespace b

namespace b {
void func(const std::string &); // b::func
namespace internal {
void func(int); // b::internal::func
namespace deep {
void test() {
  std::string s("hello");
  func(s); // error: finds only b::internal::func(int).
}
} // namespace deep
} // namespace internal
} // namespace b
```

### 参数依赖查找

如果函数有入参，会直接在入参相关联的名称空间下查找函数名，比如 `func(a::A<b::B, c::internal::C*>)` 会在入参相关联的 `a, b, c::internal` 作用域下查找函数。

```c++
namespace aspace {
struct A {};
void func(const A &); // found by ADL name lookup on 'a'
} // namespace aspace

namespace bspace {
void func(int); // found by lexical scope name lookup
void test() {
  aspace::A a;
  func(a); // aspace::func(const aspace::A&)
}
} // namespace bspace
```

对于上面的代码，函数名称查找，实际步骤如下，前两个是并行的：

1. 名称查找，找到了 `void bspace::func(int);` 然后停止
2. 参数依赖查找，找到了 `void aspace::func(const aspace::A&);` 可能会有多个
3. 重载决议，从上面两步中找到的函数来匹配最佳函数，有且只能有一个最佳匹配

一个更复杂的例子，使用了类型别名：

```c++
namespace aspace {
struct A {};
template <typename T> struct AGeneric {};
void func(const A &);
template <typename T> void find_me(const T &);
} // namespace aspace

namespace bspace {
typedef aspace::A AliasForA;
struct B : aspace::A {};
template <typename T> struct BGeneric {};
void test() {
  // ok: base class namespace searched.
  func(B());
  // ok: template parameter namespace searched.
  find_me(BGeneric<aspace::A>());
  // ok: template namespace searched.
  find_me(aspace::AGeneric<int>());
}
} // namespace bspace

namespace cspace {
// ok: note that this searches aspace, not bspace.
void test() { func(bspace::AliasForA()); }
} // namespace cspace
```

这些函数查找规则同样适用于运算符重载，一般来说我们总是最好使用限定作用域的函数调用，局部使用未限定作用域的函数调用。

