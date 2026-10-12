# 第一到六课综合复习测验

## 第一部分:预测输出

### 第 4 题:函数返回值与作用域

```python
def f(x):
    x = x + 1

def g(lst):
    lst.append(0)

n = 5
f(n)
print(n)        # 5
nums = [1, 2]
g(nums)
print(nums)     # [1, 2, 0]
print(f(3))     # None
```

**我的疑问**:为什么 `print(f(3))` 是 None?

`f` 没有 `return`,所以返回 `None`。`x = x + 1` 只修改了局部变量,既不影响外部的 `n`,也没有把结果交出去。

### 第 5 题:继承

```python
class A:
    def __init__(self):
        self.v = 1
    def show(self):
        return f"A:{self.v}"

class B(A):
    def __init__(self):
        super().__init__()
        self.v = self.v + 10
    def show(self):
        return "B+" + super().show()

print(B().show())          # B+A:11
print(isinstance(B(), A))  # True
```

**我的疑问**:为什么是 `B+A:11` 而不是 `B+A:1`?

1. `B()` → `super().__init__()` 设置 `v = 1` → 再加 10,`v = 11`
2. `B.show` 调用 `super().show()`,执行的是 **A 的代码**,但 `self` 仍是**这个 B 对象**,其 `v` 已是 11

> `super()` 只决定用哪个类的方法,不会换掉 `self`。

## 第二部分:写代码

### 第 6 题:降雨量

(a)(b) ✅ 正确

(c) ❌ 用 for 循环逐个 `print(i)`,题目要求得到列表

```python
[i for i, x in enumerate(rain) if x > 0]    # [1, 3, 4, 5]
```

### 第 7 题:月份温度

(a)(b) ✅ 正确

(c) ❌ 未完成

```python
for m in sorted(dic, key=lambda m: dic[m]):
    print(f"{m}: {dic[m]}")
```

### 第 8 题:to_celsius

❌ **我的答案**

```python
def to_celsius(values, unit="F"):
    try:
        if unit == "F":
            return((round(values) - 32) * 5 / 9
        else unit == "C":
            return round(values))
    except ValueError
    continue
```

🔍 **问题**
- `values` 是列表,`round(values)` 对整个列表操作(R 的向量化习惯)
- `continue` 只能用在循环里
- `except` 后缺冒号;`else` 不能带条件,应为 `elif`
- `return` 写在处理过程中,只会处理第一个就退出

✅ **正确写法**

```python
def to_celsius(values, unit="F"):
    if unit not in ("F", "C"):
        raise ValueError("unit 只能是 F 或 C")
    result = []
    for v in values:
        try:
            x = float(v)
        except ValueError:
            continue
        if unit == "F":
            x = (x - 32) * 5 / 9
        result.append(round(x, 1))
    return result
```

### 第 9 题:天气计数 + 文件

❌ **我的答案**

```python
counts = []                              # 应为 {}
for i in events:
    counts[a] = events.get[i, 0] + 1     # a 未定义;get 用在了列表上;方括号应为圆括号
```

✅ **正确写法**

```python
counts = {}
for e in events:
    counts[e] = counts.get(e, 0) + 1

with open("weather.txt", "w", encoding="utf-8") as f:
    for e in sorted(counts, key=lambda e: counts[e], reverse=True):
        f.write(f"{e},{counts[e]}\n")

with open("weather.txt", "r", encoding="utf-8") as f:
    lines = [line.strip() for line in f]
print(lines)      # ['rain,3', 'sun,3', 'fog,1']
```

## 第三部分:综合题

### 第 10 题:Station / RainStation

❌ **我的答案中的问题**
- `skip` 不是关键字,应为 `pass`
- `sel.readings` 拼写错误
- `return "None"` 返回字符串,应为 `None`
- `self.readings / len(...)`:列表不能直接除
- `len(self.readings > 0)`:列表不能直接与 0 比较
- `summary` 中重复计算平均值,应调用 `self.mean()`
- 子类漏写 `def summary(self):`

✅ **正确写法**

```python
class Station:
    def __init__(self, name):
        self.name = name
        self.readings = []

    def add(self, value):
        try:
            self.readings.append(float(value))
        except ValueError:
            pass

    def mean(self):
        if not self.readings:
            return None
        return sum(self.readings) / len(self.readings)

    def summary(self):
        return f"{self.name}: n={len(self.readings)}, mean={round(self.mean(), 1)}"


class RainStation(Station):
    def summary(self):
        rainy = len([r for r in self.readings if r > 0])
        return super().summary() + f", rainy_days={rainy}"
```

## 本次测验最重要的发现:R 的向量化习惯

```python
round(values)               # 第 8 题
self.readings / len(...)    # 第 10 题
self.readings > 0           # 第 10 题
```

这些写法在 R 里都正确,因为 R 默认向量化。**Python 的列表不会向量化**,逐元素操作必须用循环或推导式。

而这正是 NumPy 的价值:**NumPy 数组把向量化带回了 Python**。

```python
import numpy as np
r = np.array([0, 3.5, 12.0, 0, 0.8])
(r > 0).sum()       # 3
r.mean()            # 3.26
```

> Python 列表:逐元素操作写循环 / 推导式
> NumPy 数组:运算自动逐元素进行(和 R 一样)
