## Tip of the Week #120: Return Values are Untouchable

`C++` 标准规定，函数的返回变量在被复制或者被移动之后还能被**析构函数**隐式访问到。但是由于优化原因，这里的复制和移动操作可能会被优化掉。

因此，当一个函数的 `return var` 语句执行之后，就不要再访问其返回变量 `var` 了，即使是在析构函数里也不要访问。

```c++
struct RunWhenOutOfScope {
  RunWhenOutOfScope(std::function<void()> f) { cleanup = f; }
  ~RunWhenOutOfScope() { cleanup(); }
private:
  std::function<void()> cleanup;
};
MyStatus DoSomething() {
  MyStatus status;
  auto log_on_error = RunWhenOutOfScope([&status] {
    if (!status.ok()) LOG(ERROR) << status;
  });
  status = DoA();
  if (!status.ok()) return status;
  status = DoB();
  if (!status.ok()) return status;
  status = DoC();
  if (!status.ok()) return status;
  return status; // case #1
  // return MyStatus(); // case #2
}
MyStatus status1 = DoSomething();
```

分析上面的代码：

`case #1` 由于编译器的 `RVO` ， `status` 和 `status1` 是同一个东西，其生命周期在函数返回之后还存在，因此，`RunWhenOutOfScope` 的析构函数能访问到，但是这个依赖于编译器的 `RVO` ，不是好的的设计。

`case #2` 由于 `NRVO`，因此 `status` 被移动到了 `status1` ，`RunWhenOutOfScope` 的析构函数访问到的是一个被移动走了的 `status`，是未定义的行为。

不要依赖于编译器的行为，老老实实的写出明确的代码会更好：

```c++
MyStatus DoSomething() {
  MyStatus status;
  status = DoA();
  if (!status.ok()) return status;
  status = DoB();
  if (!status.ok()) return status;
  status = DoC();
  if (!status.ok()) return status;
  return status;
}
MyStatus DoSomethingAndLog() {
  MyStatus status = DoSomething();
  if (!status.ok()) LOG(ERROR) << status;
  return status;
}
```

