## Tip of the Week #168: `inline` Variables

头文件中的内联变量，实现文件 `.cpp` 中就无所谓了：

```c++
// a.h
inline constexpr absl::string_view kHelloWorld = "Hello World.";
```

对于上面的一行代码：

1. `inline` 意味着整个程序中只有一个 `kHelloWorld` ，尽管 `a.h` 可能会被多个文件多次包含
2. `constexpr` 意味着 `kHelloWorld` 是一个编译期常量

当内联变量没有得到语言层面支持的时候，需要按照下面的方式来实现相同功能：

```c++
inline constexpr absl::string_view HelloWorld() {
  return "Hello World.";
}
```

隐式内联数据成员 `static constexpr` 修饰：

```c++
struct X {
  static constexpr std::string_view kHelloWorld{"Hello World."}; // kHelloWorld is inline
};
```

