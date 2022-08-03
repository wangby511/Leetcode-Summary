# Concurrency - Multithread

CREATED 2022/08/01

多线程题目 - C++ - mutex solution version

**[1114. Print in Order](https://leetcode.com/problems/print-in-order/)**

Use mutex lock

```
class Foo {
    mutex m1;
    mutex m2;
public:
    Foo() {
        m1.lock();
        m2.lock();
    }

    void first(function<void()> printFirst) {
        
        // printFirst() outputs "first". Do not change or remove this line.
        printFirst();
        m1.unlock();
    }

    void second(function<void()> printSecond) {
        m1.lock();
        // printSecond() outputs "second". Do not change or remove this line.
        printSecond();
        m2.unlock();
    }

    void third(function<void()> printThird) {
        m2.lock();
        // printThird() outputs "third". Do not change or remove this line.
        printThird();
    }
};
```

**[1115. Print FooBar Alternately](https://leetcode.com/problems/print-foobar-alternately/)**

Two threads: thread A will call foo(), while thread B will call bar(). They print "foobarfoobar...foobar".

```
class FooBar {
private:
    int n;
    mutex m1;
    mutex m2;

public:
    FooBar(int n) {
        this->n = n;
        m2.lock();
    }

    void foo(function<void()> printFoo) {
        for (int i = 0; i < n; i++) {
            m1.lock();
        	// printFoo() outputs "foo". Do not change or remove this line.
        	printFoo();
            m2.unlock();
        }
    }

    void bar(function<void()> printBar) {
        
        for (int i = 0; i < n; i++) {
            m2.lock();
        	// printBar() outputs "bar". Do not change or remove this line.
        	printBar();
            m1.unlock();
        }
    }
};
```

**[1116. Print Zero Even Odd](https://leetcode.com/problems/print-zero-even-odd/)**

Print "01020304050607..." with a zero between each two numbers until n.

```
class ZeroEvenOdd {
private:
    int n;
    int num;
    mutex zeroLock;
    mutex oddLock;
    mutex evenLock;
    bool nextIsOdd;
public:
    ZeroEvenOdd(int n) {
        this->n = n;
        num = 0;
        oddLock.lock();
        evenLock.lock();
        nextIsOdd = true;
    }

    // printNumber(x) outputs "x", where x is an integer.
    void zero(function<void(int)> printNumber) {
        for(int i = 1;i <= n;i++){
            zeroLock.lock();
            printNumber(0);
            if(nextIsOdd)oddLock.unlock();
            else evenLock.unlock();
            nextIsOdd = !nextIsOdd;
        }
    }

    void even(function<void(int)> printNumber) {
        for(int i = 2;i <= n;i+=2){
            evenLock.lock();
            printNumber(i);
            zeroLock.unlock();
        }
    }

    void odd(function<void(int)> printNumber) {
        for(int i = 1;i <= n;i+=2){
            oddLock.lock();
            printNumber(i);
            zeroLock.unlock();
        }
    }
};
```
