# 装饰器

上一篇了解了什么是闭包，其实装饰器就是一个闭包，装饰器是闭包的一种应用。那么什么是装饰器呢，简言之，python装饰器就是用于**在不影响原来函数功能的情况下拓展其额外功能的一种函数**，这个函数的特殊之处在于它的输入输出都是函数。

一个比较经典的例子：

```python 
import time
from functools import wraps

def timethis(func):
    '''
    Decorator that reports the execution time.
    '''
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(func.__name__, end-start)
        return result
    return wrapper
```

其中`@wraps(func)` 注解是很重要的， 它能保留原始函数的元数据信息。

## 带参数的装饰器

我们用一个例子详细阐述下接受参数的处理过程。 假设你想写一个装饰器，给函数添加日志功能，同时允许用户指定日志的级别和其他的选项。 下面是这个装饰器的定义和使用示例：

```python
from functools import wraps
import logging

def logged(level, name=None, message=None):
    """
    Add logging to a function. level is the logging
    level, name is the logger name, and message is the
    log message. If name and message aren't specified,
    they default to the function's module and name.
    """
    def decorate(func):
        logname = name if name else func.__module__
        log = logging.getLogger(logname)
        logmsg = message if message else func.__name__

        @wraps(func)
        def wrapper(*args, **kwargs):
            log.log(level, logmsg)
            return func(*args, **kwargs)
        return wrapper
    return decorate

# Example use
@logged(logging.DEBUG)
def add(x, y):
    return x + y

@logged(logging.CRITICAL, 'example')
def spam():
    print('Spam!')
```

初看起来，这种实现看上去很复杂，但是核心思想很简单。 最外层的函数`logged()` 接受参数并将它们作用在内部的装饰器函数上面。 内层的函数`decorate()` 接受一个函数作为参数，然后在函数上面放置一个包装器。 这里的关键点是包装器是可以使用传递给 `logged()` 的参数的。

## 叠放装饰器

把 @d1 和 @d2 两个装饰器按顺序应用到 f 函数上，作用相当于 f = d1(d2(f))。 

也就是说，下述代码： 

```python
@d1 
@d2 
def f(): 
    print('f')
```

等同于：

```python
def f(): 
    print('f') 
f = d1(d2(f))
```

## 参考

1. [Python Cookbook 第九章](https://python3-cookbook.readthedocs.io/zh_CN/latest/c09/p01_put_wrapper_around_function.html#id1)
2. 《流畅的Python》第七章

