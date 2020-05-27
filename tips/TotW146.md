## Tip of the Week #146: Default vs Value Initialization

对于有确切默认初始值的对象，一般来说使用是安全的；对于默认初始值随机化的对象，使用起来就得小心，需要明确初始化之后再使用，不要依赖于编译器的默认行为。

```c++
int x;
std::string s;
std::cout << x << std::endl; // -340157392, this is a random value
std::cout << '{' << s << '}' << std::endl; // {}, this is an empty string
```

类标量数据成员的初始化：

1. 数据成员默认初始化

```c++
struct Foo {
  int v = 0;
};
```

2. 构造函数初始化

```c++
struct Foo {
  Foo() : v(0) {}
  int v;
};
struct Foo {
  Foo() {v=0;}
  int v;
};
// v is not initialized
struct Foo {
  Foo() {}
  int v;
};
```

`=default` 默认构造函数声明：

1. 在类内部使用 `=default`  声明默认构造函数，不属于用户自定义的构造函数
2. 在类外部使用 `=default`  定义默认构造函数，属于用户自定义的构造函数

```c++
struct Foo {
  Foo() = default; // this is not user defined ctor
  int v;
};

struct Bar {
  Bar();
  int v;
};
Bar::Bar() = default; // this is a user defined ctor

int main() {
  Foo f = {}; // Foo is aggregate type, and f.v is zero initialized
  Bar b = {}; // Bar is not aggregate type, and b.v is not initialized
}
```

聚合 `(Aggregate)` 类型

1. 数组是聚合类型
2. 类或者结构体需要满足 (就是可以使用 `memcpy, memset` 来拷贝和零初始化的类型)：
   1. 所有的成员都是 `public` 的
   2. 没有静态数据成员
   3. 没有用户定义的构造函数
   4. 没有基类
   5. 没有虚函数
   6. 没有成员默认初始化定义

   
