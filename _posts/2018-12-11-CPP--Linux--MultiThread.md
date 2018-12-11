---
layout:     post

title:      CPP--Linux--MultiThread

date:       2018-12-11

author:     Augustine Tong

header-img: img/steve.jpg

catalog: true

tags:
    - Go
---

# CPP--Linux--MultiThread
**代码均为自己手打过一次, 输出结果均为自己的输出结果**.  

流入与流出不配对就可能发生死锁  
为了不发生死锁：  
1. 明确保证流入和流出成对出现.  
2. 当明确无法保证流入和流出成对出现时，在流入完成后，关闭channel.  
3. 利用缓冲.  

## 一个线程安全的Counter示例
<pre>
#include <cstdint>

// A thread-safe counter
class Counter : boost::noncopyable {
    // copy-ctor and assignment should be private by default for a class.
public:
    Counter() : value_(0) {}
    int64_t value() const;
    int64_t getAndIncrease();

private:
    int64_t value_;
    mutable MutexLock mutex_;
};

int64_t Counter::value() const {
    MutexLockGuard lock(mutex_);
    return value_;
}

int64_t Counter::getAndIncrease() {
    MutexLockGuard lock(mutex_);
    int64_t ret = value_++;
    return ret;
}
// In a real world, atomic operations are preferred.
// 实际项目中，这个class用原子操作更合理，这里用锁仅仅是为了举例

</pre>

## 对象的创建
对象构造要做到线程安全，唯一的要求是在构造期间不要泄露this指针，即    
- 不要在构造函数中注册任何回调  
- 也不要在构造函数中把this传递给跨线程的对象  
- 即便在构造函数的最后一行也不行  

之所以这么规定，是因为在构造函数执行期间对象还没有完成初始化，如果this被泄露(escape)给了其他对象，(其自身创建的子对象除外)，则别的线程有可能访问这个半成品对象，这会造成难以预料的后果。  

<pre>
// Don't do this
class Foo : public Observer
{
public:
    Foo(Observable* s)
    {
        s->register_(this); // wrong, un safe thread
    }

    virtual void update();
};

// Do this
class Foo : public Observer
{
public:
    Foo();
    virtual void update();

    // 另外定义一个函数，在构造之前执行回调函数的注册工作
    void observe(Observable* s)
    {
        s->register_(this);
    }
};

Foo* pFoo = new Foo;
Observable* s = getSubject();
pFoo->observe(s);  // 二段式构造，或者直接写 s->register_(pFoo);
</pre>

这里也说明，二段式构造————即构造函数 + initialize()————有时候会是好办法，这虽然不符合C++教条，但是多线程下别无选择。另外，既然允许二段式构造，那么构造函数不必主动抛出异常，调用方法靠initialize()的返回值来判断对象是否构造成功，这能简化错误处理。   

即是是构造函数的最后一行也不要泄露this，因为Foo有可能是个基类，基类先于派生类构造，执行完Foo:Foo()的最后一行代码还会继续执行派生类的构造函数，这时most-derived class的对象还处于构造中，仍然不安全。  
相对而言，对象的构建做到线程安全还是比较容易的，毕竟曝光少，回头率为0，而析构的线程安全却不那么简单。

## 销毁太难
对象析构，这在单线程里不构成问题，最多需要注意避免空悬指针和野指针。  
而在多线程程序中，存在了太多的竞态条件。  
对一般的成员函数而言，做到线程安全的办法是让他们顺次执行，而不要并发执行(关键是不要同时读写共享状态)，也就是让每个成员函数的临界区不重叠。  
**成员函数用来保护临界区的互斥器本身必须是有效的**   
而析构函数破坏了这一假设，它会把mutex成员变量销毁掉。  

### mutex不是办法
mutex只能保证函数一个接一个的执行













