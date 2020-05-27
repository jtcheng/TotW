## Tip of the Week #161: Good Locals and Bad Locals

局部变量的使用建议：

1. 不要定义无关紧要的局部变量
2. 根据上下文合理的使用局部变量
   1. 局部变量，变量名应该携带有用的信息
   2. 局部变量的使用，应该能简化代码，消除重复
   3. 局部变量的作用域尽可能的小

不要定义无关紧要的局部变量：

```c++
MyType value = SomeExpression(args);
return value;
// remove useless local variable
return SomeExpression(args);

auto actual = SortedAges(args);
EXPECT_THAT(actual, ElementsAre(21, 42, 63));
EXPECT_THAT(SortedAges(args), ElementsAre(21, 42, 63));
```

根据上下文合理的使用局部变量来改善代码：

```c++
myproto.mutable_submessage()->mutable_subsubmessage()->set_foo(21);
myproto.mutable_submessage()->mutable_subsubmessage()->set_bar(42);
myproto.mutable_submessage()->mutable_subsubmessage()->set_baz(63);
// user local variable to simple the code
auto& subsubmessage = *myproto.mutable_submessage()->mutable_subsubmessage();
subsubmessage.set_foo(21);
subsubmessage.set_bar(42);
subsubmessage.set_baz(63);

// range based for loop and structured bindings
for (const auto& [name, age] : ages_by_name) {
  if (IsDisallowedName(name)) continue;
  if (age < 18) children.insert(name);
}
```

