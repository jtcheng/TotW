## Tip of the Week #77: Temporaries, Moves, and Copies

资源可区分引用名称计数的三种情况：

1. 2 个名称：资源的拷贝
2. 1 个名称：资源的移动，没有拷贝
3. 0 个名称：临时资源，没有拷贝

多个可区分引用名称，意味着资源的拷贝：

```c++
std::vector<int> foo;
FillAVectorOfIntsByOutputParameterSoNobodyThinksAboutCopies(&foo);
std::vector<int> bar = foo;     // Yep, this is a copy

std::map<int, string> my_map;
string forty_two = "42";
my_map[5] = forty_two;          // Also a copy: my_map[5] counts as a name
```

仅仅允许一个可区分的引用名称，意味着资源的移动：

```c++
std::vector<int> GetSomeInts() {
  std::vector<int> ret = {1, 2, 3, 4};
  return ret;
}
// Just a move: either "ret" or "foo" has the data, but never both at once
std::vector<int> foo = GetSomeInts();

// Not a copy, move allows the compiler to treat foo as a
// temporary, so this is invoking the move constructor for std::vector<int>
//
// Note that it isn’t the call to std::move that does the moving,
// it’s the constructor. The call to std::move just allows foo to
// be treated as a temporary (rather than as an object with a name)
std::vector<int> bar = std::move(foo); // DO NOT use foo again
```

没有可区分引用名称，意味着临时资源：

```c++
void OperatesOnVector(const std::vector<int>& v);

// No copies: the values in the vector returned by GetSomeInts()
// will be moved (O(1)) into the temporary constructed between these
// calls and passed by reference into OperatesOnVector()
OperatesOnVector(GetSomeInts());
```

正确使用这几个规则基本上就可以判断绝大部分的资源拷贝，移动的问题了。老代码，老思维模式中的一些观点在现代 `C++` 中已经不再正确了，我们应该编写复合直观的代码，而不是墨守陈规。

