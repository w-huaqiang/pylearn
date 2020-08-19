# 生成器

## 初涉

我们看一段小代码

```python
for i in range(10):
    print(i,end=' ')

for i in [0,1,2,3,4,5,6,7,8,9]:
    print(i,end=' ')
```

output

```bash
0 1 2 3 4 5 6 7 8 9 
0 1 2 3 4 5 6 7 8 9 
```

虽然代码中结果是一样的，但是内部其实有所不同，rang(10)并不是立马生成10个数放在内存里面（python2是这样的），而是先生成第一个数，当需要第二个的时候才生成第二个，而且第一个被使用后就从内存中清除。所以`range()`可以放很大的数。生成器就是这个逻辑。我们继续看下面代码(打开python 终端)

```python
>>> L = [ x for x in range(10)]
>>> g = ( x for x in range(10))
>>> 
>>> 
>>> L
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> g
<generator object <genexpr> at 0x10c8e69a8>
```

如上，`g`便是一个生成器。这是最简单创建生成器的方法。`L`和`g`的区别就在`[]`、`()`。那么我们怎么取出g里面的值呢

```python
>>> next(g)
0
>>> next(g)
1
>>> next(g)
2
>>> next(g)
3
>>> next(g)
4
>>> next(g)
5
>>> next(g)
6
>>> next(g)
7
>>> next(g)
8
>>> next(g)
9
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>> 
```

生成器(generator)保存的是算法，每次调用next()，就计算出下一个元素的值，直到计算到最后一个元素，没有元素时，抛出`StopIteration`异常。当然在开发过程中我们很少用`next()`，而是直接循环取出

```python
L = [ x for x in range(10) ]
g = ( x for x in range(10) )

for i in g:
    print(i)
```

## 内幕

开始之前，我们先创建一个生成斐波拉契数列的函数，至于斐波拉契数是什么可自行百度。或者参考如下:

> 斐波那契数列（Fibonacci sequence），又称[黄金分割](https://baike.baidu.com/item/黄金分割/115896)数列、因[数学家](https://baike.baidu.com/item/数学家/1210991)[莱昂](https://baike.baidu.com/item/莱昂)[纳多](https://baike.baidu.com/item/纳多)·斐波那契（Leonardoda Fibonacci）以兔子繁殖为例子而引入，故又称为“[兔子数列](https://baike.baidu.com/item/兔子数列/6849441)”，指的是这样一个数列：0、1、1、2、3、5、8、13、21、34、……在数学上，斐波那契数列以如下被以递推的方法定义：*F*(1)=1，*F*(2)=1, *F*(n)=*F*(n - 1)+*F*(n - 2)（*n* ≥ 3，*n* ∈ N*）
>
> 

```python
def fib(count):
    n,a,b = 0,0,1
    while n < count:
        print(b)
        a, b = b, a+b
        n += 1

fib(10)
```

这种方式是直接打印出10个斐波拉契数，但是我们想从第一个元素开始，推算出后面任意的元素。于是可以将函数修改为生成器，只需将`print`改为`yield`

```python
def fib(count):
    n,a,b = 0,0,1
    while n < count:
        yield b
        a, b = b, a+b
        n += 1

for f in fib(10):
    print(f)
```

此处，`fib`是不是很像`range`函数。至于`yield`关键字有点像`断点`,除非调用`next()`,否则挂在那里。

## 进阶

生成器（generator）除了`next()`函数外，还有个函数`send()`,当生成器挂在`yield`位置时，可以使用`send()`塞进去一个值，并执行`next()`。看代码如下:

```python
def generate(count):

    while True:
        val = (yield count) 
        if val is not None:
            count = val
        else:
            count += 1
f = generate(5)
print(next(f))
print(next(f))
print(next(f))
print('====================')
print(f.send(10))  #发送数字9给生成器
print(next(f))
print(next(f))
```

output

```bash
5
6
7
====================
10
11
12
```

如上我们看到本来`generate`是5开始，一次增加1。当我们`send()`10后，count就变成了10，然后10开始一次增加1。关键点包含两个部分

1. val值是什么? val值为send过来的值，和后面是否加count无关，`yield count`是为了打印count。

```python
val = yield count # val = 10
val = yield # val = 10
```

2. 每次`next()`，代码都会停到到`yield`那一行，当下一次`next()`时会继续执行`yield`行下面的代码。

```python
def generate(count):

    while True:
        val = (yield count)  # 本行执行完停止，等待下一个next() <-
        if val is not None:  #                               /
            count = val      #                              / 
        else:                #                            / 
            count += 1       #                           / 
f = generate(5)              #                         /
print(next(f)) # -------->第一次执行时代码会走到 ________/
print(next(f))
print(next(f))
print('====================')
print(f.send(10))  #发送数字9给生成器
print(next(f))
print(next(f))
```

> 注意:
>
> ​     当yield未收到send的值时，会打印None



下面是个订阅发布的例子，也是关于协程并发的模型

```python
#!/usr/bin/env python
# Author: Thomas Wang
import time

def subscribe(name):
    print("订阅者 %s 已经准备好了，可以传输数据..." % name )
    while True:
        files = yield
        print("%s 正在传输 %s ..." % (name,files) ,end=' ')
        time.sleep(2)
        print("done")

def publish():
    sub1 = subscribe("华强") #创建一个订阅者
    sub2 = subscribe("微哥") #创建另一个订阅者
    next(sub1)
    next(sub2)
    for i in ["etc","opt","var","mnt","opt"]:
        sub1.send(i)
        sub2.send(i)


publish()
```

