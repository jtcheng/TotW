## Tip of the Week #103: Flags Are Globals

`Flags` 的使用建议：

1. 尽量避免使用 `Flags`
2. 为了方便测试，建议为类添加专用的测试 `API`
3. 将 `Flags` 视为私有静态变量，然后通过 `API` 暴露给其他模块
4. 将 `Flags` 定义到全局作用域，而不是一个命名空间下
5. 在实现文件中定义 `Flags` ，在头文件中声明 `Flags`
6. 使用 `ABSL_FLAG(type, ...)` 来定义 `Flags`

