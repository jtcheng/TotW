## Tip of the Week #55: Name Counting and unique_ptr

`std::unique_ptr` 就像是它名称所表示的那样，是对象的唯一所有者。在代码的任意给定点，`std::unique_ptr` 持有的对象只能有一个可区分的名称引用。

```c++
std::unique_ptr<Foo> NewFoo() {
  return std::unique_ptr<Foo>(new Foo(1));
}

void AcceptFoo(std::unique_ptr<Foo> f) { f->PrintDebugString(); }

void Simple() {
  AcceptFoo(NewFoo());
}

void DoesNotBuild() {
  // two names, g and AcceptFoo's f
  std::unique_ptr<Foo> g = NewFoo();
  AcceptFoo(g); // DOES NOT COMPILE!
}

void SmarterThanTheCompilerButNot() {
  Foo* j = new Foo(2);
  // Compiles, BUT VIOLATES THE RULE and will double-delete at runtime.
  std::unique_ptr<Foo> k(j);
  std::unique_ptr<Foo> l(j);
}
```

`std::unique_ptr` 不能拷贝，只能通过移动来转移所有权，移动之后任然保证只有一个可区分的名称引用：

```c++
 void EraseTheName() {
   std::unique_ptr<Foo> h = NewFoo();
   AcceptFoo(std::move(h)); // Fixes DoesNotBuild with std::move
   // Don't use h again
}
```

对象的可区分名称计数是现代 `C++` 中一个很有用的技术，能帮助我们识别不必要的对象拷贝，能帮助我们正确的使用 `std::unique_ptr` ，`std::move` 相当于转移了一个名称，保持了名称数量不变。

