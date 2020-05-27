## Tip of the Week #148: Overload Sets

函数重载是为了让客户端代码的编写更自然，更贴近惯用法，不要制造迷惑给人干扰，让人不知道到底该调用哪个重载函数。

函数重载规则：

1. 函数名称相同，返回值类型忽略不计
2. 函数的参数个数，类型，修饰不同
3. 发起函数调用的对象的常量属性不同
4. 发起函数调用的对象的引用属性不同

函数重载示例：

```c++
int Add(int a, int b){};
int Add(int a, int b, int c){};
int Add(float a, float b){};

void Process(const std::string& s) { Process(s.c_str()); }
void Process(const char*);

// std::vector
void push_back(const T& value);
void push_back(T&& value);
reference       operator[]( size_type pos );
const_reference operator[]( size_type pos ) const;

// std::optional
constexpr T& value() &;
constexpr const T & value() const &;
constexpr T&& value() &&;
constexpr const T&& value() const &&;
```

类对象的拷贝构造和移动构造也会构成重载，重载构造函数会根据上下文自动调用最优的重载函数以提高性能，客户端代码不应该对此感知。在最终构造的对象上没有任何区别，只是对象构造代价上有差异。

多个重载函数构成一个重载函数集合，是 `API` 设计的良好组织方式。另外，不要滥用，给调用者造成心智负担，不要让人去选择到底该调用哪个重载函数。

