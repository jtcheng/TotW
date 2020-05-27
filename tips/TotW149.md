## Tip of the Week #149: Object Lifetimes vs. `= delete`

`=delete` 的一些不建议的用法：

1. 阻止临时对象

```c++
// prevent temporaries
class Request {
  ...
  // The provided Context must live as long as the current Request
  void SetContext(const Context& context);
  void SetContext(Context&& context) = delete; // prevent temporaries
};

// context lifetime is as long as the current Request ?
Context context;
request.SetContext(context);
```

上面的代码虽然阻止了临时 `Context` 对象，但是仍然没法保证传入的 `Context` 对象的生命周期足够长。这样做把接口变得复杂了，只是解决了一小部分问题，没有多大意义。另外，对象的生命周期问题，没法从语言层面来约束，在接口文档中说明就足够。

2. 阻止对象拷贝

```c++
// prevent copies
future<bool> DNAScan(Config c, const std::string& sequence) = delete; // prevent copies
future<bool> DNAScan(Config c, std::string&& sequence);

// copies is still needed
Config c1 = GetConfig();
Config c2 = GetConfig();
std::string s = GetDNA();
std::string s2 = s; // a copy
// Kick off scans for both configs
auto scan1 = DNAScan(c1, std::move(s));
auto scan2 = DNAScan(c2, std::move(s2));
```

上面的代码假定用户调用的入参是右值，调用完之后不再需要使用入参 `sequence`，但是实际情况往往不是这样的。这样做使得 `API` 的调用变得不方便，对外公开的接口最好不要这样设计，当对象拷贝成本较大时，对内接口可以这样设计。

不要滥用 `=delete` 来删除一些函数来达到某种目的，像是上面的两种情况，就不是好的设计，应该避免，不要凭空想象客户端代码对 `API` 的使用情况。

