## Tip of the Week #42: Prefer Factory Functions to Initializer Methods

在禁止使用异常的环境中，由于构造函数没有返回值，当对象构造失败的时候没法通知使用方，使用方可能使用了没有完全构造的对象，造成未定义行为。

一个比较通用的解决方法是将对象的构造分为两个部分：

1. 异常安全的部分
2. 异常不安全的部分，通过一个有返回值的 `init` 函数来初始化

这个要求使用方在构造对象的时候要按照设计文档来，对象将会有三种状态需要客户端来处理：

1. 完全初始化了的，`init` 函数返回成功
2. 未完全初始化的，`init` 函数没有调用
3. 初始化失败了的，`init` 函数返回失败，需要销毁对象

解决这个问题的最好方式是提供一个工厂方法，用来给客户端构造对象，工厂方法返回对象的指针：

```c++
// foo.h
class Foo {
 public:
  // Factory method: creates and returns a Foo
  // May return null on failure.
  static std::unique_ptr<Foo> Create();

  // Foo is not copyable.
  Foo(const Foo&) = delete;
  Foo& operator=(const Foo&) = delete;

 private:
  // Clients can't invoke the constructor directly
  Foo();
};

// foo.c
std::unique_ptr<Foo> Foo::Create() {
  // Note that since Foo's constructor is private, we have to use new
  return absl::WrapUnique(new Foo());
}
```

工厂方法用于构造复杂对象的好处：

- 对象是否构造成功，都在工厂方法内部处理
- 支持多态，可以返回子类对象

