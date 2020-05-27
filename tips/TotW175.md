## Tip of the Week #175: Changes to Literal Constants in C++14 and C++17

数字字面值的使用用例：

```c++
// both are 42
int iD = 42;
int iB = 0b00101010;
int iO = 052;
int iH = 0x2A;

// digit Separators
int d = 1'000'000'000;
ind b = 0b1110'0000;

// hex Floating Point Literals
double d = 0x2Ap12;  // 0x2A << 12
double d1= 0x1p-10;  // 1.0 / 2^10
double d2 = 0.314e5; // 0.314 * 10^5
```

使用建议：

1. 当涉及到底层数据，需要明示二进制位的时候，使用二进制数字字面值
2. 当数字字面值较长的时候，合理的使用分隔符
   1. 对于十进制数字建议使用 `3` 位间隔的分隔符
   2. 对于二进制数字建议使用 `4` 位间隔的分割符
   3. 对于十六进制数字建议使用 `2, 4, 8` 位间隔的分隔符

