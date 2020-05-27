## Tip of the Week #126: `make_unique` is the new `new`

智能指针相对于原始指针，主要是解决了资源的所有权问题和资源的生命周期问题。

避免使用原始指针和 `new` 运算符：

1. 智能指针可以在代码中明确的反应出资源的所有权，原始指针没法做到
2. `std::make_unique()` 非常直观的表达了意图，`new` 没法表达额外的信息
3. 直接使用安全的单步操作 `std::make_unique()` 来创建独占智能指针，避免两步走的方式
4. `std::make_shared` 和`std::make_unique` 类似，根据实际情况优先使用独占指针而不是共享指针

