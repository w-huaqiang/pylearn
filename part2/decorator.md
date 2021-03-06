# 装饰器

## 初涉

装饰器本质是函数，就是为其他函数添加附加功能。

比如，有如下代码:

```python
# this is first function,which will echo "打倒泰山"
def bigMan():
    print("打倒泰山")

bigMan()
```

output:

```bash
打倒泰山
```

现在有人质疑，不练武功怎么打倒泰山。于是你想着修改函数或者新增函数`bigMan2`。

```python
def bigMan():
    print("打倒泰山")

def bigMan2():
    print("学习乾坤大挪移")
    print("打倒泰山")

#bigMan()
bigMan2()
```

output:

```bash
学习乾坤大挪移
打倒泰山
```



如果修改`bigMan`,则无法保证之前使用的地方不出问题，新增`bigMan2`如何和老的函数`bigMan`统一，修改以前代码谈何容易,现在有一种新的方法:

```python
#!/usr/bin/env python
# Author Thomas wang

def bigMan():
    print("打倒泰山")

def bigMan2():
    print("学习乾坤大挪移")
    print("打倒泰山")

def NewBigMan(func):
    def wrapTheFunction():
        print("学习乾坤大挪移")
        func()
    return wrapTheFunction

#bigMan()
#bigMan2()
bigMan = NewBigMan(bigMan)
bigMan()
```

output:

```bash
学习乾坤大挪移
打倒泰山
```

创建一个新的函数，用来加工`bigMan`,使其重新做人。在需要使用新的`bigMan`的地方，用`NewBigMan`对其改造下，然后在用即可，这样也不需要对老的进行修改。然后这些工作量还是偏大了点，于是python帮我们做了些，上面代码优化如：

```python
def NewBigMan(func):
    def wrapTheFunction():
        print("学习乾坤大挪移")
        func()
    return wrapTheFunction

@NewBigMan # bigMan = NewBigMan(bigMan)
def bigMan():
    print("打倒泰山")

def bigMan2():
    print("学习乾坤大挪移")
    print("打倒泰山")



#bigMan()
#bigMan2()
#bigMan = NewBigMan(bigMan)
bigMan()
print(bigMan.__name__)
```

output:

```bash
学习乾坤大挪移
打倒泰山
wrapTheFunction
```



然而，细心的话会发现，此时bigMan的元数据`__name__`将是`wrapTheFunction`,这不是我们想要的，我们想要的是`bigMan`，wrapTheFunction重写了函数名字和注释文档，好在python又帮我们做了些事，那就是使用functools.wraps。以上代码可优化如下:

```python
#!/usr/bin/env python
# Author Thomas wang
from functools import wraps


def NewBigMan(func):
    @wraps(func)
    def wrapTheFunction():
        print("学习乾坤大挪移")
        func()
    return wrapTheFunction

@NewBigMan
def bigMan():
    print("打倒泰山")

def bigMan2():
    print("学习乾坤大挪移")
    print("打倒泰山")



#bigMan()
#bigMan2()
#bigMan = NewBigMan(bigMan)
bigMan()
print(bigMan.__name__)
```

output

```bash
学习乾坤大挪移
打倒泰山
bigMan
```



## 进阶

### 带参数的函数

上面例子中函数均未包含参数，若函数带有参数将不能使用，如下

```python
#!/usr/bin/env python
# Author Thomas wang
from functools import wraps


def NewBigMan(func):
    @wraps(func)
    def wrapTheFunction():
        print("学习乾坤大挪移")
        func()
    return wrapTheFunction

@NewBigMan
def bigMan():
    print("打倒泰山")

@NewBigMan
def bigMan2(name):
    print(name,"学习乾坤大挪移")
    print("打倒泰山")



#bigMan()
#bigMan2()
#bigMan = NewBigMan(bigMan)

bigMan2("thomas")

```

output:

```bash
thomaswangdeMBP:week3 thomas$ python3 decoratorTest3.py 
Traceback (most recent call last):
  File "decoratorTest3.py", line 28, in <module>
    bigMan2("thomas")
TypeError: wrapTheFunction() takes 0 positional arguments but 1 was given
```

很显然，我们不想因为被装饰的函数因为是否有参数而影响使用，于是我们修改装饰器函数如下:

```python
#!/usr/bin/env python
# Author Thomas wang
from functools import wraps


def NewBigMan(func):
    @wraps(func)
    def wrapTheFunction(*args,**kwargs):
        print("学习乾坤大挪移")
        func(*args,**kwargs)
    return wrapTheFunction
    
@NewBigMan
def bigMan():
    print("打倒泰山")

@NewBigMan
def bigMan2(name):
    print(name,"学习乾坤大挪移")
    print("打倒泰山")



#bigMan()
#bigMan2()
#bigMan = NewBigMan(bigMan)

bigMan2("thomas")
```

output

```bash
学习乾坤大挪移
thomas 学习乾坤大挪移
打倒泰山
```



### 带参数的装饰器

现在有如下需求：`定义装饰器，添加函数被调用日志，且可以自定义日志文件名称.`

经过分析可知，添加功能使用装饰器，装饰器装饰函数时可自定义传入日志文件名称参数，如`out2.log`，

由上可知伪代码如下

```python
def logger(logfile='out.log'):
  pass


@logger()
def bigMan():
  print("打倒泰山")
 
@logger(logfile='out2.log')
def bigGril():
  print("打倒金刚")
```

我们发现，此处装饰器使用为`@logger()`,和上面装饰器`@NewBigMan`有些不同，此处多了个括号。那么对比发现

```python
@NewBigMan ------> bigMan = NewBigMan(bigMan)
```

`@NewBigMan`相当于加了个`NewBigMan(bigMan)`那么

```python
@logger() -------> bigMan = logger()(bigMan)
```

所以，我们实现一个logger()，使其能返回一个类似`NewBigMan`的函数就可以了，代码如下

```python
from functools import wraps
import time


def logger(logfile='out.log'):
    def wraperOut(func):
        @wraps(func)
        def wraperIn():
            nowTime = time.asctime()
            func()
            logsString = "you calld func " + func.__name__
            with open(logfile,'a') as fileopen:
                fileopen.write(nowTime+':'+logsString+'\n')
        return wraperIn
    return wraperOut


@logger()
def bigMan():
  print("打倒泰山")
 
@logger(logfile='out2.log')
def bigGril():
  print("打倒金刚")


bigMan()
bigGril()
```

