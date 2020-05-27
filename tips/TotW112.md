## Tip of the Week #112: emplace vs. push_back

现代 `C++` 为内置的标准容器，添加 `emplace` 类插入函数，可以在容器内直接构造，插入对象元素：

```c++
std::vector<string> my_vec;
my_vec.push_back("foo");     // This is OK, so...
my_vec.emplace_back("foo");  // This is also OK, and has the same result

std::set<string> my_set;
my_set.insert("foo");        // Same here: any insert call can be
my_set.emplace("foo");       // rewritten as an emplace call
```

由于 `emplace` 是直接在容器内构造对象，没有创建临时对象，因而效率稍好一点。正因为是直接构造，少了临时对象创建的步骤，有时候会出问题：

```c++
vector<vector<int>> vv;
vv.emplace_back(1024); // use `1024` to construct a vector<int>
std::cout << vv[0].size() << std::endl; // 1024
vv.push_back(1024); // not compiles, avoid surprise
```

对于这两种容器元素插入方法，我觉得都可以，其实效率相差不大，我倾向于尽量使用 `emplace` ，至于上面的那个问题，一般遇不到，知道就可以。

