# 第四课:函数

## 知识要点

### 必须显式 return

```python
def f(x):
    x + 1          # 没有 return

print(f(5))        # None  ← R 会自动返回最后一个表达式,Python 不会
```

### 参数

```python
def power(base, exponent=2):    # 有默认值的参数放后面
    return base ** exponent

power(3)                # 位置参数
power(exponent=3, base=2)   # 关键字参数,顺序可颠倒
power(2, exponent=3)    # 混用时位置参数在前
```

**默认参数不要用可变对象**:

```python
def add_item(item, lst=[]):     # ⚠️ 默认列表只创建一次,会在多次调用间共享
    ...

def add_item(item, lst=None):   # ✅ 正确做法
    if lst is None:
        lst = []
    ...
```

### 返回多个值 = 返回 tuple

```python
def min_max(nums):
    return min(nums), max(nums)

low, high = min_max([3, 1, 4])    # 解包
```

### `*args` 与 `**kwargs`

```python
def total(*args):        # args 是 tuple
    return sum(args)

def show(**kwargs):      # kwargs 是 dict
    for k, v in kwargs.items():
        print(k, v)
```

### 作用域:重新赋值 vs 原地修改

```python
x = 10
def change():
    x = 999          # 创建了局部变量,不影响外部
change()
print(x)             # 10

lst = [1, 2, 3]
def append_item(l):
    l.append(99)     # 原地修改传入的可变对象 → 影响外部
append_item(lst)
print(lst)           # [1, 2, 3, 99]
```

### 函数是一等对象

```python
f = square           # 不加括号 = 引用函数本身
f(5)                 # 加括号 = 调用
```

## 小测验与我的答案

### 第 1 题:`is_even(n)` 返回是否为偶数

❌ **我的答案**

```python
def is_even(n):
    if n/2 == int(n/2):
        print("True")
    else:
        print("False")
```

🔍 **问题**
- 用了 `print`,没有 `return`:函数实际返回 `None`,结果无法被后续代码使用
- `"True"` 是字符串,不是布尔值 `True`

✅ **正确写法**

```python
def is_even(n):
    return n % 2 == 0
```

### 第 2 题:带默认参数的问候语

❌ **我的答案**

```python
def make_greetings(name, greeting="你好"):
    return(name, greeting)
```

🔍 **问题**
- 返回的是 tuple `("小明", "你好")`,不是拼接好的句子
- `return` 不是函数,不需要括号

✅ **正确写法**

```python
def make_greetings(name, greeting="你好"):
    return f"{greeting},{name}!"
```

### 第 3 题:同时返回和、最大值、最小值

✅ **我的答案(正确)**

```python
def stats(nums):
    return sum(nums), max(nums), min(nums)

total, mx, mn = stats([3, 1, 4, 1, 5, 9])
```

### 第 4 题:统计正数个数

❌ **我的答案**

```python
def count_positive(nums):
    n = 0
    for i in nums:
        if i > 0:
            n += 1
        print(n)         # 缩进在 for 里面
```

🔍 **问题**
- `print(n)` 缩进在循环内,每轮都打印一次中间值
- 应该 `return`,且位置要和 `for` 对齐(循环结束后)

✅ **正确写法**

```python
def count_positive(nums):
    n = 0
    for i in nums:
        if i > 0:
            n += 1
    return n
```

### 第 5 题:预测 `my_list` 的结果

```python
def add_stuff(lst):
    lst.append(100)
    lst = [999]
    lst.append(888)

my_list = [1, 2, 3]
add_stuff(my_list)
print(my_list)
```

❓ **我的答案**:不会

✅ **答案**:`[1, 2, 3, 100]`

- `lst.append(100)`:`lst` 与 `my_list` 指向同一对象,原地修改 → 外部可见
- `lst = [999]`:**重新绑定**局部变量到新列表,与 `my_list` 断开
- `lst.append(888)`:修改的是新列表,与 `my_list` 无关

> **原地修改(`.append`)影响外部;重新赋值(`=`)只改局部名字。**

## 本课教训

1. **`print` 是给人看的,`return` 是给程序用的**:函数要产出结果就用 `return`
2. **缩进决定归属**:`return` 放在循环内还是循环外,行为完全不同
