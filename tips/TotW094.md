## Tip of the Week #94: Callsite Readability and bool Parameters

对调用方来说一个函数的可读性主要体现在下面几个方面：

1. 函数名称
2. 函数参数
3. 函数注释，文档

函数的 `bool` 类型参数会大大降低函数的可读性：

```c++
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, false); // what the last argument means?
}
// bad fix
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, false /* preserve flags */);
}
// better fix
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, /*remove_flags=*/false); // clang tidy friendly
}
// better fix
int main(int argc, char* argv[]) {
  const bool remove_flags = false;
  ParseCommandLineFlags(&argc, &argv, remove_flags);
}
```

一般来说较好的解决方案是避免使用 `bool` 类型参数，使用枚举类型来替代。枚举类型可读性好，扩展性好，能表示更多的状态选项：

```c++
enum ShouldRemoveFlags { kDontRemoveFlags, kRemoveFlags };
void ParseCommandLineFlags(int* argc, char*** argv, ShouldRemoveFlags remove_flags);
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, kDontRemoveFlags);
}
// use enum class
enum class ShouldRemoveFlags { kNo, kYes };
void ParseCommandLineFlags(int* argc, char*** argv, ShouldRemoveFlags remove_flags);
int main(int argc, char* argv[]) {
  ParseCommandLineFlags(&argc, &argv, ShouldRemoveFlags::kNo);
}
```

