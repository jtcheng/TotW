## Tip of the Week #141: Beware Implicit Conversions to `bool`

在条件判断语句中，条件表达式会进行运算，最终转化为 `bool` 值进而控制代码的执行分支，在这个过程中要注意 `bool` 隐式转换。另外，当背后的值是 `bool` 类型的时候问题进一步复杂化：

1. 使用 `nullptr` 来测试空指针
2. 对于 `std::optional` 明确的调用 `has_value` ，而不是依赖其隐式 `bool` 转换
3. 使用具体的枚举值来测试枚举变量

```c++
if (foo != nullptr) {
  DoSomething(*foo);
}

if (std::optional<Foo> foo = MaybeFoo(); foo.has_value()) {
  DoSomething(*foo);
}

void ParseCommandLineFlags(
    const char* usage, int* argc, char*** argv,
    StripFlagsMode strip_flags_mode) {
  if (strip_flags_mode == kPreserveFlags) {
    ...
  }
}

std::optional<bool> b = MaybeBool();
if (b) { ... }  // What happens when the function returns absl::optional(false)
```

