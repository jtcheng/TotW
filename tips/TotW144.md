## Tip of the Week #144: Heterogeneous Lookup in Associative Containers

对于关联容器，我们可以通过一个 `key` 去查询对应的 `value` ，这里的 `key` 必须是与插入时类型相同的对象。这个约束比较严格，有时候使用很不方便，可能会需要构造临时 `key` 对象。

**异构查询**，使用任何 `key` 都可以进行查询：

1. 自定义一个比较函数，支持所有可能会使用到的 `key` 的比较
2. 必须声明一个内部的 `is_transparent` 类型，类型随意，这类型就是一个标志

```c++
class Employee {
  public:
  explicit Employee(int id, std::string const &name) : id_(id), name_(name) {}
  int getId() const { return id_; }
  std::string getName() const { return name_; }
  private:
  int id_;
  std::string name_;
};
struct CompareId {
  using is_transparent = void; // must add this line
  bool operator()(Employee const &e1, Employee const &e2) const {
    return e1.getId() < e2.getId();
  }
  bool operator()(int id, Employee const &e) const { return id < e.getId(); }
  bool operator()(Employee const &e, int id) const { return e.getId() < id; }
};

std::set<Employee, CompareId> employees = {Employee(1, "John"),
                                           Employee(2, "Bill")};
std::cout << employees.find(1)->getName() << '\n'; // use `1` to find John
```

从 `C++14` 开始 `std::{map,set,multimap,multiset}` 的 `Lookup` 类函数都支持异构查询。

