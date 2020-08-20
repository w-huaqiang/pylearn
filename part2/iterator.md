# 迭代器
## 初涉
迭代器是一种对象，该对象包含值的可计数数字。
迭代器是可迭代对象，这意味着可以遍历所有的值。
如下例子
```python
>>> L=[1,2,3,4,5,6]
>>> 
>>> for i in L:
...   print(i)
... 
1
2
3
4
5
6
```
此时我想像上节生成器那样用`next()`取出L的值怎么办?很简单，我们使用`iter()`函数将其转变成迭代器就可以了，如下:
```python
>>> l = iter(L)
>>> next(l)
1
>>> next(l)
2
>>> next(l)
3
>>> next(l)
4
>>> next(l)
5
>>> next(l)
6
>>> next(l)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```



### 迭代对象
上面转换过程中我们发现`iter`很简单，但是什么样的对象可以转换成迭代器呢？答案是:`可迭代对象`

在理解迭代器之前我们先要了解可迭代对象(iterable)。像字符串、列表、元组、字典和集合都是可迭代对象。那么如何判断对象是否是iterable呢
```python

>>> from collections import Iterable
>>> 
>>> isinstance([],Iterable)
True
>>> isinstance((),Iterable)
True
>>> isinstance({},Iterable)
True
>>> isinstance('good',Iterable)
True
>>> isinstance(123,Iterable)
False


```

对可迭代对象进行`iter()`转换，我们就可以使用`next()`进行循环取值了.

## 内幕
```python
L = [ x for x in range(10) ]
g = ( x for x in range(10) )

for i in g:
    print(i)

```
以上代码是上节内容，我们并未执行`next`为啥能取出值呢？其实`for`循环实际上创建了迭代器，并为每个循环执行`next()`方法

## 进阶

要想把class创建为迭代器，那么必须实现`_ _iter_ _()`和`_ _next()_ _`方法,如下创建一个不断增加数字的迭代器


```python
class autoIncrement:
    def __iter__(self):
        self.a = 1
        return self
    
    def __next__(self):
        x = self.a
        self.a += 1
        return x

myAuto = iter(autoIncrement())

print(next(myAuto))
print(next(myAuto))
print(next(myAuto))

```
output
```bash
1
2
3
```