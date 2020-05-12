## Tip of the Week #11: Return Policy

返回值优化 `RVO` 是一个比较早就存在的编译器优化技术，在 `C++98` 年代，没有移动语义的情况下，正确使用 `RVO` 能减少对象的拷贝成本，绝大多数编译器都支持。

对于下面的的类定义：

```c++
class SomeBigObject {
 public:
  SomeBigObject() { ... }
  SomeBigObject(const SomeBigObject& s) {
    printf("Expensive copy …\n", …);
    …
  }
  SomeBigObject& operator=(const SomeBigObject& s) {
    printf("Expensive assignment …\n", …);
    …
    return *this;
  }
  ~SomeBigObject() { ... }
  …
};
SomeBigObject SomeBigObject::SomeBigObjectFactory(...) {
  SomeBigObject local;
  …
  return local;
}
```

`RVO` 起作用的情况：

```c++
// RVO will happen here:
// uses a new variable to store the return value
SomeBigObject obj = SomeBigObject::SomeBigObjectFactory(...);

// RVO will happen here:
// uses one variable and returns it in multiple places
SomeBigObject local;
if (...) {
  local.DoSomethingWith(...);
  return local;
} else {
  local.DoSomethingWith(...);
  return local;
}

// RVO also works here for temporary objects
SomeBigObject SomeBigObject::ReturnsTempFactory(...) {
  return SomeBigObject::SomeBigObjectFactory(...);
}
EXPECT_EQ(SomeBigObject::SomeBigObjectFactory(...).Name(), s);
```

`RVO` 不起作用的情况：

```c++
// RVO won’t happen here:
// reuses an existing variable to store the return value
obj = SomeBigObject::SomeBigObjectFactory(s2);

// RVO won’t happen here:
// uses more than one variable for the return value
static SomeBigObject NonRvoFactory(...) {
  SomeBigObject object1, object2;
  object1.DoSomethingWith(...);
  object2.DoSomethingWith(...);
  if (flag) {
    return object1;
  } else {
    return object2;
  }
}
```

最后提一点，`RVO` 有点反直觉，使用的时候要注意正确性永远比效率更重要，当对象需要拷贝的时候就拷贝，性能问题是多方面的，不要盲目优化。