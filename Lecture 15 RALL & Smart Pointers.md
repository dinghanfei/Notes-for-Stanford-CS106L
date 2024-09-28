# Lecture 15 RALL & Smart Pointers

### RALL

<u>Resource Acquisition Is Initialization</u>

```
"The best example of why I shouldn't be in marketing"
"I didn't have a good day when I named that"
```



在获取后需要被释放的资源：

| Resources   | Acquire  | Release |
| ----------- | -------- | ------- |
| Heap memory | new      | delete  |
| Files       | open     | close   |
| Locks       | try_lock | unlock  |
| Sockets     | socket   | close   |



RALL有一个更好的名字是SBRM(**S**cope **B**ased Memo**r**y **M**anagement)
作用域基础内存管理，使用作用域的思想来自动释放内存。

也可以叫做CADRE(**C**onstructor **A**cquires, **D**estructor **Re**leases)
构造函数获取，析构函数释放。可以把释放资源的代码放在析构函数中，这样无论发生什么，只要你退出了函数，资源就会被释放。 此外，如果你获得了一个资源，你应该总是在构造函数中获取！



### Smart Pointer

eg：

```c++
std::unique_ptr
std::shared_ptr
std::weak_ptr
```

#### std::unique_ptr

单一的拥有某种资源，并在对象被销毁的时候，它将在析构函数中删除该资源。它是不可被复制的！如果我们试图去复制它，那么它就无法单一的拥有某种资源。

我们如何说明一个class不允许复制？

```
delete copy constructor and copy assignment
```

#### std::shared_ptr

资源可以被任意数量的指针指向，当不再有指针指向这个资源的时候，这个资源将被释放。

声明共享指针的方式：

```
在创建第一个指针之后，使用copy constructor声明所有后续的指针。
```

#### std::weak_ptr

作用类似于共享指针，但不会增加引用计数。