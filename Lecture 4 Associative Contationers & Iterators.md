# Lecture 4 Associative Contationers & Iterators

<u>据说是最重要的一节课</u>

### 回顾：

使用Vec[i]，访问一个越界的位置会发生什么？

```
会导致undefined behavior,也就是造成了非常大的问题，并且这个问题是不可预测的。比如在Mac上很可能会继续运行，仿佛无事发生，然而在其他系统上可能会直接停止运行。
```



双端队列deque

```
基本上和vector功能一致，但是可以很快的在队头插入元素，不过访问中间元素更慢。
一般来说使用vector，但如果要在队头/尾进行很多操作，那么使用deque
```



###  Container Adaptors

https://oi-wiki.org/lang/csl/container-adapter/

eg: stack queue



stack和queue可以用sequence container实现吗？

```
c++ 的栈和队列在底层实际就是向量和双端队列，只不过是功能受限
```



既然stack和queue的本质是由vector和deque实现的，那么两者的效率相比是如何的？

```
stack和queue使用的是vector和deque中常数时间量级的函数，所以实际上它们比vector和deque更快，因为它们只使用了vector和deque中最快的函数
```



### Associative Containers

https://oi-wiki.org/lang/csl/associative-container/

无法按照索引访问，数据按照键值对的形式存在。但实际上在底层set和map会按照key的某种特性排序(可以重载`<`来定义排序顺序)，而unordered_map/set不会。

eg: map/set/unordered_map/unordered_set



### Iterators

https://oi-wiki.org/lang/csl/iterator/

<u>Iterators允许在任何一个容器上迭代，无论它是有序的还是无序的</u>



如何迭代一个map/set？（毕竟它们不能通过index进行迭代）

```
借助二叉树进行遍历
（然而我们现在不需要关心这件事
```



Iterator 的类型是？

类型就是`Iterator`,并且iterator取决于正在使用的集合的类型



 
