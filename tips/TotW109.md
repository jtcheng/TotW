## Tip of the Week #109: Meaningful `const` in Function Declarations

声明和定义的区别：

1. 声明就是声明，定义可以同时也是声明
2. 可以有多个声明，但是有且只能有一个定义

```c++
void F(int);                     // 1: declaration of F(int)
void F(const int);               // 2: re-declaration of F(int)，ignore constness
void F(int) { /* ... */ }        // 3: definition of F(int)
void F(const int) { /* ... */ }  // 4: error: re-definition of F(int)
```

有意义的 `const` 声明：

```c++
// all x itself are not const
void F(const int* x);                  // 1, pointer to an int that is const
void F(const int& x);                  // 2, reference to an int that is const
void F(std::unique_ptr<const int> x);  // 3, unique_ptr to an int that is const
void F(int* x);                        // 4, no const
```

无意义的 `const` 声明，参数本身的 `const` 属性会被编译器忽略：

```c++
// all x itself are const, compiler will ignore x itself constness
void F(const int x);          // 1: declares F(int)
void F(int const x);          // 2: same as 1
void F(int* const x);         // 3: declares F(int*)
void F(const int* const x);   // 4: declares F(const int*)
```

使用规则：

1. 不要在非定义的函数声明中使用 `const` 形参， `const` 属性会被编译器忽略
2. 在函数定义中可以使用 `const` 参数，相当于在函数内部定义了 `const` 的本地栈变量

