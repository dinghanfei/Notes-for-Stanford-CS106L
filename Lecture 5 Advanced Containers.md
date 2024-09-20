# Lecture 5 Advanced Containers



### Multimap

和普通的map有什么区别？

```
普通的map中一个value不会对应多个key，而multimap允许相同的key指向不同的values
```



### Map Iterators

它和普通的iterators略有不同，因为map包含key和value，所以map iterators  是一个pair，类似这种`std::pair<string, int>`

 可以通过 `(*i).first`  和 `(*i).second` 的方式分别访问所遍历map的key和value



### Further Iterator Usages

https://oi-wiki.org/lang/csl/algorithm/

- `find`：顺序查找。`find(v.begin(), v.end(), value)`，其中 `value` 为需要查找的值。
- `count(x)` 返回 `set/map`内键为 x 的元素数量。

​       *`find`其实比`count`更快，因为`count`的底层是依照`find`实现的

- `sort`：排序。`sort(v.begin(), v.end(), cmp)` 或 `sort(a + begin, a + end, cmp)`，其中 `end` 是排序的数组最后一个元素的后一位，`cmp` 为自定义的比较函数。

- `lower_bound`：在一个有序序列中进行二分查找，返回指向第一个 **大于等于** x 的元素的位置的迭代器。如果不存在这样的元素，则返回尾迭代器。`lower_bound(v.begin(),v.end(),x)`。
- `upper_bound`：在一个有序序列中进行二分查找，返回指向第一个 **大于** x 的元素的位置的迭代器。如果不存在这样的元素，则返回尾迭代器。`upper_bound(v.begin(),v.end(),x)`。

#### Ranges

|       |     [a,b]      |     [a,b)      |     (a,b]      |     (a,b)      |
| :---: | :------------: | :------------: | :------------: | :------------: |
| begin | lower_bound(a) | lower_bound(a) | upper_bound(a) | upper_bound(a) |
|  end  | upper_bound(b) | lower_bound(b) | upper_bound(b) | lower_bound(b) |



### Iterator Type

所有iterator都可以完成：

+ 创建
+ 自增++
+ 比较 == 或 ！=

然而有一些iterator有着更加强大的功能：

#### input/output iterators

用于顺序单通道(sequential single-pass)数据结构，iterators只能递增一，并且input iterator是只读不写的；output iterator是只写的。

eg:  find, count

#### forward iterators

可读可写，可以实现multiple passes 

eg: replace

#### bidirectional iterators

可以实现递减`--`操作

eg: reverse

#### random access iterators

功能最强大的iterator！

可读可写，可以任意递增或递减，比如`+3/-2`这样，而不用局限于`++/--`

