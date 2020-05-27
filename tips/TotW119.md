## Tip of the Week #119: Using-declarations and namespace aliases

符号导入声明和命名空间别名声明：

1. 声明的时候，只在 `.cc` 文件中声明
2. 声明的时候，在最内层的命名空间中声明
3. 声明的时候，使用全名称声明，以 `::` 开头

```c++
namespace example {
namespace makers {
namespace {
using ::otherlib::BazBuilder; // import symbol, such as `using std::cout`
using ::mylib::BarFactory;
namespace abc = ::applied::bitfiddling::concepts; // namespace aliases
// Private helper code here.
}  // namespace
// Interface implementation code here.
}  // namespace makers
}  // namespace examp
```

注意事项：

1. 声明位置在作用域上要尽量靠近即将被使用的名称空间

```c++
// problem Quz, maybe breaked by ::example::Quz or ::example::util::Quz
using ::foo::Quz;

namespace example 
namespace util {
using ::foo::Bar;
}  // namespace example
}  // namespace util
```

2. 避免使用相对名称空间声明

```c++
namespace example {
namespace util {
// no leading `::` so myabe breaked by ::example::foo::Bar or ::example::foo::util::Bar
using foo::Bar;
}  // namespace example
}  // namespace util
```

3. 合理使用匿名命名空间

```c++
namespace example {
namespace util {
namespace { // Unnamed Namespaces
// Put all using-declarations in here. Don't spread them over the file.
using ::foo::Bar;
using ::foo::Quz;
// In here, Bar and Quz refer inalienably to your aliases
}  // namespace
// Can use both Bar and Quz here too
// But don't declare any entities called Bar or Quz yourself now
```

