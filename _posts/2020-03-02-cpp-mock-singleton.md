

```cpp
template <typename Singleton>
class User
{
public:
    virtual ~User() {}
    void act()
    {
        Singleton::getInstance().callFunction();
    }
};
```

```cpp
class MockSingleton
{
public:
    static MockSingleton& getInstance()
    {
        static MockSingleton instance;
        return instance;
    }
    MOCK_METHOD(void, callFunction, ());
};

TEST(UserTest, SingletonCallTest)
{
    User<MockSingleton> user;
    EXPECT_CALL(MockSingleton::getInstance(), callFunction).Times(1);

    user.act();
}
```
