## Tip of the Week #143: C++11 Deleted Functions (`= delete`)

设计一个类，要求阻止类对象的拷贝构造，一般有下面的一些方法：

1. 文档中强调说明对象的拷贝没有意义，不要调用拷贝构造 - **文档**
2. 定义一个虚假的拷贝函数，然后在运行时检查是否发生了对象的拷贝调用 - **运行时**
3. 定义非公开的拷贝函数 - **编译时**
4. 声明拷贝构造函数，但是不定义拷贝构造函数 - **链接时**
5. 使用 `=delete` 明确删除拷贝构造函数 - **编译时**，错误提示信息比 `#3` 友好

```c++
// C++98， #3
class MyType {
 private: // in private
  MyType(const MyType&);  // Not defined anywhere.
  MyType& operator=(const MyType&);  // Not defined anywhere.
  // ...
};
// C++11， #5
class MyType {
 public: // in public
  // Disable copy semantics.
  MyType(const MyType&) = delete;
  MyType& operator=(const MyType&) = delete;
  //Allow move
  MyType(MyType&&) = default;
  MyType& operator=(MyType&&) = default;
};
```

`=delete` 可以删除类成员函数，可以删除其它自由函数，也可以删除模板函数的特化版本：

```c++
// case #1, delete class member function, Don't allow new T[]
class NoHeapArraysPlease {
 public:
  void* operator new[](std::size_t) = delete;
  void operator delete[](void*) = delete;
};
// case #2, delete free function
void print(int value);
void print(char) = delete;
// case #3, delete template function
template<typename T> void processPointer(T* ptr);
template<> void processPointer<char>(char*) = delete;
```



