```cpp
class Base
{
public:
    virtual ~Base() {}
    //virtual void foo(int num = 1) = 0;
    void print(int num = 3)
    {
        doPrint(num);
    }
    virtual void doPrint(int num) = 0;
};

class Derived : public Base
{
public:
    virtual ~Derived() {}
//    virtual void print(int num = 3) override
//    {
//        cout << num << endl;
//    }
    virtual void doPrint(int num) override
    {
        cout << num << endl;
    }
};

int main(int argc, char *argv[]) 
{
    Derived derived;
    derived.foo();

    Base* pBase = &derived;
    pBase->foo();
    return 0
}
```
