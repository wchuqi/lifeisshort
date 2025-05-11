# 简介

学习心得：

- 1、从工程的角度深入理解Python，以实用为出发点，多练习、多阅读、多做项目，这样才能有质的提高。
- 2、牢牢掌握一门编程语言及其学习方法，是日后在所有领域深造的根基。不同语言，需融会贯通。
- 3、基础不牢，地动山摇。更深刻、实质的基础理解，才是更牢固的知识大厦的根基。大厦之基，勤加练习。
- 4、没上过战场开过枪的人，不可能做主官；没有实战经验的语言学习者，不可能成为高手。
- 5、对于Python的源码阅读，主要是CPython，用C写的，可能读起来比较费劲，需要一定的C基础，但是很多时候也不需要详细知道每一句话的意思，大概知道这段干什么就可以了。
- 6、代码规范，必不可少。
- 7、开发经验，质的突破。

特点：

- 1、语言简洁
- 2、开发效率高
- 3、可移植性强
- 4、可以和其他编程语言（比如 C++）轻松无缝衔接


领域：

* 1、机器学习：TensorFlow框架
* 2、人工智能的推荐排序系统与算法
* 3、数据处理
* 4、Web开发
* 5、爬虫
* 6、运维脚本
* 7、数据挖掘（Data Mining）

# 基础

![Python知识图谱](./images/Python知识图谱.png)

## 列表和元组

相同点：

- 1、列表和元组，都是一个可以放置任意数据类型的有序集合。
> 在绝大多数编程语言中，集合的数据类型必须一致。不过，对于 Python 的列表和元组来说，并无此要求。
- 2、Python 中的列表和元组都支持负数索引，-1 表示最后一个元素，-2 表示倒数第二个元素，以此类推。
- 3、列表和元组都支持切片操作。
- 4、列表和元组都可以随意嵌套。
- 5、两者也可以通过`list()`和`tuple()`函数相互转换

不同点：

- 1、列表是动态的，长度大小不固定，可以随意地增加、删减或者改变元素（mutable）。
- 2、而元组是静态的，长度大小固定，无法增加删减或者改变（immutable）。

```text
count(item) 表示统计列表 / 元组中 item 出现的次数。
index(item) 表示返回列表 / 元组中 item 第一次出现的索引。

list.reverse() 和 list.sort() 分别表示原地倒转列表和排序（注意，元组没有内置的这两个
函数)。

reversed() 和 sorted() 同样表示对列表 / 元组进行倒转和排序，但是会返回一个倒转后
或者排好序的新的列表 / 元组。
```

### 列表和元组存储方式的差异

列表空间分配的过程，为了减小每次增加 / 删减操作时空间分配的开销，Python每次分配空间时都会额外多分配一些，这样的机制（`over-allocating`）保证了其操作的高效性：增加 / 删除的时间复杂度均为 O(1)。

### 列表和元组的性能
结论：**元组要比列表更加轻量级一些，所以总体上来说，元组的性能速度要略优于列表。**

list和tuple的内部实现都是array的形式。

list因为可变，所以是一个over-allocate的array，tuple因为不可变，所以长度大小固定。

具体可以参照源码list:

https://github.com/python/cpython/blob/master/Objects/listobject.c. 

先来看 Python 3.7 的 list 源码。你可以先自己阅读下面两个链接里的内
容。

listobject.h：

https://github.com/python/cpython/blob/949fe976d5c62ae63ed505ecf729f815d0baccfc/Include/listobject.h#L23

listobject.c: 

https://github.com/python/cpython/blob/3d75bd15ac82575967db367c517d7e6e703a6de3/Objects/listobject.c#L33


tuple:

https://github.com/python/cpython/blob/master/Objects/tupleobject.c

下面是 Python 3.7 的 tuple 源码，同样的，你可以
先自己阅读一下。

tupleobject.h： 

https://github.com/python/cpython/blob/3d75bd15ac82575967db367c517d7e6e703a6de3/Include/tupleobject.h#L25

tupleobject.c：

https://github.com/python/cpython/blob/3d75bd15ac82575967db367c517d7e6e703a6de3/Objects/tupleobject.c#L16


创建一个空的列表，可以用下面的 A、B 两种方式，它们在效率上有什么区别吗？应该优先考虑使用哪种呢？
```python
# 创建空列表
# option A
empty_list = list()
# option B
empty_list = []
```
> 区别主要在于list()是一个function call，Python的function call会创建stack，并且进行一系列参
数检查的操作，比较expensive，反观[]是一个内置的C函数，可以直接被调用，因此效率高。

```shell
# 元组的初始化速度，要比列表快 5 倍。
python3 -m timeit 'x=(1,2,3,4,5,6)'
20000000 loops, best of 5: 9.97 nsec per loop
python3 -m timeit 'x=[1,2,3,4,5,6]'
5000000 loops, best of 5: 50.1 nsec per loop

# 索引操作的话，两者的速度差别非常小，几乎可以忽略不计
python3 -m timeit -s 'x=[1,2,3,4,5,6]' 'y=x[3]'
10000000 loops, best of 5: 22.2 nsec per loop
python3 -m timeit -s 'x=(1,2,3,4,5,6)' 'y=x[3]'
10000000 loops, best of 5: 21.9 nsec per loop
```

### 列表（list）

### 元组（tuple）

## 字典和集合

字典是一系列由键（key）和值（value）配对组成的元素的集合。

集合和字典基本相同，唯一的区别，就是集合没有键和值的配对，是一系列无序的、唯一的元素组合。

**Python 中字典和集合，无论是键还是值，都可以是混合类型。**

**集合并不支持索引操作，因为集合本质上是一个哈希表，和列表不一样。**

> 在 Python3.7+，字典被确定为有序（注意：在 3.6 中，字典有序是一个implementation detail，在 3.7 才正式成为语言特性，因此 3.6 中无法 100% 确保其有序性），而 3.6 之前是无序的，其长度大小可变，元素可以任意地删减和改变。

注意：
> 集合的 pop() 操作是删除集合中最后一个元素，可是集合本身是无序的，无法知道会删除哪个元素，因此这个操作得谨慎使用。

对于集合，其排序和前面讲过的列表、元组很类似，直接调用 sorted(set) 即可，结果会返回一个排好序的列表。

### 字典和集合性能

**字典和集合是进行过性能高度优化的数据结构，特别是对于查找、添加和删除操作。**

不同于其他数据结构，字典和集合的内部结构都是一张哈希表。
> 对于字典而言，这张表存储了哈希值（hash）、键和值这 3 个元素。
> 而对集合来说，区别就是哈希表内没有键和值的配对，只有单一的元素了。


初始化字典的方式，哪一种更高效？
```python
# Option A
d = {'name': 'jason', 'age': 20, 'gender': 'male'}
# Option B
d = dict({'name': 'jason', 'age': 20, 'gender': 'male'})
```
> 直接使用大括号更高效，直接调用C函数。

字典的键可以是一个列表吗？下面这段代码中，字典的初始化是否正确呢？
```python
d = {'name': 'jason', ['education']: ['Tsinghua University', 'Stanford University']}
```
> 列表不可以作为key，因为列表是可变类型，可变类型不可hash。


### 字典

哈希冲突（`hash collision`）



### 集合

## 字符串

字符串是由独立字符组成的一个序列，通常包含在单引号（''）双引号（""）或者三引号之中（''' '''或""" """，两者一样）

三引号字符串，则主要应用于多行字符串的情境，比如函数的注释等等。
```python
def calculate_similarity(item1, item2):
    """
    Calculate similarity between two items
    Args:
    item1: 1st item
    item2: 2nd item
    Returns:
    similarity score between item1 and item2
    """
    pass
```

Python 也支持转义字符。
![python转义字符](./images/python转义字符.png)

可以把字符串想象成一个由单个字符组成的数组，所以，Python 的字符串同样支持索引，切片和遍历等等操作。

和其他数据结构，如列表、元组一样，字符串的索引同样从 0 开始，index=0 表示第一个元素（字符），[index:index+2] 则表示第 index 个元素到 index+1 个元素组成的子字符串。

**Python 的字符串是不可变的（immutable）。**
```python
s = 'hello'
s[0] = 'H'
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```

每次想要改变字符串，往往需要 O(n) 的时间复杂度，其中，n 为新字符串的长度。

操作符'+='的字符串拼接方法。是一个例外，打破了字符串不可变的特性。

`str1 += str2 # 表示 str1 = str1 + str2`

自从 Python2.5 开始，每次处理字符串的拼接操作时（str1 += str2），Python 首先会检测 str1 还有没有其他的引用。如果没有的话，就会尝试原地扩充字符串 buffer 的大小，而不是重新分配一块内存来创建新的字符串并拷贝。

```text
字符串内置的 join 函数
`string.join(iterable)，表示把每个元素都按照指定的格式连接起来。`
string.split(str)，分割字符串；
string.strip(str)，表示去掉首尾的 str 字符串；
string.lstrip(str)，表示只去掉开头的 str 字符串；
string.rstrip(str)，表示只去掉尾部的 str 字符串。
string.find(sub, start, end)，表示从start 到 end 查找字符串中子字符串 sub 的位置
```

在新版本的 Python（2.5+）中，下面的两个字符串拼接操作，你觉得哪个更优呢？
```python
s = ''
for n in range(0, 100000):
    s += str(n)

l = []
for n in range(0, 100000):
    l.append(str(n))
s = ' '.join(l)
```
> += 每次都会扩容，而 [] 不会每次扩容
> 如果字符串拼接的次数较少，比如range(100)，那么方法一更优，因为时间复杂度精确的来说第一种是O(n)，第二种是O(2^n)，如果拼接的次数较多，比如range(1000000)，方法二稍快一些，虽然方法二会遍历两次，但是join的速度其实很快，列表append和join的开销要比字符串+=小一些。


### 字符串的格式化

`string.format()`

`%s`

str.format格式相对好一些，但参数多了或者处理更长字符串时还是冗长。
f-string这种方式可以更加简化表达过程。还支持大小写（f.或者F.）

## 输入和输出

### 输入

来自键盘操作

`input()` 函数暂停程序运行，同时等待键盘输入；直到回车被按下，函数的参数即为提示语，输入的类型永远是字符串型（str）。

```python
a = input()
1
b = input()
2
print('a + b = {}'.format(a + b))
########## 输出 ##############
a + b = 12
print('type of a is {}, type of b is {}'.format(type(a), type(b)))
########## 输出 ##############
type of a is <class 'str'>, type of b is <class 'str'>

print('a + b = {}'.format(int(a) + int(b)))
########## 输出 ##############
a + b = 3
```

**Python 对 int 类型没有最大限制**



### 输出


## 条件与循环

Python不支持switch语句

![if缺省](./images/if缺省.png)

Python中的循环一般通过 for 循环和 while 循环实现。

Python 内置的函数`enumerate()`

```python
expression1 if condition else expression2 for item in iterable
等同：
for item in iterable:
    if condition:
        expression1
    else:
        expression2

如果没有 else 语句，则需要写成：
expression for item in iterable if condition
```

```python
复用并不仅仅局限于一个循环。

比如，给定两个列表 x、y，要求返回 x、y 中所有元素对组成的元祖，相等情况除外。
[(xx, yy) for xx in x for yy in y if xx != yy]
等价于：
l = []
for xx in x:
    for yy in y:
        if xx != yy:
            l.append((xx, yy))
```

```python
attributes = ['name', 'dob', 'gender']
values = [['jason', '2000-01-01', 'male'],
['mike', '1999-01-01', 'male'],
['nancy', '2001-02-01', 'female']
]

s = [dict(zip(attributes,v)) for v in values]
s = [dict(zip(attributes,value)) for value in values if len(attributes)==len(value)]

res=[]
for value in values:
    if len(attributes)!=len(value):
        continue
    temp={}
    for index,each in enumerate(value):
        temp[attributes[index]]=each
    res.append(temp)
print(res)

# expected outout:
[{'name': 'jason', 'dob': '2000-01-01', 'gender': 'male'},
{'name': 'mike', 'dob': '1999-01-01', 'gender': 'male'},
{'name': 'nancy', 'dob': '2001-02-01', 'gender': 'female'}]
```

## 异常处理

错误和异常

> 异常，通常是指程序运行的过程中遇到了错误，终止并退出。
> 我们通常使用 try except语句去处理异常，这样程序就不会被终止，仍能继续执行。



[异常doc](https://docs.python.org/3/library/exceptions.html#bltin-exceptions)

```python
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except (ValueError, IndexError) as err:
    print('Error: {}'.format(err))
    print('continue')

或者：
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except ValueError as err:
    print('Error: {}'.format(err))
    print('continue')
except IndexError as err:
    print('Error: {}'.format(err))
    print('continue')
# Exception 是其他所有非系统异常的基类，能够匹配任意非系统异常。
except Exception as err:
    print('Other error: {}'.format(err))
# 在 except 后面省略异常类型，这表示与任意异常相匹配（包括系统异常等）
except:
    print('Other error')
```

### 自定义异常
```python
class MyInputError(Exception):
    """Exception raised when there're errors in input"""
    def __init__(self, value): # 自定义异常类型的初始化
        self.value = value
    def __str__(self): # 自定义异常类型的 string 表达形式
        return ("{} is invalid input".format(repr(self.value)))
try:
    raise MyInputError(1) # 抛出 MyInputError 这个异常
except MyInputError as err:
    print('error: {}'.format(err))
```

```python
e = 1
try:
    1/0
except ZeroDivisionError as e:
    pass

print(e) # NameError: name 'e' is not defined
```

查阅官方文档，会看到这么一句话`When an exception has been assigned using as target, it is cleared at the end of the except clause. `

如果你在异常处理的 except block 中，把异常赋予了一个变量，那么这个变量会在 except block 执行结束时被删除。相当于下面这样的表示：
```python
e = 1
try:
    1/0
except ZeroDivisionError as e:
    try:
        pass
    finally:
        del e
```

如果全局变量指向的对象是可变的，比如是列表、字典等等，你就可以在函数内部修改它了。
```python
x = [1]
def func():
    x.append(2)

func()
print(x) # [1, 2]

x = 1
def func():
    # 程序默认函数内部的 x 是局部变量，而你没有为其赋值就直接引用，显然是不可行。
    x += 1

func() # UnboundLocalError: local variable 'x' referenced before assignment
```

## 函数

> def 是可执行语句，这意味着函数直到被调用前，都是不存在的。
> 当程序调用函数时，def 语句才会创建一个新的函数对象，并赋予其名字。

> Python 是 dynamically typed 的，可以接受任何数据类型（整型，浮点，字符串等等）。
> 对函数参数来说，这一点同样适用。

```python
def name(param1, param2, ..., paramN):
    statements
    return/yield value # optional
```

```python
def find_largest_element(l):
    if not isinstance(l, list):
        print('input is not type of list')
        return
    if len(l) == 0:
        print('empty input')
        return
    largest_element = l[0]
    for item in l:
        if item > largest_element:
            largest_element = item
    print('largest element is: {}'.format(largest_element))

find_largest_element([8, 1,-3, 2, 0])
# 输出
# largest element is: 8
```

函数的嵌套能够保证内部函数的隐私。
> 内部函数只能被外部函数所调用和访问，不会暴露在全局作用域，因此，如果你的函数内部有一些隐私数据（比如数据库的用户、密码等），不想暴露在外，那你就可以使用函数的的嵌套，将其封装在内部函数中，只通过外部函数来访问。

```python
def connect_DB():
    def get_DB_configuration():
        ...
        return host, username, password
    conn = connector.connect(get_DB_configuration())
    return conn
```

函数内修改全局变量
```python
MIN_VALUE = 1
MAX_VALUE = 10
def validation_check(value):
    global MIN_VALUE
    ...
    MIN_VALUE += 1
    ...
# 调用
validation_check(5)
```

> global 关键字，并不表示重新创建了一个全局变量 MIN_VALUE，而是告诉Python 解释器，函数内部的变量 MIN_VALUE，就是之前定义的全局变量，并不是新的全局变量，也不是局部变量。
> 这样，程序就可以在函数内部访问全局变量，并修改它的值了。

对于嵌套函数来说，内部函数可以访问外部函数定义的变量，但是无法修改，若要修改，必须加上 nonlocal 这个关键字。

```python
def outer():
    x = "local"
def inner():
    nonlocal x # nonlocal 关键字表示这里的 x 就是外部函数 outer 定义的变量 x
    x = 'nonlocal'
    print("inner:", x)
    inner()
    print("outer:", x)

outer()
# 输出
inner: nonlocal
outer: nonlocal
```

### 闭包(closure)

> 和嵌套函数优点类似，函数开头需要做一些额外工作，而你又需要多次调用这个函数时，将那些额外工作的代码放在外部函数，就可以减少多次调用导致的不必要的开销，提高程序的运行效率。
> 闭包常常和装饰器（decorator）一起使用。

```python
def nth_power(exponent):
    def exponent_of(base):
        return base ** exponent
    return exponent_of # 返回值是 exponent_of 函数

square = nth_power(2) # 计算一个数的平方
cube = nth_power(3) # 计算一个数的立方

square
# 输出
<function __main__.nth_power.<locals>.exponent(base)>

cube
# 输出
<function __main__.nth_power.<locals>.exponent(base)>

print(square(2)) # 计算 2 的平方
print(cube(2)) # 计算 2 的立方
# 输出
4 # 2^2
8 # 2^3
```

[参考](https://zhuanlan.zhihu.com/p/26934085)

什么是闭包？闭包有什么用？为什么要用闭包？

首先，Python函数是第一类对象。闭包和函数紧密联系在一起。

介绍闭包前有必要先介绍一些背景知识，诸如嵌套函数、变量的作用域等概念。

作用域
> 作用域是程序运行时变量可被访问的范围，定义在函数内的变量是局部变量，局部变量的作用范围只能是函数内部范围内，它不能在函数外引用。

定义在模块最外层的变量是全局变量，它是全局范围内可见的，当然在函数里面也可以读取到全局变量的。例如：
```python
num = 10 # 全局作用域变量
def foo():
    print(num)  # 10
```

而在函数外部则不可以访问局部变量。例如：
```python
def foo():
    num = 10
print(num)  # NameError: name 'num' is not defined
```

嵌套函数
> 函数不仅可以定义在模块的最外层，还可以定义在另外一个函数的内部，像这种定义在函数里面的函数称之为嵌套函数（nested function）

例如：
```python
def print_msg():
    # print_msg 是外围函数
    msg = "zen of python"

    def printer():
        # printer是嵌套函数
        print(msg)
    printer()
# 输出 zen of python
print_msg()
```

对于嵌套函数，它可以访问到其外层作用域中声明的非局部（non-local）变量，比如代码示例中的变量 msg 可以被嵌套函数 printer 正常访问。

那么有没有一种可能即使脱离了函数本身的作用范围，局部变量还可以被访问得到呢？答案是闭包。

什么是闭包
> 函数身为第一类对象，它可以作为函数的返回值返回。

如下的例子：
```python
def print_msg():
    # print_msg 是外围函数
    msg = "zen of python"
    def printer():
        # printer 是嵌套函数
        print(msg)
    return printer

another = print_msg()
# 输出 zen of python
another()
```

这段代码和前面例子的效果完全一样，同样输出 "zen of python"。不同的地方在于内部函数 printer 直接作为返回值返回了。

一般情况下，函数中的局部变量仅在函数的执行期间可用，一旦 print_msg() 执行过后，我们会认为 msg变量将不再可用。

然而，在这里我们发现 print_msg 执行完之后，在调用 another 的时候 msg 变量的值正常输出了，这就是闭包的作用，闭包使得局部变量在函数外被访问成为可能。

看完这个例子，我们再来定义闭包，维基百科上的解释是:
> 在计算机科学中，闭包（Closure）是词法闭包（Lexical Closure）的简称，是引用了自由变量的函数。
> 这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。
> 所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。

这里的 another 就是一个闭包，闭包本质上是一个函数，它有两部分组成，printer 函数和变量 msg。闭包使得这些变量的值始终保存在内存中。

闭包，顾名思义，就是一个封闭的包裹，里面包裹着自由变量，就像在类里面定义的属性值一样，自由变量的可见范围随同包裹，哪里可以访问到这个包裹，哪里就可以访问到这个自由变量。

为什么要使用闭包
> 闭包避免了使用全局变量，此外，闭包允许将函数与其所操作的某些数据（环境）关连起来。
> 这一点与面向对象编程是非常类似的，在面对象编程中，对象允许我们将某些数据（对象的属性）与一个或者多个方法相关联。

一般来说，当对象中只有一个方法时，这时使用闭包是更好的选择。来看一个例子：
```python
def adder(x):
    def wrapper(y):
        return x + y
    return wrapper

adder5 = adder(5)
# 输出 15
adder5(10)
# 输出 11
adder5(6)
```
这比用类来实现更优雅，此外装饰器也是基于闭包的一中应用场景。

所有函数都有一个 __closure__属性，如果这个函数是一个闭包的话，那么它返回的是一个由 cell 对象 组成的元组对象。

cell 对象的cell_contents 属性就是闭包中的自由变量。

```shell
>>> adder.__closure__
>>> adder5.__closure__
(<cell at 0x103075910: int object at 0x7fd251604518>,)
>>> adder5.__closure__[0].cell_contents
5
```

这解释了为什么局部变量脱离函数之后，还可以在函数之外被访问的原因的，因为它存储在了闭包的cell_contents中了。

## 匿名函数(lambda)

格式：
`lambda argument1, argument2,... argumentN : expression`

lambda 是一个表达式（expression），并不是一个语句（statement）。

lambda 的主体是只有一行的简单表达式，并不能扩展成一个多行的代码块。

Python 之所以发明 lambda，就是为了让它和常规函数各司其职：lambda 专注于简单的任务，而常规函数则负责更复杂的多行逻辑。（[python之父的解释](https://www.artima.com/weblogs/viewpost.jsp?thread=147358)）

匿名函数通常的使用场景是：程序中需要使用一个函数完成一个简单的功能，并且该函数只调用一次。


在一些情况下，使用匿名函数 lambda，可以帮助我们大大简化代码的复杂度，提高代码的可读性。

```python
对一个列表中的所有元素做平方操作
squared = map(lambda x: x**2, [1, 2, 3, 4, 5])

def square(x):
    return x**2
squared = map(square, [1, 2, 3, 4, 5])
```

lambda 可以用在列表内部，而常规函数却不能
```python
[(lambda x: x*x)(x) for x in range(10)]
# 输出
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

lambda 可以被用作某些函数的参数，而常规函数 def 也不能
```python
l = [(1, 20), (3, 0), (9, 10), (2, -1)]
l.sort(key=lambda x: x[1]) # 按列表中元祖的第二个元素排序
print(l)
# 输出
[(2, -1), (3, 0), (9, 10), (1, 20)]
```

```python
# 对一个字典，根据值进行由高到底的排序
d = {'mike': 10, 'lucy': 2, 'ben': 30}
sorted(d.item(), key = lambda x: x[1], reverse = True)
```



Python 函数式编程
> Python 的函数式编程特性，与匿名函数lambda，有着密切的联系。
> 所谓函数式编程，是指代码中每一块都是不可变的（immutable），都由纯函数（pure function）的形式组成。
> 这里的纯函数，是指函数本身相互独立、互不影响，对于相同的输入，总会有相同的输出，没有任何副作用。

> 函数式编程的优点，主要在于其纯函数和不可变的特性使程序更加健壮，易于调试（debug）和测试；
> 缺点主要在于限制多，难写。当然，Python 不同于一些语言（比如Scala），它并不是一门函数式编程语言。
> 不过，Python 也提供了一些函数式编程的特性，值得我们了解和学习。

Python 主要提供了这么几个函数：map()、filter() 和 reduce()，通常结合匿名函数lambda 一起使用。


函数 map(function, iterable) 的第一个参数是函数对象，第二个参数是一个可以遍历的集合，它表示对iterable 的每一个元素，都运用 function 这个函数，最后返回一个新的可遍历的集合。
```python
l = [1, 2, 3, 4, 5]
new_list = map(lambda x: x * 2, l) # [2， 4， 6， 8， 10]
```

性能对比：
```shell
# lambda
python3 -mtimeit -s'xs=range(1000000)' 'map(lambda x: x*2, xs)'
2000000 loops, best of 5: 171 nsec per loop

# list comprehension
python3 -mtimeit -s'xs=range(1000000)' '[x * 2 for x in xs]'
5 loops, best of 5: 62.9 msec per loop

# 常规
python3 -mtimeit -s'xs=range(1000000)' 'l = []' 'for i in xs: l.append(i * 2)'
5 loops, best of 5: 92.7 msec per loop
```
可以看到，map() 是最快的。因为 map() 函数直接由 C 语言写的，运行时不需要通过Python 解释器间接调用，并且内部做了诸多优化，所以运行速度最快。

filter(function, iterable) 函数，它和 map 函数类似，function 同样表示一个函数对象。filter() 函数表示对 iterable 中的每个元素，都使用 function 判断，并返回True 或者 False，最后将返回 True 的元素组成一个新的可遍历的集合。
```python
l = [1, 2, 3, 4, 5]
new_list = filter(lambda x: x % 2 == 0, l) # [2, 4]
```

reduce(function, iterable) 函数，它通常用来对一个集合做一些累积操作。function 同样是一个函数对象，规定它有两个参数，表示对 iterable 中的每个元素以及上一次调用后的结果，运用 function 进行计算，所以最后返回的是一个单独的数值。
```python
from functools import reduce
# 计算某个列表元素的乘积
l = [1, 2, 3, 4, 5]
product = reduce(lambda x, y: x * y, l) # 1*2*3*4*5 = 120
```

类似的，filter() 和 reduce() 的功能，也可以用 for 循环或者 list comprehension来实现。

## 类（面向对象OOM）

类，一群有着相同属性和函数的对象的集合。

`OOP (object oriented programming)`

```python
def __init__(self, title, author, context):
    # 表示构造函数，意即一个对象生成时会被自动调用的函数。
    pass
```

如果一个属性以 __ （注意，此处有两个 _） 开头，我们就默认这个属性是私有属性。

类函数的第一个参数一般为 cls，表示必须传一个类进来。类函数最常用的功能是实现不同的 init 构造函数.

类函数需要装饰器 @classmethod 来声明。

每个类都有构造函数，继承类在生成对象的时候，是不会自动调用父类的构造函数的，因此你必须在 init() 函数中显式调用父类的构造函数。它们的执行顺序是 子类的构造函数 -> 父类的构造函数。

问题：多重继承
> 多重继承，是基于mro进行查找，使用的是一种C3的算法。
> 可以通过 xxx.__mro__ 查看继承的顺序

多重继承有两种初始化方法：
> 第一种，super(BOWInvertedIndexEngineWithCache, self).__init__()直接初始化该类的第一个父类，不过使用这种方法时，要求继承链的最顶层父类必须要继承object；
> 第二种，对于多重继承，如果有多个构造函数需要调用， 我们就必须用传统的方法LRUCache.__init__(self) 。



菱形继承问题？
> 菱形继承潜在的问题：一个基类的初始化函数可能被调用两次。
> 在一般的工程中，这显然不是我们所希望的。正确的做法应该是使用 super 来召唤父类的构造函数，而且python 使用一种叫做方法解析顺序的算法（具体实现算法叫做 C3），来保证一个类只会被初始化一次。

私有变量能被继承吗？如果不能，你想继承应该怎么去做呢？
> Python是没有严格的private变量的。子类确实无法直接访问父类"self.__var"变量。
> 子类不能继承父类私有属性，只可透过self._Parent__varname去读取该私有属性的值。
> 或在父类创建方法返回私有属性的值，然后子类调用父类方法去取得该私有属性的值。



### 抽象函数和抽象类

抽象类是一种特殊的类，它生下来就是作为父类存在的，一旦对象化就会报错。

同样，抽象函数定义在抽象类之中，子类必须重写该函数才能使用。

相应的抽象函数，则是使用装饰器@abstractmethod 来表示。

## 模块化

`from your_file import function_name, class_name`的方式调用

在 Facebook 的编程规范中，除了一些极其特殊的情况，import 必须位于程序的最前端。

还需要在模块所在的文件夹新建一个 __init__.py，内容可以为空，也可以用来表述包对外暴露的模块接口。

不过，事实上，这是 Python 2 的规范。在 Python 3 规范中，__init__.py 并不是必须的。

通常，一个 Python 文件在运行的时候，都会有一个运行时位置，最开始时即为这个文件所在的文件夹。

当然，这个运行路径以后可以被改变。

运行 sys.path.append("..") ，则可以改变当前 Python 解释器的位置。

不过，一般而言我并不推荐，固定一个确定路径对大型工程来说是非常必要的。

首先，你会发现，相对位置是一种很不好的选择。

因为代码可能会迁移，相对位置会使得重构既不雅观，也易出错。

因此，在大型工程中尽可能使用绝对位置是第一要义。

对于一个独立的项目，所有的模块的追寻方式，最好从项目的根目录开始追溯，这叫做相对的绝对路径。

如果你有兴趣，可以参考这篇论文：

https://cacm.acm.org/magazines/2016/7/204032-why-google-stores-billions-of-lines-of-code-in-a-single-repository/fulltext

**以项目的根目录作为最基本的目录，所有的模块调用，都要通过根目录一层层向下索引的方式来 import**

实际上，Python 解释器在遇到 import 的时候，它会在一个特定的列表中寻找模块。

这个特定的列表，可以用下面的方式拿到：
```python
import sys
print(sys.path)

########## 输出 ##########
# ['', '/usr/lib/python36.zip', '/usr/lib/python3.6', '/usr/lib/python3.6/lib-dynload' ...]
```

请注意，它的第一项为空。

其实，Pycharm 做的一件事，就是将第一项设置为项目根目录的绝对地址。

这样，每次你无论怎么运行 main.py，import 函数在执行的时候，都会去项目根目录中找相应的包。

有2种方式：

1、
```python
import sys
sys.path[0] = '/home/ubuntu/workspace/your_projects'
```

2、
是修改 PYTHONHOME。
Python 的 Virtual Environment（虚拟运行环境）。

在一个 Virtual Environment 里，你能找到一个文件叫activate，在这个文件的末尾，填上下面的内容：
`export PYTHONPATH="/home/ubuntu/workspace/your_projects"`

Python 可以通过 Virtualenv 工具，非常方便地创建一个全新的 Python 运行环境。

这样，每次你通过 activate 激活这个运行时环境的时候，它就会自动将项目的根目录添加到搜索路径中去。

神奇的 `if __name__ == '__main__'`

Python 是脚本语言，和 C++、Java 最大的不同在于，不需要显式提供 main() 函数入口。

import 在导入文件的时候，会自动把所有暴露在外面的代码全都执行一遍。

因此，如果你要把一个东西封装成模块，又想让它可以执行的话，你必须将要执行的代码放在 if__name__ == '__main__'下面。


为什么呢？

其实，__name__ 作为 Python 的魔术内置参数，本质上是模块对象的一个属性。

我们使用 import 语句时，__name__ 就会被赋值为该模块的名字，自然就不等于__main__了。




# 进阶

## 对象的比较、拷贝

`'==' VS 'is'`

等于（==）和 is 是 Python 中对象比较常用的两种方式。简单来说，'=='操作符比较对象之间的值是否相等。

而'is'操作符比较的是对象的身份标识是否相等，即它们是否是同一个对象，是否指向同一个内存地址。

在 Python 中，每个对象的身份标识，都能通过函数 id(object) 获得。因此，'is'操作符，相当于比较对象之间的 ID 是否相等。

```python
a = 10
b = 10
 
a == b
True
 
id(a)
4427562448
id(b)
4427562448
 
a is b
True
```

这里，首先 Python 会为 10 这个值开辟一块内存，然后变量 a 和 b 同时指向这块内存区域，即 a 和 b 都是指向 10 这个变量，因此 a 和 b 的值相等，id 也相等，a == b和a is b都返回 True。

不过，需要注意，对于整型数字来说，以上a is b为 True 的结论，只适用于 -5 到 256 范围内的数字。

```python
a = 257
b = 257
 
a == b
True

id(a)
4473417552 
id(b)
4473417584
 
a is b
False
```

事实上，出于对性能优化的考虑，Python 内部会对 -5 到 256 的整型维持一个数组，起到一个缓存的作用。

这样，每次你试图创建一个 -5 到 256 范围内的整型数字时，Python 都会从这个数组中返回相对应的引用，而不是重新开辟一块新的内存空间。

但是，如果整型数字超过了这个范围，比如上述例子中的 257，Python 则会为两个 257 开辟两块内存区域，因此 a 和 b 的 ID 不一样，a is b就会返回 False 了。

通常来说，在实际工作中，当我们比较变量时，使用'=='的次数会比'is'多得多，因为我们一般更关心两个变量的值，而不是它们内部的存储地址。

但是，当我们比较一个变量与一个单例（singleton）时，通常会使用'is'。一个典型的例子，就是检查一个变量是否为 None：

```python
if a is None:
    pass 
if a is not None:
    pass
```

注意，比较操作符'is'的速度效率，通常要优于'=='。

因为'is'操作符不能被重载，这样，Python 就不需要去寻找，程序中是否有其他地方重载了比较操作符，并去调用。执行比较操作符'is'，就仅仅是比较两个变量的 ID 而已。

但是'=='操作符却不同，执行a == b相当于是去执行`a.__eq__(b)`，而 Python 大部分的数据类型都会去重载__eq__这个函数，其内部的处理通常会复杂一些。

比如，对于列表，__eq__函数会去遍历列表中的元素，比较它们的顺序和值是否相等。

不过，对于不可变（immutable）的变量，如果我们之前用'=='或者'is'比较过，结果是不是就一直不变了呢？
答案自然是否定的。

```python
t1 = (1, 2, [3, 4])
t2 = (1, 2, [3, 4])
t1 == t2
True
 
t1[-1].append(5)
t1 == t2
False
```

我们知道元组是不可变的，但元组可以嵌套，它里面的元素可以是列表类型，列表是可变的，所以如果我们修改了元组中的某个可变元素，那么元组本身也就改变了，之前用'is'或者'=='操作符取得的结果，可能就不适用了。

浅拷贝（shallow copy）和深度拷贝（deep copy）

常见的浅拷贝的方法，是使用数据类型本身的构造器
```python
l1 = [1, 2, 3]
l2 = list(l1)
l2
[1, 2, 3]

l1 == l2
True
 
l1 is l2
False
 
s1 = set([1, 2, 3])
s2 = set(s1)
s2
{1, 2, 3}

s1 == s2
True

s1 is s2
False
```

这里，l2 就是 l1 的浅拷贝，s2 是 s1 的浅拷贝。当然，对于可变的序列，我们还可以通过切片操作符':'完成浅拷贝:
```python
l1 = [1, 2, 3]
l2 = l1[:]
 
l1 == l2
True
 
l1 is l2
False
```

当然，Python 中也提供了相对应的函数 copy.copy()，适用于任何数据类型：
```python
import copy
l1 = [1, 2, 3]
l2 = copy.copy(l1)
```

不过，需要注意的是，对于元组，使用 tuple() 或者切片操作符':'不会创建一份浅拷贝，相反，它会返回一个指向相同元组的引用：
```python
t1 = (1, 2, 3)
t2 = tuple(t1)
 
t1 == t2
True
 
t1 is t2
True
```
这里，元组 (1, 2, 3) 只被创建一次，t1 和 t2 同时指向这个元组。

**浅拷贝，是指重新分配一块内存，创建一个新的对象，里面的元素是原对象中子对象的引用。**

因此，如果原对象中的元素不可变，那倒无所谓；但如果元素可变，浅拷贝通常会带来一些副作用，尤其需要注意。

```python
l1 = [[1, 2], (30, 40)]
l2 = list(l1)
l1.append(100)
l1[0].append(3)
 
l1
[[1, 2, 3], (30, 40), 100]
 
l2
[[1, 2, 3], (30, 40)]
 
l1[1] += (50, 60)
l1
[[1, 2, 3], (30, 40, 50, 60), 100]
 
l2
[[1, 2, 3], (30, 40)]
```

首先初始化了一个列表 l1，里面的元素是一个列表和一个元组；然后对 l1 执行浅拷贝，赋予 l2。因为浅拷贝里的元素是对原对象元素的引用，因此 l2 中的元素和 l1 指向同一个列表和元组对象。

接着往下看。l1.append(100)，表示对 l1 的列表新增元素 100。这个操作不会对 l2 产生任何影响，因为 l2 和 l1 作为整体是两个不同的对象，并不共享内存地址。操作过后 l2 不变，l1 会发生改变。

l1[0].append(3)，这里表示对 l1 中的第一个列表新增元素 3。因为 l2 是 l1 的浅拷贝，l2 中的第一个元素和 l1 中的第一个元素，共同指向同一个列表，因此 l2 中的第一个列表也会相对应的新增元素 3。操作后 l1 和 l2 都会改变。

最后是l1[1] += (50, 60)，因为元组是不可变的，这里表示对 l1 中的第二个元组拼接，然后重新创建了一个新元组作为 l1 中的第二个元素，而 l2 中没有引用新元组，因此 l2 并不受影响。操作后 l2 不变，l1 发生改变。

**深度拷贝，是指重新分配一块内存，创建一个新的对象，并且将原对象中的元素，以递归的方式，通过创建新的子对象拷贝到新对象中。因此，新对象和原对象没有任何关联。**

Python 中以 copy.deepcopy() 来实现对象的深度拷贝。比如上述例子写成下面的形式，就是深度拷贝：
```python
import copy
l1 = [[1, 2], (30, 40)]
l2 = copy.deepcopy(l1)
l1.append(100)
l1[0].append(3)
 
l1
[[1, 2, 3], (30, 40), 100]
 
l2 
[[1, 2], (30, 40)]
```

可以看到，无论 l1 如何变化，l2 都不变。因为此时的 l1 和 l2 完全独立，没有任何联系。

不过，深度拷贝也不是完美的，往往也会带来一系列问题。如果被拷贝对象中存在指向自身的引用，那么程序很容易陷入无限循环：
```python
import copy
x = [1]
x.append(x)

x
[1, [...]]
 
y = copy.deepcopy(x)
y
[1, [...]]
```
上面这个例子，列表 x 中有指向自身的引用，因此 x 是一个无限嵌套的列表。但是我们发现深度拷贝 x 到 y 后，程序并没有出现 stack overflow 的现象。这是为什么呢？

其实，这是因为深度拷贝函数 deepcopy 中会维护一个字典，记录已经拷贝的对象与其 ID。

拷贝过程中，如果字典里已经存储了将要拷贝的对象，则会从字典直接返回，我们来看相对应的源码就能明白：
```python
def deepcopy(x, memo=None, _nil=[]):
    """Deep copy operation on arbitrary Python objects.
    	
	See the module's __doc__ string for more info.
	"""
	
    if memo is None:
        memo = {}
    d = id(x) # 查询被拷贝对象 x 的 id
	y = memo.get(d, _nil) # 查询字典里是否已经存储了该对象
	if y is not _nil:
	    return y # 如果字典里已经存储了将要拷贝的对象，则直接返回
        ...    
```

总结：

1、比较操作符'=='表示比较对象间的值是否相等，而'is'表示比较对象的标识是否相等，即它们是否指向同一个内存地址。

2、比较操作符'is'效率优于'=='，因为'is'操作符无法被重载，执行'is'操作只是简单的获取对象的 ID，并进行比较；而'=='操作符则会递归地遍历对象的所有值，并逐一比较。

3、浅拷贝中的元素，是原对象中子对象的引用，因此，如果原对象中的元素是可变的，改变其也会影响拷贝后的对象，存在一定的副作用。

4、深度拷贝则会递归地拷贝原对象中的每一个子对象，因此拷贝后的对象和原对象互不相关。另外，深度拷贝中会维护一个字典，记录已经拷贝的对象及其 ID，来提高效率并防止无限递归的发生。


思考题
```python
import copy
x = [1]
x.append(x)
 
y = copy.deepcopy(x)
 
# 以下命令的输出是？
x == y
```
运行代码时报错。原因是进行"=="时需要递归对比每一个元素，该循环是一个无限循环（无限个元素）。然而python专门设置的一种机制用来防止无限递归造成Python溢出崩溃，换句话说不能遍历比较到所有值，所以报错“RecursionError: maximum recursion depth exceeded in comparison”

## Python里参数是如何传递的

**值传递，通常就是拷贝参数的值，然后传递给函数里的新变量。这样，原变量和新变量之间互相独立，互不影响。**

**引用传递，通常是指把参数的引用传给新的变量，这样，原变量和新变量就会指向同一块内存地址。如果改变了其中任何一个变量的值，那么另外一个变量也会相应地随之改变。**

基本的变量赋值例子：
```python
a = 1
b = a
a = a + 1
print(a) # 2
print(b) # 1
```

```python
l1 = [1, 2, 3]
l2 = l1
l1.append(4)

l1
[1, 2, 3, 4]
l2
[1, 2, 3, 4]
```

```python
l = [1, 2, 3]
del l
```
Python 里的变量可以被删除，但是对象无法被删除。

`del l` 删除了 l 这个变量，从此以后你无法访问 l，但是对象 [1, 2, 3] 仍然存在。

Python 程序运行时，其自带的垃圾回收系统会跟踪每个对象的引用。如果 [1, 2, 3] 除了 l 外，还在其他地方被引用，那就不会被回收，反之则会被回收。

总结：

1、变量的赋值，只是表示让变量指向了某个对象，并不表示拷贝对象给变量；而一个对象，可以被多个变量所指向。

2、可变对象（列表，字典，集合等等）的改变，会影响所有指向该对象的变量。

3、对于不可变对象（字符串，整型，元祖等等），所有指向该对象的变量的值总是一样的，也不会改变。但是通过某些操作（+= 等等）更新不可变对象的值时，会返回一个新的对象。

4、变量可以被删除，但是对象无法被删除。

Remember that arguments are passed by assignment in Python. Since assignment just creates references to objects, there’s no alias between an argument name in the caller and callee, and so no call-by-reference per Se.

准确地说，**Python 的参数传递是赋值传递** （pass by assignment），或者叫作对象的引用传递（pass by object reference）。Python 里所有的数据类型都是对象，所以参数传递时，只是让新变量与原变量指向相同的对象而已，并不存在值传递或是引用传递一说。

```python
def my_func1(b):
	b = 2

a = 1
my_func1(a)
a
1

def my_func2(b):
	b = 2
	return b

a = 1
a = my_func2(a)
a
2

# 不过，当可变对象当作参数传入函数里的时候，改变可变对象的值，就会影响所有指向它的变量
def my_func3(l2):
	l2.append(4)

l1 = [1, 2, 3]
my_func3(l1)
l1
[1, 2, 3, 4]

def my_func4(l2):
	l2 = l2 + [4]

l1 = [1, 2, 3]
my_func4(l1)
l1
[1, 2, 3]

def my_func5(l2):
	l2 = l2 + [4]
	return l2

l1 = [1, 2, 3]
l1 = my_func5(l1)
l1
[1, 2, 3, 4]
```

总结：

**和其他语言不同的是，Python 中参数的传递既不是值传递，也不是引用传递，而是赋值传递，或者是叫对象的引用传递。**

**需要注意的是，这里的赋值或对象的引用传递，不是指向一个具体的内存地址，而是指向一个具体的对象。**

如果对象是可变的，当其改变时，所有指向这个对象的变量都会改变。

如果对象不可变，简单的赋值只能改变其中一个变量的值，其余变量则不受影响。

清楚了这一点，如果你想通过一个函数来改变某个变量的值，通常有两种方法。

一种是直接将可变数据类型（比如列表，字典，集合）当作参数传入，直接在其上修改；

第二种则是创建一个新变量，来保存修改后的值，然后将其返回给原变量。

在实际工作中，我们更倾向于使用后者，因为其表达清晰明了，不易出错。

## 装饰器

**所谓的装饰器，其实就是通过装饰器函数，来修改原函数的一些功能，使得原函数不需要修改。**
> Decorators is to modify the behavior of the function through a wrapper so we don’t have to actually modify the function.

而实际工作中，装饰器通常运用在身份认证、日志记录、输入合理性检查以及缓存等多个领域中。合理使用装饰器，往往能极大地提高程序的可读性以及运行效率。

PS：
> python的装饰器的应用场景有点像AOP的应用场景，把一些常用的业务逻辑分离，提高程序可重用性，降低耦合度，提高开发效率。



- 日志记录
- 方法耗时
- 参数校验
- 身份认证
- 缓存

LRU cache，在 Python 中的表示形式是@lru_cache。@lru_cache会缓存进程中的函数参数和结果，当缓存满了以后，会删除 least recenly used 的数据。


### 函数装饰器

**函数 -> 装饰器**



在 Python 中，函数是一等公民（first-class citizen），函数也是对象。我们可以把函数赋予变量。

可以把函数当作参数，传入另一个函数中。

可以在函数里定义函数，也就是函数的嵌套。

函数的返回值也可以是函数对象（闭包）


```python
def my_decorator(func):
    def wrapper():
        print('wrapper of decorator')
        func()
    return wrapper
 
def greet():
    print('hello world')
 
greet = my_decorator(greet)
greet()
 
# 输出
wrapper of decorator
hello world

# 更简单、更优雅的表示：
def my_decorator(func):
    def wrapper():
        print('wrapper of decorator')
        func()
    return wrapper

# @，我们称之为语法糖，@my_decorator就相当于前面的greet=my_decorator(greet)语句，只不过更加简洁。
@my_decorator
def greet():
    print('hello world')

greet()
```

### 类装饰器

类装饰器主要依赖于函数__call_()，每当你调用一个类的示例时，函数__call__()就会被执行一次。

### 装饰器的嵌套

执行顺序从里到外

```python
@decorator1
@decorator2
@decorator3
def func():
    pass

或者
decorator1(decorator2(decorator3(func)))
```

## metaclass

事实上，meta-class 的 meta 这个词根，起源于希腊语词汇 meta，包含下面两种意思：

- “Beyond”，例如技术词汇 metadata，意思是描述数据的超越数据；
- “Change”，例如技术词汇 metamorphosis，意思是改变的形态。

metaclass，一如其名，实际上同时包含了“超越类”和“变形类”的含义，完全不是“基本类”的意思。所以，要深入理解 metaclass，我们就要围绕它的超越变形特性。

接下来，我将为你展开 metaclass 的超越变形能力，讲清楚 metaclass 究竟有什么用？怎么应用？Python 语言设计层面是如何实现 metaclass 的 ？以及使用 metaclass 的风险。

metaclass 的超越变形特性有什么用？

YAML是一个家喻户晓的 Python 工具，可以方便地序列化 / 逆序列化结构数据。

YAMLObject 的一个超越变形能力，就是它的任意子类支持序列化和反序列化（serialization & deserialization）。比如说下面这段代码：

```python
import yaml

class Monster(yaml.YAMLObject):
    yaml_tag = u'!Monster'

    def __init__(self, name, hp, ac, attacks):
        self.name = name
        self.hp = hp
        self.ac = ac
        self.attacks = attacks

    def __repr__(self):
        return "%s(name=%r, hp=%r, ac=%r, attacks=%r)" % (
            self.__class__.__name__, self.name, self.hp, self.ac,
            self.attacks)


yaml.load("""
--- !Monster
name: Cave spider
hp: [2,6]    # 2d6
ac: 16
attacks: [BITE, HURT]
""", Loader=yaml.FullLoader)

Monster(name='Cave spider', hp=[2, 6], ac=16, attacks=['BITE', 'HURT'])

print(yaml.dump(Monster(
    name='Cave lizard', hp=[3, 6], ac=16, attacks=['BITE', 'HURT'])))
# 输出：
# !Monster
# ac: 16
# attacks:
# - BITE
# - HURT
# hp:
# - 3
# - 6
# name: Cave lizard
```

调用统一的 yaml.load()，就能把任意一个 yaml 序列载入成一个 Python Object；而调用统一的 yaml.dump()，就能把一个 YAMLObject 子类序列化。对于 load() 和 dump() 的使用者来说，他们完全不需要提前知道任何类型信息，这让超动态配置编程成了可能。

对于 YAML 的使用者，这一点也很方便，你只要简单地继承 yaml.YAMLObject，就能让你的 Python Object 具有序列化和逆序列化能力。是不是相比普通 Python 类，有一点“变态”，有一点“超越”？

metaclass 能够拦截 Python 类的定义。它是怎么做到的？

要理解 metaclass 的底层原理，你需要深入理解 Python 类型模型。

第一，所有的 Python 的用户定义类，都是 type 这个类的实例。

事实上，类本身不过是一个名为 type 类的实例。在 Python 的类型世界里，type 这个类就是造物的上帝。

```python
class MyClass:
  pass
 
instance = MyClass()
 
type(instance)
# 输出
<class '__main__.C'>
 
type(MyClass)
# 输出
<class 'type'>
```

instance 是 MyClass 的实例，而 MyClass 不过是“上帝”type 的实例。

第二，用户自定义类，只不过是 type 类的__call__运算符重载。

当我们定义一个类的语句结束时，真正发生的情况，是 Python 调用 type 的__call__运算符。简单来说，当你定义一个类时，写成下面这样时：
```python
class MyClass:
  data = 1
```
Python 真正执行的是下面这段代码：
```python
class = type(classname, superclasses, attributedict)
```

这里等号右边的type(classname, superclasses, attributedict)，就是 type 的__call__运算符重载，它会进一步调用：
```python
type.__new__(typeclass, classname, superclasses, attributedict)
type.__init__(class, classname, superclasses, attributedict)
```

```python
class MyClass:
  data = 1
  
instance = MyClass()
MyClass, instance
# 输出
(__main__.MyClass, <__main__.MyClass instance at 0x7fe4f0b00ab8>)
instance.data
# 输出
1
 
MyClass = type('MyClass', (), {'data': 1})
instance = MyClass()
MyClass, instance
# 输出
(__main__.MyClass, <__main__.MyClass at 0x7fe4f0aea5d0>)
 
instance.data
# 输出
1
```
由此可见，正常的 MyClass 定义，和你手工去调用 type 运算符的结果是完全一样的。

第三，metaclass 是 type 的子类，通过替换 type 的__call__运算符重载机制，“超越变形”正常的类。

正是 Python 的类创建机制，给了 metaclass 大展身手的机会。

一旦你把一个类型 MyClass 的 metaclass 设置成 MyMeta，MyClass 就不再由原生的 type 创建，而是会调用 MyMeta 的__call__运算符重载。
```python
class = type(classname, superclasses, attributedict) 
# 变为了
class = MyMeta(classname, superclasses, attributedict)
```

不过，凡事有利必有弊，尤其是 metaclass 这样“逆天”的存在。正如你所看到的那样，metaclass 会"扭曲变形"正常的 Python 类型模型。所以，如果使用不慎，对于整个代码库造成的风险是不可估量的。

换句话说，metaclass 仅仅是给小部分 Python 开发者，在开发框架层面的 Python 库时使用的。而在应用层，metaclass 往往不是很好的选择。

也正因为这样，据我所知，在很多硅谷一线大厂，使用 Python metaclass 需要特例特批。

metaclass 是 Python 黑魔法级别的语言特性。天堂和地狱只有一步之遥，你使用好 metaclass，可以实现像 YAML 那样神奇的特性；而使用不好，可能就会打开潘多拉魔盒了。

对初学者的科普和警告：不要轻易尝试 mateclass。

## 深入理解迭代器和生成器

容器、可迭代对象和迭代器

在 Python 中一切皆对象，对象的抽象就是类，而对象的集合就是容器。

列表（list: [0, 1, 2]），元组（tuple: (0, 1, 2)），字典（dict: {0:0, 1:1, 2:2}），集合（set: set([0, 1, 2])）都是容器。

对于容器，你可以很直观地想象成多个元素在一起的单元；而不同容器的区别，正是在于内部数据结构的实现方法。然后，你就可以针对不同场景，选择不同时间和空间复杂度的容器。

所有的容器都是可迭代的（iterable）。

严谨地说，迭代器（iterator）提供了一个 next 的方法。调用这个方法后，你要么得到这个容器的下一个对象，要么得到一个 StopIteration 的错误（苹果卖完了）。你不需要像列表一样指定元素的索引，因为字典和集合这样的容器并没有索引一说。比如，字典采用哈希表实现，那么你就只需要知道，next 函数可以不重复不遗漏地一个一个拿到所有元素即可。

而可迭代对象，通过 iter() 函数返回一个迭代器，再通过 next() 函数就可以实现遍历。for in 语句将这个过程隐式化，所以，你只需要知道它大概做了什么就行了。

怎么判断一个对象是否可迭代。当然，这还有另一种做法，是 isinstance(obj, Iterable)。
```python
def is_iterable(param):
    try: 
        iter(param) 
        return True
    except TypeError:
        return False
 
params = [
    1234,
    '1234',
    [1, 2, 3, 4],
    set([1, 2, 3, 4]),
    {1:1, 2:2, 3:3, 4:4},
    (1, 2, 3, 4)
]
    
for param in params:
    print('{} is iterable? {}'.format(param, is_iterable(param)))
 
########## 输出 ##########
1234 is iterable? False
1234 is iterable? True
[1, 2, 3, 4] is iterable? True
{1, 2, 3, 4} is iterable? True
{1: 1, 2: 2, 3: 3, 4: 4} is iterable? True
(1, 2, 3, 4) is iterable? True
```

**生成器是懒人版本的迭代器。**

```python
import os
import psutil


# 显示当前 python 程序占用的内存大小
def show_memory_info(hint):
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))


def test_iterator():
    show_memory_info('initing iterator')
    list_1 = [i for i in range(100000000)]
    show_memory_info('after iterator initiated')
    print(sum(list_1))
    show_memory_info('after sum called')


def test_generator():
    show_memory_info('initing generator')
    list_2 = (i for i in range(100000000))
    show_memory_info('after generator initiated')
    print(sum(list_2))
    show_memory_info('after sum called')

test_iterator()

test_generator()

# initing iterator memory used: 6.6796875 MB
# after iterator initiated memory used: 3448.6953125 MB
# 4999999950000000
# after sum called memory used: 3877.38671875 MB
# initing generator memory used: 6.72265625 MB
# after generator initiated memory used: 6.72265625 MB
# 4999999950000000
# after sum called memory used: 6.7265625 MB
```

声明一个迭代器很简单，[i for i in range(100000000)]就可以生成一个包含一亿元素的列表。每个元素在生成后都会保存到内存中，你通过代码可以看到，它们占用了巨量的内存，内存不够的话就会出现 OOM 错误。

不过，我们并不需要在内存中同时保存这么多东西，比如对元素求和，我们只需要知道每个元素在相加的那一刻是多少就行了，用完就可以扔掉了。

于是，生成器的概念应运而生，在你调用 next() 函数的时候，才会生成下一个变量。生成器在 Python 的写法是用小括号括起来，(i for i in range(100000000))，即初始化了一个生成器。

这样一来，你可以清晰地看到，生成器并不会像迭代器一样占用大量内存，只有在被使用的时候才会调用。而且生成器在初始化的时候，并不需要运行一次生成操作，相比于 test_iterator() ，test_generator() 函数节省了一次生成一亿个元素的过程，因此耗时明显比迭代器短。

```python
'''
数学中有一个恒等式，(1 + 2 + 3 + ... + n)^2 = 1^3 + 2^3 + 3^3 + ... + n^3
'''

def generator(k):
    i = 1
    while True:
        yield i ** k
        i += 1


gen_1 = generator(1)
gen_3 = generator(3)
print(gen_1)
print(gen_3)

def get_sum(n):
    sum_1, sum_3 = 0, 0
    for i in range(n):
        next_1 = next(gen_1)
        next_3 = next(gen_3)
        print('next_1 = {}, next_3 = {}'.format(next_1, next_3))
        sum_1 += next_1
        sum_3 += next_3
    print(sum_1 * sum_1, sum_3)


get_sum(8)

########## 输出 ##########
# <generator object generator at 0x000001938CDAD2E0>
# <generator object generator at 0x000001938CDAD200>
# next_1 = 1, next_3 = 1
# next_1 = 2, next_3 = 8
# next_1 = 3, next_3 = 27
# next_1 = 4, next_3 = 64
# next_1 = 5, next_3 = 125
# next_1 = 6, next_3 = 216
# next_1 = 7, next_3 = 343
# next_1 = 8, next_3 = 512
# 1296 1296
```
这段代码中，你首先注意一下 generator() 这个函数，它返回了一个生成器。

接下来的 yield 是魔术的关键。对于初学者来说，你可以理解为，函数运行到这一行的时候，程序会从这里暂停，然后跳出，不过跳到哪里呢？答案是 next() 函数。那么 i ** k 是干什么的呢？它其实成了 next() 函数的返回值。

这样，每次 next(gen) 函数被调用的时候，暂停的程序就又复活了，从 yield 这里向下继续执行；同时注意，局部变量 i 并没有被清除掉，而是会继续累加。我们可以看到 next_1 从 1 变到 8，next_3 从 1 变到 512。

聪明的你应该注意到了，这个生成器居然可以一直进行下去！没错，事实上，迭代器是一个有限集合，生成器则可以成为一个无限集。我只管调用 next()，生成器根据运算会自动生成新的元素，然后返回给你，非常便捷。

```python
# 问题：给定一个 list 和一个指定数字，求这个数字在 list 中的位置
def index_normal(L, target):
    result = []
    for i, num in enumerate(L):
        if num == target:
            result.append(i)
    return result
 
print(index_normal([1, 6, 2, 4, 5, 2, 8, 6, 3, 2], 2))
 
########## 输出 ##########
[2, 5, 9]

def index_generator(L, target):
    for i, num in enumerate(L):
        if num == target:
            yield i

# index_generator 会返回一个 Generator 对象，需要使用 list 转换为列表后，才能用 print 输出。 
print(list(index_generator([1, 6, 2, 4, 5, 2, 8, 6, 3, 2], 2)))

########## 输出 ##########
[2, 5, 9]
```

```python
'''
问题：给定两个序列，判定第一个是不是第二个的子序列。
（LeetCode 链接如下：https://leetcode.com/problems/is-subsequence/ ）
'''


def is_subsequence(a, b):
    b = iter(b)
    return all(i in b for i in a)

# print(is_subsequence([1, 3, 5], [1, 2, 3, 4, 5]))
# print(is_subsequence([1, 4, 3], [1, 2, 3, 4, 5]))
########## 输出 ##########
# True
# False

def is_subsequence1(a, b):
    b = iter(b)
    print(b)

    gen = (i for i in a)
    print(gen)

    for i in gen:
        print(i)

    gen = ((i in b) for i in a)
    print(gen)

    for i in gen:
        print("," + str(i))

    # 生成器只能遍历一次，继续调用 next() 会 raise StopIteration。只有复位生成器才能重新进行遍历。
    for i in gen:
        print(",," + str(i))
    # return all(((i in b) for i in a))
    return all(gen)


print(is_subsequence1([1, 3, 5], [1, 2, 3, 4, 5]))
print(is_subsequence1([1, 4, 3], [1, 2, 3, 4, 5]))

########## 输出 ##########
```

```python
(i in b)，大致等价于下面这段代码：
while True:
    val = next(b)
    if val == i:
        yield True
```

```python
b = (i for i in range(5))
 
print(2 in b)
print(4 in b)
print(3 in b)
########## 输出 ##########
True
True
False
```

all() 函数用来判断一个迭代器的元素是否全部为 True，如果是则返回 True，否则就返回 False.

四种不同的对象，分别是容器、可迭代对象、迭代器和生成器。

容器是可迭代对象，可迭代对象调用 iter() 函数，可以得到一个迭代器。

迭代器可以通过 next() 函数来得到下一个元素，从而支持遍历。

生成器是一种特殊的迭代器（注意这个逻辑关系反之不成立）。使用生成器，你可以写出来更加清晰的代码；合理使用生成器，可以降低内存占用、优化程序结构、提高程序速度。

生成器在 Python 2 的版本上，是协程的一种重要实现方式；而 Python 3.5 引入 async await 语法糖后，生成器实现协程的方式就已经落后了。

## 协程

使用生成器，是 Python 2 开头的时代实现协程的老方法了，Python 3.7 提供了新的基于 asyncio 和 async / await 的方法。

```python
import time
 
def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    time.sleep(sleep_time)
    print('OK {}'.format(url))
 
def main(urls):
    for url in urls:
        crawl_page(url)
 
%time main(['url_1', 'url_2', 'url_3', 'url_4'])
 
########## 输出 ##########
crawling url_1
OK url_1
crawling url_2
OK url_2
crawling url_3
OK url_3
crawling url_4
OK url_4
Wall time: 10 s
```

协程怎么写：
```python
import asyncio
 
async def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    await asyncio.sleep(sleep_time)
    print('OK {}'.format(url))
 
async def main(urls):
    for url in urls:
        await crawl_page(url)
 
%time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4']))
 
########## 输出 ##########
crawling url_1
OK url_1
crawling url_2
OK url_2
crawling url_3
OK url_3
crawling url_4
OK url_4
Wall time: 10 s
```

**await 是同步调用**

async 修饰词声明异步函数，于是，这里的 crawl_page 和 main 都变成了异步函数。而调用异步函数，我们便可得到一个协程对象（coroutine object）。

举个例子，如果你 print(crawl_page(''))，便会输出<coroutine object crawl_page at 0x000002BEDF141148>，提示你这是一个 Python 的协程对象，而并不会真正执行这个函数。

再来说说协程的执行。执行协程有多种方法，常用的三种:

首先，我们可以通过 await 来调用。await 执行的效果，和 Python 正常执行是一样的，也就是说程序会阻塞在这里，进入被调用的协程函数，执行完毕返回后再继续，而这也是 await 的字面意思。代码中 await asyncio.sleep(sleep_time) 会在这里休息若干秒，await crawl_page(url) 则会执行 crawl_page() 函数。

其次，我们可以通过 asyncio.create_task() 来创建任务。

最后，我们需要 asyncio.run 来触发运行。asyncio.run 这个函数是 Python 3.7 之后才有的特性，可以让 Python 的协程接口变得非常简单，你不用去理会事件循环怎么定义和怎么使用的问题。

一个非常好的编程规范是，asyncio.run(main()) 作为主程序的入口函数，在程序运行周期内，只调用一次asyncio.run。

```python
import asyncio
 
async def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    await asyncio.sleep(sleep_time)
    print('OK {}'.format(url))
 
async def main(urls):
    tasks = [asyncio.create_task(crawl_page(url)) for url in urls]
    # 写法1、
    for task in tasks:
        await task
    # 写法2、
    # await asyncio.gather(*tasks)
 
%time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4']))
 
########## 输出 ##########
crawling url_1
crawling url_2
crawling url_3
crawling url_4
OK url_1
OK url_2
OK url_3
OK url_4
Wall time: 3.99 s
```

另外，asyncio.create_task，asyncio.run 这些函数都是 Python 3.7 以上的版本才提供的，自然，相比于旧接口它们也更容易理解和阅读。

```python
import asyncio
 
async def worker_1():
    print('worker_1 start')
    await asyncio.sleep(1)
    print('worker_1 done')
 
async def worker_2():
    print('worker_2 start')
    await asyncio.sleep(2)
    print('worker_2 done')
 
async def main():
    print('before await')
    await worker_1()
    print('awaited worker_1')
    await worker_2()
    print('awaited worker_2')
 
%time asyncio.run(main())
 
########## 输出 ##########
 
before await
worker_1 start
worker_1 done
awaited worker_1
worker_2 start
worker_2 done
awaited worker_2
Wall time: 3 s
```

```python
import asyncio
 
async def worker_1():
    print('worker_1 start')
    await asyncio.sleep(1)
    print('worker_1 done')
 
async def worker_2():
    print('worker_2 start')
    await asyncio.sleep(2)
    print('worker_2 done')
 
async def main():
    # task1 和 task2 任务被创建，并进入事件循环等待运行
    task1 = asyncio.create_task(worker_1())
    task2 = asyncio.create_task(worker_2())
    print('before await')
    await task1
    print('awaited worker_1')
    await task2
    print('awaited worker_2')
 
# asyncio.run(main())，程序进入 main() 函数，事件循环开启
%time asyncio.run(main())
 
########## 输出 ##########
 
before await
worker_1 start
worker_2 start
worker_1 done
awaited worker_1
worker_2 done
awaited worker_2
Wall time: 2.01 s
```

1、asyncio.run(main())，程序进入 main() 函数，事件循环开启；

2、task1 和 task2 任务被创建，并进入事件循环等待运行；运行到 print，输出 'before await'；

3、await task1 执行，用户选择从当前的主任务中切出，事件调度器开始调度 worker_1；

4、worker_1 开始运行，运行 print 输出'worker_1 start'，然后运行到 await asyncio.sleep(1)， 从当前任务切出，事件调度器开始调度 worker_2；

5、worker_2 开始运行，运行 print 输出 'worker_2 start'，然后运行 await asyncio.sleep(2) 从当前任务切出；

6、以上所有事件的运行时间，都应该在 1ms 到 10ms 之间，甚至可能更短，事件调度器从这个时候开始暂停调度；

7、一秒钟后，worker_1 的 sleep 完成，事件调度器将控制权重新传给 task_1，输出 'worker_1 done'，task_1 完成任务，从事件循环中退出；

8、await task1 完成，事件调度器将控制器传给主任务，输出 'awaited worker_1'，·然后在 await task2 处继续等待；

9、两秒钟后，worker_2 的 sleep 完成，事件调度器将控制权重新传给 task_2，输出 'worker_2 done'，task_2 完成任务，从事件循环中退出；

10、主任务输出 'awaited worker_2'，协程全任务结束，事件循环结束。

```python
import asyncio


async def worker_1():
    await asyncio.sleep(1)
    return 1


async def worker_2():
    await asyncio.sleep(2)
    return 2 / 0


async def worker_3():
    await asyncio.sleep(3)
    return 3


async def main():
    task_1 = asyncio.create_task(worker_1())
    task_2 = asyncio.create_task(worker_2())
    task_3 = asyncio.create_task(worker_3())

    await asyncio.sleep(2)
    task_3.cancel()

    res = await asyncio.gather(task_1, task_2, task_3, return_exceptions=True)
    print(res)

asyncio.run(main())

########## 输出 ##########
# [1, ZeroDivisionError('division by zero'), CancelledError('')]
```
worker_1 正常运行，worker_2 运行中出现错误，worker_3 执行时间过长被我们 cancel 掉了，这些信息会全部体现在最终的返回结果 res 中。

不过要注意return_exceptions=True这行代码。如果不设置这个参数，错误就会完整地 throw 到我们这个执行层，从而需要 try except 来捕捉，这也就意味着其他还没被执行的任务会被全部取消掉。为了避免这个局面，我们将 return_exceptions 设置为 True 即可。

到这里，发现了没，线程能实现的，协程都能做到。

```python
'''
用协程来实现一个经典的生产者消费者模型
'''
import asyncio
import random


async def consumer(queue, id):
    while True:
        val = await queue.get()
        print('{} get a val: {}'.format(id, val))
        await asyncio.sleep(1)


async def producer(queue, id):
    for i in range(5):
        val = random.randint(1, 10)
        await queue.put(val)
        print('{} put a val: {}'.format(id, val))
        await asyncio.sleep(1)


async def main():
    queue = asyncio.Queue()

    consumer_1 = asyncio.create_task(consumer(queue, 'consumer_1'))
    consumer_2 = asyncio.create_task(consumer(queue, 'consumer_2'))

    producer_1 = asyncio.create_task(producer(queue, 'producer_1'))
    producer_2 = asyncio.create_task(producer(queue, 'producer_2'))

    await asyncio.sleep(10)
    consumer_1.cancel()
    consumer_2.cancel()

    await asyncio.gather(consumer_1, consumer_2, producer_1, producer_2, return_exceptions=True)

asyncio.run(main())

########## 输出 ##########
```

协程和多线程的区别，主要在于两点，一是协程为单线程；二是协程由用户决定，在哪些地方交出控制权，切换到下一个任务。

协程的写法更加简洁清晰，把 async / await 语法和 create_task 结合来用，对于中小级别的并发需求已经毫无压力。

写协程程序的时候，你的脑海中要有清晰的事件循环概念，知道程序在什么时候需要暂停、等待 I/O，什么时候需要一并执行到底。

**最后的最后，请一定不要轻易炫技。多线程模型也一定有其优点，一个真正牛逼的程序员，应该懂得，在什么时候用什么模型能达到工程上的最优，而不是自觉某个技术非常牛逼，所有项目创造条件也要上。技术是工程，而工程则是时间、资源、人力等纷繁复杂的事情的折衷。**

## 并发编程之Futures

并发（Concurrency）和并行（Parallelism）

并发通常应用于 I/O 操作频繁的场景，比如你要从网站上下载多个文件，I/O 操作的时间可能会比 CPU 运行处理的时间长得多。

而并行则更多应用于 CPU heavy 的场景，比如 MapReduce 中的并行计算，为了加快运行速度，一般会用多台机器、多个处理器来完成。

单线程：
```python
import requests
import time
 
def download_one(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))
    
def download_all(sites):
    for site in sites:
        download_one(site)
 
def main():
    sites = [
        'https://en.wikipedia.org/wiki/Portal:Arts',
        'https://en.wikipedia.org/wiki/Portal:History',
        'https://en.wikipedia.org/wiki/Portal:Society',
        'https://en.wikipedia.org/wiki/Portal:Biography',
        'https://en.wikipedia.org/wiki/Portal:Mathematics',
        'https://en.wikipedia.org/wiki/Portal:Technology',
        'https://en.wikipedia.org/wiki/Portal:Geography',
        'https://en.wikipedia.org/wiki/Portal:Science',
        'https://en.wikipedia.org/wiki/Computer_science',
        'https://en.wikipedia.org/wiki/Python_(programming_language)',
        'https://en.wikipedia.org/wiki/Java_(programming_language)',
        'https://en.wikipedia.org/wiki/PHP',
        'https://en.wikipedia.org/wiki/Node.js',
        'https://en.wikipedia.org/wiki/The_C_Programming_Language',
        'https://en.wikipedia.org/wiki/Go_(programming_language)'
    ]
    start_time = time.perf_counter()
    download_all(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))
    
if __name__ == '__main__':
    main()
```

多线程：
```python
import concurrent.futures
import requests
import threading
import time
 
def download_one(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))
 
 
def download_all(sites):
    # 多线程
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        executor.map(download_one, sites)

# 多进程
def download_all_1(sites):
    # 多进程
    # 函数 ProcessPoolExecutor() 表示创建进程池，使用多个进程并行的执行程序。不过，这里我们通常省略参数 workers，因为系统会自动返回 CPU 的数量作为可以调用的进程数。
    with futures.ProcessPoolExecutor() as executor: 
        executor.map(download_one, sites)

# 写法2
def download_all_2(sites):
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        to_do = []
        for site in sites:
            future = executor.submit(download_one, site)
            to_do.append(future)
            
        for future in concurrent.futures.as_completed(to_do):
            future.result()

def main():
    sites = [
        'https://en.wikipedia.org/wiki/Portal:Arts',
        'https://en.wikipedia.org/wiki/Portal:History',
        'https://en.wikipedia.org/wiki/Portal:Society',
        'https://en.wikipedia.org/wiki/Portal:Biography',
        'https://en.wikipedia.org/wiki/Portal:Mathematics',
        'https://en.wikipedia.org/wiki/Portal:Technology',
        'https://en.wikipedia.org/wiki/Portal:Geography',
        'https://en.wikipedia.org/wiki/Portal:Science',
        'https://en.wikipedia.org/wiki/Computer_science',
        'https://en.wikipedia.org/wiki/Python_(programming_language)',
        'https://en.wikipedia.org/wiki/Java_(programming_language)',
        'https://en.wikipedia.org/wiki/PHP',
        'https://en.wikipedia.org/wiki/Node.js',
        'https://en.wikipedia.org/wiki/The_C_Programming_Language',
        'https://en.wikipedia.org/wiki/Go_(programming_language)'
    ]
    start_time = time.perf_counter()
    download_all(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))
 
if __name__ == '__main__':
    main()
```

concurrent.futures 和 asyncio 中的Future 的区别是什么？

https://stackoverflow.com/questions/29902908/what-is-the-difference-between-concurrent-futures-and-asyncio-futures

## 并发编程之Asyncio

Sync VS Async

Sync（同步）和 Async（异步）

所谓 Sync，是指操作一个接一个地执行，下一个操作必须等上一个操作完成后才能执行。

而 Async 是指不同操作间可以相互交替执行，如果其中的某个操作被 block 了，程序并不会等待，而是会找出可执行的操作继续执行。

事实上，Asyncio 和其他 Python 程序一样，是单线程的，它只有一个主线程，但是可以进行多个不同的任务（task），这里的任务，就是特殊的 future 对象。这些不同的任务，被一个叫做 event loop 的对象所控制。你可以把这里的任务，类比成多线程版本里的多个线程。

```python
import asyncio
import aiohttp
import time
 
async def download_one(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            print('Read {} from {}'.format(resp.content_length, url))
 
async def download_all(sites):
    tasks = [asyncio.create_task(download_one(site)) for site in sites]
    await asyncio.gather(*tasks)
 
def main():
    sites = [
        'https://en.wikipedia.org/wiki/Portal:Arts',
        'https://en.wikipedia.org/wiki/Portal:History',
        'https://en.wikipedia.org/wiki/Portal:Society',
        'https://en.wikipedia.org/wiki/Portal:Biography',
        'https://en.wikipedia.org/wiki/Portal:Mathematics',
        'https://en.wikipedia.org/wiki/Portal:Technology',
        'https://en.wikipedia.org/wiki/Portal:Geography',
        'https://en.wikipedia.org/wiki/Portal:Science',
        'https://en.wikipedia.org/wiki/Computer_science',
        'https://en.wikipedia.org/wiki/Python_(programming_language)',
        'https://en.wikipedia.org/wiki/Java_(programming_language)',
        'https://en.wikipedia.org/wiki/PHP',
        'https://en.wikipedia.org/wiki/Node.js',
        'https://en.wikipedia.org/wiki/The_C_Programming_Language',
        'https://en.wikipedia.org/wiki/Go_(programming_language)'
    ]
    start_time = time.perf_counter()
    asyncio.run(download_all(sites))
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))
    
if __name__ == '__main__':
    main()
```

Async 和 await 关键字是 Asyncio 的最新写法，表示这个语句 / 函数是 non-block 的，正好对应前面所讲的 event loop 的概念。如果任务执行的过程需要等待，则将其放入等待状态的列表中，然后继续执行预备状态列表里的任务。

主函数里的 asyncio.run(coro) 是 Asyncio 的 root call，表示拿到 event loop，运行输入的 coro，直到它结束，最后关闭这个 event loop。事实上，asyncio.run() 是 Python3.7+ 才引入的，相当于老版本的以下语句：
```python
loop = asyncio.get_event_loop()
try:
    loop.run_until_complete(coro)
finally:
    loop.close()
```

实际工作中，想用好 Asyncio，特别是发挥其强大的功能，很多情况下必须得有相应的 Python 库支持。

没有使用requests 库，而是用了 aiohttp 库，原因就是 requests 库并不兼容 Asyncio，但是 aiohttp 库兼容。

Asyncio 软件库的兼容性问题，在 Python3 的早期一直是个大问题，但是随着技术的发展，这个问题正逐步得到解决。

多线程还是 Asyncio

遇到实际问题时，多线程和 Asyncio 到底如何选择呢？

总的来说，你可以遵循以下伪代码的规范：
```python
if io_bound:
    if io_slow:
        print('Use Asyncio')
    else:
        print('Use multi-threading')
else if cpu_bound:
    print('Use multi-processing')
```

- 如果是 I/O bound，并且 I/O 操作很慢，需要很多任务 / 线程协同实现，那么使用 Asyncio 更合适。
- 如果是 I/O bound，但是 I/O 操作很快，只需要有限数量的任务 / 线程，那么使用多线程就可以了。
- 如果是 CPU bound，则需要使用多进程来提高程序运行效率。

不同于多线程，Asyncio 是单线程的，但其内部 event loop 的机制，可以让它并发地运行多个不同的任务，并且比多线程享有更大的自主控制权。

Asyncio 中的任务，在运行过程中不会被打断，因此不会出现 race condition 的情况。尤其是在 I/O 操作 heavy 的场景下，Asyncio 比多线程的运行效率更高。因为 Asyncio 内部任务切换的损耗，远比线程切换的损耗要小；并且 Asyncio 可以开启的任务数量，也比多线程中的线程数量多得多。

但需要注意的是，很多情况下，使用 Asyncio 需要特定第三方库的支持，比如前面示例中的 aiohttp。而如果 I/O 操作很快，并不 heavy，那么运用多线程，也能很有效地解决问题。

## Python GIL（全局解释器锁）

Python 的线程，的的确确封装了底层的操作系统线程，在 Linux 系统里是 Pthread（全称为 POSIX Thread），而在 Windows 系统里是 Windows Thread。另外，Python 的线程，也完全受操作系统管理，比如协调何时执行、管理内存资源、管理中断等等。

所以，虽然 Python 的线程和 C++ 的线程本质上是不同的抽象，但它们的底层并没有什么不同。

事实上，Python 的解释器并不是线程安全的，为了解决由此带来的 race condition 等问题，Python 便引入了全局解释器锁，也就是同一时刻，只允许一个线程执行。当然，在执行 I/O 操作时，如果一个线程被 block 了，全局解释器锁便会被释放，从而让另一个线程能够继续执行。

GIL，是最流行的 Python 解释器 CPython 中的一个技术术语。它的意思是全局解释器锁，本质上是类似操作系统的 Mutex。每一个 Python 线程，在 CPython 解释器中执行时，都会先锁住自己的线程，阻止别的线程执行。

当然，CPython 会做一些小把戏，轮流执行 Python 线程。这样一来，用户看到的就是“伪并行”——Python 线程在交错执行，来模拟真正并行的线程。

那么，为什么 CPython 需要 GIL 呢？这其实和 CPython 的实现有关。

Python 的内存管理机制

CPython 使用引用计数来管理内存，所有 Python 脚本中创建的实例，都会有一个引用计数，来记录有多少个指针指向它。当引用计数只有 0 时，则会自动释放内存。

CPython 引进 GIL 其实主要就是这么两个原因：
> 一是设计者为了规避类似于内存管理这样的复杂的竞争风险问题（race condition）；
> 二是因为 CPython 大量使用 C 语言库，但大部分 C 语言库都不是原生线程安全的（线程安全会降低性能和增加复杂度）。

GIL 的设计，主要是为了方便 CPython 解释器层面的编写者，而不是 Python 应用层面的程序员。作为 Python 的使用者，我们还是需要 lock 等工具，来确保线程安全。

如何绕过 GIL？

Python 的 GIL，是通过 CPython 的解释器加的限制。如果你的代码并不需要 CPython 解释器来执行，就不再受 GIL 的限制。

事实上，很多高性能应用场景都已经有大量的 C 实现的 Python 库，例如 NumPy 的矩阵运算，就都是通过 C 来实现的，并不受 GIL 影响。

所以，大部分应用情况下，你并不需要过多考虑 GIL。因为如果多线程计算成为性能瓶颈，往往已经有Python 库来解决这个问题了。

换句话说，如果你的应用真的对性能有超级严格的要求，比如 100us 就对你的应用有很大影响，那我必须要说，Python 可能不是你的最优选择。

当然，可以理解的是，我们难以避免的有时候就是想临时给自己松松绑，摆脱 GIL，比如在深度学习应用里，大部分代码就都是 Python 的。在实际工作中，如果我们想实现一个自定义的微分算子，或者是一个特定硬件的加速器，那我们就不得不把这些关键性能（performance-critical）代码在 C++ 中实现（不再受 GIL 所限），然后再提供 Python 的调用接口。

总的来说，你只需要重点记住，绕过 GIL 的大致思路有这么两种就够了：
- 绕过 CPython，使用 JPython（Java 实现的 Python 解释器）等别的实现；
- 把关键性能代码，放到别的语言（一般是 C++）中实现。

## Python 垃圾回收机制

众所周知，我们当代的计算机都是图灵机架构。

图灵机架构的本质，就是一条无限长的纸带，对应着我们今天的存储器。

在工程学的演化中，逐渐出现了寄存器、易失性存储器（内存）和永久性存储器（硬盘）等产品。

其实，这本身来自一个矛盾：速度越快的存储器，单位价格也越昂贵。

因此，妥善利用好每一寸高速存储器的空间，永远是系统设计的一个核心。

深入看一下 Python 内部的引用计数机制。
```python
import sys
 
a = []
 
# 两次引用，一次来自 a，一次来自 getrefcount
print(sys.getrefcount(a))
 
def func(a):
    # 四次引用，a，python 的函数调用栈，函数参数，和 getrefcount
    print(sys.getrefcount(a))
 
func(a)
 
# 两次引用，一次来自 a，一次来自 getrefcount，函数 func 调用已经不存在
print(sys.getrefcount(a))
 
########## 输出 ##########
2
4
2
```
sys.getrefcount() 这个函数，可以查看一个变量的引用次数。不过别忘了，getrefcount 本身也会引入一次计数。

另一个要注意的是，在函数调用发生的时候，会产生额外的两次引用，一次来自函数栈，另一个是函数参数。
```python
import sys
 
a = []
 
print(sys.getrefcount(a)) # 两次
 
b = a
 
print(sys.getrefcount(a)) # 三次
 
c = b
d = b
e = c
f = e
g = d
 
print(sys.getrefcount(a)) # 八次
 
########## 输出 ##########
2
3
8
```

想手动释放内存，应该怎么做呢？

只需要先调用 del a 来删除一个对象；然后强制调用 gc.collect()，即可手动启动垃圾回收。
```python
import gc
 
show_memory_info('initial')
 
a = [i for i in range(10000000)]
 
show_memory_info('after a created')
 
del a
gc.collect()
 
show_memory_info('finish')
# print(a)
 
########## 输出 ##########
initial memory used: 48.1015625 MB
after a created memory used: 434.3828125 MB
finish memory used: 48.33203125 MB
```

Python 使用标记清除（mark-sweep）算法和分代收集（generational），来启用针对循环引用的自动垃圾回收。

先来看标记清除算法。我们先用图论来理解不可达的概念。对于一个有向图，如果从一个节点出发进行遍历，并标记其经过的所有节点；那么，在遍历结束后，所有没有被标记的节点，我们就称之为不可达节点。显而易见，这些节点的存在是没有任何意义的，自然的，我们就需要对它们进行垃圾回收。

当然，每次都遍历全图，对于 Python 而言是一种巨大的性能浪费。所以，在 Python 的垃圾回收实现中，mark-sweep 使用双向链表维护了一个数据结构，并且只考虑容器类的对象（只有容器类对象才有可能产生循环引用）。具体算法这里我就不再多讲了，毕竟我们的重点是关注应用。

而分代收集算法，则是另一个优化手段。

Python 将所有对象分为三代。刚刚创立的对象是第 0 代；经过一次垃圾回收后，依然存在的对象，便会依次从上一代挪到下一代。而每一代启动自动垃圾回收的阈值，则是可以单独指定的。当垃圾回收器中新增对象减去删除对象达到相应的阈值时，就会对这一代对象启动垃圾回收。

事实上，分代收集基于的思想是，新生的对象更有可能被垃圾回收，而存活更久的对象也有更高的概率继续存活。因此，通过这种做法，可以节约不少计算量，从而提高 Python 的性能。

引用计数是其中最简单的实现，不过切记，引用计数并非充要条件，它只能算作充分非必要条件；至于其他的可能性，我们所讲的循环引用正是其中一种。

调试内存泄漏

不过，虽然有了自动回收机制，但这也不是万能的，难免还是会有漏网之鱼。内存泄漏是我们不想见到的，而且还会严重影响性能。有没有什么好的调试手段呢？

答案当然是肯定的，接下来我就为你介绍一个“得力助手”。

它就是 objgraph，一个非常好用的可视化引用关系的包。在这个包中，我主要推荐两个函数，第一个是 show_refs()，它可以生成清晰的引用关系图。

通过下面这段代码和生成的引用调用图，你能非常直观地发现，有两个 list 互相引用，说明这里极有可能引起内存泄露。这样一来，再去代码层排查就容易多了。
```python
import objgraph
 
a = [1, 2, 3]
b = [4, 5, 6]
 
a.append(b)
b.append(a)
 
objgraph.show_refs([a]) # 生成的引用调用图
objgraph.show_backrefs([a])
```

总结：
- 1、垃圾回收是 Python 自带的机制，用于自动释放不会再用到的内存空间；
- 2、引用计数是其中最简单的实现，不过切记，这只是充分非必要条件，因为循环引用需要通过不可达判定，来确定是否可以回收；
- 3、Python 的自动回收算法包括标记清除和分代收集，主要针对的是循环引用的垃圾收集；
- 4、调试内存泄漏方面， objgraph 是很好的可视化分析工具。



# 规范

## 编码规范
《8 号 Python 增强规范》（Python Enhacement Proposal #8），以下简称 PEP8；

《Google Python 风格规范》（Google Python Style Guide），以下简称 Google Style，这是源自 Google 内部的风格规范。公开发布的社区版本，是为了让 Google 旗下所有 Python 开源项目的编程风格统一。

http://google.github.io/styleguide/pyguide.html

相对来说，Google Style 是比 PEP8 更严格的一个编程规范。

因为 PEP8 的受众是个人和小团队开发者，而 Google Style 能够胜任大团队，企业级，百万行级别代码库。

开发效率，关乎三类对象，也就是阅读者、编程者和机器。

他们的优先级是阅读者的体验 >> 编程者的体验 >> 机器的体验。

阅读者的体验 >> 编程者的体验

写过代码的人可能都有体会，在我们的实际工作中，真正在打字的时间，远比阅读或者 debug 的时间要少。

事实正是如此，研究表明，软件工程中 80% 的时间都在阅读代码。

所以，为了提高开发效率，我们要优化的，不是你的打字时间，而是团队阅读的体验。

其实，不少的编程规范，本来就是为了优化读者体验而存在的。

举个例子，对于命名原则，我想很多人应该都有所理解，PEP8 第 38 条规定命名必须有意义，不能是无意义的单字母。但是当你作为阅读者时，一定能分辨下面两种代码的可读性不同：
```python
# 错误示例
if (a <= 0):
   return
elif (a > b):
   return
else:
  b -= a
 
# 正确示例
if (transfer_amount <= 0):
   raise Exception('...')
elif (transfer_amount > balance):
   raise Exception('...')
else:
  balance -= transfer_amount
```

再举一个例子，Google Style 2.2 条规定，Python 代码中的 import 对象，只能是 package 或者 module。
```python
# 错误示例
from mypkg import Obj
from mypkg import my_func
 
my_func([1, 2, 3])
 
# 正确示例
import numpy as np
import mypkg
 
np.array([6, 7, 8])
```

编程者的体验 >> 机器的体验

说完了阅读者的体验，再来聊聊编程者的体验。我常常见到的一个错误倾向，是过度简化自己的代码，包括我自己也有这样的问题。一个典型的例子，就是盲目地使用 Python 的 list comprehension。
```python
# 错误示例
result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]
```

我敢打赌，一定很少有人能一口气写出来这么复杂的 list comprehension。这不仅容易累着自己，也让阅读者看得很累。其实，如果你用一个简单的 for loop，会让这段代码更加简洁明了，自己也更为轻松。
```python
# 正确示例
result = []
for x in range(10):
  for y in range(5):
     if x * y > 10:
       result.append((x, y))
```

机器的体验也很重要

讲完了编程者和阅读者的重要性，我们不能忽视了机器的体验。我们最终希望代码能正确、高效地在电脑上执行。但是，一些危险的编程风格，不仅会影响程序正确性，也容易成为代码效率的瓶颈。

```python
# 错误示例
x = 27
y = 27
print(x is y)
 
x = 721
y = 721
print(x is y)
```

PEP8 和 Google Style 都特别强调了，何时使用 is， 何时使用 ==，何时使用隐式布尔转换。

Google Style 2.8 对于遍历方式的选择作出了限制。

- 在代码评审工具里，添加必须的编程规范环节；
- 把团队确定的代码规范写进 Pylint 里（https://www.pylint.org/），能够在每份代码提交前自动检查，不通过的代码无法提交。

## 学会合理分解代码，提高代码可读性

Guido van Rossum（吉多·范罗苏姆，Python 创始人 ）说过，代码的阅读频率远高于编写代码的频率。

毕竟，即使是在编写代码的时候，你也需要对代码进行反复阅读和调试，来确认代码能够按照期望运行。

PEP 是 Python Enhancement Proposal 的缩写，翻译过来叫“Python 增强规范”。正如我们写文章，会有句式、标点、段落格式、开头缩进等标准的规范一样，Python 书写自然也有一套较为官方的规范。PEP 8 就是这样一种规范，它存在的意义，就是让 Python 更易阅读，换句话，增强代码可读性。

事实上，Pycharm 已经内置了 PEP 8 规范检测器，它会自动对编码不规范的地方进行检查，然后指出错误，并推荐修改方式。

Settings>Editor>Code Style>Inspections>pep 8

代码规范甚至是比代码准确更重要的事情，因为实际工作中，代码可读性的重要性一定比你想象的多得多。

PEP 8 规范告诉我们，请选择四个空格的缩进，不要使用 Tab，更不要 Tab 和空格混着用。

第二个要注意的是，每行最大长度请限制在 79 个字符。

PEP 8 规定，全局的类和函数的上方需要空两个空行，而类的函数之间需要空一个空行。

一份优秀的代码，离不开优秀的注释。

请注意，行注释并不是很推荐的方式。

## 命名规范
计算机科学的两件难事：缓存失效和命名。

一般来说，变量使用小写，通过下划线串联起来。

如果是类的私有变量，请记得前面增加两个下划线。

对于常量，最好的做法是全部大写，并通过下划线连接。

对于函数名，同样也请使用小写的方式，通过下划线连接起来。

对于类名，则应该首字母大写，然后合并起来。

## 如何合理利用assert？



# 问题

## Python中循环结构while和for谁的效率高呢？

代码中所有直接调用C的操作的语句肯定比通过Python间接调用C（Python的解释器是C写的）快的多。

比如下面的例子：B用for循环比例子A用while循环快很多。

因为range这个函数是C写的，直接调用。但是i += 1这个操作得通过解释器间接调用C，而这个简单的增值操作又涉及到object的创建和删除（i是immutable的，i += 1 相当于 i =new int(i + 1)）），因此相对来说很耽误时间。

```python
# (A)
i = 0
while i < 100000000:
    i += 1
# (B)
for i in range(0, 100000000):
    pass
```


# 模块

## objgraph

官方doc：

https://mg.pov.lt/objgraph/




## asyncio


## time

## functools

## Virtualenv



## pylru

## collections 模块


## dis分析字节码


## timeit 模块

`python3 -m timeit` 
> 是 Python 的一个命令行工具，用于测量小段代码的执行时间。它属于 Python 标准库中的 timeit 模块，专门用于简单、快速的性能测试。这个工具特别适合比较不同实现方式的性能差异。

shell中用法：`python3 -m timeit "your_code"`

注意：Windows 通常直接使用 python 而不是 python3
cmd用法：`python -m timeit "your_code"`

由于 Windows 命令行的语法规则（特别是引号的处理）与 Unix/Linux 有所不同，可能会遇到一些问题。
特殊注意事项
- 1、引号规则不同：
> 如果代码中包含双引号 "，需要：外层用单引号 ' 包裹或者用转义双引号 \"
- 2、避免特殊字符：
> 如果代码中包含 |、> 等 Windows 特殊字符，建议用单引号包裹或转义。


常用参数：
```shell
-n N：设置每次测试运行的次数（默认自动调整）

-r N：设置重复测试的轮数（默认 5 轮）

-s SETUP 或 --setup=SETUP：设置初始化代码（只执行一次）

-p 或 --process：测量进程时间而非 wall time
```

测试列表推导式的速度：
`python3 -m timeit "[x*x for x in range(100)]"`

带初始化代码的测试：
`python3 -m timeit -s "arr=list(range(1000))" "sum(arr)"`

自定义运行参数：
`python3 -m timeit -n 1000 -r 3 "sorted(list(range(100)))"`

输出解读：
> 100000 loops, best of 5: 1.23 usec per loop
表示：系统自动选择了运行 100,000 次，5 轮测试中最好的结果是每次循环 1.23 微秒

注意事项：
- 1、测试的代码需要用引号包裹
- 2、避免测试太快的操作（可能得到不准确的结果）
- 3、对于复杂测试，建议先用 -s 设置初始化代码

也可以在 Python 脚本中使用这个模块
```python
import timeit
timeit.timeit('"-".join(str(n) for n in range(100))', number=10000)
```
