# Lecture 8 STL & Lecture 9 Classes and Const Correctness



### Abstraction in the STL

在我们学习c++的过程中最早接触到的就是c++中的`basic types`，然而在编程的过程中我们往往需要用到这些basic types的collection，这些collection就是STL中的`container`；再进一步抽象，`iterator`允许我们对container进行操作，不管这个container的类型是什么；最后`algorithm`允许我们对任何类型的容器使用任何类型的函数，它不仅允许我们运算，还允许我们传递predicate以找到满足条件的元素。



<u>`classes`的内容在CS106B中有更加详细的说明</u>



### Const

const的两种用法：

+ 通过引用传递的常量参数
+ 将const用作函数，const函数不能修改任何类变量或传递给它的任何变量

使用const对保护我们程序的安全性有很大的帮助。

const允许我们推断我们传递给某个函数的变量是否会被修改。当我们传递const参数时， const方法保证不会改变我们传递的值。

eg:

```c++
void f(int& x){
	// the value of x here
  aConstMethod(x);
  anotherConstMethod(x);
  // is the same value of x here
}
```



### const & classes

实际上，我们不仅可以使参数变为const，我们还可以使函数本身成为const，也就是`const member functions`。现在我们意识到，实际上函数可以分为两类，一类是`const member functions`另一类是`non-const member functions`，前者能提供const接口，处理const和non-const变量，而后者仅可以处理non-const变量。



### const pointer

const pointer所指向的内容是可以更改的(eg: **(*p)++**是可以的)，但是这个指针本身是不能更改的(eg:**p++**是不可以的)。

const可以修饰指针，也可以修饰常量，还可以既修饰指针又修饰常量！

- const修饰指针(常量指针) `const int* p`：指针的指向可以修改，但指针指向的值不能修改
- const修饰常量(指针常量) `int * const p`：指针的指向不可以改，但是指针指向的值可以改
- const既修饰指针又修饰常量`const int* const p`：指针的指向和指针指向的值都不能变

### const iterators

可以改变iter指向的内容，但不能改变它本身

```c++
vector v{1, 123}
const vector<int>::iterators itr = v.begin();
++ iter; // doesn't compile
*iter = 15; // compiles

vector<int>::const_iterator itr = v.begin();
*iter = 5;// bad!!! cant change value of iter
++ iter; //ok
int value = *iter;// ok
```



