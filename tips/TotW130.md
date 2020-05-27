## Tip of the Week #130: Namespace Naming

`C++` 的命名空间相对于 `Java` 的命名空间，更容易发生命名空间名称污染问题：

```c++
// In C++, Baz can be:
// 1. ::foo::bar::f()::Baz
// 2. ::foo::bar::Baz
// 3. ::foo::Baz
// 4. ::Baz
namespace foo {
namespace bar {
void f() {
  Baz b;
}
}
}

// In java, will never search names in parent scope
import com.google.foo.bar.Baz;
public void f() {
  Baz b = new Baz();
}
```

关于 `C++` 中的名称查找问题请参考：

[Tip of the Week #49: Argument-Dependent Lookup][TotW049]
[Tip of the Week #119: Using-declarations and namespace aliases][TotW119]

`C++` 命名空间最佳实践：

1. 记录并保持项目命名空间名称的唯一性
2. 不要定义嵌套层次太深的子命名空间，同时也好注意子命名空间的命名
3. 谨慎的向已经存在的命名空间下添加新东西
4. 声明命名空间别名的时候，总是使用全名称声明，以 `::` 开头
5. 不要为了防止命名空间名称污染，而处处使用全命名空间名称，影响代码可读性



[TotW049]: TotW049.md
[TotW119]: TotW119.md

