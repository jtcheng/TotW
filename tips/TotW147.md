## Tip of the Week #147: Use Exhaustive `switch` Statements Responsibly

`switch` 语句与枚举变量：

```c++
enum AnEnum { kFoo, kBar, kBaz };
std::string AnEnumToString(AnEnum an_enum) {
  switch (an_enum) {
  case kFoo:
    return "kFoo";
  case kBar:
    return "kBar";
  case kBaz:
    return "kBaz";
  // No default
  // The API of AnEnum guarantees no new enumerators will be added
  }
  // LOG(ERROR) << "Unexpected value for AnEnum: " << an_enum;
  // return kUnknownAnEnumString;
}
```

对于上面的代码有下面的两个问题，注意注释部分的代码：

1. 这里没有 `default` 分支，当未处理的枚举值传进来的时候会发生未定义行为
2. 当别人修改了 `AnEnum` ，比如进行了增删枚举值的时候，可能会影响编译

对于没有 `default` 分支的 `switch-case-enum` 语句：

1. `-Werror` 可以捕捉到没有处理某些枚举值的情况
2. 对于枚举值的增删，应该修改多处代码，保持一致
3. 要么保证枚举类型不会再新增枚举值，要么保证处理函数不会传入未处理的枚举值
4. 当我们要公开一个枚举类型给其他客户端使用的时候也要考虑到这些问题

