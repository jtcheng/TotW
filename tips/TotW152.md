## Tip of the Week #152: `AbslHashValue` and You

`absl::Hash` 框架现在是 `abseil` 库 `absl::{flat,node}_hash_{set,map}` 的默认哈希实现，哈希值的计算变得很简单，提供了所谓的**万能哈希函数**。因此，很多对象都可以放到哈希容器里面，而不用太担心哈希函数的设计。

```c++
struct Song {
  std::string name;
  std::string artist;
  absl::Duration duration;

  // AbslHashValue
  template <typename H>
  friend H AbslHashValue(H h, const Song& s) {
    return H::combine(std::move(h), s.name, s.artist, s.duration);
  }
  // operator == and != omitted for brevity
};
```

