# 第七课:NumPy 基础 —— ndarray

## 知识要点

### ndarray 与 list 的根本区别

```python
lst = [1, 2, 3]
arr = np.array([1, 2, 3])

lst * 2      # [1, 2, 3, 1, 2, 3]   列表:重复
arr * 2      # array([2, 4, 6])     数组:逐元素运算(同 R)
lst + 1      # TypeError
arr + 1      # array([2, 3, 4])
```

| | list | ndarray |
|---|---|---|
| 元素类型 | 可混合 | 必须统一(同 R 的原子向量) |
| 运算 | 不向量化 | 逐元素向量化 |
| 维度 | 嵌套列表模拟 | 原生多维 |
| 速度 | 慢 | 快(底层 C) |

> 第六课综合测验里写不出来的 `round(values)`、`x > 0`,在 NumPy 里都成立。

### 创建数组

```python
np.array([[1, 2, 3], [4, 5, 6]])   # 嵌套列表,每个内层列表是一行
np.zeros((2, 3))                   # 形状用元组
np.ones((2, 3)); np.full((2, 3), 7); np.eye(3)

np.arange(2, 10, 2)        # [2 4 6 8]       左闭右开,≈ seq(2, 8, by = 2)
np.linspace(0, 1, 5)       # 含终点,≈ seq(0, 1, length.out = 5)

rng = np.random.default_rng(42)
rng.random(3); rng.normal(0, 1, size=(2, 3)); rng.integers(1, 7, size=5)
```

浮点步长用 `linspace`,`arange` 可能因舍入误差多一个或少一个点。

### dtype

- 数组元素类型统一,混合时自动向上转换:`[1, 2.5]` → float,`[1, "a"]` → 全部变字符串
- `astype(int)` 转整数是**向 0 截断**:`[1.9, -1.9]` → `[1, -1]`
- `np.nan` 是浮点数:`np.array([1, 2, np.nan]).dtype` 为 `float64`;R 的 `NA` 可放进任何类型,NumPy 的 `nan` 不行
- `np.isnan()` ≈ `is.na()`

### 形状属性

```python
b.shape   # (2, 3)  ≈ dim()
b.ndim    # 2
b.size    # 6       ≈ length()
```

### reshape:默认按行填充(与 R 相反)

```python
np.arange(6).reshape(2, 3)
# [[0 1 2]
#  [3 4 5]]                 NumPy:按行(C 顺序)

np.arange(6).reshape(2, 3, order="F")
# [[0 2 4]
#  [1 3 5]]                 按列,等同 R 的 matrix(0:5, nrow = 2)
```

### `(n,)`、`(1, n)`、`(n, 1)` 是三种不同形状

```python
v = np.array([1, 2, 3])
v.shape                 # (3,)    一维,既不是行也不是列
v.reshape(1, 3).shape   # (1, 3)  行向量
v.reshape(3, 1).shape   # (3, 1)  列向量
v.T.shape               # (3,)    一维数组没有转置
```

### 向量化运算

```python
x = np.array([1.0, 4.0, 9.0])
x + 10; x * x; np.sqrt(x); np.log(x); x > 3
```

数学函数用 `np.` 版本,内置的 `round`、`math.sqrt` 不能处理数组。

## 本课三个预测的答案

| 代码 | 结果 | 原因 |
|---|---|---|
| `np.array([1.9, -1.9]).astype(int)` | `[1, -1]` | 向 0 截断,不是向下取整 |
| `np.array([1, 2, np.nan]).dtype` | `float64` | `nan` 是浮点数,整数被向上转换 |
| `np.array([1, 2, 3]).T.shape` | `(3,)` | 一维数组转置不变 |

## 我问过的问题

### `a.reshape(3, -1)` 为什么是 (3, 2)?

`np.arange(6)` 有 6 个元素,reshape 要求**元素总数不变**。`-1` 表示"这一维由 NumPy 计算":6 ÷ 3 = 2。

```python
a.reshape(3, -1)    # (3, 2)
a.reshape(-1, 3)    # (2, 3)
a.reshape(4, -1)    # ValueError:6 不能被 4 整除
```

一次只能有一个 `-1`。

### 那为什么 `a.reshape(-1)` 直接是一维?

**括号里写了几个数,结果就有几维**,`-1` 不改变维数,只决定那一维的大小。`reshape(-1)` 只给了一个维度,所以是一维 `(6,)`,效果是**展平**。

```python
m = np.arange(6).reshape(2, 3)
m.reshape(-1)    # [0 1 2 3 4 5]  按行展开
m.ravel()        # 同上
m.flatten()      # 同上,总是返回副本
```

R 的 `as.vector(m)` 按列展开,得到 `0 3 1 4 2 5`,顺序与 NumPy 相反。

### `(r > 0).sum()` 为什么是 3?

`r > 0` 得到的是布尔数组,已经不含降雨量本身;`.sum()` 把 True 记为 1,所以是**计数**。

```python
r = np.array([0, 3.5, 12.0, 0, 0.8])
r > 0               # [False  True  True False  True]
(r > 0).sum()       # 3     满足条件的个数(天数)
(r > 0).mean()      # 0.6   比例
r[r > 0].sum()      # 16.3  满足条件的值之和
```

与 R 一致:`sum(r > 0)` 是计数,`sum(r[r > 0])` 是求和。括号不能省,`r > 0.sum()` 会报 `SyntaxError`。

## 本课要点

> - ndarray:类型统一 + 逐元素向量化,接近 R 的向量 / 矩阵
> - `arange` 左闭右开,`linspace` 含终点;随机数用 `default_rng`
> - `astype(int)` 向 0 截断;`np.nan` 只存在于浮点数组
> - **reshape 默认按行填充**(≠ R);`-1` 自动推算;`reshape(-1)` 展平
> - `(n,)`、`(1, n)`、`(n, 1)` 不同,一维数组无转置
> - 布尔数组 `.sum()` 是计数,`.mean()` 是比例
