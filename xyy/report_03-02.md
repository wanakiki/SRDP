# 寒假总结

  寒假期间主要进行python的学习，学习材料为[廖雪峰的python教程](https://www.liaoxuefeng.com/wiki/1016959663602400)，目前已学习到高级特性部分。

## 主要学习内容

### python基础

* 输入输出函数
* 数据类型及变量
* 使用list、tuple、dict和set
* 条件判断
```
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```
* 循环
```
for x in ...
while <判断语句>：
    <执行语句>
```

### 函数

* 函数调用
* 定义函数
```
练习：定义一个函数quadratic(a, b, c)，接收3个参数，返回一元二次方程 ax2+bx+c=0 的两个解。

import math
def quadratic(a,b,c):
    if not isinstance(a,(int,float)):
        raise TypeError('bad operand type')
    if not isinstance(b,(int,float)):
        raise TypeError('bad operand type')
    if not isinstance(c,(int,float)):
        raise TypeError('bad operand type')
    t = b*b - 4*a*c
    if t < 0:
        return '此方程无解'
    elif t == 0:
        print('此方程仅有一解：')
        return -b/(2*a)
    else:
        x = math.sqrt(t)
        x1 = (-b+x)/(2*a)
        x2 = (-b-x)/(2*a)
        return x1, x2
```
* 函数参数
```
位置参数
默认参数
可变参数（*args）
关键字参数(**kw)
命名关键字参数(分隔符\*)
```
* 递归函数
```
汉诺塔问题:

def hanoi(n, a, b, c):
    if n == 1:
        print(a, '-->', c)
    else:
        hanoi(n-1, a, c, b)
        print(a, '-->', c)
        hanoi(n-1, b, a, c)
```

 ### 高级特性

 * 切片
 L[0:10:2]表示 从索引0开始取，隔两个元素取一个，直到索引10，不包括索引10。适用于list，tuple和字符串。
 * 迭代
 通过for…in完成迭代
 通过collections.abc模块中Iterable类型判断是否为可迭代对象
 ```
 from collections.abc import Iterable
 isinstance(对象，Iterable)
 ```
 * 列表生成器
 ```
 [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

[x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]

[m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```
---
目前只学到了这里，进度比较慢，还需要多做题加强练习。