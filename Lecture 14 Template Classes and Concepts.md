# Lecture 14 Template Classes and Concepts

### Templates vs. Derived Classes

模板是隐式接口，派生类是显式接口。

+ 模板的多态性是通过类型参数实现的，也称为静态多态性。编译器在编译时根据传入的类型生成相应的代码。
+ 派生类的多态性是通过虚函数实现的，称为动态多态性。运行时根据对象的实际类型调用相应的函数。

区别总结：

多态的实现方式：

- **模板**：静态多态性，编译时决定类型。
- **派生类**：动态多态性，运行时决定调用哪个函数。

应用场景：

+ **模板**：
	+ 用于编写通用代码，如数据结构（如 `std::vector`）。
	+ 运行时速度/效率很重要
	+ 没有共同的基类

+ **派生类**：
	+ 用于需要共享接口和实现的场景，如基于类层次结构的设计。 
	+ 编译时速度/效率很重要
	+ 不想出现`code bloat`，含有template的代码在编译的时候可能会有多个模板版本，int版，double版...

类型安全性：

- 模板在编译时检查类型，确保类型安全。
- 派生类使用虚函数时，运行时根据对象的实际类型进行调用。



### Casting

<u>补充知识点</u>

```c++
int a = (int)b;
int a = int(b);
int a = static_cast<int>(b);//best practice
```



### Template Classes

一个`class template`描述了如何构建一堆看上去相似的类。通过使用模板，可以实现代码的重用和类型独立性。

类模板的定义使用关键字 `template`，后面跟着模板参数。在类模板中，可以使用这些参数作为数据成员的类型或成员函数的参数类型。

eg：

```c++
// 定义类模板
template <typename T>
class Box {
private:
    T value; // 数据成员，类型为 T
public:
    Box(T val) : value(val) {} // 构造函数
    // 成员函数，显示值
    void display() {
        cout << "Value: " << value << endl;
    }
    // 成员函数，返回值
    T getValue() {
        return value;
    }
};

int main() {
    // 创建 Box<int> 对象
    Box<int> intBox(123);
    intBox.display(); // 输出: Value: 123

    // 创建 Box<double> 对象
    Box<double> doubleBox(45.67);
    doubleBox.display(); // 输出: Value: 45.67

    // 使用 getValue
    cout << "Integer Box Value: " << intBox.getValue() << endl; // 输出: 123
    cout << "Double Box Value: " << doubleBox.getValue() << endl; // 输出: 45.67

    return 0;
}
```



### Concept & Constraints

<u>concept: named set of constraints.</u>

概念（Concepts）是用于指定模板参数要求的一种机制。允许我们将template中那些隐式接口转换为显式要求。concepts可以帮助我们定义一个模板所需的特性，从而提高代码的可读性和可维护性。

约束（Constraints）是应用概念的一种方式，限制模板参数的类型。通过约束，编译器可以确保只有满足特定条件的类型才能用于模板实例化。

eg：

```c++
// 定义一个概念，要求类型 T 必须是可迭代的
template <typename T>
concept Iterable = requires(T a) {
    { begin(a) } -> convertible_to<typename T::iterator>;
    { end(a) } -> convertible_to<typename T::iterator>;
};

// 定义一个函数，计算可迭代容器的总和
template <Iterable T>
auto sum(const T& container) {
    typename T::value_type total = 0; // 使用容器的值类型
    for (const auto& item : container) {
        total += item;
    }
    return total;
}

int main() {
    vector<int> intVec = {1, 2, 3, 4, 5};
    list<double> doubleList = {1.1, 2.2, 3.3};

    cout << "Sum of intVec: " << sum(intVec) << endl;        // 输出: Sum of intVec: 15
    cout << "Sum of doubleList: " << sum(doubleList) << endl; 
  	// 输出: Sum of doubleList: 6.6

    return 0;
}
```

特点：

+ **提高可读性**：使用概念可以使模板代码更易于理解。

+ **早期错误检测**：编译器可以在模板实例化时检查约束，提供更早的错误反馈。

	+ eg：第20行

	```c++
	// 定义一个概念，要求类型 T 必须是可比较的
	template <typename T>
	concept Comparable = requires(T a, T b) {
	    { a < b } -> convertible_to<bool>;
	};
	
	// 使用概念约束模板参数
	template <Comparable T>
	void compare(T a, T b) {
	    if (a < b) {
	        cout << a << " is less than " << b << endl;
	    } else {
	        cout << a << " is not less than " << b << endl;
	    }
	}
	
	int main() {
	    compare(5, 10);       // 整数可以比较
	    compare(3.14, 2.71);  // 浮点数可以比较
	    // compare("hello", "world"); // 编译错误，因为字符串不可比较
	    return 0;
	}
	
	```

+ **灵活性**：概念允许组合多个类型要求，增加了模板编程的灵活性。
