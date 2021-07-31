# 闭包
在理解闭包前，先了解下什么函数嵌套。

## 函数嵌套

> 如果在一个函数内部定义了另一个函数，我们称外部的函数为外函数，内部的函数为内函数，这种行为，我们叫它，函数嵌套

示例代码：

```python
def out_func():
    def inner_func1():      # 在内部定义函数1
        print("the first inner func")
        return

    def inner_func2():      # 在内部定义函数2
        print("the second inner func")
        return

    inner_func1()   # 调用内部函数1
    inner_func2()   # 调用内部函数2
    return

out_func()      # 调用外部函数
```

输出结果：

```
the first inner func
the second inner func
```

## 闭包

>在一个外函数内定义了一个内函数，内函数里运用了外函数的临时变量，并且外函数的返回值是内函数的引用，这样就构成了一个闭包。

一般情况下，如果一个函数结束，函数内部所有的东西都会释放掉，还给内存，局部变量都会消失。但是闭包是一种特殊情况，如果外函数在结束的时候发现有自己的临时变量将来会在内部函数中用到，就把这个临时变量绑定给了内部函数，然后自己再结束。

示例：

```python
# 闭包函数的实例：outer()是外部函数，a和b都是外部函数的临时变量
def outer(a):
    b = 10
    # inner()是内部函数

    def inner():
        # 在内部函数中用到了外部函数的临时变量
        print(a+b)

    # 外部函数返回值是内部函数的引用
    return inner


# 调用外部函数，传入参数5。此时外函数两个临时变量a是5 b是10，并创建了内部函数，然后把内部函数的引用返回给了demo进行存储，外部函数结束的时候发现内部函数将会调用到自己的临时变量，这两个临时变量就不会释放，会绑定给这个内部函数
demo1 = outer(5)

# demo存储了外部函数的返回值，也就是inner()函数的引用，这里相当于执行inner函数
demo1()     # 15

demo2 = outer(7)
demo2()     # 17
```

**说明：**

- 外函数返回内函数的引用：在Python中一切都是对象，当我们进行变量赋值时(a=1)，实际上在内存中已经存储了值1，然后用a这个变量名存储1所在内存位置的引用。引用就好比C语言的指针，可以理解为地址。a只是变量名，a里面存储的是1的这个值所在的地址，就是a里面存储了数值1的引用。同理，当我们在Python中定义函数def demo():时，内存中会开辟一些空间，存下这个函数的代码、内部的局部变量等等。这个demo只不过是一个变量名，它里面存了这个函数所在位置的引用而已。我们还可以进行x=demo，y=demo，这样的操作就相当于把demo里存的内容赋值给x和y，这样x和y都指向了demo函数所在的引用，在这之后我们可以用x()或者y()来调用我们创建的demo()，**调用的本质就是执行一个函数**，x、y和demo三个变量名存了同一个函数的引用。对于上面的例子，在外部函数outer中最后返回inner，我们在调用外部函数demo=outer()时，outer返回了inner，inner是内部函数的引用，这个引用被存入了demo中，所以接下来我们再进行demo()时，相当于执行了inner函数。
- 在内部函数中想修改闭包变量(外部函数绑定给内部函数的局部变量)时：Python3中，可以使用`nonlocal`关键字声明一个变量，表示这个变量不是局部变量空间的变量，需要向上一层变量空间找这个变量；Python2中没有nonlocal这个关键字，可以把闭包变量改成**可变类型数据**进行修改，比如：**列表**, **字典**等可变数据类型。

```python
# 修改闭包变量实例
def outer(a):  # outer是外部函数，a和b都是外部函数的临时变量
    b = 10  # a和b都是闭包变量
    c = [a]  # 这里对应修改闭包变量的方法2

    def inner():  # inner是内部函数
        # 内函数中想修改闭包变量
        nonlocal b  # 方法1 nonlocal关键字声明
        b += 1

        c[0] += 1  # 方法2 把闭包变量修改成可变数据类型，比如：列表
        print(f'after inner b: {b}, c:{c}')
        return b, c

    print(f'before inner b: {b}, c:{c}')

    return inner  # 外部函数返回内部函数的引用


demo = outer(5)
print(demo())
# before inner b: 10, c:[5]
# after inner b: 11, c:[6]
# (11, [6])
```

**注意：**使用闭包的过程中，一旦外部函数被调用一次返回了内部函数的引用，虽然每次调用内部函数是开启一个函数执行过后消亡，但是闭包变量实际上只有一份，每次开启内部函数都在使用同一份闭包变量，如下代码：

```python
def outer(x):
    def inner(y):
        nonlocal x
        x += y
        return x
    return inner


demo = outer(10)
print(demo(1))     # 11
print(demo(3))     # 14
```

参考：



1: [Python 嵌套函数和闭包]:https://www.cnblogs.com/love9527/p/9022316.html2

2: [深入浅出python闭包]:https://zhuanlan.zhihu.com/p/22229197

