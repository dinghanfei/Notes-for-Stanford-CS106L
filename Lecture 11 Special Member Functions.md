# Lecture 11 Special Member Functions

`special member functions`之所以`special`是因为如果你不在类中声明这些函数，编译器会为你创建它们。但问题是，有时候编译器为你创建的函数可能不是你实际需要的。所以需要知道如何声明它们，以备不时之需。



### copy operations

由编译器生成的special member functions通常意味着有很多例外，但通常是以下四个：

- Default construction：创建对象时没有参数
- **Copy construction**：在使用现有对象创建新对象时调用，只是创建一个现有对象的副本。
- **Copy assignment**：有两个对象的时候，一个正在赋值给另一个，赋值的那个对象必须首先**清除**那里的一切，并用另一个vector的内容替换它。
- Destruction：越界时将对象destory

eg：

```c++
StringVector function(StringVector vec0){  //vec0:Copy constructor
  StringVector vec1;  //Default constructor
  StringVector vec2{"ding", "han", "fei"};  //Normal constructor
  StringVector vec3();  //declares a function
  StringVector vec4(vec2);  //Copy constructor
  StringVector vec5{};  //Default constructor
  StringVector vec6{vec3 + vec4};  //Copy constructor
  StringVector vec7 = vec4;  //Copy constructor
  vec7 = vec2;  //Copy assignment
  return vec7;  // Copy constructor creates a copy of local variable
}
```



在函数return的时候会发生`copy construction`，创建一个局部变量的拷贝，以便在其他地方也能访问这个被拷贝的变量。



### emplace_back

https://www.apiref.com/cpp-zh/cpp/container/vector/emplace_back.html

emplace_back和push_back有什么区别？

```
当需要添加一个新元素的时候，push_back会先创建一个临时对象，然后再移动/拷贝到vector中。然而与其创建这个临时的对象，不如直接在vector尾部创建这个元素，这样就省去了移动/拷贝的过程。
```



# Lecture 12 Move Semantics

### lvalues and rvalues

<u>this is a simplification of a complicated topic!</u>

`l-value`：有`name(identity)`

+ 可以用`&`来获取它的地址
+ 可以出现在`=`的左右两边

`r-value`：没有`name(identity)`

+ 临时的值

+ 不能用`&`来获取它的地址
+ 只能出现在`=`的右边

eg：`=`的左都是`l-value`

```c++
int val = 2; //2是r-value，val是l-value
int* ptr = 0x1235532;  //ptr是l-value，0x1235532是l-value
vector<int> v1{1, 2, 3};

auto v4 = v1 + v2;//v1，v2是l-value，但v1+v2是r-value，
v1 += v4; // v4：l-value
size_t size = v.size();  //size：l-value，v.size()：r-value
val =  static_cast<int>(size);  //static_cast<int>(size)：r-value
v1[1] = 4 * i;  //4 * i：r-value，v1[1]：l-value，因为v[]这种形式是通过引用(reference)返回的
ptr = &val;  //&val:r-value
v1[2] = *ptr;  //*ptr：l-value
```

### lvalues reference and rvalues reference

 ~~lvalues reference就是对lvalue的reference~~

```c++
int val = 2;
int* ptr = 0x1235532;  
vector<int> v1{1, 2, 3};

auto& ptr2 = ptr;  //相当于创建一个对原值的引用，对ptr2的任何更改都在改变ptr
auto&& v4 = v1 + v2;  //v4是一个rvalue的reference，相当于延长了临时值的生命周期
//任何对v4进行的更改都会改变其对应的临时值
//&&表示这是一个r-value的reference
auto& ptr3 = &val;//error!不能将lvalue reference绑定到rvalue

auto&& val2 = val;//error!
//不能将rvalue reference绑定到lvalue

const auto& ptr3 = ptr + 5;//可以将一个常量左值引用绑定到右值
```



### move operations

`r-value`是临时的值，它很快就会消失，因此可以窃取(`steal`)它的资源。

+ `l-value`是不可丢弃(disposable)的

+ `r-value`是可丢弃的

### 2 new special member functions!

`Move constructor`：从现有的r-value创建一个新对象。直接将内容移动到新的对象那里。

`Move assignment`：使用一个现有的r-value覆盖另一个已经存在的对象。因为r-value是临时的，可以被steal，所以在清除对象的原有内容之后直接将内容移动过去就可以了。



在实际情况中，如果`=`左边是一个l-value，那么我们所做的操作是copy而不是move，eg：

```c++
class axess{
  public:
  	axess()
    //other special member functions
    axess(axess&& other) : student(other.students){}
  	axess& operator=(axess&& rhs){
      students = rhs.students;
    }
  private:
  	vector<student> students;
}
```

`students = rhs.students`这里的` rhs.students`实际上是一个l-value，因此我们在这里的操作是copy，如果这是一个右值，那么才是move操作。

使用`std::move`可以将它变成move操作：`students = std::move(rhs.students)`

`std::move`无条件将变量强制转换为r-value。这也意味着你对某个东西调用了`move`之后，不应该再使用它。

eg：使用move的方法写swap()函数

```c++
template <typename T>
void swap(T& a,T& b)
{
  T temp = std::move(a);
  a = std::move(b);
  b = std::move(temp);
}
```







