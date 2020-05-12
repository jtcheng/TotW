## Tip of the Week #86: Enumerating with Class

非限定作用域的枚举类型：

1. 枚举值的作用域和枚举类型的作用域相同
2. 枚举值会隐式的转换为整数类型
3. 现代 `C++` 将枚举值放到了枚举类型的作用域内，但任然保持兼容规则 1

```c++
enum CursorDirection { kLeft, kRight, kUp, kDown };
CursorDirection d = kLeft; // OK: enumerator in scope
int i = kRight;            // OK: enumerator converts to int
// error: redeclarations of kLeft and kRight
enum PoliticalOrientation { kLeft, kCenter, kRight };

CursorDirection d = CursorDirection::kLeft;  // OK in C++11
int i = CursorDirection::kRight;             // OK: still converts to int
```

限定作用域的枚举类型：

1. 将枚举值放到了枚举类型的作用域内
2. 枚举值不能隐式的转换为整数类型

```c++
enum class CursorDirection {kLeft, kRight, kUp, kDown};
CursorDirection d = kLeft;                    // error: kLeft not in this scope
CursorDirection d2 = CursorDirection::kLeft;  // OK
int i = CursorDirection::kRight;              // error: no conversion

// OK: kLeft and kRight are local to each scoped enum
enum class PoliticalOrientation {kLeft, kCenter, kRight};
```

限定作用域的枚举类型的枚举值类型指定：

```c++
// Issue an build error if an enumerator value exceeds the range of the underlying type
// Use "int" as the underlying type for CursorDirection
enum class CursorDirection : int {kLeft, kRight, kUp, kDown};
// Use "char" as the underlying type for CursorDirection
enum class PoliticalOrientation : char {kLeft, kRight, kUp, kDown};

// to underlying type
template <typename T, std::enable_if_t<std::is_enum_v<T>, int> = 0>
constexpr auto toUType(const T &e) noexcept {
  return static_cast<std::underlying_type_t<T>>(e);
};
```

