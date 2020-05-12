## Tip of the Week #36: New Join API

`abseil` 库新的 `join API` ，这个和字符串拼接有些类似：

```c++
std::vector<std::string> v = {"a", "b", "c"};
std::string s = absl::StrJoin(v, "-");
// s == "a-b-c"

std::vector<absl::string_view> v = {"a", "b", "c"};
std::string s = absl::StrJoin(v.begin(), v.end(), "-");
// s == "a-b-c"

std::vector<int> v = {1, 2, 3};
std::string s = absl::StrJoin(v, "-");
// s == "1-2-3"

const int a[] = {1, 2, 3};
std::string s = absl::StrJoin(a, "-");
// s == "1-2-3"

std::map<std::string, int> m = {{"a", 1}, {"b", 2}, {"c", 3}};
std::string s = absl::StrJoin(m, ";", absl::PairFormatter("="));
// s == "a=1;b=2;c=3"

std::vector<Foo> foos = GetFoos();
std::string s = absl::StrJoin(foos, ", ", [](std::string* out, const Foo& foo) {
  absl::StrAppend(out, foo.ToString());
});
```

使用标准库又该怎么设计呢：

```c++
// generic strjoin
template <typename InIt, typename F>
std::string strjoin(InIt first, InIt last, std::string_view sep, F &&fmt) {
  std::string Result{};
  // Append first element
  if (first != last) {
    Result += fmt(*first);
    ++first;
  }
  for (; first != last; ++first) {
    // Add separator
    Result += sep;
    // Add element
    Result += fmt(*first);
  }
  return Result;
}

int main() {
  const std::vector<int> v = {1, 2, 3};
  auto fmt = [](auto i) { return std::to_string(i); };
  auto str = strjoin(v.begin(), v.end(), "-", fmt);
  std::cout << str << std::endl; // 1-2-3

  return 0;
}
```

