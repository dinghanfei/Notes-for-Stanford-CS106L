# Lecture 6&7 Templates

### Templates

或许运用template就如它的名字一样，也就是给一堆有共性的事物提取出来一个模板

比如，现在要比较两者之间的大小，返回时需要小的在前面，大的在后面。比较的对象的类型可以是int double string 等，然而对于这些类型来说，比较的方式是完全相同的，因此可以抽象成下面这样：

```c++
template <typename T>
pair<T, T> my_minmax(T a, T b){
  if(a < b) return {a, b};
  else return {b, a};
}
```

### instantiation

最清晰的指定上文中的 `T` 的方式就是显式实例化(explicit instantiation)

eg: 调用`my_minmax`函数的时候指明`T`代表了什么(整个过程发生在**编译时**)

```c++
my_minmax<double>
my_minmax<string>
my_minmax<vector<int>>
...
```



### Generic Programming and Concept Lifting

泛型函数让我们可以编写出可以在多种不同上下文中使用的函数，它非常灵活。

概念提升意味着我们将从一个函数开始，然后审视我们对于函数的参数假设，质疑它们是否真的有必要？利用这些假设，我们将尝试给函数添加模版。

eg: 统计一个整数在向量中的出现次数

```c++
template<>
int countOccurences(const vector<int>& vec, int val){
  int count = 0;
  for(size_t i = 0;i < vec.size(); ++i){
    if (vec[i] == val) ++count;
  }
  return count;
}
```

我们可以发现，统计出现次数这件事未必需要发生在整数和向量之间，也可以是`[type][val]`在`vector of [type]`中出现的次数，修改后如下：

```c++
template <typename DataType>
int countOccurences(const vector<DataType>& vec, DataType val){
  int count = 0;
  for(size_t i = 0;i < vec.size(); ++i){
    if (vec[i] == val) ++count;
  }
  return count;
}
```

 然而`vector`也不是必须的！我们可以统计`[type][val]`在`[collection] of [type]`中出现的次数，修改后如下：

```C++
template <typename Collection, typename DataType>
int countOccurences(const Collection<DataType>& list, DataType val){
  int count = 0;
  for(size_t i = 0;i < list.size(); ++i){
    if (list[i] == val) ++count;
  }
  return count;
}
```

<u>*如果这里的`Collection`是`set/map`那么代码将不起作用，因为`set/map`无法索引</u>

 但可以用iterator，它提供了一个可以遍历任何容器的统一接口。

```c++
template <typename Collection, typename DataType>
int countOccurences(const Collection<DataType>& list, DataType val){
  int count = 0;
  for(auto iter = list.begin(); iter != list.end(); ++iter){
    if (*iter == val) ++count;
  }
  return count;
}
```

最后，👆的函数假设我们需要遍历整个`Collection`，我们也可以只遍历一个范围，只需要传入一个两个iterator，一个用来开始，另一个用来结束：

```c++
template <typename InputIterator, typename DataType>
int countOccurences(InputIterator begin,InputIterator end,
                    DataType val)
{
  int count = 0;
  for(auto iter = begin; iter != end; ++iter)
  {
    if (*iter == val) ++count;
  }
  return count;
}
```

<u>*这里写的是`InputIterator`，如果是`random access iterators`其实会限制可以使用函数的`Collection`。这里的`iterator`只需要有读的功能就可以了，不需要其他的功能了。</u>

### Implicit Interfaces and Concepts

在做concept lifting的时候需要十分小心，因为这就是隐式接口(implicit interfaces)出现的地方。我们没有显示的说明一些要求是什么，但是代码本身会强制这些要求。

```
The compiler literally replaces each template parameter with whatever you instantiate it with.
```

当你声明模板时，你可以直接尝试将任何类型插入模版中，编译器会试图推断类型是什么，然后将其直接放入代码中，有时这样做是有效的，有时是无效的。

模板函数定义了每个模板参数必须满足的隐式接口，eg：

```c++
template <typename InputIterator, typename DataType>
int countOccurences(InputIterator begin,InputIterator end,
                    DataType val)
{
  int count = 0;
  for(auto iter = begin; iter != end; ++iter)
  {
    if (*iter == val) ++count;
  }
  return count;
}
```

+ begin必须是可以复制的(copyable)，eg：`stream`是不可复制的
+ iter必须可以和end比较
+ iter必须是可以递增的

eg:

```c++
template <typename Collection, typename DataType>
int countOccurences(const Collection& list, DataType val){
  int count = 0;
  for(size_t i = 0;i < list.size(); ++i){
    if (list[i] == val) ++count;
  }
  return count;
}
```

+ list必须有size()方法，并可以通过size()返回一个整数
+ list必须可以索引，并且支持`[]`
+ list中的元素需要能和`DataType`所表示的类型可以比较

### Functions and Lambdas

如果想要对之前统计某元素出现数量的函数做进一步的提升，可以采用统计**有多少元素满足某一条件**的方法，其中的**某一条件**可以是判断两元素是否相等，也可以是元素是否小于/大于某一个值等。

我们可以通过一个函数来实现判断元素是否满足条件这件事。我们将这个`function`称为`predicate`

```c++
template <typename InputIterator,typename UniaryPredicate>
int countOccurences(InputIterator begin, InputIterator end,
                    UniaryPredicate predicate)
{
  int count = 0;
  for(auto iter = begin; iter != end; ++iter)
  {
    if (predicate(*iter)) ++count;
  }
  return count;
}
```

eg :

```c++
bool isLessThan5(int val){
  return val < 5;
}
int main()
{
  vector<int> vec{1, 3, 5, 7, 9};
  countOccurences(vec.begin(), vec.end(),isLessThan5);
  //returns 2
  return 0
}
```

<u>*`predicates`必须返回一个布尔值，`predicates`用于调用某些东西并看它是否返回`true/false`</u>

我们称这种方法为`function pointers`，我们本质上在传递另一个函数。必须编写函数，并且必须知道调用哪个函数。这种方法有两个主要问题：

+ 我们必须为功能相似的任务编写不同的函数，比如判断是否小于x这件事，随着x值的不同，要写非常多几乎相似的函数来实现对应的功能。

+ 从上面的例子中可以发现，当调用predicate的时候，只传递了<u>**一个**</u>参数！如果尝 试声明另一个有多个参数的predicate，那么就会报错，代码将会是无法编译的。

	

解决方法：

+ function objects (functors)
+ <u>lambda functions</u>
	+ 你可以创建一个非常轻量的函数，这个函数是一个对象，但它表现得像一个函数。

eg：

```c++
int main()
{
  int limit = getInteger("Minimum for A?");
  vector<int> grades = readSrudentGrades();
  auto func = [limit](auto val) {return val >= limit;};
  countOccurences(grades.begin(), grades.end(), func);
}
```

lambda函数的格式如下：

```c++
auto func = [capture-clause](parameters) -> return-value{
	//body
};
//lambda函数的返回值一般是非常明显的，通常对于predicate它们返回boolean
//所以其实return-value那里可以不写
auto func = [capture-clause](parameters) {
	//body
};
//capture-clause的部分用于捕捉这个作用域中的变量并使其在func中可以使用
//parameters的部分可以使用auto，body的部分和函数是一样的
```

`capture-clause`的部分可以选择使用`reference`的方式来捕捉，也可以不用。

eg:

```c++
set<string> teas{"black", "green", "oolong"};
string banned = "boba";
auto likedByAvery = [&teas, banned](auto type){
  return teas.count(type) && type != banned;
};
```



### Algorithm

可参阅文档：

​	https://www.apiref.com/cpp-zh/cpp/algorithm.html

​	https://oi-wiki.org/lang/csl/algorithm/

+ 上文中提到的`countOccurences`其实在STL中有更好的实现方法，即`count/count_if`。`count`是没有谓词的，你只需要提供数据值，然后它会检查有多少个实例。`count_if`需要谓词。

- `sort`：https://www.apiref.com/cpp-zh/cpp/algorithm/sort.html

	排序。`sort(v.begin(), v.end(), cmp)` 或 `sort(a + begin, a + end, cmp)`，其中 `end` 是排序的数组最后一个元素的后一位，`cmp` 为自定义的比较函数。

	- `stable_sort`：稳定排序，用法同 `sort()`。

- `nth_element`：按指定范围进行分类，即找出序列中第 n大的元素，使其左边均为小于它的数，右边均为大于它的数。`nth_element(v.begin(), v.begin() + mid, v.end(), cmp)` 或 `nth_element(a + begin, a + begin + mid, a + end, cmp)`。

+ `copy_if`：https://www.apiref.com/cpp-zh/cpp/algorithm/copy.html

	查看某一个范围，并将所有满足predicate的元素复制到目标位置。

	` std::copy(iterator source_first, iterator source_end, iterator target_start);`

	其中

	- `iterator source_first, iterator source_end`- 是源容器的迭代器位置。
	- `iterator target_start`- 是目标容器的开始迭代器。

	返回值： `iterator`- 它是指向已复制元素的目标范围末尾的迭代器。

	eg：这种情况下是无效的：

	```c++
	std::copy_if(courses.begin(), courses.end(), cscourses, isDep);
	```

	vector会有一个默认的大小，但是这些algorithm并不是vector本身的成员函数，所以它们其实不能改变那个vector的大小。所以它们其实是尝试向vector中写入，然而会超出end iterator，然后继续写入东西。

	解决方法：有一种特殊的迭代器，可以改表container的大小，即`back_inserter`

	```c++
	std::copy(course.begin(), course.end(), back_inserter(cscourses), isDep);
	```

+ `remove_if`：https://www.apiref.com/cpp-zh/cpp/algorithm/remove.html

	删除。从向量中移除所有满足predicate返回true的项。但是注意，它删除后不会改变container的大小，即使用remove后，container有效部分的后面会有一堆垃圾数据。

	解决方法：`erase-remove`

	```c++
	v.erase(
	  std::remove_if(v.begin(),v.end(),pred),
	  v.end()
	)
	```

	这样就删掉了尾部的垃圾数据。
