## Tip of the Week #64: Raw String Literals

原始字符串 `raw string` 字面值，不需要转义特殊字符：

`R"tag(whatever you want to say)tag"`

`tag` 可以有 0～16 个字符，但不能包含，小括号，反斜杠，空白字符。

`raw string` 应用到多行字符串的时候，需要注意额外的换行符和前置空格。

```c++
// concert and concert_raw are equal
const std::string concert = "id: 17\n"
                            "artist: \"Beyonce\"\n"
                            "date: \"Wed Oct 10 12:39:54 EDT 2012\"\n"
                            "price_usd: 200\n";
const std::string concert_raw = R"(id: 17
artist: "Beyonce"
date: "Wed Oct 10 12:39:54 EDT 2012"
price_usd: 200
)";
assert(concert == concert_raw);

// non-empty tag is useful when the sequence )" happens to appear in your string
std::string my_string = R"foo(This contains quoted parens "()")foo";
```

`raw string` 不是特别有用，可以合理的应用到单行字符串上，尤其是正则表达式。

`R"regexp((?:"(?:\\"|[^"])*"|'(?:\\'|[^'])*'))regexp";`

