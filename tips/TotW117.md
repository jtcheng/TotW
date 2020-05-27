## Tip of the Week #117: Copy Elision and Pass-by-value

拷贝消除是编译器的特性，类似于 `RVO` ，这个只有在按值传递的时候才会发生：

```c++
class Widget {
 public:
  …
 private:
  string name_;
};

// First constructor version
explicit Widget(const std::string& name) : name_(name) {}
// Second constructor version
explicit Widget(std::string name) : name_(std::move(name)) {}

// construct widget
Widget widget(absl::StrCat(bar, baz));
string local_str;
Widget widget(local_str);
```

由于最终总是要拷贝内容到 `name_` ，因此拷贝不可避免：

第一个版本是线性复制，第二个版本使用了 `std::move` 稍微高效点，并且对临时对象优化更友好。

按值传参的适用情形：

1. 当拷贝不可避免，比如 `copy-and-swap` 惯用法
2. 按值传参，并且函数内部的实现逻辑和代码相差不大

总体来说，要倾向于编写简单清晰的代码，不要为了性能而不顾代码的可读性。

