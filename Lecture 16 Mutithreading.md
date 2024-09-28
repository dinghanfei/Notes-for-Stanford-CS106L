# Lecture 16 Mutithreading

https://www.runoob.com/cplusplus/cpp-multithreading.html

线程是程序中的轻量级执行单元，允许程序同时执行多个任务。但实际上同一时刻只有一个在执行，但从一段时间上来看，确实有多个任务被执行了。（上课时举的拿粉笔写数字的例子）

C++11 及以后的标准提供了多线程支持，核心组件包括：

- **`std::thread`**：用于创建和管理线程。
- **`std::mutex`**：用于线程之间的互斥，防止多个线程同时访问共享资源。
- **`std::lock_guard`** 和 **`std::unique_lock`**：用于管理锁的获取和释放。
- **`std::condition_variable`**：用于线程间的条件变量，协调线程间的等待和通知。
- **`std::future`** 和 **`std::promise`**：用于实现线程间的值传递和任务同步。

eg：

```c++
#include <iostream>
#include <thread>
void printMessage(int count) {
    for (int i = 0; i < count; ++i) {
        std::cout << "Hello from thread (function pointer)!\n";
    }
}
int main() {
    std::thread t1(printMessage, 5); // 创建线程，传递函数指针和参数
    t1.join(); // 等待线程完成
    return 0;
}
```

`join() `用于等待线程完成执行。如果不调用` join()` 或 `detach()` 而直接销毁线程对象，会导致程序崩溃。上述例子中，只有`t1`线程rejoin主线程之后，主线程才会继续执行。