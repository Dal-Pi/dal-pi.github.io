---
title: "[C++] 레퍼런스로 다형성 사용하기"
date: 2021-04-15 01:00:00 -0400
categories:
  - C++
tags:
  - C++
---
Polymorphism by Reference

객체지향 언어인 C++에서 다형성(Polymorphism)을 사용하려면 보통 raw pointer, 혹은 smart pointer를 사용한다고 알려져 있다.  
하지만 reference로도 동일하게 다형성을 구현할 수 있다.

```cpp
class Base {
public:
    virtual void foo() {
        cout << "Base::foo() called" << endl;
    }
};

class Derived : public Base {
public:
    virtual void foo() override { // override는 쓰면 손해볼일이 없는 좋은 키워드다
        cout << "Derived::foo() called" << endl;
    }
};

int main() {
    Derived derived;
    Base* p_base = &derived;
    p_base->foo();

    shared_ptr<Base> sp_base = make_shared<Derived>();
    sp_base->foo();

    Base& ref_base = derived;
    ref_base.foo();

    return 0;
}
```
결과는 모두 `Base`가 아닌 `Derived`의 `foo()`가 불리게 된다
```
Derived::foo() called
Derived::foo() called
Derived::foo() called
```

레퍼런스를 통한 다형성의 구현은 스마트포인터를 사용한 구현의 문제점을 해결하는데 사용할 수 있다.  
아래의 `MyClass`에서 `shared_ptr`로 `Base`의 다형성을 사용하려는데 `Derived`가 `singleton`인 경우를 생각하면 아래와 같은 문제가 발생할 수 있다.
```cpp
//(Base)

class Derived : public Base {
public:
    static Derived& getInstance() {
        //mayer's singleton
        static Derived instance;
        return instance;
    }

    virtual void foo() override {
        cout << "Derived::foo() called" << endl;
    }
private:
    Derived() {}
};

class MyClass {
public:
    MyClass(shared_ptr<Base> base) : mBase(base) {}
private:
    shared_ptr<Base> mBase;
};

int main() {
    MyClass myClass1 = MyClass(shared_ptr<Base>(&Derived::getInstance())); // Do not! 종료시 Derived singleton객체를 파괴하려 한다
    MyClass myClass2 = MyClass(shared_ptr<Base>(&Derived::getInstance())); // Do not! Derived singleton객체의 control block이 중복 생성된다

    return 0;
}
```

raw pointer를 사용해도 해결이 되지만 raw pointer의 사용을 지양하고자 하면(애초에 smart pointer는 raw pointer를 사용하지 않기 위해 사용하기에)  
레퍼런스를 사용하면 좋은 해결 방안이 된다.
```cpp
//(Base)
//(Derived)

class MyClass {
public:
    MyClass(Base& base) : mBase(base) {}
private:
    Base& mBase;
};

int main() {
    MyClass myClass1 = MyClass(Derived::getInstance());
    MyClass myClass2 = MyClass(Derived::getInstance());

    return 0;
}
```
