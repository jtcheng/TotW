## Tip of the Week #93: using absl::Span

当处理无所有权的 `string` 时可以使用 `string_view` 来作为函数入参和返回值，是一个轻量级的设计。其实针对这种无所有权的连续存储空间的系列对象，有一个更通用的设计 `std::span` 。在实现上仅仅包含一个指针和一个长度，非常的轻量高效，可以按值传递，`std::span` 需要 `C++20` 的支持。

`abseil` 库的 `absl::Span` :

基本应用：

```c++
void TakesVector(const std::vector<int>& ints);
void TakesSpan(absl::Span<const int> ints);

void PassOnlyFirst3Elements() {
  std::vector<int> ints = MakeInts();
  // We need to create a temporary vector, and incur an allocation and a copy
  TakesVector(std::vector<int>(ints.begin(), ints.begin() + 3));
  // No copy or allocations are made when using Span
  TakesSpan(absl::Span<const int>(ints.data(), 3));
}

void PassALiteral() {
  // This creates a temporary std::vector<int>
  TakesVector({1, 2, 3});
  // Span does not need a temporary allocation and copy, so it is faster
  TakesSpan({1, 2, 3});
}
void IHaveAnArray() {
  int values[10] = ...;
  // Once more, a temporary std::vector<int> is created
  TakesVector(std::vector<int>(std::begin(values), std::end(values)));
  // Just pass the array. Span detects the size automatically
  // No copy was made
  TakesSpan(values);
}
```

一个更安全的 `memcpy` ：

```c++
// Bad code
void BadUser() {
  int src[] = {1, 2, 3};
  int dest[2];
  memcpy(dest, src, ABSL_ARRAYSIZE(src) * sizeof(int)); // oops! Dest overflowed
}
// A simple example, but takes advantage that the sizes of the Spans are known
// and prevents the above mistake
template <typename T>
bool SaferMemCpy(absl::Span<T> dest, absl::Span<const T> src) {
  if (src.size() > dest.size()) {
    return false;
  }
  memcpy(dest.data(), src.data(), src.size() * sizeof(T));
  return true;
}

void GoodUser() {
  int src[] = {1, 2, 3}, dest[2];
  // No overflow
  SaferMemCpy(absl::MakeSpan(dest), absl::Span<const int>(src));
}
```

保持指针容器的常量属性：

```c++
void FrobFastWeak(const std::vector<Foo*>& v);
void FrobSlowStrong(const std::vector<const Foo*>& v);
void FrobFastStrong(absl::Span<const Foo* const> v);
// fast and easy to type but not const-safe
FrobFastWeak(v);
// slow and noisy, but safe
FrobSlowStrong(std::vector<const Foo*>(v.begin(), v.end()));
// fast, safe, and clear
FrobFastStrong(v);
```

```c++
// Bad code
class DontDoThis {
 public:
  // Don’t modify my Foos, pretty please
  const std::vector<Foo*>& shallow_foos() const { return foos_; }
 private:
  std::vector<Foo*> foos_;
};
void Caller(const DontDoThis& my_class) {
  // Modifies a foo even though my_class is a reference-to-const
  my_class->foos()[0]->SomeNonConstOp();
}
// Good code
class DoThisInstead {
 public:
  absl::Span<const Foo* const> foos() const { return foos_; }
 private:
  std::vector<Foo*> foos_;
};
void Caller(const DoThisInstead& my_class) {
  // This one doesn't compile
  // my_class.foos()[0]->SomeNonConstOp();
}
```

