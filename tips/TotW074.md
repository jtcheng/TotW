## Tip of the Week #74: Delegating and Inheriting Constructors

同一个类的多个构造函数，如何共用相同的代码呢？一个简单的做法是定义一个私有函数来避免代码重复：

```c++
class C {
 public:
  C(int x, string s) { SharedInit(x, s); }
  explicit C(int x) { SharedInit(x, ""); }
  explicit C(string s) { SharedInit(0, s); }
  C() { SharedInit(0, ""); }
 private:
  void SharedInit(int x, string s) { … } // can used by ctor
};
```

这个问题可以使用现代 `C++` 的委托构造来更好的完成：

```c++
class C {
 public:
  C(int x, string s) { … } // delegated constructor
  explicit C(int x) : C(x, "") {}
  explicit C(string s) : C(0, s) {}
  C() : C(0, "") {}
};
```

现代 `C++` 还引入了继承构造来复用基类的构造函数：

```c++
// class D does not add new data members that need to be initialized explicitly
class D : public C {
 public:
  using C::C;  // inherit all constructors from C
  void NewMethod();
};
```

可以合理的使用委托构造和继承构造来消除代码重复，另外，应该尽量设计简单清晰的类，使得类的构造简单清晰化。

