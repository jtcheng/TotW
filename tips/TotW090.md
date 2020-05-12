## Tip of the Week #90: Retired Flags

在 [Tip of the Week #45: Avoid Flags, Especially in Library Code][TotW045] 中我们提到了使用 `Flags` 的问题，那么当问题出现了，我们又该怎样去清理一个不用的 `Flags` 呢？

可以使用 `ABSL_RETIRED_FLAG` 来"退役"一个不用的 `Flags` ：

一个"退役"了的 `Flags` 不会出现在 `--help` 里面，但是可以继续作为命令行参数来使用，并触发错误日志，方便定位。

使用 `abseil` 库管理的 `Flags` 可以使用下面的步骤来清理，下面使用 `FLAGS_frobber` 来举例说明：

1. 从代码里删除使用了`FLAGS_frobber` 的代码
2. 修改`FLAGS_frobber` 的定义
   1. `ABSL_FLAG(type, frobber, "default", "Which frobber to use?");`
   2. `ABSL_RETIRED_FLAG(type, frobber, "default", "retired");`

3. 发行新的版本文件
4. 修改配置文件，直到没有关于`FLAGS_frobber` 的缺失问题
5. 删除 2.2 的 `ABSL_RETIRED_FLAG` 定义


[TotW045]: TotW045.md

