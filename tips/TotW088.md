## Tip of the Week #88: Initialization: =, (), and {}

现代 `C++` 的大括号初始化 (也叫统一初始化)，统一了变量的初始化形式，但是很多时候不直观，不要滥用：

```c++
int x{2};
std::string foo{"Hello World"};
std::vector<int> v{1, 2, 3};
```

变量初始化最佳实践？有待商榷：

1. 直接使用 `=` 更直观，背后生成的代码是一模一样，不存在性能影响
   1. 内置变量类型直接赋值
   2. 简单结构体 `POD` 的初始化
   3. 调用拷贝构造函数初始化变量

```c++
int x = 2;
std::string foo = "Hello World";
std::vector<int> v = {1, 2, 3};
std::unique_ptr<Matrix> matrix = NewMatrix(rows, cols);
MyStruct x = {true, 5.0};
MyProto copied_proto = original_proto;
// Bad code
int x{2};
std::string foo{"Hello World"};
std::vector<int> v{1, 2, 3};
std::unique_ptr<Matrix> matrix{NewMatrix(rows, cols)};
MyStruct x{true, 5.0};
MyProto copied_proto{original_proto};
```

2. 当类有初始化列表和其他普通构造函数时，通过明确的调用具体的构造函数来避免误用：

```c++
Frobber frobber(size, &bazzer_to_duplicate);
std::vector<double> fifty_pies(50, 3.14);
// Bad code
// Could invoke an intializer list constructor, or a two-argument constructor
Frobber frobber{size, &bazzer_to_duplicate};
// Makes a vector of two doubles
std::vector<double> fifty_pies{50, 3.14};
```

3. 当类有初始化列表构造函数时，并且其他构造函数无法满足要求时使用 `{}` 来初始化对象
4. 不要混用 `auto` 与大括号初始化

```c++
// Bad code
auto x{1};
auto y = {2}; // This is a std::initialize, NOT a int
```

规则是人定的，保持代码的一致性，适合自己的才是最好的，做到心中有数，不要掉坑里就行。我个人倾向于使用大括号初始化方式来形式统一的初始化变量。

