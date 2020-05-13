## Tip of the Week #107: Reference Lifetime Extension

临时对象生命周期会延长的情形：

```c++
typedef struct X {
  std::string name_;
  int age_;
} x_t;

struct X getX() {
  return {"stone", 120};
}

// use local (const T&) or (T&&) to store the temporaries T
const x_t &x1 = getX();
x_t &&x2 = getX();
// use local (const T&) or (T&&) to store the T subobject of a temporaries object
const std::string &n1 = getX().name_;
std::string &&n2 = getX().name_;
```

临时对象生命周期延长的**规则**：

1. 返回值接收类型必须是 `const T&` 和 `T&&` ，其它类型都不行
2. 赋值的时候不能发生类型转换
3. 子对象必须直接获取，不能分多个步骤间接获取和使用


- 临时对象的生命周期一旦被延长，就会延长到和接收它的对象的生命周期一样长
- 临时对象的生命周期没有被延长，就会在下个 `;` 处立即销毁

`C++` 的这个特性，并不能带来多大的性能提升，但是，会使得代码的阅读，理解，维护变得困难，不建议刻意使用。

在 `range based for loop` 中使用时要格外注意：

```c++
std::vector<int> GetInts();
for (int i : GetInts()) { }  // lifetime extension on the vector is important
// Return string_views of size 1 for each char in this string.
std::vector<absl::string_view> Explode(const string& s);
// Lifetime extension kicks in on the vector, but *not* on the temporary string!
for (absl::string_view s : Explode(StrCat("oo", "ps"))) { }  // WRONG

MyProto GetProto();

// Lifetime extension *doesn't work* here: sub_protos (a repeated field)
// is destroyed by MyProto going out of scope, and the lifetime extension rules
// don't kick in here to magically lifetime extend the MyProto returned by
// GetProto().  The sub-object lifetime extension only works for simple
// is-a-member-of relationships: the compiler doesn't see that sub_protos()
// itself returning a reference to an sub-object of the outer temporary.
for (const SubProto& p : GetProto().sub_protos()) { }  // WRONG
```

