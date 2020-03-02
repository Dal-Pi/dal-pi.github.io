---
title: "[C++] gmock으로 Singleton mocking하기"
date: 2020-03-03 00:00:00 -0400
categories:
  - C++
tags:
  - C++
  - Singleton
  - googletest
---
Singleton은 인터페이스를 통해 추상화하지 않고 해당 클래스를 그대로 참조하므로 mock을 만드는데 어려움이 있다.  
[#MockingNonVirtualMethods](https://github.com/google/googletest/blob/master/googlemock/docs/cook_book.md#mocking-non-virtual-methods-mockingnonvirtualmethods) 를 활용하면 template를 통해 mocking이 가능하도록 만들 수 있다.

## 1. Class 이름이 typename이 되도록 수정하기

일단 Singleton과 이를 사용하는 User에 대한 코드이다.
```cpp
class Singleton
{
public:
    static Singleton& getInstance()
    {
        static Singleton instance;
        return instance;
    }
    void callFunction() { /*do somting*/ } //테스트하고자 하는 함수, 심지어 가상함수도 아니다.
};
```
```cpp
class User
{
public:
    void act()
    {
        Singleton::getInstance().callFunction(); //당연하지만 "Singleton"은 class이름으로 사용되고 있다
    }
};
```
User 클래스는 Singleton을 직접 참조하고 있으므로 Singleton 클래스를 mock으로 대체하여 테스트할 수 없는 상태이다.

이 때 Singleton의 클래스이름이 typename이 되도록 테스트하는 코드에 template을 적용한다.  
(Singleton 을 흔히 사용하는 T 가 된다고 생각하면 편하다)  
이렇게 하면 User::act() 함수 안에서는 typename Singleton을 만족하는 모든 타입들에 대해 동일하게 callFunction()을 호출할 수 있게 된다.
```cpp
template <typename Singleton>
class User
{
public:
    void act()
    {
        Singleton::getInstance().callFunction(); //이제부터 "Singleton"은 typename이다!
    }
};
```

## 2. typename에 맞게 mocking하기

[간단한 Singleton 작성하기](https://dal-pi.github.io/c++/001-cpp-singleton/)를 사용해서 getInstance() 를 만들어주고 테스트할 함수인 callFunction()을 mocking 해 준다.
```cpp
class MockSingleton //Singleton을 상속받는 것이 아니다!
{
public:
    static MockSingleton& getInstance() //getInstance의 함수 이름이 있어야 하며 원래의 class Singleton처럼 동작하도록 작성한다.
    {
        static MockSingleton instance;
        return instance;
    }
    MOCK_METHOD(void, callFunction, ()); //Singleton을 상속받는 것이 아니므로 override가 아니다
};

TEST(UserTest, SingletonCallTest)
{
    User<MockSingleton> user;
    EXPECT_CALL(MockSingleton::getInstance(), callFunction).Times(1);

    user.act(); //callFunction()이 한 번 호출되어 pass된다.
}
```

테스트하고자 하는 원본 코드에도 수정이 가해야져 하는 단점이 있지만 Singleton을 mocking할 수 있다는 점에서 쓸만해 보인다.
