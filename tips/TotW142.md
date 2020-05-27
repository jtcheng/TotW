## Tip of the Week #142: Multi-parameter Constructors and `explicit`

对于编程来说，显式总是比隐式要好，就像 `python` 之禅说的那样。在 `C++` 中存在各式各样的隐式行为，其中包括构造函数的隐式调用，`explicit` 关键字就是为了阻止构造函数被隐式调用。

在 `C++11` 之前，只有单个参数的构造函数会发生隐式调用：

```c++
class X {
  public:
  X(int x) : x_(x) { std::cout << "ctor called\n"; }
  private:
  int x_;
};

X x1 = 4; // ctor called
```

在 `C++11` 之后由于括号初始化的引入，情况变得更复杂了，对于多参数的构造函数同样存在问题：

```c++
class X {
  public:
  X(int x, std::string name) : x_(x), name_(name) {
    std::cout << "ctor called\n";
  }
  private:
  int x_;
  std::string name_;
};

X x1 = {4, "X"}; // ctor called
// explicit ctor
class Y {
  public:
  explicit Y(int x) : x_(x) { std::cout << "ctor called\n"; }
  private:
  int x_;
};

Y y1 = 4; // not compiles
Y y1(4);  // ctor called
```

构造函数的设计经验:

1. 拷贝构造函数和移动构造函数不要设计为 `explicit`
2. 总是设计构造函数为 `explicit` 除非初始化物能直接表达对象的含义
3. 类型包含额外信息，比如 `std::unique<T>` 的对象的构造，也应该设计为 `explicit`
4. 一些设计上的需要，不希望客户端直接调用构造函数，可以设计工厂方法来创建对象

