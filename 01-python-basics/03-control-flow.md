# 第三课:控制流 if / for / while

## 知识要点

### 缩进定义代码块(语法,不是风格)

```python
if x > 0:               # 末尾冒号
    print("正")          # 缩进 4 个空格 = 属于 if
print("不属于 if")       # 未缩进 = 在 if 外
```

- 没有 R 的 `{}`
- 同一代码块缩进必须一致,混用空格和 Tab 会报 `IndentationError`

### if / elif / else

```python
if x > 10:
    ...
elif x > 5:      # 不是 else if
    ...
else:
    ...
```

### 逻辑运算用英文单词

| R | Python |
|---|---|
| `&&` | `and` |
| `\|\|` | `or` |
| `!` | `not` |

支持链式比较:`0 < x < 10`(R 不支持)。

### for 循环遍历的是元素

```python
for fruit in ["apple", "banana"]:      # 直接拿到元素
    print(fruit)

for i in range(5):                     # 0,1,2,3,4(从 0,左闭右开)
    ...

for i, fruit in enumerate(fruits):     # 同时要索引和元素
    ...

for key, value in person.items():      # 遍历字典的键值对
    ...
```

| R | Python |
|---|---|
| `1:5` → 1,2,3,4,5 | `range(5)` → 0,1,2,3,4 |
| `seq(2, 8, by = 2)` | `range(2, 10, 2)` |

### while、增量赋值、break / continue

```python
count = 0
while count < 3:
    count += 1       # R 没有 +=

for i in range(10):
    if i == 5:
        break        # 跳出循环
    if i == 2:
        continue     # 跳过本次
```

## 练习

本课练习均已完成并跑通。

### 综合示例:统计正数、负数、零

```python
nums = [3, -1, 0, 5, -2, 0, 8]
positive = negative = zero = 0
for n in nums:
    if n > 0:
        positive += 1
    elif n < 0:
        negative += 1
    else:
        zero += 1
print(f"正数:{positive},负数:{negative},零:{zero}")
```
