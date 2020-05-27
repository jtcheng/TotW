## Tip of the Week #173: Wrapping Arguments in Option Structs

当函数的参数较多时，可以将相关参数包装成一个参数结构体，再结合结构体的指定初始化技术，可以大大简化函数的设计和使用：

```c++
void PrintDouble(double value, absl::string_view prefix,  int precision,
                 char thousands_separator, char decimal_separator,
                 bool scientific);

struct PrintDoubleOptions {
  absl::string_view prefix = "";
  int precision = 8;
  char thousands_separator = ',';
  char decimal_separator = '.';
  bool scientific = false;
};
void PrintDouble(double value,
                 const PrintDoubleOptions& options = PrintDoubleOptions{});
std::string name = "my_value";
PrintDouble(5.0, {.prefix = absl::StrCat(name, "="), .scientific = true});
```

使用建议：

1. 当函数的相关参数较多，将来可能还会增加，并且有多个默认值时，可以选择包装参数结构体
2. 参数结构体应该只用来包装彼此相关的参数，不相关的参数应该保持独立使用
3. 使用参数结构体，结合结构体的指定初始化技术，可以简化函数的使用，避免临时变量

