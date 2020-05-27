## Tip of the Week #166: When a Copy is not a Copy

`C++17` 中的拷贝消除特性：

```c++
class BigExpensiveThing {
 public:
  static BigExpensiveThing Make() {
    // ...
    return BigExpensiveThing();
  }
  // ...
 private:
  BigExpensiveThing();
  std::array<OtherThing, 12345> data_;
};

BigExpensiveThing MakeAThing() {
  return BigExpensiveThing::Make();
}
void UseTheThing() {
  BigExpensiveThing thing = MakeAThing();
  // ...
}
```

对于上面的代码，在 `C++17` 下是直接在 `thing` 的地址上构建 `BigExpensiveThing` 没有任何临时对象的生成与拷贝！

得益于 `C++` 中的值类型：

1. **glvalue** 提供有名称的地址：`thing`
2. **prvalue** 提供无名称的初始化物：`BigExpensiveThing()`

