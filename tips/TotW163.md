## Tip of the Week #163: Passing `absl::optional` parameters

假设您需要实现一个带有参数的函数，该参数可能存在，也可能不存在。对于这个需求，首先想到的可能是 `absl::optional` ：

```c++
void MyFunc(const absl::optional<Foo>& foo);  // Copies by value
void MyFunc(absl::optional<const Foo&> foo);  // Doesn't compile
```

如果对象拷贝的代价比较大，这个可能不是我们想要的，可以有下面的解决方案：

```c++
void MyFunc(const Foo* foo); // nullptr means not exists
// absl::optional can not hold reference, but we can use std::reference_wrapper
void MyFunc(absl::optional<std::reference_wrapper<const Foo>> foo); // also works
```

`std/absl::optional` 使用建议：

1. 当拥有资源的所有权时，表示资源的可有可无状态
2. 作为函数的返回值，对调用方友好
3. 当对象拷贝代价不大，可以按值传递时，可以作为函数入参使用

