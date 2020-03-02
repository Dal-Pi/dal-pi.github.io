
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
private:
};
```
