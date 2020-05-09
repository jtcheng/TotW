## Tip of the Week #3: String Concatenation and operator+ vs. StrCat()

一般不建议使用字符串连接符来拼接多个字符串，那应该怎样高效的拼接多个字符串呢？

当拼接两个字符串的时候，下面的两种方式性能相当：

```c++
std::string foo = LongString1();
std::string bar = LongString2();
std::string foobar = foo + bar;  // operator+

// use abseil
std::string foo = LongString1();
std::string bar = LongString2();
std::string foobar = absl::StrCat(foo, bar);
```

当拼接多个字符串的时候，情况变得不同了，使用 `abseil` 库性能更好：

```c++
std::string foo = LongString1();
std::string bar = LongString2();
std::string baz = LongString3();
string foobar = foo + bar + baz;
// <==>
// std::string temp = foo + bar;  // operator+
// std::string foobar = std::move(temp) + baz;  // operator+

// use abseil
std::string foo = LongString1();
std::string bar = LongString2();
std::string baz = LongString3();
std::string foobar = absl::StrCat(foo, bar, baz);
```

当使用标准库来拼接多个字符串的时候：

- 使用 `string::reserve` 来避免由于 `string::capacity` 不够而导致的内存重分配问题。
- 使用 `string::append` 来避免由于 `string::operator+` 无法处理多个字符串的问题。

```c++
std::string foo{"qwertyuiop"};
std::string bar{"asdfghjkl"};
std::string baz{"zxcvbnm"};
std::string foobarbaz;
foobarbaz.reserve(foo.size() + bar.size() + baz.size());  // reserve
foobarbaz.append(foo).append(bar).append(baz);  // append
foobarbaz.shrink_to_fit();  // reduces unused memory
```

