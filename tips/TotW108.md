## Tip of the Week #108: Avoid `std::bind`

使用 `std::bind` 的一些问题：

1. `std::bind` 不能自动绑定调用参数，需要 `std::placeholders` 来配合

```c++
void DoStuffAsync(std::function<void(Status)> cb);
class MyClass {
  void Start() {
    DoStuffAsync(std::bind(&MyClass::OnDone, this)); // this line not compiles
  }
  void OnDone(Status status);
};
// fix
DoStuffAsync(std::bind(&MyClass::OnDone, this, std::placeholders::_1));
DoStuffAsync([this](Status status) { OnDone(status); });
// use abseil::bind_front
absl::bind_front(&MyClass::OnDone, this)
```

2. 误用 `std::bind` 有时候不会触发编译器错误检查

```c++
void DoStuffAsync(std::function<void(Status)> cb);
class MyClass {
  void Start() {
    DoStuffAsync(std::bind(&MyClass::OnDone, this));
  }
  void OnDone();  // No Status here, but no compiler time warnings
};
```

3. `std::bind`  不等价于 `std::function` 和 `lambda` ，不要用于不是自己设计的类型上

`std::bind` 的其它常见推荐用法：

1. 忽略某些入参 `std::bind(F, _2)`
2. 重复使用某些入参  `std::bind(F, _1, _1)`
3. 控制入参以及固定参数相对位置  `std::bind(F, _1, 42)` ， `std::bind(F, _2, _1)` 
4. 函数组合  `std::bind(F, std::bind(G))` 
5. 使用 `lambda` 替代 `std::bind`

