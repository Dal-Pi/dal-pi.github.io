---
title: "[C++] simplest singleton"
categories:
  - C++
tags:
  - C++
  - Singleton
  - DesignPattern
---

C++11에서 간단하게 Singleton 구현하기

Singleton은 인스턴스가 항상 한 개만 존재하도록 하는 유명한 패턴이다.
구현에서 가장 유의할 점은 Singleton 인스턴스가 생성되는 부분의 동기화 문제인데 이에 대해서는 정말 기술들이 존재한다.
하지만 동기화 문제의 위험이 적을 때는 아래와 같이 구성하면 간단하게 Singleton의 원자(atomic)성을 얻을 수 있다.
```cpp
class Singleton
{
public:
    static Singleton& getInstance()
    {
        static Singleton instance;
        return instance;
    }
};
```
가능한 이유는 C+11의 아래 규칙 때문이다
> If control enters the declaration concurrently while the variable is being initialized, the concurrent execution shall wait for completion of the initialization.
정적 지역변수의 초기화가 멀티스레드에서 한 번만 일어난다는 내용으로 언어차원에서 이를 보장한다

물론 클래스의 생성자 내에서 또 다른 복잡한 동기화 문제가 있거나 언어 표준을 확실하게 지원하지 않는 환경에서는 지원되지 않을 수 있다.

위의 코드는 동기화 문제에 대한 부분만이므로 "컴파일러가 자동으로 만들어 주는 함수들" 까지 신경써서 아래와 같이 구성할 수 있다. 
간단한 Singleton을 구성할 때 유용하다.
```cpp
class Singleton
{
public:
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;
    Singleton(Singleton&&) = delete;
    Singleton& operator=(Singleton&&) = delete;
    static Singleton& getInstance()
    {
        static Singleton instance;
        return instance;
    }
    void callFunction() {}

protected:
    Singleton() {}
    virtual ~Singleton() {}
};
```
