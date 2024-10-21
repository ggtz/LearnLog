# 学习内容
# 数据抽象
## 简介
type(num)#可查看num的类
python的三种原始数据类型：int（`int` 值是无界的） 、 float（求解是是近似值） 、 complex
**注：**float中包含近似值**
![[Pasted image 20241021093302.png]]![[Pasted image 20241021093404.png]]
# 序列
## 列表（list）
  
``` python
>>> digits = [1, 8, 2, 8]
>>> len(digits)
4
>>> digits[3]
8
```

### + 和 * 的含义
```   python
digits = [1, 8, 2, 8]

>>> [2, 7] + digits * 2
[2, 7, 1, 8, 2, 8, 1, 8, 2, 8]
```

### 列表list遍历
``` python
for <name> in <expression>:
    <suite>
```

**序列解包**：
这种将多个名称绑定到固定长度序列中的多个值的模式称为序列解包（sequence unpacking）
``` python
pairs = [[1, 2], [2, 2], [2, 3], [4, 4]]
>>> same_count = 0
>>> for x, y in pairs:
        if x == y:
            same_count = same_count + 1
>>> same_count
2
```

### range函数
range(1,5) == [1,5)
range(10) == [0,10)

### 序列处理
**列表推导式**：
``` python
>>> odds = [1, 3, 5, 7, 9]
>>> [x+1 for x in odds]
[2, 4, 6, 8, 10]
```
  
**列表筛选：**
``` python
>>> [x for x in odds if 25 % x == 0]
[1, 5]
```

**聚合（Aggregation）**：将序列中的所有值聚合为一个值

![[Pasted image 20241021094948.png]]

### 序列抽象-（成员资格-in、切片[start:end:step]）
  成员资格：
``` python
>>> digits
[1, 8, 2, 8]
>>> 2 in digits
True
>>> 1828 not in digits
True
```

切片：最经典的例子是使用 s[ : : -1] 得到 `s` 的逆序排列

## 字符串
python没有字符类型，只有字符串；
可使用性质：
- len()
+ and * 
+ 成员资格-in

**字符串强制转换**：

``` python
>>> str(2) + ' is an element of ' + str(digits)
'2 is an element of [1, 8, 2, 8]'
```

# 可变数据
## 引入
对象：属性+行为
Python 中所有的值都是对象。也就是说，所有的值都有行为和属性，它们拥有它们所代表的数据的行为。

## 序列对象
基本数据类型的示例：不可变
列表：可变
数据共享：
![[Pasted image 20241021100345.png]]
chinese、suits绑定同一个实例。
**以下就不是数据共享**
![[Pasted image 20241021100520.png]]
对数据绑定的深刻理解
![[Pasted image 20241021100728.png]]
## 元组-不可变序列
元组是指 Python 内置类型 `tuple` 的实例对象，其是不可变序列。
元组中可以放置任意对象
元组常用方法：
	元组.count(值)
	元组.index(值)
尽管**无法修改元组的元素**，但是**如果元组中的*元素本身是可变数据*，那我们也是可以对该元素进行操作的**。


## 字典-用来存储和操作带有映射关系的数据
一个字典包含一组键值对（key-value pairs），其中键和值都是对象。
字典的 key 一般都是字符串（String）
一个 key 只能对应一个 value

字典类型也有一些限制：

- 字典的 key 不可以是可变数据，也不能包含可变数据
- 一个 key 只能对应一个 value

字典中一个很有用的方法是 `get`，它返回指定 key 在字典中对应的 value；如果该 key 在字典中不存在，则返回默认值。`get` 方法接收两个参数，一个 key，一个默认值。
``` python
>>> numerals.get('A', 0)
0
>>> numerals.get('V', 0)
5
```
字典推导式：{x: x * x for x in range(3,6)}

## 局部状态 与 非局部状态
状态（state）就意味着当前的值有可能发生变化。
局部状态：数据、函数只存在于函数调用时创建的帧环境中
非局部（nonlocal）声明：当需要使用该数据时，不是寻找当前环境的该数据，而是去定义该数据处使用该数据值。![[Pasted image 20241021104512.png]]

## 消息传递
``` python
>>> def dictionary():
        """返回一个字典的函数实现"""
        records = []
        def getitem(key):
            matches = [r for r in records if r[0] == key]
            if len(matches) == 1:
                key, value = matches[0]
                return value
        def setitem(key, value):
            nonlocal records
            non_matches = [r for r in records if r[0] != key]
            records = non_matches + [[key, value]]
        def dispatch(message, key=None, value=None):
            if message == 'getitem':
                return getitem(key)
            elif message == 'setitem':
                setitem(key, value)
        return dispatch
```
使用消息传递方法来组织我们的实现、不必在通过调用函数来实现功能，而是通过一个特定的消息传递函数来做到统一

注意看实现：太像面向对象了，通过定义的函数来实现了数据的共享
``` python
def account(initial_balance):
    def deposit(amount):
        dispatch['balance'] += amount
        return dispatch['balance']
    def withdraw(amount):
        if amount > dispatch['balance']:
            return 'Insufficient funds'
        dispatch['balance'] -= amount
        return dispatch['balance']
    dispatch = {'deposit':   deposit,
                'withdraw':  withdraw,
                'balance':   initial_balance}
    return dispatch

def withdraw(account, amount):
    return account['withdraw'](amount)
def deposit(account, amount):
    return account['deposit'](amount)
def check_balance(account):
    return account['balance']

a = account(20)
deposit(a, 5)
withdraw(a, 17)
check_balance(a)
```

## 调度字典（Dispatch Dictionaries）
`dispatch` 函数是实现抽象数据消息传递接口的通用方法。
``` python
def account(initial_balance):
    def deposit(amount):
        dispatch['balance'] += amount
        return dispatch['balance']
    def withdraw(amount):
        if amount > dispatch['balance']:
            return 'Insufficient funds'
        dispatch['balance'] -= amount
        return dispatch['balance']
    dispatch = {'deposit':   deposit,
                'withdraw':  withdraw,
                'balance':   initial_balance}
    return dispatch

def withdraw(account, amount):
    return account['withdraw'](amount)
def deposit(account, amount):
    return account['deposit'](amount)
def check_balance(account):
    return account['balance']

a = account(20)
deposit(a, 5)
withdraw(a, 17)
check_balance(a)
```
通过函数中字典的应用实现了对于initial_balance属性的传递，并且可以不使用非局部状态
# 面向对象
# 类、对象、方法、属性
类属性、
实例（对象）属性

# 未来学习规划
准备跟完这个项目flax部分：https://github.com/gordicaleksa/get-started-with-JAX
