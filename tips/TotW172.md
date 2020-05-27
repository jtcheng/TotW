## Tip of the Week #172: Designated Initializers

结构体指定初始化：

1. 对于给定了初始化值的，就用那个初始化值
2. 对于没有给定初始化值的，使用 `={}` 来初始化
   1. 对于 `POD` 类型，  `={}` 就是"零"初始化
   2. 对于其他类型， `={}` 相当于调用默认构造函数

```c++
struct Point {
  double x;
  double y;
  double z;
};

Point point = {
    .x = 3.0,
    // y will be 0.0 // y = {}
    .z = 5.0,
};

const Point character_position = { .x = 3.0 };

std::vector<Point> points;
points.push_back(Point{.x = 3.0, .y = 3.0});
points.push_back(Point{.x = 4.0, .y = 4.0});
```

结构体指定初始化是聚合 `aggregate` 初始化的一种：

```c++
struct Point {
  double x;
  double y;
  double z;
};
std::cout << std::boolalpha << std::is_aggregate_v<Point>; // true
```

其实 `C99` 早就支持这种初始化方式，但是和 `C++` 在语义上是不同的，在使用上也更严格。