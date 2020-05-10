## Tip of the Week #45: Avoid Flags, Especially in Library Code

不要使用标志 `Flags` 来控制代码的运行流程，尤其是在库代码中。

`Flags` 就是全局变量，代码运行起来之后很难知道 `Flags` 的确切值，代码很多地方都可能会修改 `Flags` ，修改之后也不会通知别人。一般使用一个公共的函数来修改 `Flags` ，然后记录日志，可以在一定程度上缓解问题。

`Flags` 会使得删除老代码变得困难，很多代码都依赖于老代码中定义的各种 `Flags` ，维护多个 `Flags` 之间的关系也很复杂。

`Flags` 也可以在一些情况下合理的使用：

- 调试 `Flags` 控制调试信息
- 特性 `Flags` 控制产品功能
- `main` 函数启动时的键值对 `Flags` 参数

`Flags` 的替换方案：

- 使用编译期常量
- 显式的使用配置文件

