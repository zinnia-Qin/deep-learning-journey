# 我的常见错误汇总

从第一到第六课的练习中整理,按出现频率和类别排列。写代码前过一遍,写完后对照检查。

## 一、R 思维习惯(最需要警惕)

| R 写法 / 直觉 | Python 正确做法 | 出现位置 |
|---|---|---|
| `length(x)` | `len(x)` | 第 5 课 SpeciesRecord |
| `mean(x)` | `sum(x) / len(x)`(或 NumPy 的 `np.mean`) | 第 6 课 safe_mean |
| `1:length(x)` 写循环 | `for x in lst` 或 `range(len(lst))` | 第 4 课 |
| `a:b` 表示序列 | 冒号在 `{}` 里是"键: 值";序列用 `range()` | 第 6 课字典推导式 |
| 对整个向量直接运算 `round(x)`、`x / n`、`x > 0` | 列表不向量化,用推导式;或改用 NumPy 数组 | 综合复习第 8、10 题 |
| 索引从 1 开始 | 从 0 开始,`x[-1]` 是最后一个 | 第 2 课 |
| `TRUE` / `FALSE` | `True` / `False` | 第 1 课 |
| `b <- a` 后改 b 不影响 a | list 赋值是引用,复制用 `.copy()` | 第 2、4 课 |

## 二、函数与返回值

- **用 `print` 代替 `return`**:函数实际返回 `None`,结果无法被使用(第 4 课第 1、4 题)
- **忘写 `return`**:Python 没有隐式返回(综合复习第 4 题)
- **返回类型不对**:用 f-string 返回了 `"2"` 而不是数字 `2`;返回了 tuple 而不是拼接的字符串(第 4、5 课)
- **区分原地修改与重新赋值**:`lst.append()` 影响外部,`lst = [...]` 只改局部(第 4 课第 5 题)

## 三、类

- **方法定义漏 `self`**:报 `takes 1 positional argument but 2 were given`(第 5 课,出现 3 次)
- **方法内访问属性漏 `self.`**:报 `NameError`
- **把固定初值写成 `__init__` 参数**:与调用处 `Counter()`、`Square(3)`、`Doubler()` 不一致(第 5 课,出现 4 次)
- **属性与方法同名**:`self.area = 0` 会覆盖 `area()` 方法
- **`self.training` 单独一行不是赋值**,要写 `self.training = True`
- **super 调用错方法或漏传参数**:扩展 `log` 时应 `super().log(msg)`,不是 `super().__init__(msg)`

## 四、字典(第 6 课的核心难点)

- **遍历字典、`sorted(字典)` 得到的是键**,不是值,也不是键值对
- **按值排序**:`sorted(d, key=lambda k: d[k])`,而不是 `lambda k: k[1]`
- **计数**:`d[k] = d.get(k, 0) + 1`;`.get` 是字典的方法,列表没有
- **`d[k] += 1` 在键不存在时报 `KeyError`**
- **计数容器用 `{}`,不是 `[]` 或 `0`**
- **用键取值**:`zip(names, d)` 拿不到值,要 `d[name]`

## 五、推导式与内置函数

- **条件写在当前元素上**:`if c > 10`,不是 `if counts > 10`
- **筛选的 `if` 放末尾,变换的 `if-else` 放前面**
- **`int()` 是截断,四舍五入用 `round()`**
- **`sorted()` 返回新列表,要用变量接住**;`lst.sort()` 返回 `None`

## 六、语法细节

- 一条语句占一行;`.` 不是语句分隔符
- `else` 不能带条件,带条件用 `elif`
- `except`、`def`、`if`、`for` 末尾都要冒号
- "什么都不做"用 `pass`(不是 `skip`)
- `continue` 只能出现在循环里
- 大小写和拼写:`Timelogger` / `TimeLogger`、`sef`、`most_commom`、`w` / `W`

## 七、调试习惯

1. **原样运行题目给的测试代码**,对照预期输出
2. **报错先读最后一行**,它通常已经说明了原因
3. 不确定类型时 `print(type(x))`;查看对象属性用 `obj.__dict__`
4. 修改了导入的 `.py` 文件后**重启内核**
