# 内置函数

## 使用较多
- `abs()` 返回函数绝对值,如果是复数，则返回它大小
```python
abs(-40)
```
output
```bash
40
```


- `min()` 返回给定参数最小值，参数可以是序列. 对应函数`max()`
```python
a = [1,6,42,3213,21,0]
print(min(a))
```
output
```bash
1
```

- `setattr()` 设置属性值，对于函数`getattr()`
```python
class A(object):
    bar = 1

a = A()
getattr(a,'bar')
setattr(a,'bar',5)
```
output
```bash
1
5
```
- `enumerate(sequence,[start=0])` 将一个可遍历的数据对象（如列表、元组或者字符串）组合为一个索引序列。一般在for循环中使用。
```python
s = ['Spring','Summer','Fall','Winter']
for k,v in enumerate(s):
    print(k,v)
```
output
```bash
0 Spring
1 Summer
2 Fall
3 Winter
```
## 不常用
`dict()` 创建一个字典
`help()` 返回对象帮助信息
