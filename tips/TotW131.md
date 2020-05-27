## Tip of the Week #131: Special Member Functions and `= default`

`C++` 类的特殊成员函数：

1. 默认构造函数
2. 析构函数
3. 拷贝构造函数
4. 拷贝赋值操作符
5. 移动构造函数
6. 移动赋值操作符

默认情况下，编译器会在特定的时候帮我们自动生成其中的一些特殊成员函数，但是我们可以使用 `=default` 与 `=delete` 来控制编译器生成特殊成员函数的行为。

`=default` ，告诉编译器帮我生成一个对应的特殊成员函数：

1. 编译器生成的特殊成员函数可以和用户自定义的特殊成员函数同时存在
2. 编译器生成的拷贝和移动特殊成员函数，能自动应付类数据成员增删的情况
3. 编译器生成的特殊成员函数，性能往往更好
4. 编译器对于简单类(POD)生成的默认构造函数支持聚合初始化，用户自定义的则不支持
5. 显示指定 `=default` 给我们以提示，避免了隐式的行为
6. 类模板中，`=default` 是针对模板具体特化类型的

`=default` 的特殊成员函数必须要能内联，否则就相当于 `=delete`

```c++
class C {
 public:
  C() = default;  // misleading: C has a implicitly-deleted default constructor
 private:
  const int i;  // const => must always be initialized
};
C c; // not compiles, since the default ctor is implicitly-deleted

class D {
 public:
  D() = default;  // unsurprising, but not explicit: D has a default constructor
 private:
  std::unique_ptr<int> p;  // std::unique_ptr has a default constructor
};
```

