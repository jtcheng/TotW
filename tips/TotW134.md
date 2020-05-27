## Tip of the Week #134: `make_unique` and `private` Constructors

`std::make_unique` 需要调用类的公开构造函数：

```c++
class Widget {
 public:
  static std::unique_ptr<Widget> Make() {
    return absl::make_unique<Widget>(GenerateId()); // this line not compiles
  }

 private:
  Widget(int id) : id_(id) {}
  static int GenerateId();

  int id_;
}
// Using `new` to access a non-public constructor.
return absl::WrapUnique(new Widget(...));
```

在标准 `C++` 下这个问题没有很好的解决方案，另外，构造函数私有化，一般是过度设计了。

我们可以使用 `passkey` 惯用法来处理这种情况，对于 `std::make_shared` 也同样适用：

```c++
class Widget {
  class Token {
  private:
    Token() {}
    friend Widget;
  };

public:
  static std::unique_ptr<Widget> Make() {
    return std::make_unique<Widget>(Token{}, GenerateId());
  }

  Widget(Token, int id) : id_(id) {} // a special public ctor

private:
  static int GenerateId();
  int id_;
};
```

