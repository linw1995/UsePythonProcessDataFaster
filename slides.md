---
author: 林玮 (Jade Lin)
date: 2006-01-02
theme: ./theme.json
paging: 第 %d / %d 页 
---

# 如何用 Python 快速处理数据

本讲旨在让 **Python** 初学者学会如何快速地处理文本数据

~~~sh
cfonts 'Hello' -c 'blue' -a center
cfonts 'World' -c 'red' -a right
cfonts 'Hello' -c 'yellow' -a center
cfonts 'Python' -c 'blue' -a right
~~~

> 通过调整终端大小，从小往大调整，直至本页面将要变形，
> 即可获得更好的阅读体验。
> **MacOS** 可通过快捷键 `command` + `+/-` 来调整字体大小

---

# 安装使用 Python

希望大家从官网 [python.org](https://www.python.org/downloads/) 上，下载安装一下 **Python**。

一边随着幻灯片，一边通过实践来学习。

原生 python repl 不太好用，推荐顺便安装下 [IPython](https://ipython.org/)。
在终端运行 `pip install ipython` 即可安装，再通过 `ipython` 启用该 repl。

> REPL -- Read–eval–print loop 就是我们输入，repl执行，再把结果输出给我们，如此反复。
> 比如在 ipython 中键入以下代码，再按一下回车键，即可看到其输出 "hello world"。

```python
print('hello world')
```

---

# 数据处理的的三大阶段

可简单分为三个阶段

~~~graph-easy --as=boxart
[ parse ] -> [ process ] -> [ export ]
~~~

* **parse** 文本数据解析
* **process** 数据处理
* **export** 数据导出

常见的文本数据格式有 **JSON** **CSV** ，本讲会以这两种格式为例子。

先从 **数据处理** 开始讲起，再讲 **文本数据的解析及导出** ，
最后举一个例子来串联下知识点。

---

# 数据处理

## 基本数据类型

### 数值类型

* `int` 整型，即整数像 `-1` `0` `1` `2` `3`。
* `float` 浮点型，表示带小数部分的数字，像 `-1.0` `0.1` `.3` 。
  无论是计算还是存储，遇到 float 都需要考虑精度的问题。
* `decimal.Decimal` 如果需要高精度的十进制浮点数计算，用它就没错了

关于 `int` `float` 不用展开的讲，只提几个小知识点

1. Python 中的 `int` 没有大小限制，不像需要编译的语言，大数计算需要用特定模块。
2. 低精度的计算不用 `decimal` ，代码运行的速度会更块。
    需要高精度的话，就一定要用 `decimal` 模块


```python
a = 1
print(a)

b = 1.0
print(b)
```

> 按下 `ctrl+e` 可执行代码块。

> `a = 1` 及 `b = 1.0` 为赋值语句 Assignment statement，
> 其中的 `a`, `b` 为变量 Variable，`1`, `1.0` 为字面量 Literal。

> `print(a)` 为调用语句 call statement，
> 其中的 `print` 是变量，其类型是函数，作用是输出传入参数到标准输出流，即显示 `a` 变量的值。


---

## 基本数据类型

### 数值类型

#### Decimal

```python
from decimal import *

a = Decimal('17.2')
b = Decimal('17.4')

c = b - a
print(type(c), c)  # 调用 type 函数获取变量 c 的类型

# 可与 int 进行数值计算，但与 float 不行（由于 float 不能保证自身精度）
d = c * 30
print(type(d), d)
e = 1 / Decimal(3)
print(type(e), e)

# 修改上下文中的精度配置
getcontext().prec = 6
print("6:", Decimal(1) / Decimal(7))
getcontext().prec = 28 # 默认值
print("28", Decimal(1) / Decimal(7))
```

> `from decimal import *` 导入 `decimal` 模块到全局作用域。
> 
> 这样就可以通过直接通过 `Decimal('17.2')` 调用来做 `Decimal` 变量的构建。
> 
> `getcontext` 函数同样来自于 `decimal` 模块。
> 
> 如果不清楚变量的来源可以尝试用 `help(getcontext)` 函数查询。

> 而如果用 `import decimal` 来导入 `decimal` 模块，
> 则需要通过 `decimal.Decimal` 来调用 `Decimal` 构建。

> 更多使用姿势可以见 [Decimal objects](https://docs.python.org/3/library/decimal.html#quick-start-tutorial)

---

### 字符串类型

- `bytes` 字节数组，可以当作是元素只能为 `0` - `255` （一字节，八比特）的整型数组
- `str` 字符串类型

```python
a = '字符串'
b = b'\xe5\xad\x97\xe8\x8a\x82\xe6\x95\xb0\xe7\xbb\x84'

# 向终端打印字符串很方便
print("str:", a) # 即可

# bytes 就很麻烦了，需要解码
print("bytes:", b.decode('utf-8'))
# 或者直接写入标准输出流
import os

os.write(1, b + b'\n\n')
```

> 直接写入标准输出流，反而会先输出。
> 因为一般情况下， `print` 往标准输出流写入是会带缓冲的（可以看一下全全局变量 `sys.stdout` 的类型是什么）

---

### 字符串类型

字符串类型为什么有 `str` 和 `bytes`，光 str 不行吗？回答这个问题之前，先了解一下编码

#### 编码

编码其实就是储存字符的一种方式，比如最简单的 [**ascii**](https://zh.wikipedia.org/wiki/ASCII) 用 `97` 来代表字符 `'a'`。 
比如直接数组 `bytes([97, 98, 99])` 就是 `b'abc'`。而 `str` 类型为 **Python** 中默认的字符串类型，以 **unicode** 进行编码，把字符串存储在内存中。

**unicode** 为至今最大的一个编码集合，能涵盖所有字符（有趣的是 emoji 也包含在内）。

```python
# 通过不同的字面量写法，打印出同一个字符
print('character name:','\N{rolling on the floor laughing}')
print('charactor code:', '\U0001f923')
```

可以这么理解 **unicode** 为所有编码的超集，其他编码都能通过一定规则来转换成 **unicode** 编码。
理所当然，这就该是默认的字符串类型。

通过 `ord` 及 `chr` 可以进行编码与字符的转换。

```python
# str
print(ord('🤣'), chr(129315))
# one byte
print(ord('a'.encode('ascii')), chr(97))
```

> 更多关于编码的知识，可以查看 [Unicode HOWTO](https://docs.python.org/3/howto/unicode.html)

---

### 字符串类型
#### 字符串的输入输出

字符串 str 用的是 **Python** 内部的编码方式，字符串的输出输入肯定是要做编码解码的。

比如读取文件，保存文件，都要选择编码（默认 `"utf-8"`，是 **unicode** 的一种实现)。

以下代码中 `encoding="utf-8"` 代表了输出所要执行编码类型 **utf-8**，`"w"` 代表要写入。
（如果换成简单，字符集合小的 **ascii** 编码，以下代码就会抛出编码异常错误）

```python
with open("./assets/demo.txt", "w", encoding="utf-8") as f:
    f.write('🤣')

with open("./assets/demo.txt", "r", encoding="utf-8") as f:
    print(f.read())
```

> 我们向标准输出流打印字符串的过程，也有在进行编码。
> 所以在调用 `print` 函数的时候，也可能会越到编码错误。
> 遇到这样的问题，一般更正一下终端的 locale 即可解决。


- 执行 `locale -a` 查看所有可配置项
- 修改当前的配置，通过变更环境变量 `LANG` 来变更编码。
  比如执行以下代码会发现 **GB18030** 编码 emoji 产生了乱码

```bash
LANG=zh_CN.GB18030 python -c 'print("GB18030:", chr(129315))'
LANG=en_US.UTF-8 python -c 'print("UTF-8:", chr(129315))'
```

---

# 数据处理
## 容器

讲完了，数据处理中常用的基本类型。

接下来讲一下 **Python** 中常用的容器及用法。

* list 数组
* dict 关联数组（字典）
* set 集合

---

## List 数组

### 创建数组的方法

字面量写法

```python
# 简单的一对中括号即可创建一个空数组
arr = []
print(arr)

# 中括号包裹变量也是数组，不限元素类型
arr = [1, 2, 3, arr, None, 1.2]
print(arr)
```

---

### 创建数组的方法

其他写法

```python
# 数组推导式，迭代 range(10) 获取从 0 到 9 十个整数
arr = [i for i in range(10)]
print(arr)

# 或者简单通过 list 构建
arr = list(range(5)) # list() 空数组
print(arr)
```

> 数组推导式 list comprehension，一种语法糖。比如 `arr = [i for i in range(10)]` 等同于以下代码

```python
arr = []
for i in range(10):
    arr.append(i)
```

---

### 访问数组中的元素

通过中括号 + 数组元素下标（序号，从零开始计数）获取

```python
arr = [1, 3, 2, 5, 4]
print(arr[2], arr[3])
```

通过 **for-in** 语句遍历访问

```python
arr = [1, 3, 2]
for x in arr:
    print(x)

# 遍历过程中同时获取元素的下标
for idx, x in enumerate(arr):
    print(idx, x)
```

> 只有可迭代 Iterable 对象可以通过 **for-in** 遍历，比如 list dict 等
> `enumerate` 是个生成器创建函数。生成器 Generator 的优点是懒执行，只有遍历到了才会给出当前的下标
> 等同一下代码，定义了个 `enumerate` 的生成器函数

```python
def enumerate(iterable):
    idx = 0
    for value in iterable:
        yield idx, value
        idx += 1

```

> `yield` 关键字，会暂停函数的执行，把 `idx, value` 提供给 **for-in** 语句。
> 直到 **for-in** 语句的代码块执行完之后，再继续执行函数。如此往复，直到 `iterable` 被遍历完

---

#### 访问数组中的元素 -- 分片访问

```python
arr = [1, 3, 2, 5, 4]
# 等同 arr[:2]，获取下标为 0, 1 的元素
# 相当于创建了一个新的数组，包含下标为 0，1 的元素
print(arr[0:2])

# 还可以通过分片赋值来修改数组
arr[0:2] = reversed(arr[0:2])
print(arr)
```

---

### 修改数组

#### 修改数组 -- 修改元素

```python
arr = [1, 3, 2]

# 通过数组下标修改
arr[1] = 2
arr[2] = 3
print("A:", arr)

# 通过分片修改一连串的元素
arr[1:3] = [-2, -3]
print("B:", arr)

arr[1:3] = [-2]
print("C:", arr)
```

---

#### 修改数组 -- 添加元素

```python
arr = [1, 2, 3]

# 从数组尾部添加新元素
arr.append(4)
print("A:", arr)

# 从数组首部插入新元素
arr.insert(0, -1)
print("B:", arr)
```

---

#### 修改数组 -- 删除

根据数组中的元素来删除

```python
arr = [1, 2, 2, 3]

# 删除数组元素（删除第一个遍历到的）
arr.remove(2)
print("A:", arr)

```

---

#### 修改数组 -- 删除

根据数组中的元素下标来删除

```python
# 根据数组下标删除元素
arr = [1, 2, 3]
del arr[1]
print("A:", arr)

## 删除并获取
arr = [1, 2, 3]
print("B:", arr)
# 获取尾部元素并从数组中移除
print("B:", arr.pop())
# 获取下标为 0 的元素并从数组中移除
print("B:", arr.pop(0))
print("B:", arr)

# 删除数组中一连串元素
arr = list(range(5))
print("C:", arr)
del arr[1:3]
print("C:", arr)
```

---

### 数组排序

如何让数组从小到大排序？

```python
arr = [1, 3, 2, 5, 4]

# 返回排序好的结果，不会修改原数组
new_arr = sorted(arr)
print(new_arr, arr)

# 就地排序
arr.sort()
print(arr)
```

---

#### 数组排序 -- 自定义

通过传入 `key=lambda x: -x` 使得数组反序排序，从大到小排列。等同 `reversed(sorted(arr))`。

```python
arr = [1, 3, 2, 5, 4]
print(sorted(arr, key=lambda x: -x))
```

其中 `lambda x: -x` 是匿名函数，方便，不用写函数定义。等同以下写法

```python
def negative(x):
    return -x

print(sorted([1, 3, 2, 5, 4], key=negative))
```

---

#### 数组排序 -- 自定义

`key` 参数是用来包装变量的，`sorted` 函数再通过包装后的键（变量的负值）去按从小到大排序，
最终解包装返回排序后的结果，即从大到小排序。
(这个过程被称作 **Wrap-Sort-Unwrap** 或者 **Decorate-Sort-Undecorate** )

~~~graph-easy --as=boxart
[\[1, 3, 2, 5, 4\]] { flow: down; } -- wrap -->
[\[-1, -3, -2, -5, -4\]] { flow: right} -- sort -->
[\[-5, -4, -3, -2, -1\]] { flow: up} -- unwrap -->
[\[5, 4, 3, 2, 1\]]
~~~

有这个参数后能十分方便地做各种排序（也支持 `arr.sort(key=lamdba x: -1)` ）。
比如，以单词列表以单词长度从小到大排序

```python
arr = ['red', 'yellow', 'green']
print(sorted(arr, key=len)) 
```

> 更多的排序姿势见 [Sorting HowTo](https://docs.python.org/zh-cn/3/howto/sorting.html)

---

### 过滤数组中的元素

如何过滤掉变量 `arr` 中小于等于 3 的值

```python
arr = [1, 3, 2, 5, 4]

# 用 filter 函数，由于该函数返回结果的类型是生成器，需通过 list 去转换它
filtered_arr = list(filter(lambda x: x>3, arr)) 
print(filtered_arr)

# 或者通过推导式语句
filtered_arr = [x for x in arr if x > 3]
print(filtered_arr)
```

---

### 过滤数组中的元素

复杂的过滤最好通过 for-in 语句去处理

```python
arr = [1, 3, 2, 5, 4]
filtered_arr = []
for x in arr:
    if x > 3:
        # 比如在这里处理其他逻辑
        print("take", x)
        filtered_arr.append(x)
    else:
        # 比如在这里处理其他逻辑
        print("drop", x)

print(filtered_arr)
```

---

### list 类型的总结

探索更多使用姿势，和其他相似类型，可以看官方文档 

[Sequence Types — list, tuple, range](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range)

---

## Dict 字典（关联数组）

一切 **不可变 (Immutable)** 类型的值都可以做字典的键 key ，比如 int 整型，float 浮点型, tuple 元组, frozenset 固定集合。
而字典的值 value 可以是任意类型的值。

### 创建字典的方法

字面量写法

```python
# 简单的一对花括号即可创建一个空字典
d = {}
print(d)

# 花括号加键值对
d = {
    "int": 1,
    "str": "str",
    "dict": d,
    "list": [],
    0: "int key",
}
print(d)
```

---

### 创建字典的方法

其他写法

```python
# 字典推导式
d = {i:i for i in range(3)}
print(d)

# 或者通过 dict 构建
d = dict()  # 等同 d = {}
d = dict([('int', 1), (0, 'int key')])
print(d)
d = dict(int=1, str='str')
print(d)
```

---

### 字典的常用用法

#### 访问修改字典中的元素

通过中括号加键变量即可访问

```python
cost = {"apple": 30, "banana": 20}
print(cost["apple"])
cost["banana"] = 10
print(cost)
```

通过 `dict.get` 方法访问，还可以配置缺省值

```python
cost = {}
print(cost.get("未知"), cost)
print(cost.get("未知", 100), cost)
```

通过 `dict.setdefault` 方法访问，还可以配置初始值

```python
cost = {}
print(cost.setdefault("未知", 100), cost)
```

---

#### 通过 **for-in** 语句遍历访问

```python
cost = {"apple": 30, "banana": 20}

# 遍历 keys
for k in cost:  # 等同 cost.keys()
    print(k, cost[k])

# 遍历 values
for v in cost.values():
    print(v)

# 遍历键值对 key-value pairs
for k, v in cost.items():
    print(k, v)
```

---

#### 元素分组

比如我们有一组单词数据，需要以字符串长度按特定区间分类。

```python
arr = ["hello", "world", "red", "yellow", "green"]
group = {}
for item in arr:
    strlen = len(item)          # 获取字符串长度
    if strlen not in group:     # 判断 key 是否存在
        group[strlen] = []      # 不存在，则创建空数组
    group[strlen].append(item)  # 给数组添加新元素

print(group)
```

通过使用 `defaultdict` 会更方便些，使用后就不需要判断 key 是否存在，和初始化空数组。

```python
from collections import defaultdict

arr = ["hello", "world", "red", "yellow", "green"]
group = defaultdict(list)
for item in arr:
    strlen = len(item)          # 获取字符串长度
    group[strlen].append(item)  # 给数组添加新元素

print(group)
```

---

### dict 类型的总结

探索更多使用姿势，可以看官方文档 
[Mapping Types — dict](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict)

### 实践

遍历文件 [pythonZen.txt](./assets/pythonZen.txt) 里的每一个单词，来统计单词出现的词频，获取出现最多的前 5 个单词。

```python
# 统计词频
counter = {}
with open("./assets/pythonZen.txt") as f:
    for line in f:                  # 按行遍历文件文本
        for world in line.split():  # 按空白字符分割出单词
            counter[world] = counter.get(world, 0) + 1

# 获取出现最多的前 5 个单词
most = sorted(counter.items(), key=lambda item: -item[1])
print(most[:5])
```

---

## Set 集合

### 创建集合的方法

```python
# 字面量创建，需要注意的一点是，字面量 {} 是 dict 
print({1,})
print({1, 2})

# 集合推导式
print({i for i in range(4)})

# 通过 set 创建
print(set(range(4)))
```

---

### 访问集合中的元素

通过 `set.add` 方法添加新元素

```python
s = set()
s.add('apple')
print(s)
```

通过 `in` 语句来判断元素是否存在

```python
s = {'apple',}
print('apple' in s)
print('apple' not in s)
print('banana' in s)
```

---

### 访问删除集合中的元素

通过 `set.remove` 方法来删除元素

```python
s = {'apple',}
s.remove('apple')
print(s)
```

通过 `set.pop` 方法来随机拿走一个集合中的元素

```python
s = {'apple','banana'}
print(s.pop(), s.pop(), s)
```

---

### 集合的常用用法

#### 元素去重

```python
print(set(["apple", "apple", "banana"]))
```

#### 去重但保留顺序

用一个数组来辅助记录

```python
order = []
seen = set()
for world in ["apple", "apple", "banana"]:
    if world in seen:
        continue
    order.append(world)
    seen.add(world)

print(order)
```

> 这个还可以通过 dict 来做（dict 在3.7之后，是保留插入顺序，即遍历的顺序与插入的相同）
```python
print(dict.fromkeys(["apple", "apple", "banana"]).keys())
```

---

#### 获取差异

通过 `set` 可以快速的比较数据差异。

```python
# 求两个集合的差异，等同 `set.symmetric_difference` 方法
print({1, 2, 3, 4} ^ {3, 4, 5})

# 求交集，等同 `set.intersection` 方法
print({1, 2, 3, 4} & {3, 4, 5})

# 求并集，等同 `set.union` 方法
print({1, 2, 3, 4} | {3, 4, 5})

# 求不存在的集合 A 里，但存在集合 B 的元素
a = {1, 2, 3}
b = {4, }
print(b - a)
```

---

### set 类型的总结

探索更多使用姿势，可以看官方文档 
[Set Types — set, frozenset](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset)

---

# 文本数据解析及导出

常见的数据文件类型为 CSV 和 JSON。

## CSV

其中 CSV 可以被各种表格 App 打开，比如微软家的 Excel ，苹果家的 Numbers 。反过来，它们也支持导出 CSV 格式的文本数据。

现在以计算下面的数据的总价为例子，介绍如何解析 CSV 文本数据。

| Year | Make  | Model                                  | Description                       | Price   |
| ---- | ----- | -------------------------------------- | --------------------------------- | ------- |
| 1997 | Ford  | E350                                   | ac, abs, moon                     | 3000.00 |
| 1999 | Chevy | Venture "Extended Edition"             |                                   | 4900.00 |
| 1999 | Chevy | Venture "Extended Edition, Very Large" |                                   | 5000.00 |
| 1996 | Jeep  | Grand Cherokee                         | MUST SELL! air, moon roof, loaded | 4799.00 |

---

## CSV -- 按行解析成 list

```python
import csv
import decimal

rows = []
header = []
# 解析 CSV，按行解析成 list
with open("./assets/example.csv") as f:
    reader = csv.reader(f)
    header = next(reader)
    rows = list(reader)

# 处理数据
total = 0
exchange_rate = decimal.Decimal('6.47')
print(header)               # 打印表格的行头
for row in rows:
    price = row[4] = decimal.Decimal(row[4]) * exchange_rate # 字符串需转换成 Decimal
    total += price
    print(row, total)
    
# 导出数据成 CSV
with open("./assets/example_output.csv", "w") as f:
    writer = csv.writer(f)
    writer.writerow(header)     # 写入表格的行头
    writer.writerows(rows)
    writer.writerow([''] * 3 + [total])
```

---

## CSV -- 按行解析成 dict

使用 `csv.DictReader` 处理数据会更方便些，还能提高代码的易阅读性

```python
import csv
import decimal

rows = []
header = []
# 解析 CSV，根据行头按行解析成 dict
with open("./assets/example.csv") as f:
    reader = csv.DictReader(f)
    header = reader.fieldnames
    rows = list(reader)

# 处理数据
total = 0
exchange_rate = decimal.Decimal('6.47')
print(header)               # 打印表格的行头
for row in rows:
    price = row['Price'] = decimal.Decimal(row['Price']) * exchange_rate  # 字符串需转换成 Decimal
    total += price
    print(row, total)

# 导出数据成 CSV
with open("./assets/example_output.csv", "w") as f:
    writer = csv.DictWriter(f, fieldnames=header)
    writer.writeheader()     # 写入表格的行头
    writer.writerows(rows)
    writer.writerow({"Price": total})
```

---

## CSV 用 Excel 打开有乱码问题

正常情况下 UTF-8 是不需要带 BOM，就是写在文件头部的这三个字节 `0xEF 0xBB 0xBF`。微软家的 App 会需要这个 BOM 才能正确解析处理 UTF-8 文本。用 Python 打开文件时，选择用 `"utf-8-sig"` 编码即可自动地处理这个问题。 

> 详情见维基百科介绍 [字节顺序标记](https://zh.wikipedia.org/wiki/位元組順序記號)

```python
import csv

header = ['名称', '备注']
rows = [{'名称': '张三', '备注': '法外狂徒'}, {'名称': '李四', '备注': ''}]

with open('./assets/example.utf-8.csv', 'w', encoding='utf-8') as f:
    writer = csv.DictWriter(f, fieldnames=header)
    writer.writeheader()
    writer.writerows(rows)

with open('./assets/example.utf-8-sig.csv', 'w', encoding='utf-8-sig') as f:
    writer = csv.DictWriter(f, fieldnames=header)
    writer.writeheader()
    writer.writerows(rows)

print("done")
```

---

## CSV 用 Excel 打开有乱码问题

通过 `hexdump` 命令查看文件字节，可以看出第二个文件头部只多了个 `0XEFBBBF` 。

~~~bash
echo "**hexdump ./assets/example.utf-8.csv**"
hexdump ./assets/example.utf-8.csv
echo
echo "**hexdump ./assets/example.utf-8-sig.csv**"
hexdump ./assets/example.utf-8-sig.csv
~~~

---

## JSON

JSON 常用来当前后端数据传输的序列化格式。

```json
[
  {
    "Year": "1997",
    "Make": "Ford",
    "Model": "E350",
    "Description": "ac, abs, moon",
    "Price": "3000.00"
  },
  {
    "Year": "1999",
    "Make": "Chevy",
    "Model": "Venture \"Extended Edition\"",
    "Description": "",
    "Price": "4900.00"
  },
]
```

---

### JSON 解析与导出

```python
import json

# 从文件中解析获取
with open("./assets/example.json") as f:
    rows = json.load(fp=f)

print(rows[0])  # 打印 JSON Array 中的第一个 Object，对应 Python 的 dict 类型

# 序列化 Python 数据成 JSON 字符串
# 反过来通过 json.loads 可以解析 JSON 字符串
print(json.dumps(rows[0], indent=2))  
print(json.loads('{"hello": "world"}'))

# 导出到文件
with open("./assets/example_output.json", "w") as f:
    json.dump(obj=rows, fp=f)
```

---

# 实战

获取 v2ex 上当前的热门话题，并输出成 CSV 表格。

https://www.v2ex.com/api/topics/hot.json  

通过命令行查看接口的响应内容

```bash
http https://www.v2ex.com/api/topics/hot.json  | jq -C | less
```

`http` 命令是 [httpie](https://httpie.io/) 一个更易用的类 curl CLI 工具，用来获取服务的接口数据。
`jq` 命令是 [jq](https://stedolan.github.io/jq/) 一个方便易用的 JSON CLI 工具，这里用来美化 JSON 展示。

---

## 工欲善其事，必先利其器

寻找合适的轮子来应对，也是十分重要的。

### HTTP 客户端

- [Requests](https://docs.python-requests.org/en/master/)
    Python 中最出名最常用的 HTTP 客户端库，使用它最大的好处就是遇到问题，绝大部分都能靠搜索引擎解决。
- [HTTPx](https://www.python-httpx.org/)
    新一代的 HTTP 客户端库，有着类 Requests 的调用姿势，支持更多新特性（比如原生协程，HTTP2 支持）

```python
import httpx

def fetch_hot_topics():
    # 访问热门话题接口，获取数据
    r = httpx.get('https://www.v2ex.com/api/topics/hot.json')
    # 返回响应的 JSON 解析结果
    return r.json()
```

---

### 数据提取

- [JSONPath-Extractor](https://github.com/linw1995/jsonpath)
    支持用来查询 JSON 数据的 QL 引擎
- [Data-Extractor](https://github.com/linw1995/data_extractor)
    支持结构化数据提取的轮子

#### JSONPath 通过 CLI 查询例子

```bash
# 获取最新数据
# http GET https://www.v2ex.com/api/topics/hot.json > ./assets/hot.json

jp '$[*].title' -f ./assets/hot.json
```

---

### 数据提取

#### DataExtractor 提取数据

```python
from pprint import pprint

import httpx
from data_extractor import Item, Field as F, JSONExtractor as J

def fetch_hot_topics():
    # 访问热门话题接口，获取数据
    r = httpx.get('https://www.v2ex.com/api/topics/hot.json')
    # 返回响应的 JSON 解析结果
    return r.json()

class Topic(Item):
    title = F(J("title"))
    url = F(J("url"))
    author = F(J("member.username"))
    topic_name = F(J("node.title"))

pprint(Topic(J("$[*]"), is_many=True).extract(fetch_hot_topics()))
```

---

### 数据导出 -- 小作业

通过本讲的这些知识点，你们能把 v2ex 的热门话题输出成 CSV 表格吗？

按以下需求，分别输出两份文件

1. 按话题名称排序
2. 根据最后更新时间从近到旧排序

---

# 推荐阅读

本讲从处理数据的角度出发，略过了大量的 Python 知识，推荐想深入学习的伙伴们阅读以下内容

## Python Cookbook 第三版

有大量的实践案例，十分适合当作工具书

- 1 数据结构和算法
- 2 字符串和文本
- 3 数字、日期和时间
- 5.1 读写文本数据
- 6.1 读写 CSV 数据
- 6.2 读写 JSON 数据

## [Python编程快速上手](https://automatetheboringstuff.com/)

阅读这本书并实践，能较快速地上手 Python 编程。（有中文版书籍)

## 善用搜索引擎

Python 有着大量的用户基数，有什么问题可以先搜一搜，一般都能找到答案。

---

# 谢谢大家
