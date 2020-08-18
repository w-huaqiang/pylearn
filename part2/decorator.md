# 装饰器

装饰器本质是函数，就是为其他函数添加附加功能。

比如，有如下代码:

```python
# this is first function,which will echo "打倒泰山"
def bigMan():
    print("打倒泰山")

bigMan()
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



如果修改`bigMan`,则无法保证之前使用的地方不出问题，新增`bigMan2`如何和老的函数`bigMan`统一，修改以前代码谈何容易呀 ？现在有一种新的方法:

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

