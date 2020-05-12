## Tip of the Week #59: Joining Tuples

可以使用 `abseil` 库来拼接 `tuple` ，使用很方便：

```c++
auto tup = std::make_tuple(123, "abc", 0.456);
std::string s = absl::StrJoin(tup, "-");
s = absl::StrJoin(std::make_tuple(123, "abc", 0.456), "-");
int a = 123;
std::string b = "abc";
double c = 0.456;
// Works, but copies all arguments
s = absl::StrJoin(std::make_tuple(a, b, c), "-");
// No copies, but only works with lvalues
s = absl::StrJoin(std::tie(a, b, c), "-");
// No copies, and works with lvalues and rvalues
s = absl::StrJoin(std::forward_as_tuple(123, MakeFoo(), c), "-");

// user defined formatter
struct Foo {};
struct Bar {};
struct MyFormatter {
  void operator()(string* out, const Foo& f) const {
    out->append("Foo");
  }
  void operator()(string* out, const Bar& b) const {
    out->append("Bar");
  }
};
std::string s = absl::StrJoin(std::forward_as_tuple(Foo(), Bar()), "-", MyFormatter());
EXPECT_EQ(s, "Foo-Bar");
```

使用标准库又该怎么设计呢：

```c++
template <typename... T, typename F>
std::string strjoin(const std::tuple<T...> &tuple, std::string_view sep, F &&fmt) {
  std::string Result{};
  auto join = [&Result, &sep, &fmt](T const &... t) {
    decltype(sizeof...(T)) n{0};
    ((Result.append(fmt(t)).append(++n != sizeof...(T) ? sep : "")), ...);
  };
  std::apply(join, tuple);
  return Result;
}

int main() {
  const std::tuple t{1.1, 2, 3.0};
  auto fmt = [](auto i) { return std::to_string(i); };
  auto str = strjoin(t, "-", fmt);
  std::cout << str << std::endl; // 1.100000-2-3.000000

  return 0;
}
```

