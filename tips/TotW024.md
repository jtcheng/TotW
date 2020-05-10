## Tip of the Week #24: Copies, Abbrv.

当我们不确定对象的拷贝是否会发生时，我们可以检查有多少个具名变量使用了我们的对象，多个具名变量意味着多份拷贝，这个规则同样适用于`RVO` 。

```c++
std::string build();

std::string foo(std::string arg) {
  return arg;  // no copying here, only one name for the data “arg”
}

void bar() {
  std::string local = build();  // only 1 instance -- only 1 name

  // no copying, a reference won’t incur a copy
  std::string& local_ref = local;

  // one copy operation, there are now two named collections of data
  std::string second = foo(local);
}
```

大多数时候，这些都不重要，更重要的是确保代码的可读性和一致性，而不是担心副本和性能。`C++` 中零时对象的拷贝规则一直在发展，在做优化之前要先做优化分析找到热点代码，不要盲目优化。