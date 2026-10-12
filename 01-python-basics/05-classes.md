# 第五课:类、对象与继承

## 知识要点

### 基本结构

```python
class Dog:
    def __init__(self, name, age):     # 构造方法,创建对象时自动调用
        self.name = name               # 实例属性
        self.age = age

    def bark(self):                    # 方法,第一个参数必须是 self
        return f"{self.name}: 汪汪!"

dog1 = Dog("旺财", 3)    # 不传 self,Python 自动传入
dog1.name                # 属性:不加括号
dog1.bark()              # 方法:加括号
```

| 术语 | 含义 |
|---|---|
| 类 | 模板,定义有哪些数据和操作 |
| 对象 / 实例 | 根据模板创建的具体个体,可创建多个,互相独立 |
| 属性 | 对象的数据,`self.xxx` |
| 方法 | 对象的操作,类里定义的函数 |

### self 的机制

```python
dog1.bark()
# Python 实际执行:Dog.bark(dog1)  → 方法里的 self 就是 dog1
```

所以方法定义漏了 `self` 时,会报 `takes 1 positional argument but 2 were given`。

调试工具:`id(obj)` 查看是否同一对象;`obj.__dict__` 查看对象的全部属性。

### 继承

```python
class Animal:
    def __init__(self, name):
        self.name = name
    def describe(self):
        return f"{self.name} 是一只动物"
    def speak(self):
        return "..."

class Dog(Animal):                      # 继承
    def __init__(self, name, breed):
        super().__init__(name)          # 先执行父类的初始化
        self.breed = breed              # 再加子类自己的属性
    def speak(self):                    # 重写(override)
        return f"{self.name}: 汪汪"
```

- 方法查找顺序:子类 → 父类 → … → `object`(`Dog.__mro__`)
- `isinstance(dog, Animal)` 为 True;`type(dog) == Animal` 为 False
- 多态:同一调用 `a.speak()`,不同子类执行不同版本

### `__call__`

定义后对象可以像函数一样调用:`m(5)` 实际执行 `m.__call__(5)`。

### 对应 PyTorch

```python
class MyModel(nn.Module):              # 继承
    def __init__(self):
        super().__init__()             # 必须,漏掉会报错
        self.fc = nn.Linear(10, 1)     # 子类属性
    def forward(self, x):              # 重写
        return self.fc(x)

output = model(x)                      # 通过 __call__ 调用 forward
```

## 我问过的问题

### "调用者不需要提供的东西,不放进参数"是什么意思?谁是调用者?

**调用者 = 使用这个类的那一行代码**,例如 `s = Square(3)`。

`__init__` 的参数列表声明的是"创建对象时外部必须提供哪些信息",调用处传入的值按位置依次填入。

| 信息 | 谁决定 | 处理方式 |
|---|---|---|
| 正方形的边长 | 外部,每个不同 | 做参数 |
| "正方形"这个名字 | 类本身 | 在 `__init__` 内写死 |
| 计数器初始值 0 | 类本身 | `self.count = 0` |
| 物种列表初始为空 | 类本身 | `self.species = []` |

**参数列表必须与调用处一致**:测试写 `Doubler()`,那 `__init__` 除 `self` 外就不能有必需参数。

### 什么时候用 super?

子类重写某个方法时,问一句:**还需要父类版本做的事吗?**

| 情况 | 写法 | 用 super? |
|---|---|---|
| 不改动 | 子类不写该方法 | 否,自动继承 |
| 完全替换 | 重写,父类逻辑不需要 | 否 |
| 在父类基础上扩展 | 重写,但仍需父类先做它的事 | **是** |

实用规则:**子类写了 `__init__`,第一行几乎总是 `super().__init__(...)`**。

## 小测验与我的答案(类与对象)

### 第 1 题:Student

✅ **我的答案(正确)**

```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    def introduce(self):
        return f"我是{self.name},分数{self.score}"
```

### 第 2 题:Counter

❌ **我的答案**

```python
class Counter:
    def __init__(self, count):     # 要求传 count,但测试是 Counter()
        self.count = count
```

🔍 计数从 0 开始是类自己决定的,不该做参数。

✅ `def __init__(self): self.count = 0`

### 第 3 题:两个计数器是否独立

✅ **我的答案**:a 是 5,b 是 2,二者独立。每次 `Counter()` 都创建新对象,`self.count` 是各自的实例属性。

### 第 4 题:SpeciesRecord

❌ **我的答案(第一版)**

```python
class SpeciesRecord:
    def __init__(self, site, species):     # species 不该是参数
        self.site = site
        self.species = species
    def add(name):                         # 漏了 self
        species.append(name)               # 漏了 self.;没有去重
    def summary(self):
        return f"{self.site}:共{length(sef.species)}个物种"   # length 是 R 的写法;sef 拼写错误
```

❌ **我的答案(第二版,能运行但有问题)**

```python
def add(self, name):
    if name in self.species:
        self.species              # 空操作
    else:
        self.species.append(name)

def richness(self):
    return f"{len(self.species)}"     # 返回的是字符串,不是数字
```

✅ **正确写法**

```python
class SpeciesRecord:
    def __init__(self, site):
        self.site = site
        self.species = []

    def add(self, name):
        if name not in self.species:
            self.species.append(name)

    def richness(self):
        return len(self.species)

    def summary(self):
        return f"{self.site}:共{len(self.species)}个物种"
```

### 第 5 题:LinearLayer

❌ **我的答案**:`def forward(x):`、`def update(dw, db, lr):` 都漏了 `self`

🔍 报错 `forward() takes 1 positional argument but 2 were given`:调用 `layer.forward(3)` 时 Python 会把 `layer` 自动作为第一个参数传入。

✅ `def forward(self, x):`、`def update(self, dw, db, lr):`

补充:`forward(3)` 可能输出 `6.6499999999999995`,是浮点数二进制表示误差,用 `round(x, 2)` 处理显示。

## 小测验与我的答案(继承)

### 第 1 题:Shape / Square / Circle

❌ **我的答案**

```python
class Shape:
    def area(self):
        self.area = 0            # 把方法覆盖成了整数 0
        return self.area
    def describe(self):
        return f"{self.name}的面积是{self.area()}的结果"   # 多了"的结果"

class Square(Shape):
    def __init__(self, name, side):    # 测试是 Square(3),不应要求 name
        ...
```

🔍 **问题**
- 构造参数与调用处不一致(名字应由子类决定)
- 属性和方法共用命名空间,`self.area = 0` 会让 `area()` 变成 `'int' object is not callable`
- 题目中的 `{area()的结果}` 是说明文字,不是要原样输出

✅ **正确写法**

```python
class Shape:
    def __init__(self, name):
        self.name = name
    def area(self):
        return 0
    def describe(self):
        return f"{self.name} 的面积是 {self.area()}"

class Square(Shape):
    def __init__(self, side):
        super().__init__("正方形")
        self.side = side
    def area(self):
        return self.side ** 2

class Circle(Shape):
    def __init__(self, r):
        super().__init__("圆")
        self.r = r
    def area(self):
        return 3.14 * self.r ** 2
```

### 第 2 题:子类 `__init__` 未调用父类

✅ 修改正确:在 `B.__init__` 中加 `super().__init__()`。原代码 `b.x` 报 `AttributeError`,因为子类定义了 `__init__` 后父类的不会自动执行。

### 第 3 题:TimeLogger

❌ **我的答案(第一版)**:`return` 未缩进;`super().log()` 漏传 `msg`;类名 `Timelogger` 与 `TimeLogger` 大小写不一致;`"2024"` 后缺空格

❌ **我的答案(第二版)**

```python
class TimeLogger(Logger):
    def log(self):                   # 没有参数接收 "启动"
        super().__init__(msg)        # 这里需要的是父类的 log,不是 __init__
        base = super().log()         # 漏传 msg
        return "2024" + base
```

🔍 追踪数据流:`t.log("启动")` → 子类 `log` 的 `msg` → `super().log(msg)` → 父类 `log` 的 `msg`

✅ **正确写法**

```python
class TimeLogger(Logger):
    def log(self, msg):
        base = super().log(msg)
        return "2024 " + base
```

### 第 4 题:多态 + 总面积

❌ 只打印了 `describe()`,没有累加总面积

✅ **正确写法**

```python
total = 0
for a in [Square(2), Circle(1), Square(5)]:
    print(a.describe())
    total += a.area()
print(f"总面积:{total}")
```

### 第 5 题:模仿 nn.Module

❌ **我的答案**

```python
class Module:
    def __init__(self, training=True):
        self.training            # 只是读取,没有赋值 → AttributeError

class Doubler(Module):
    def __init__(self, training):    # 测试是 Doubler(),不应要求参数
        super().__init__(training)
```

✅ **正确写法**

```python
class Module:
    def __init__(self):
        self.training = True
    def train(self):
        self.training = True
    def eval(self):
        self.training = False
    def forward(self, x):
        return x
    def __call__(self, x):
        return self.forward(x)

class Doubler(Module):
    def __init__(self):
        super().__init__()
    def forward(self, x):
        return x * 2
```

## 本课教训

1. **类里每个方法第一个参数都是 `self`**
2. **方法内访问属性一律写 `self.属性名`**
3. **`__init__` 的参数 = 调用者必须提供的信息**;类自己能决定的在内部赋值
4. **写完后原样运行测试代码**,大部分错误(参数数量、缩进、大小写)会立刻暴露
5. 报错先读最后一行,例如 `takes 1 positional argument but 2 were given` 已经说明了问题
