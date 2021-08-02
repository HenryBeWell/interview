# 拷贝

在研究Python拷贝之前，先聊聊什么变量吧。在《流畅的python》中，有一个很形象的图解释了这一点：

![变量](..\assets\images\variable.png)

```python
>>> a = [1, 2, 3] 
>>> b = a 
>>> a.append(4) 
>>> b 
[1, 2, 3, 4]
```

在Python中，我们可以将变量简单理解为，标注或者便利贴。

## 标识、相等性和别名

Lewis Carroll 是 Charles Lutwidge Dodgson 教授的笔名。Carroll 先生指的就是 Dodgson 教授，二者是同一个人。示例 1 用 Python 表达了这个概念。

示例 **1** charles 和 lewis 指代同一个对象 

```python
>>> charles = {'name': 'Charles L. Dodgson', 'born': 1832} 

>>> lewis = charles ➊ 

>>> lewis is charles 

True 

>>> id(charles), id(lewis) ➋ 

(4300473992, 4300473992) 

>>> lewis['balance'] = 950 ➌ 

>>> charles 

{'name': 'Charles L. Dodgson', 'balance': 950, 'born': 1832} 
```

❶ lewis 是 charles 的别名。 

❷ is 运算符和 id 函数确认了这一点。 

❸ 向 lewis 中添加一个元素相当于向 charles 中添加一个元素。 

然而，假如有冒充者（姑且叫他 Alexander Pedachenko 博士）生于1832年，声称他是 Charles L. Dodgson。这个冒充者的证件可能一样，但是Pedachenko博士不是 Dodgson 教授。这种情况如图 2 所示。

![8-2](..\assets\images\equal.png)

图 **2**：**charles** 和 **lewis** 绑定同一个对象，**alex** 绑定另一个具有相同内容的对象 

示例 4 实现并测试了图 2 中那个 alex 对象。 

示例 **4** alex 与 charles 比较的结果是相等，但 alex 不是charles 

```python
>>> alex = {'name': 'Charles L. Dodgson', 'born': 1832, 'balance': 950} ➊ 
>>> alex == charles ➋ 
True 
>>> alex is not charles ➌ 
True 
```

❶ alex 指代的对象与赋值给 charles 的对象内容一样。 

❷ 比较两个对象，结果相等，这是因为 dict 类的 \__eq__ 方法就是这样实现的。 

❸ 但它们是不同的对象。这是 Python 说明标识不同的方式：a is not b。

示例 3 体现了别名。在那段代码中，lewis 和 charles 是别名，即两个变量绑定同一个对象。而 alex 不是 charles 的别名，因为二者绑定的是不同的对象。alex 和charles 绑定的对象具有相同的值（== 比较的就是值），但是它们的标识不同。

## 浅拷贝

创建新对象，其内容是原对象的引用

浅拷贝有三种形式： 切片操作，工厂函数，copy模块中的copy函数。

如： 

`lst = [1,2,[3,4]]`

切片操作：`lst1 = lst[:]`或者 `lst1 = [each for each in lst]`

工厂函数：`lst1 = list(lst)`

copy函数：`lst1 = copy.copy(lst)`

浅拷贝之所以称为浅拷贝，是它仅仅只拷贝了一层，拷贝了最外围的对象本身，内部的元素都只是拷贝了一个引用而已，在`lst`中有一个嵌套的`list[3,4]`，如果我们修改了它，情况就不一样了。

## 深拷贝

和浅拷贝对应，深拷贝拷贝了对象的所有元素，包括多层嵌套的元素。深拷贝出来的对象是一个全新的对象，不再与原来的对象有任何关联。

所以改变原有被复制对象不会对已经复制出来的新对象产生影响。

只有一种形式，copy模块中的deepcopy函数

## 总结

**对于可变对象深浅拷贝(外层改变元素)**

```text
import copy
l=[1,2,3,[4, 5]]

l1=l #赋值
l2=copy.copy(l) #浅拷贝
l3=copy.deepcopy(l) #深拷贝
l.append(6)

print(l)  
print(l1)
print(l2)
print(l3)
```

结果：

```text
[1, 2, 3, [4, 5], 6]     #l添加一个元素6
[1, 2, 3, [4, 5], 6]     #l1跟着添加一个元素6
[1, 2, 3, [4, 5]]        #l2保持不变
[1, 2, 3, [4, 5]]        #l3保持不变
```

  **对于可变对象深浅拷贝(内层改变元素)**

```text
import copy
l=[1,2,3,[4, 5]]

l1=l #赋值
l2=copy.copy(l) #浅拷贝
l3=copy.deepcopy(l) #深拷贝
l[3].append(6) 

print(l) 
print(l1)
print(l2)
print(l3)
```

结果：

```text
[1, 2, 3, [4, 5, 6]]      #l[3]添加一个元素6
[1, 2, 3, [4, 5, 6]]      #l1跟着添加一个元素6
[1, 2, 3, [4, 5, 6]]      #l2跟着添加一个元素6
[1, 2, 3, [4, 5]]         #l3保持不变
```

1. 外层添加元素时，浅拷贝不会随原列表变化而变化；内层添加元素时，浅拷贝才会变化。
2. 无论原列表如何变化，深拷贝都保持不变。
3. 赋值对象随着原列表一起变化。

## 参考

1. 《流畅的Python》第八章
2.  [Python中的赋值(复制)、浅拷贝与深拷贝 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/54011712)

