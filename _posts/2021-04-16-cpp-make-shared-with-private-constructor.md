---
title: "[C++] std::make_shared를 통해 private 생성자를 가진 class를 생성하는 방법"
date: 2021-04-16 02:20:00 +0900
categories:
  - C++
tags:
  - C++
---
make_shared with private constructor  

`std::enable_shared_from_this` 을 사용하거나 `std::shared_ptr`을 사용하여 객체를 생성하고자 할 때 아래의 create() 와 같은 factory method를 사용하곤 한다.
```
class A {
public:
    static shared_ptr<A> create() {
        return shared_ptr<A>(new A);
    }
private:
    A() { }
};

int main() {
    shared_ptr<A> sp_a = A::create();
}
```
이 때 `std::shared_ptr`대신 예외 안정성이 높은 `std::make_shared`를 사용하는 경우 해당 클래스의 생성자가 private라면 컴파일 에러가 발생한다.  
```
static shared_ptr<A> create() {
    return make_shared<A>();
}
```
```
'A::A': private 멤버('A' 클래스에서 선언)에 액세스할 수 없습니다.	
```
에러가 나는 이유는 `std::make_shared`는 함수 template 이며 A의 범위 밖에 있으므로 private영역을 볼 수 없기 때문이다.  
최대한 간단한 형태로(실제로 더 복잡함) `std::make_shared`를 구성해보면 함수에 전달되는 `args`를 `T`에 그대로 perfect-forwarding하고 있음을 알 수 있다.
```
template< class T, class... Args >
shared_ptr<T> make_shared( Args&&... args ) {
    return new T(std::forward<Args>(args)...);
}
```
  
어떻게든 `std::make_shared`를 쓰고 싶은(당신의 코드에서 모든 new 키워드가 보이지 않도록 하고 싶은) 경우에는 간단한 wrapper를 통해 동작하게 할 수 있다.  
```
class A {
public:
    static shared_ptr<A> create() {
    struct MakeSharedEnabler : public A {
        MakeSharedEnabler() : A() { }
    };
    return make_shared<MakeSharedEnabler>();
}
private:
    A() { }
};
```
`create()`는 `A`의 함수이므로 private 함수에 접근이 가능하며 리턴이 `MakeSharedEnabler` 형식으로 생성하지만 상속을 통해 `A`의 동작이 그대로 수행되도록 하는 방법이다.


출처 : https://stackoverflow.com/questions/8147027/how-do-i-call-stdmake-shared-on-a-class-with-only-protected-or-private-const
