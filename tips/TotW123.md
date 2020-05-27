## Tip of the Week #123: `absl::optional` and `std::unique_ptr`

类数据成员的包装属性，一般根据实际情况按照下面的优先级来使用：

1. 无包装形式
2. `std::optional` 包装形式
3. `std::unique_ptr` 包装形式

```c++
#include <memory>
#include "third_party/absl/types/optional.h"
#include ".../bar.h"

class Foo {
  ...
 private:
  Bar val_;
  absl::optional<Bar> opt_; // `std::optional` also works
  std::unique_ptr<Bar> ptr_;
};
```

三种类数据成员包装属性的对比情况：

|                                 | `Bar`                   | `absl::optional`                           | `std::unique_ptr`                                                |
| :------------------------------ | :---------------------- | :----------------------------------------- | :--------------------------------------------------------------- |
| Supports delayed construction   |                         | ✓                                          | ✓                                                                |
| Always safe to access           | ✓                       |                                            |                                                                  |
| Can transfer ownership of `Bar` |                         |                                            | ✓                                                                |
| Can store subclasses of `Bar`   |                         |                                            | ✓                                                                |
| Movable                         | If `Bar` is movable     | If `Bar` is movable                        | ✓                                                                |
| Copyable                        | If `Bar` is copyable    | If `Bar` is copyable                       |                                                                  |
| Friendly to CPU caches          | ✓                       | ✓                                          |                                                                  |
| No heap allocation overhead     | ✓                       | ✓                                          |                                                                  |
| Memory usage                    | `sizeof(Bar)`           | `sizeof(Bar) + sizeof(bool)`               | `sizeof(Bar*)` when null, `sizeof(Bar*) + sizeof(Bar)` otherwise |
| Object lifetime                 | Same as enclosing scope | Restricted to enclosing scope              | Unrestricted                                                     |
| Call `f(Bar*)`                  | `f(&val_)`              | `f(&opt_.value())` or `f(&*opt_)`          | `f(ptr_.get())` or `f(&*ptr_)`                                   |
| Remove value                    | N/A                     | `opt_.reset();` or `opt_ = absl::nullopt;` | `ptr_.reset();` or `ptr_ = nullptr;`                             |

