## Tip of the Week #10: Splitting Strings, not Hairs

字符串分割是一个比较常见的编程需求，`abseil` 库比较好用：

```c++
// Splits on commas. Stores in vector of string_view (no copies).
std::vector<absl::string_view> v = absl::StrSplit("a,b,c", ',');

// Splits on commas. Stores in vector of string (data copied once).
std::vector<std::string> v = absl::StrSplit("a,b,c", ',');

// Splits on literal string "=>" (not either of "=" or ">")
std::vector<absl::string_view> v = absl::StrSplit("a=>b=>c", "=>");

// Splits on any of the given characters (',' or ';')
using absl::ByAnyChar;
std::vector<std::string> v = absl::StrSplit("a,b;c", ByAnyChar(",;"));

// Stores in various containers (also works w/ absl::string_view)
std::set<std::string> s = absl::StrSplit("a,b,c", ',');
std::multiset<std::string> s = absl::StrSplit("a,b,c", ',');
std::list<std::string> li = absl::StrSplit("a,b,c", ',');
std::deque<std::string> d = absl::StrSplit("a,b,c", ',');

// Yields "a"->"1", "b"->"2", "c"->"3"
std::map<std::string, std::string> m = absl::StrSplit("a,1,b,2,c,3", ',');
```

使用标准库又该怎么实现呢，下面一共设计两个版本？

第一个版本只能处理字符串：

```c++
// string split
void split(std::string_view sv, std::vector<std::string> &output, char splitval = ' ') {
  std::string_view::size_type first = 0, second = 0, length = sv.size();
  while (second != std::string_view::npos && first < length) {
    second = sv.find(splitval, first);
    if (first != second) {
      output.emplace_back(sv.substr(first, second - first));
    }
    first = second + 1;
  }
}
```

第二个版本是一个通用版本：

```c++
// generic split
template <typename InIt, typename OutIt, typename T, typename F>
OutIt split(InIt first, InIt last, OutIt target, T splitval, F binfun) {
  InIt second = first;
  while (second != last && first != last) {
    second = std::find(first, last, splitval);
    if (first != second) {
      *target++ = binfun(first, second);
    }
    first = std::next(second);
  }
  return target;
}
```

测试下性能，竟然通用版本的性能要稍好一点点：

```c++
#include <chrono>
#include <iostream>
#include <vector>

template <typename TFunc> void RunAndMeasure(const char *title, TFunc func) {
  const auto start = std::chrono::steady_clock::now();
  auto res = func();
  const auto end = std::chrono::steady_clock::now();
  std::cout << title << ": "
            << std::chrono::duration<double, std::milli>(end - start).count()
            << " ms, res " << res << '\n';
}

int main(int argc, const char **argv) {
  const int ITERS = argc > 1 ? atoi(argv[1]) : 10000;
  const std::string str{
      "  q w e r t y u i o p a s d  f g h j k l z x c v b n m  "};

  RunAndMeasure("string_view split", [ITERS, &str]() {
    std::size_t sizes = 0;
    for (int i = 0; i < ITERS; ++i) {
      std::vector<std::string> output;
      split(str, output, ' ');
      sizes += output.size();
    }
    return sizes;
  });
  // string_view split: 12.4279 ms, res 260000

  RunAndMeasure("generic split", [ITERS, &str]() {
    auto binfun = [](auto first, auto second) {
      return std::string(first, second);
    };
    std::size_t sizes = 0;
    for (int i = 0; i < ITERS; ++i) {
      std::vector<std::string> output;
      split(str.begin(), str.end(), std::back_inserter(output), ' ', binfun);
      sizes += output.size();
    }
    return sizes;
  });
  // generic split: 10.4446 ms, res 260000

  return 0;
}
```

