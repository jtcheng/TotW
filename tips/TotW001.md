## Tip of the Week #1: string_view

当我们要设计一个接收(常量)字符串参数的函数时，一般有下面 4 种方案：

```c++
void TakesCharStar(const char* s);          // C convention
void TakesString(const string& s);          // old standard C++ convention
void TakesStringView(absl::string_view s);  // abseil C++ convention
void TakesStringView(std::string_view s);   // C++17 C++ convention
```

前面 2 种方案，在调用时可能会涉及到实参到形参的转换：

```c++
void AlreadyHasString(const string& s) {
  TakesCharStar(s.c_str());  // explicit conversion
}
void AlreadyHasCharStar(const char* s) {
  TakesString(s);  // compiler will make a copy
}
```

后面 2 种方案，使用实参来构造 `string_view` 避免了参数转换，轻量高效：

```c++
void AlreadyHasString(const string& s) {
  // operator std::basic_string_view<CharT, Traits>() const noexcept;
  TakesStringView(s);  // no explicit conversion, convenient
}
void AlreadyHasCharStar(const char* s) {
  TakesStringView(s);  // no copy, efficient, but calls strlen(s) O(n)
}
```

`string_view` 是其背后实际字符串的一个只读视图，不拥有实际字符串的所有权。在实现上仅仅包含一个指针和一个长度，非常的轻量高效。

`string_view` 支持丰富的操作，但也有一些注意事项：

```c++
// string_view is not necessarily NUL-terminated
printf("%s\n", sv.data());  // DON’T DO THIS
printf("%.*s\n", static_cast<int>(sv.size()), sv.data());  // works
// operator<<
std::cout << "Took '" << sv << "'";  // works
// dangling pointer
std::string_view badsv("a temporary string"s); // badsv holds a dangling pointer
```

