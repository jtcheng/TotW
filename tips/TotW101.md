## Tip of the Week #101: Return Values, References, and Lifetimes

先来看一段代码：

```c++
const string& name = obj.GetName();
std::unique_ptr<Consumer> consumer(new Consumer(name));
```

针对代码提三个问题：

1. `GetName()` 的返回值是什么类型
2. `name` 是什么类型
3. 如果 `GetName()` 返回引用，`name` 是否会有什么周期问题

我们使用 `string` 举例来模拟一些情形：

#### case 1

```c++
// case #1
string GetName(){...};
string name = GetName(); // RVO
```

#### case 2

```c++
// case #2
string& GetName(){...};
// or
const string& GetName(){...};
string name = GetName(); // two names, hence a copy
```

#### case 3

```c++
// case #3
string GetName(){...};
string& name = GetName(); // not compiles
```

#### case 4

```c++
// case #4
const string& GetName(){...};
string& name = GetName(); // not compiles, impossible to drop const
```

#### case 5:

```c++
// case #5
const string& GetName(){...};
const string& name = GetName(); // const reference just like a pointer, works well
```

#### case 6: 

```c++
// case #6
string& GetName(){...};
string& name = GetName(); // same as case 5, but no const
```

#### case 7:

```c++
// case #7
string& GetName(){...};
const string& name = GetName(); // same as case 5
```

#### case 8:

```c++
// case #8
string GetName(){...};
const string& name = GetName(); 
// special-case for const reference, lifetime extend
// temporary string is not destroyed until the reference `name` goes out of scope
```

好好体会这里的 8 种情形，根据上下文选用合适的方式。另外，避免拷贝是好的，但是别把代码搞得复杂不堪。