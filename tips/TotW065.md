## Tip of the Week #65: Putting Things in their Place

现代 `C++` 的 `emplace()` 容器元素插入新方法：

1. 老式容器元素插入方法：
   1. 创建一个临时对象
   2. 拷贝或者移动临时对象到容器中
2. 新式容器元素插入方法，直接在容器内构造对象，比较高效

```c++
class Foo {
 public:
  Foo(int x, int y);
  …
};

void addFoo() {
  std::vector<Foo> v1;
  v1.push_back(Foo(1, 2)); // create a temporary Foo and then copy or move to v1
}
void addBetterFoo() {
  std::vector<Foo> v2;
  v2.emplace_back(1, 2); // avoiding temporary Foo
}
```

几乎全部 `STL` 容器都新增了 `emplace()` 类容器元素插入新方法，另外由于是在容器内直接构造对象，性能要好一点，对只能移动的对象也更友好。

