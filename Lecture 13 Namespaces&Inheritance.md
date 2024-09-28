# Lecture 13 Namespaces&Inheritance

### Namespaces

它是一种方法，这种方法可以让你将代码行或你声明的类分组到一个单一的`namesapce`中，以便以后引用。

标准库中有非常多的函数，在我们编写程序的过程中很可能会出现和标准库中函数重名的情况。`namesapce`将全局作用域划分成不同的部分，从而不同`namesapce`中的标识符可以重名而不发生冲突。

假设在编写程序的过程中定义了一个`int`型的变量`count`，而此时我们又想使用`algorithm`中的`count`函数，因此就需要有一种方式来告诉编译器我们使用的是哪个`count`。<u>引入`namesapce`可以更好的控制标识符的作用域。</u>本质上，命名空间就是定义了一个范围。



### Scope Resolution

作用域解析的运算符是两个冒号写在一起`::`。用于访问命名空间中的成员，指明要使用哪个命名空间下的标识符。



总结：

+ **命名空间**：用于组织代码，避免名称冲突。

+ **作用域解析运算符 `::`**：用于访问命名空间中的成员。

eg：

```c++
#include <iostream>

// 定义命名空间 MyMath
namespace MyMath {
    int add(int a, int b) {
        return a + b;
    }

    int subtract(int a, int b) {
        return a - b;
    }
}

// 定义另一个命名空间 MyString
namespace MyString {
    void printHello() {
        std::cout << "Hello, World!" << std::endl;
    }
}

int main() {
    // 使用作用域解析运算符访问 MyMath 命名空间中的函数
    int sum = MyMath::add(5, 3);
    int difference = MyMath::subtract(5, 3);

    std::cout << "Sum: " << sum << std::endl;           // 输出: Sum: 8
    std::cout << "Difference: " << difference << std::endl; // 输出: Difference: 2

    // 使用作用域解析运算符访问 MyString 命名空间中的函数
    MyString::printHello(); // 输出: Hello, World!

    return 0;
}

```



### Inheritance

#### interfaces

C++中没有interface(接口)的关键字

+ 如果想成为一个`interface`，需要确保`class`中只包含`pure virtual functions`
+ 如果想要实现一个`interface`，那么那个`class`必须定义所有的`pure virtual functions`，不然它将无法编译。



如果我们想要在`class`中定义一些函数呢？

```
如果一个class中包含至少一个`pure virtual functions`，那么这个class被称作是一个abstract class(抽象类)。

abstract class不能被实例化，只有当一个class中不含`pure virtual functions`的时候才能被实例化。
```



#### public & protected & private

**public**：公有属性，凡是在它下面声明的变量和函数，都可以在类的**内部**和**外部**访问。

**protected**: 保护属性，凡是在它下面声明的变量和函数，只能在类的**内部**以及**派生类**（子类）中访问。

**private**: 私有属性，凡是在它下面声明的变量和函数，只能在类的**内部**访问。可以使用公有成员函数，用于获取私有变量的值。

有三种继承方式：

+ **public继承：**基类`public`成员，`protected`成员，`private`成员的访问属性在派生类中分别变成：`public,` `protected`, `private`

+ **protected继承：**基类`public`成员，`protected`成员，`private`成员的访问属性在派生类中分别变成：`protected`, `protected`, `private`
+ **private继承：**基类`public`成员，`protected`成员，`private`成员的访问属性在派生类中分别变成：`private`, `private`, `private`



结构体（`struct`）的继承在 C++ 中与类（`class`）的继承基本相同。不同之处在于默认的访问控制修饰符：

- 在 `struct` 中，默认的继承方式是 **public**。
- 在 `class` 中，默认的继承方式是 **private**。



#### Inherited Member

`Inherited Member`指的是基类中定义的属性（成员变量）和方法（成员函数），这些成员在派生类中可以直接访问和使用。通过继承，派生类能够获得基类的功能，进而实现代码重用和扩展。



当派生类中定义了一个与基类**同名**的成员（无论是成员变量还是成员函数），派生类中的成员会**覆盖**基类中的同名成员，使得基类的同名成员无法通过派生类的对象直接访问。

eg：

**名称隐藏**：派生类中的 `display(int)` 方法隐藏了基类中的 `display()` 方法，因此在派生类对象中无法直接访问基类的 `display()` 方法。

**访问隐藏的方法**：可以通过作用域解析运算符（`Base::`）访问基类的同名成员。

```c++
#include <iostream>
using namespace std;

class Base {
public:
    void display() {
        cout << "Display from Base" << endl;
    }
};

class Derived : public Base {
public:
    void display(int) { // 重新定义了 display 方法
        cout << "Display from Derived" << endl;
    }
};

int main() {
    Derived obj;
    obj.display(10); // 调用 Derived 的 display 方法

    // obj.display(); // 错误：无法调用 Base 的 display 方法，因为它被隐藏了
    obj.Base::display(); // 可以通过作用域解析运算符调用基类的 display 方法

    return 0;
}

```



#### Abstract Classes

如果一个`class`中包含至少一个`pure virtual functions`，那么这个`class`被称作是一个`abstract class`(抽象类)。

`Interfaces`是`abstract class`的子集。

eg：

```c++
class Base{
  public:
  	virtual void foo() = 0; // pure virtual function
  	virtual void foo2(); //non-pure virtual function
  	void bar() = {return 42;}; // regular function
}
```

概括来说，如果你想要你的函数可以被`overwrite`，你需要声明它为`virtual`(虚函数)，如果你想强制它被`overwrite`，可以通过第三行代码那样添加0来声明它是`pure virtual functions`。



#### Base class & Derived class

**基类**是一个被其他类继承的类。它通常包含一些通用的属性和方法，这些属性和方法可以被派生类继承和重用。

- 特点

	- 可以包含数据成员和成员函数。
	- 可以是抽象类（包含纯虚函数）或具体类。

	

**派生类**是从基类继承而来的类。它可以扩展基类的功能，添加新的属性和方法，或重写基类的方法。

- 特点

	- 继承基类的成员。
	- 可以重写基类中的虚函数，以提供特定实现。
	- 可以添加新的成员变量和方法。

	

#### Destructors

如果你想使你的类是可继承的，需要将析构函数设为虚拟的，否则可能会出现内存泄露的情况。
