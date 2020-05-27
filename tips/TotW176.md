## Tip of the Week #176: Prefer Return Values to Output Parameters

函数的输出参数：

```c++
// Extracts the foo spec and the bar spec from the provided doodad
// Returns false if the input is invalid
bool ExtractSpecs(Doodad doodad, FooSpec* foo_spec, BarSpec* bar_spec);
```

相信很多人都写过类似上面的函数，传入输出参数来接收结果，对于这样的函数，使用起来有很多疑问：

1. `doodad` 是输入参数， `foo_spec, bar_spec` 是输出参数？
2.  `foo_spec, bar_spec` 原来持有的内容怎么处理，是否可以为 `nullptr`？
3. 返回 `false` 的时候  `foo_spec, bar_spec`  的状态如何？
4.  `foo_spec, bar_spec`  生命周期要求？

使用 `absl::optional` 结合按值返回来优雅的解决问题：

```c++
struct ExtractSpecsResult {
  FooSpec foo_spec;
  BarSpec bar_spec;
};
// Extracts the foo spec and the bar spec from the provided doodad.
// Returns nullopt if the input is invalid.
absl::optional<ExtractSpecsResult> ExtractSpecs(Doodad doodad);
```

函数设计建议：

1. 尽量设计按值返回的函数，少用输出参数，避免误用
2. 当函数需要返回多个值的时候，可以使用 `struct, std::pair, std::tuple` 来聚合返回值
3. 对于返回值可能不存在的情况，使用 `std::optional` 来包装函数的返回值
4. 按值返回性能上可能会有点影响，可能涉及到对象的拷贝，但是拷贝也有可能被优化掉
5. 按值返回可能不适合函数参数既是输入参数也是输出参数的情况

