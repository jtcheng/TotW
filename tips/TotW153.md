## Tip of the Week #153: Don't Use using-directives

不要直接使用 `using` 导入整个命名空间：

1. 这个会导致命名空间名称污染，同名符号被无意的隐藏等问题
2. 代码可能会使用到不是所期望的同名符号
3. 随着代码的进化，随时可能会导致编译错误或者运行时错误

一些好的用法：

```c++
namespace fs = std::filesystem; // namespace alias
using std::filesystem::path     // using declaration

namespace totw {
namespace example {
namespace {
void hello(void) {
  using std::cout; // use it in a limited scope
  cout << "hello\n";
}
} // namespace
} // namespace example
} // namespace totw
```
对于很稳定的代码，在不会继续开发的情况下可以酌情使用，更多信息请参考：
[Tip of the Week #130: Namespace Naming][TotW130]


[TotW130]: TotW130.md

