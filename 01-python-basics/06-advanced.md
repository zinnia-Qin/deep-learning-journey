# 第六课:进阶特性

## 知识要点

### 列表推导式

```python
[x ** 2 for x in range(5)]                       # 变换
[x for x in range(10) if x % 2 == 0]             # 筛选:if 放末尾
["正" if x > 0 else "非正" for x in nums]          # 逐个变换:if-else 放前面

{w: len(w) for w in words}                       # 字典推导式
{len(w) for w in words}                          # 集合推导式
```

逻辑复杂时用普通 for 循环,可读性更好。

### lambda 匿名函数

```python
square = lambda x: x ** 2       # 冒号左边是参数,右边是返回值
```

主要用途是作为 `key` 参数传入。

### 常用内置函数

```python
sorted(lst)                                   # 返回新列表
sorted(lst, reverse=True)
sorted(students, key=lambda s: s[1])          # 按自定义依据排序
lst.sort()                                    # 原地排序,返回 None!

for name, score in zip(names, scores): ...    # 多个序列按位置配对
for i, name in enumerate(names, start=1): ... # 给一个序列配上索引

any(x < 0 for x in nums)
all(x > 0 for x in nums)
max(students, key=lambda s: s[1])             # min / max 也支持 key
```

### 异常处理

```python
try:
    x = float(v)
except ValueError as e:      # 捕获具体类型,不要用裸 except
    ...
else:
    ...                      # 没出错时执行
finally:
    ...                      # 总会执行

raise ValueError("x 不能为负数")    # 主动抛出,类似 R 的 stop()
```

### 模块

- 任何 `.py` 文件都是模块,可以 `import`
- 模块导入后会被缓存,**修改 `.py` 文件后要重启内核**

### 文件读写

```python
with open("notes.txt", "w", encoding="utf-8") as f:     # 结束时自动关闭
    f.write("第一行\n")

with open("notes.txt", "r", encoding="utf-8") as f:
    lines = [line.strip() for line in f]
```

## 我问过的问题

### `lambda s: s[1]` 中的 `s: s[1]` 是什么意思?

`lambda s: s[1]` 整体是一个函数:`s` 是参数,冒号分隔,`s[1]` 是返回值。等价于:

```python
def get_score(s):
    return s[1]
```

`sorted(students, key=...)` 会把每个元素依次传给它,用返回值作为排序依据,但**排列的仍是原来的元素**。

### `s` 没有事先定义,为什么能用?

`lambda s:` 本身就在**定义**参数 `s`,和 `def f(s):` 一样。值是在调用时由 `sorted` 逐个传入的。`for x in ...`、推导式里的变量也是就地声明的。

名字可以随意取,`lambda name: a[name]` 完全等价;作用范围越小,名字可以越短。

### zip 与 enumerate 的区别

| | enumerate | zip |
|---|---|---|
| 输入 | 一个序列 | 多个序列 |
| 每次给出 | (索引, 元素) | (序列1元素, 序列2元素, ...) |
| 用途 | 需要"第几个" | 需要"一一对应" |

`enumerate(x)` 等价于 `zip(range(len(x)), x)`。可组合使用:`for i, (a, b) in enumerate(zip(x, y))`。

### `{sp: counts[sp] for sp in counts_order}` 我总以为是"从 sp 到 counts[sp]"

这是 **R 的习惯**:R 里 `a:b` 表示序列。**Python 的冒号没有这个含义。**

在花括号里,冒号分隔**键和值**。这一行等价于:

```python
ordered = {}
for sp in counts_order:
    ordered[sp] = counts[sp]
```

Python 中冒号的几种含义:

| 位置 | 含义 | 例子 |
|---|---|---|
| 行末 | 开始代码块 | `if x > 0:` |
| `{}` 内 | 键: 值 | `{"a": 1}` |
| lambda 后 | 参数: 返回值 | `lambda s: d[s]` |
| `[]` 内 | 切片 | `lst[1:3]` |

生成整数序列用 `range(a, b)`,不是冒号。

### `sorted(a, key=lambda s: a[s])` 到底排了什么?

对字典 `a` 的**键**排序;每个键 `s` 通过 `a[s]` 查出数值作为排序依据;返回**按依据排好的键列表**。`a[s]` 只用于比较,不出现在结果里,原字典不变。

## 小测验一与我的答案

### 第 1 题:温度列表

❌ **我的答案**

```python
print([int(x) for x in temps]) print(["warm" if x>0 else "cold" for x in temps])
```

🔍 **问题**
- (a) 筛选正值未写
- (b) `int()` 是截断,不是四舍五入,应用 `round()`(`round(12.5)` 为 12,银行家舍入)
- 两条语句写在同一行,会报 `SyntaxError`
- (c) ✅ 正确

✅ **正确写法**

```python
print([x for x in temps if x > 0])
print([round(x) for x in temps])
print(["warm" if x > 0 else "cold" for x in temps])
```

### 第 2–5 题

❌ **我的答案中的典型错误**

```python
dict(zip(species, counts)) if counts > 10 else 0 for x in counts   # 条件写在整个列表上
sorted(a, key=lambda a: a[1], reverse=True)    # 遍历字典得到键,a[1] 是名字的第 2 个字
return mean(values)                            # Python 没有 mean()
if self.species in ...                         # 应该用参数 species
def save(filename):                            # 漏 self
```

🔍 **共同问题**
1. 一条语句占一行;`.` 不是语句分隔符
2. 推导式里的条件要写在**当前元素**上,不是整个列表
3. 遍历字典、`sorted(字典)` 拿到的是**键**;按值排序要用 `lambda k: d[k]`
4. 平均值用 `sum(x) / len(x)`

✅ **正确写法**

```python
# 第 2 题
a = {s: c for s, c in zip(species, counts)}
b = {s: c for s, c in zip(species, counts) if c > 10}

# 第 3 题
ranked = sorted(a, key=lambda s: a[s], reverse=True)
for i, name in enumerate(ranked, start=1):
    print(f"第{i}名:{name}({a[name]})")

# 第 4 题
def safe_mean(values):
    valid = []
    for v in values:
        try:
            valid.append(float(v))
        except ValueError:
            continue
    if not valid:
        raise ValueError("没有有效数值")
    return sum(valid) / len(valid)

# 第 5 题
class PlotSurvey:
    def __init__(self, plot):
        self.plot = plot
        self.records = {}

    def add(self, species, n):
        self.records[species] = self.records.get(species, 0) + n

    def top(self, k):
        ranked = sorted(self.records, key=lambda s: self.records[s], reverse=True)
        return ranked[:k]

    def save(self, filename):
        with open(filename, "w", encoding="utf-8") as f:
            for sp, n in self.records.items():
                f.write(f"{sp},{n}\n")
```

## 小测验二与我的答案(针对性巩固)

| 题 | 内容 | 结果 |
|---|---|---|
| 1 | 推导式筛选 | ✅ |
| 2 | 推导式变换 `.upper()` | ✅ |
| 3 | zip → 字典 + 筛选 | ✅ 可简化 |
| 4 | 按值排序字典 | ✅ |
| 5 | 字典计数 | ❌ |
| 6 | try/except 跳过坏数据 | ✅ |
| 7 | 计数 + 排序 + 打印 | ❌ 最后一步 |
| 8 | Tally 类 | ❌ |

### 第 3 题(可简化)

```python
dicbig = {s: a for s, a in zip(sites, areas) if dic[s] < 200}    # 我的写法,绕了一圈
dicbig = {s: a for s, a in zip(sites, areas) if a < 200}         # a 就是当前面积
```

### 第 5 题:字典计数

❌ **我的答案**

```python
for sp in observations:
    zip(sp, observations.get(sp))
```

🔍 不需要 `zip`;`.get` 是字典的方法,列表没有;缺少存结果的字典

✅ **正确写法**

```python
counts = {}
for sp in observations:
    counts[sp] = counts.get(sp, 0) + 1
```

### 第 7 题:最后一步

❌ **我的答案**

```python
a = {s: c for s, c in zip(counts_order, counts)}
# 结果:{'麻栎': '麻栎', '青冈': '青冈', '枫香': '枫香'}
```

🔍 遍历字典得到的是键,`zip` 配对的两边都是物种名,次数没有参与

✅ **正确写法**

```python
for sp in counts_order:
    print(f"{sp}: {counts[sp]}")
```

### 第 8 题:Tally

❌ **我的答案**

```python
class Tally:
    def __init__(self):
        self.counts = 0                         # 应为 {}
    def add(self, item):
        self.counts[item] += 1                  # 键不存在时 KeyError
    def most_commom(self):                      # 拼写错误
        return max(self.counts, key=lambda s:)  # lambda 未写完
```

✅ **正确写法**

```python
class Tally:
    def __init__(self):
        self.counts = {}
    def add(self, item):
        self.counts[item] = self.counts.get(item, 0) + 1
    def most_common(self):
        return max(self.counts, key=lambda s: self.counts[s])
```

## 本课教训

**字典的键是入口,值要通过 `字典[键]` 去取。** 排序时 `key=lambda s: d[s]`、打印时 `d[sp]`、找最大值时 `max(d, key=lambda s: d[s])`,都是同一个动作。

需要记住的四个模式:

```python
{k: v for k, v in zip(keys, vals) if 条件}          # 配对 + 筛选成字典
sorted(d, key=lambda k: d[k], reverse=True)          # 按值排序
try: ... except ValueError: continue                 # 跳过坏数据
d[k] = d.get(k, 0) + 1                               # 累加计数
```
