## Tip of the Week #122: Test Fixtures, Clarity, and Dataflow

测试代码和产品代码的一个区别是，测试代码是未经测试的。因此，测试代码就更需要清晰，明了，测试用例，测试数据需要合理的组织。

```c++
class FrobberTest : public ::testing::Test {
 protected:
  void ConfigureExampleA() {
    example_ = "Example A";
    frobber_.Init(example_);
    expected_ = "Result A";
  }
  void ConfigureExampleB() {
    example_ = "Example B";
    frobber_.Init(example_);
    expected_ = "Result B";
  }
  Frobber frobber_;
  string example_;
  string expected_;
};

TEST_F(FrobberTest, CalculatesA) {
  ConfigureExampleA();
  string result = frobber_.Calculate();
  EXPECT_EQ(result, expected_);
}

TEST_F(FrobberTest, CalculatesB) {
  ConfigureExampleB();
  string result = frobber_.Calculate();
  EXPECT_EQ(result, expected_);
}
```

上面的测试代码比较啰嗦，不直观，需要阅读上下文来理解，测试数据流也不清晰。

```c++
TEST(FrobberTest, CalculatesA) {
  Frobber frobber;
  frobber.Init("Example A");
  EXPECT_EQ(frobber.Calculate(), "Result A");
}

TEST(FrobberTest, CalculatesB) {
  Frobber frobber;
  frobber.Init("Example B");
  EXPECT_EQ(frobber.Calculate(), "Result B");
}
```

上面的测试代码就比较清晰明了，只看单个测试用例，就知道测试内容。但测试数据直接和测试用例耦合在一起的，对于简单的测试用例问题不大，对于复杂的测试数据的情形，需要调整。

```c++
BobberProto RecentCheapConcert() {
  return PARSE_TEXT_PROTO(R"(
      id: 21
      artist: "The Shouting Matches"
      when: "2016-08-24 20:30:21 -04:00"
      price_usd: 60)");
}
BobberProto PastExpensiveConcert() {
  return PARSE_TEXT_PROTO(R"(
      id: 17
      artist: "Beyonce"
      when: "2012-10-10 12:39:54 -04:00"
      price_usd: 200)");
}
TEST(BobberTest, UsesProtos) {
  Bobber bobber({PastExpensiveConcert(), RecentCheapConcert()});
  SomeCall();
  EXPECT_THAT(bobber.MostRecent(), EqualsProto(RecentCheapConcert()));
}
```

测试代码编写经验：

1. 避免写辅助测试类，直接写测试用例就好
2. 如果需要写辅助测试类，避免测试数据成员化，使用全局测试数据来启动测试用例
3. 当测试数据构造比较复杂的情况下，使用辅助函数来构造测试数据
4. 当测试数据成员化不可避免时，通过测试类方法来返回测试数据，保持测试数据流的清晰化
5. 对于每个公开的方法，先构思测试代码，再写声明，最后写实现

