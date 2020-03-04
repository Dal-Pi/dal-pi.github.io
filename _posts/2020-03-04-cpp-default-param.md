---
title: "[C++] 기본 매개변수(Default Parameter)가 있는 함수의 상속"
date: 2020-03-02 16:00:00 -0400
categories:
  - C++
tags:
  - C++
  - Effective C++
---
기본 매개변수(Default Parameter)가 있는 함수는 override하면 의도하지 않은 동작을 할 수 있다.

> 어떤 함수에 대해서도 상속받은 기본 매개변수 값은 절대로 재정의하지 말자

Effective C++의 한 항목 제목이다. 왜 절대 하지 말라는지 아래 코드와 를 보면 알 수 있다.
```cpp
class Base
{
public:
    virtual void print(int num = 1) = 0;
};

class Derived : public Base
{
public:
    virtual void print(int num = 3) override //기본 매개변수를 변경하였다
    {
        cout << num << endl;
    }
};

int main()
{
    Derived derived;
    derived.print();

    Base* pBase = &derived;
    pBase->print();
}
```
`Derived::print()`는 `Base::print()`를 override하면서 기본 매개변수를 변경하였다.  
`pBase`가 `Base`의 포인터지만 가상함수 테이블에 따라 `Derived::print()`가 불리며 3이 출력될 것 같지만 결과는 아래와 같다.
```
3
1 //왜 이게 나오지?
```
원인은 기본 매개변수는 **정적으로 바인딩**되며 가상 함수는 런타임에 **동적으로 바인딩**되기 때문이다.  
쉽게 말하면 파생(Derived) 클래스의 가상 함수를 호출해도 기본(Base) 클래스의 기본 매개변수를 사용해 버린다는 이야기다.  
그렇다면 기본 매개변수를 항상 동일하게 맞춰 주면 괜찮은가? 하고 생각해보면 문제는 없어질 테지만 Base 클래스의 기본 매개변수가 변경되면 반드시 Derived 클래스도 바꿔줘야 하는 의존성이 생겨 좋지 않다.

그렇다면 기본 매개변수를 재정의하지 못한다면 어떻게 `Derived::print()` 를 호출할 수 있을까? 만약 기본 매개변수를 지우면 매개변수가 필요한 `Derived::print(int)` 로만 함수를 호출할 수 있게 된다.  
이 문제를 해결하는 방법은 **"비 가상 인터페이스"** 라는 방법이다. 비슷한 용어로 Wrapper 정도를 들 수 있겠다.  
요지는 기본 매개변수를 override할 수 없게 하는 대신 동작에 대한 부분만 override하도록 제공하는 것이다.
```cpp
class Base
{
public:
    void print(int num = 1) //비 가상 인터페이스
    {
        doPrint(num);
    }
private:
    virtual void doPrint(int num) = 0; //동작을 재정의하려면 doPrint를 override하면 된다
};

class Derived : public Base
{
private:
    virtual void doPrint(int num) override
    {
        cout << num << endl;
    }
};

//(...)
```
이제 파생 클래스는 `Base::print()`를 override할 수 없지만 `Derived::print()`가 호출 가능하게 되며 위에서 설명한 기본 매개변수로 인한 문제까지 예방할 수 있다.

실무에서도 자주 접하는 내용인데 잘못할 여지가 많아 보이는 내용이다.  
기본 클래스를 설계하는 개발자는 이런 부분까지 염두해 둘 수 있어야 한다는 것을 새삼 느꼈다.
