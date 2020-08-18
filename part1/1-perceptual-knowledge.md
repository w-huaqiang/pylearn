# 感性认识python

## 调用解释器

在Python可用的机器上，可以直接调出python解释器

```bash
# default
thomaswangdeMBP:~ thomas$ python
Python 2.7.10 (default, Feb 22 2019, 21:55:15) 
[GCC 4.2.1 Compatible Apple LLVM 10.0.1 (clang-1001.0.37.14)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 

# 若同时有python2和python3一般为python3
thomaswangdeMBP:~ thomas$ python3
Python 3.7.0 (v3.7.0:1bf9cc5093, Jun 26 2018, 23:26:24) 
[Clang 6.0 (clang-600.0.57)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

可以查看下`python`和`python3`分别在哪个目录

```bash
thomaswangdeMBP:~ thomas$ which python
/usr/bin/python
thomaswangdeMBP:~ thomas$ which python3
/Library/Frameworks/Python.framework/Versions/3.7/bin/python3
thomaswangdeMBP:~ thomas$ 
```

可以看出，python两个版本分别安装在了不同的路径，但为什么`python`可以直接调用出python2.7呢，如果我们创建一个python指向python3的链接，一样可以通过`python`调用出python3的版本.

## 解释器使用

### 数字

解释器可以像计算器一样进行加减乘除运算，表达式很简单，运算符和其他语言也类似`+`、`-`、`*`、`/`。

```python
>>> 3 + 1
4
>>> 3 + ( 2 * 3) # 和四则运算一样 （ 可以更改运算优先级 ）
9
>>> 50/2
25.0
>>> 8/5   # division always returns a floating point number
1.6
>>> 
```

可以看出`/`返回的永远是浮点数类型。如果想要做到向下整除(floor division)，可以使用`//`，如果取余数，可以使用`%`

```python
>>> 8//5
1
>>> 50//2
25
>>> 8%5
3
>>> 50%2
0
>>> 
```

可以使用`**`运算来计算乘方

```python
>>> 2**2
4
>>> 2**3
8
>>> 
```

可以使用变量来储存值

```python
>>> width = 20
>>> height = 5
>>> width * height
100
>>> 
```

> 
>
> 注意： 在交互模式下，上一次打印出来的表达式被赋值给`_`
>
> ```python
> >>> width = 20
> >>> height = 5
> >>> width * height
> 100
> >>> 2 + _
> 102
> >>> 
> ```
>
> 

### 字符串

python同样可以操作字符串，字符串用单引号`''`，双引号`""`表示。使用反斜线转译`\`

```python
>>> 'thomas' # single quotes
'thomas'
>>> 'thomas\'s work' # use \ to escape the single quotes
"thomas's work"
>>> "thomas's work" # use double quotes instead
"thomas's work"
>>>
```

使用`print()`打印可读性更强

```python
>>> print("thomas")
thomas
>>> print("thomas's work")
thomas's work
>>> print("thomas's work is \n administrator") # use \n to newline
thomas's work is 
 administrator
```

可以看到`print()`默认帮助转译，若不想转移怎么办？在引号前面加一个`r`就行

```python
>>> print("c:\name")
c:
ame
>>> print(r"c:\name") # note the r before quote
c:\name
>>> 
```

字符串可以跨行连续输出，使用`'''....'''`或者`"""..."""`，字符串中的回车换行会自动包含到输出中，若不想包含在行尾加一个`\`

```python
>>> print('''\
... Usage: connect [OPTIONS]
...     -h             Display this usage messge
...     -H hostname    Hostname to connect
... learnmore \
... ''')
Usage: connect [OPTIONS]
    -h             Display this usage messge
    -H hostname    Hostname to connect
learnmore 
>>> 
```

字符串可以使用`+`拼接，也可以使用`*`重复

```python
>>> 'thomas' * 2
'thomasthomas'
>>> 'thomas' * 2 + 'good'
'thomasthomasgood'
>>> 
```

字符串是可以被索引的，第一个下标为0

```python
>>> word = 'Python'
>>> word[0] # first character
'P'
>>> word[5] # character in position 5
'n'
>>> word[-1] # last character
'n'
>>> word[-2] # second-last character
'o'
>>> 
```

字符串同时可以被切片

```python
>>> word[0:2] # character from position 0 to 2 (excluded)
'Py'
>>> word[2:5] # character from position 2 to 5 (excluded)
'tho'
>>> 
```

字符串切片有默认值，缺省索引开始为0，结束为字符串结尾

```python
>>> word[:2]   # character from the beginning to position 2 (excluded)
'Py'
>>> word[4:]   # characters from position 4 (included) to the end
'on'
>>> word[-2:]  # characters from the second-last (included) to the end
'on'
```

字符串切片可以跨界，但是取字符串索引位置时不能越界

```python
>>> word[2:40]
'thon'
>>> word[40]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range
>>> 
```

字符串长度可用内建函数`len()`

```python
>>> len(word)
6
>>> 
```

### 列表

列表是python中最常见的序列，可以通过方括号括起、逗号分隔的一组值（元素）得到。一个列表可以包含多种数据类型，但是一般使用时，各元素类型相同。列表和字符串一样支持索引和切片。

```python
>>> list = [1,3,6,7,8]
>>> list
[1, 3, 6, 7, 8]
>>> list[0]
1
>>> list[-1]
8
>>> list[-3:]
[6, 7, 8]
>>> 
```

所有的切片操作都返回一个新列表，这个新列表包含所需要的元素。切片会返回列表的一个新的(浅)拷贝

```python
>>> list2 = list[:]
>>> list2
[1, 3, 6, 7, 8]
>>> list2[0]=2
>>> list
[1, 3, 6, 7, 8]
>>> list2
[2, 3, 6, 7, 8]
>>> 
```

如上可以看出，列表是`mutable`,这是和字符串的不同，字符串是`immutable`。可以直接对列表索引位置元素更改，若增加元素，可以使用`append()`函数，给列表追加元素。

```python
>>> list2
[2, 3, 6, 7, 8]
>>> list2.append(10)
>>> list2
[2, 3, 6, 7, 8, 10]
```

可以直接对列表切片更改，从而更改列表

```python
>>> list
[1, 3, 6, 7, 8]
>>> list[0:2]=[100,300] # replace of position 0 to 2
>>> list
[100, 300, 6, 7, 8]
>>> list[0:2]=[] # remove some values
>>> list
[6, 7, 8]
>>> list[:]=[] # clear the list by replacing all the elements with an empty list
>>> list
[]
```

内置函数`len()`也可以作用到列表上:

```python
>>> list2
[2, 3, 6, 7, 8, 10, [10, 11]]
>>> len(list2)
7
```

