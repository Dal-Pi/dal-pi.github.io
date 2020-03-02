---
title: "[C++] simplest singleton"
categories:
  - C++
tags:
  - C++
  - Singleton
  - DesignPattern
---

## C++11에서 간단하게 Singleton 구현하기
아래와 같이 작성하면 간단하게 Singleton이 구현된다.
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
이게 가능한 이유는 C+11의 아래 규칙 때문이다.
> 정적 지역변수의 초기화가 멀티스레드 환경에서도 한 번만 수행됨이 보장된다.

원문은 이렇다.
> If control enters the declaration concurrently while the variable is being initialized, the concurrent execution shall wait for completion of the initialization.

물론 클래스의 생성자 내에서 또 다른 복잡한 동기화 문제가 있거나 언어 표준을 확실하게 지원하지 않는 환경에서는 지원되지 않을 수 있다.

위의 코드는 동기화 문제에 대한 부분만이므로 "컴파일러가 자동으로 만들어 주는 함수들(Effective C++ 참고)"이나 상속에 대한 부분 까지 신경쓰면 아래와 같이 구성할 수 있다.
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

하지만 역시 Singleton은 쓰지 않는 것이 제일 좋다.
