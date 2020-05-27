## Tip of the Week #136: Unordered Containers

这篇文章主要介绍了 `abseil` 库增强的哈希容器：

1.  `absl::flat_hash_map`
2.  `absl::flat_hash_set`
3.  `absl::node_hash_map`
4.  `absl::node_hash_set`

其中的 `3, 4` 和 `C++` 标准库的哈希容器在内存布局上是类似的，`1, 2` 在内存布局上更紧凑，对 `CPU` 缓存更友好。因此，`abseil` 库建议大家优先使用 `1, 2` ，对于仅仅使用标准的 `C++` 项目就没的选了。

更多更新的信息请参考下面的连接：

[abseil container](https://abseil.io/docs/cpp/guides/container)

