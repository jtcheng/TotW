## Tip of the Week #61: Default Member Initializers

类非静态数据成员默认初始化 `NSDMIs` ：

```c++
// fundamental type Non-static data members
class Client {
private:
  int chunks_in_flight_ = 0;
};

struct Options {
  bool use_loas = true;
  bool log_pii = false;
  int timeout_ms = 60 * 1000;
  std::array<int, 4> timeout_backoff_ms = {10, 100, 1000, 10 * 1000};
};
```

类非静态数据成员默认初始化与类构造函数数据成员初始化列表：

```c++
class Frobber {
public:
  // the constructor provides an initializer for len_
  Frobber() : ptr_(nullptr), len_(0) {} // not use NSDMIs
  Frobber(const char *ptr, size_t len) : ptr_(ptr), len_(len) {} // not use NSDMIs
  // the constructor not provides an initializer for len_
  Frobber(const char *ptr) : ptr_(ptr) {} // use NSDMIs

private:
  const char *ptr_;
  // len_ has a non-static class member initializer (NSDMIs)
  const size_t len_ = strlen(ptr_);
};
```

注意事项：

1. `NSDMIs` 不能提升性能，但能防止数据成员漏初始化
2. 数据成员初始化列表是按照数据成员在类中声明的顺序进行
3. 数据成员初始化列表会接替 `NSDMIs` 来进行数据成员初始化
4. 静态数据成员初始化和 `NSDMIs` 是两码事

