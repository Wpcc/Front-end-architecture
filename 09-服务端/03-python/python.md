# python

## 虚拟环境

### venv

**出身：**Python3.3及以上版本内置的模块（不用额外安装），`.venv`是大家约定俗成的虚拟环境文件夹名

**本质：**只管理Python环境（Python版本、pip安装的包）

**适用场景：**纯Python项目，轻量、简单，无需额外安装

```bash
# 创建虚拟环境（文件夹名 .venv）
python -m venv .venv

# 激活环境（Windows）
.venv\Scripts\activate.bat
# 激活环境（macOS/Linux）
source .venv/bin/activate

# 退出环境
deactivate
```

### conda

**出身：**由 Anaconda 开发的开源包管理器 / 环境管理器，最初为数据科学设计。

**本质**：不仅能管理 Python 环境，还能管理 C/C++、R 等其他语言的依赖，甚至能管理不同版本的 Python 本身。

**核心特点**：

- 自带 Python：创建环境时可以直接指定 Python 版本（比如 `conda create -n myenv python=3.10`），无需系统先装对应版本。
- 跨平台 + 跨语言：适合数据科学（numpy、pandas、tensorflow 等包，conda 安装更稳定，尤其含 C 扩展的包）。

```shell
# 创建名为 myenv 的环境，指定 Python 3.10
conda create -n myenv python=3.10

# 激活环境
conda activate myenv

# 退出环境
conda deactivate
```

### 设置

**PyCharm** 通过右上角Setting -》 python interpret 可以选择或添加解析器从而生成.venv环境包

## 基础

**代码块：**python使用缩进也就是空格进行代码块判断的，这个和其他语言不一样

```python
# 如果是多行的话，需要使用严格缩进
if True:
    print('hello python')
```

**多行语句：**可以使用`\`反斜杠将一行的语句分为多行

```python
salary1 = 100
salary2 = 200
total = salary1 + \
		salary2
print(total)
```

**引号：**Python 可以使用引号( **'** )、双引号( **"** )、三引号( **'''** 或 **"""** ) 来表示字符串

```python
# 三引号可以换行
word = 'word'
sentence = "这是一个句子。"
paragraph = """这是一个段落。
包含了多个语句"""
```

**注释：**

```python
# 单行注释
'''
多行注释
'''
"""
多行注释
"""
```

**同一行显示多条语句**

```python
# 使用分号分隔
import sys; x = 'runoob'; sys.stdout.write(x + '\n')
```

**多个语句组成代码组：**像if、while、def和class这样的复合语句，首行以关键字开始，以冒号( : )结束

```python
if expression : 
   suite 
elif expression :  
   suite  
else :  
   suite 
```

### 变量

**Python**中的变量赋值不需要声明变量类型，但python中有五个标准的变量类型：

- Number（数字）
- String（字符串）
- List（列表）
- Tuple（元组）
- Dictionary（字典）



加号 **+** 是列表连接运算符，星号 ***** 是重复操作，冒号：则可以理解为至

```python
simplelist = ['zhangsan','lisi',5,'wangwu']
tinylist = [123,'john']

print(simplelist)               # 输出完整列表
print(simplelist[0])            # 输出列表的第一个元素
print(simplelist[1:3])          # 输出第二个至第三个元素[第一个元素，最后一个元素)
print(simplelist[2:])           # 输出从第三个开始至列表末尾的所有元素
print(tinylist * 2)             # 输出列表两次
print(simplelist + tinylist)    # 打印组合的列表
```

### 条件

```python
# 条件判断后面使用冒号 if elif else
# 逻辑使用 and or

num = 9
if num <= 0 or num >= 5:
    print('hello (无限,0] and [5,无限)')
```

### 循环

对于`for`存在三种遍历方式：`in`、`range`+`len`、`enumerate`

```python
# 循环 while for 
num = 9
while num > 0:
    print(num)
    num -= 1
    
'''
python中循环使用的是in,直接遍历List和字符串
如果要使用index遍历，需要使用len和range函数
len：获取字符串长度
range：生成整数数列
'''
fruit = 'apple'
for letter in fruit:
    print(letter)

```

```python
'''
range(n)	从 0 开始，到 n-1 结束（步长 1）	range(5) → 0,1,2,3,4
range(a, b)	从 a 开始，到 b-1 结束（步长 1）	range(2,6) → 2,3,4,5
range(a, b, s)	从 a 开始，到 b-1 结束，步长为 s	range(1,9,2) → 1,3,5,7

'''
fruit = 'apple'
for index in range(len(fruit)):
    print(fruit[index])
```

```python
# enumerate 枚举：可以直接获取到index和item
fruit = 'apple'
for index,item in enumerate(fruit):
    print(index,item)
```

### 列表

类似于JavaScript中的数组

```python
arr = [1,2,3]
print(len(arr))
```

### 集合

set，不重复的数组，类似于JavaScript中的set

- 自动去重
- 创建一个空集合必须用 **set()** 而不是 **{ }**，因为 **{ }** 是用来创建一个空字典

```python
arr = {1,2,3}
print(len(arr))
```

### 元组

类似于**java**中的枚举，Python中的元组和类别类似，不同之处在于元组的元素不能修改。元组使用小括号。

```python
# 元组一个元素的时候，结尾需要使用逗号（，）
temp = ('apple',)
print(temp[0])

# 元组不能修改但是可以连接
temp1 = ('apple',)
temp2 = ('banana',)

temp3 = temp1 + temp2
print(temp3)

# 元组数据不能删除，但是可以删除整个元组
tup = ('physics', 'chemistry', 1997, 2000)
 
print(tup)
del tup[0]
```

### 字典

通过`key:value`键值对的形式存储数据。类似于`Javascript`中的对象（Map），不同的是Python中列表和元组可以作为key。

- 除了通过`['a']`这种方式访问
- 还有`indict.get('a')`这种方式

```python
# 所以python中访问字典的值只能是[]这种形式
indict = {'a':1, 1:2}

# 删除字典中的值，以及清空 del使用字典的clear()
del indict['a']
print(indict)
indict.clear()
print(indict)
```

### 模块

导入模块**import**，导入模块部分功能**from import**

- 同一个文件夹下直接导入使用
- 不同文件夹
  - 规范处理：需要引入的文件夹中生成`__init__.py`文件
  - 编译器处理：pyCharm可通过右键文件夹添加`Mark Directory As Resource`
  - 使用`os`和`sys`进行硬编码处理

```python
# 规范导入 使用__init__文件将当前文件夹标记为包 使用包名.文件名的方式导入
from funDemo import fnPrint
from modules.utilsOther import say

name = 'basicDemo'

fnPrint(name)
say(name)
```

```python
# 硬编码导入
import sys
import os

# 修复：给modules加引号，正确拼接路径
module_path = os.path.join(os.path.dirname(__file__), "modules")
# 将路径添加到Python搜索路径
sys.path.append(module_path)

from funDemo import fnPrint
from utilsOther import say
```

### name

`__name__`

- 是一个内置变量，用于表示当前模块的名称
- 当模块作为主程序运行时：`__name__` 的值被设置为 `__main__`

`__main__`

- 是一个特殊的字符串，用于表示当前模块是作为主程序运行的

```python
if __name__ == "__main__":
    # 这里的代码只有在模块作为主程序运行时才会执行
    main()
```

### 对象字符串

获取对象的字符串

- str：string的缩写
- repr：representation的缩写

```python
# 1. 字符串对象
s = "Hello\nWorld"  # 包含换行符
print("str(s):", str(s))   # 面向用户，显示直观的换行
print("repr(s):", repr(s)) # 面向开发者，显示转义符（可还原）

# 2. 数字/日期对象
import datetime
dt = datetime.datetime(2026, 3, 3, 12, 0)
print("\nstr(dt):", str(dt))   # 易读的日期格式
print("repr(dt):", repr(dt))   # 精确的构造函数格式（可直接还原对象）
```

### 格式化字符串字面量

处理字符串中的变量

- **f-string**：Python 3.6 + 首选，语法`f"{变量/表达式:格式符}"`
- **str.format()**：兼容所有版本，语法`"占位符".format(参数)`

```python
# f-string 写法（推荐）
name = "小明"
age = 18
score = 89.789
print(f"用户：{name}，年龄：{age}，成绩：{score:.1f}")  # 输出：用户：小明，年龄：18，成绩：89.8

# str.format() 写法
print("用户：{}，年龄：{}，成绩：{:.1f}".format(name, age, score))  # 输出同上
# 或关键字写法
print("用户：{n}，年龄：{a}，成绩：{s:.1f}".format(n=name, a=age, s=score))  # 输出同上
```

### 类

python：通过class关键字定义类

python：类有一个名为 __init__() 的特殊方法（**构造方法**），该方法在类实例化时会自动调用

python：类的方法必须含有self参数，且为第一个参数

`python`:通过class关键字定义类

```python
# 一个简单的类
class MyClass:
    i = 12345
    def f(self):
        return self.i

x = MyClass()

print('MyClass 类的属性i为：',x.i)
print('MyClass 类的方法f返回：',x.f())
```

类有一个名为 __init__() 的特殊方法（**构造方法**），该方法在类实例化时会自动调用

```python
# self 代表类的实例
class MyClass:
    def __init__(self, value):
        self.value = value

    def display_value(self):
        print(self.value)

# 创建一个类的实例
obj = MyClass(42)

# 调用实例的方法
obj.display_value() # 输出 42
```

python中类的方法必须含有self参数，且为第一个参数

```python
#类定义
class Persion:
    #定义基本属性
    name = ''
    age = 0
    #定义私有属性,私有属性在类外部无法直接进行访问
    __weight = 0
    #定义构造方法
    def __init__(self,n,a,w):
        self.name = n
        self.age = a
        self.__weight = w
    def speak(self):
        print("%s 说: 我 %d 岁。" %(self.name,self.age))
 
#单继承示例
class Student(People):
    grade = ''
    def __init__(self,n,a,w,g):
        #调用父类的构函
        people.__init__(self,n,a,w)
        self.grade = g
    #覆写父类的方法
    def speak(self):
        print("%s 说: 我 %d 岁了，我在读 %d 年级"%(self.name,self.age,self.grade))
 
 
 
s = student('ken',10,60,3)
s.speak()
```

### JSON

- `json.dumps`：将python对象转换成JSON对象
- `json.loads`：将JSON对象转换成python对象

```python
import json
 
# Python 字典类型转换为 JSON 对象
data = {
    'no' : 1,
    'name' : 'Runoob',
    'url' : 'https://www.runoob.com'
}
 
json_str = json.dumps(data)
print ("Python 原始数据：", repr(data))
print ("JSON 对象：", json_str)
```

### pass

pass是python中的空语句，占据位置但不执行操作，这样系统不报错

```python
# 定义一个函数，暂时还不想写实现
def say():
    pass
```

### 打包解包

 `**` 

- 当调用函数的时候：把一个**字典**（`data`）里的键值对，拆解成「关键字参数 = 值」的形式，传给类的初始化方法。
- 当在参数赋值的时候：是 “参数收集（打包）”

`*`

- 当调用函数的时候：把一个**元组（tuple）**里的键值对，拆解成「关键字参数 = 值」的形式，传给类的初始化方法。
- 当在参数赋值的时候：是 “参数收集（打包）”

#### 拆包

**使用pydantic说明**

```python
# 1.定义一个模型
from pydantic import BaseModel

# 继承BaseModel的自定义模型
class AgentInput(BaseModel):
    name: str
    age: int
        
# 2.创建一个字典
data = {"name": "张三", "age": 20}

# 3.手动传参
input_data = AgentInput(name=data["name"], age=data["age"])

# 3.使用自动解包
input_data = AgentInput(**data)
```

**使用原生说明**

```python
# 1.创建一个类
class AgentInput:
    name: str
    age: int = 0
    def __init__(self,name,age):
        self.name = name
        self.age = age

    def say(self):
        print(self.name)
        print(self.age)

data = {'name':'123', 'age':10}

# 2.使用自动解包传参
agent = AgentInput(**data)
agent.say()
```

#### 打包

```python
# 在函数定义中参数收集
class AgentInput:
    name: str
    age: int = 0

    # 参数收集
    def __init__(self, **innerdata):
        self.name = innerdata.get("name")
        self.age = innerdata.get("age")

    def say(self):
        print(self.name)
        print(self.age)


data = {'name': '123', 'age': 10}
agent = AgentInput(**data)
agent.say()

```



### 类型注解

Python 3.5 是**基础类型注解语法**的正式版本（2015 年 9 月发布）

| 类型   | 注解写法 | 示例                 | 说明                      |
| ------ | -------- | -------------------- | ------------------------- |
| 整数   | `int`    | `age: int = 18`      | 正整数、负整数、0         |
| 浮点数 | `float`  | `price: float = 9.9` | 带小数的数值              |
| 字符串 | `str`    | `name: str = "张三"` | 单 / 双引号、三引号字符串 |
| 布尔值 | `bool`   | `is_ok: bool = True` | 仅 True/False 两个值      |
| 空值   | `None`   | `res: None = None`   | 表示无返回 / 无值         |

Python 3.9 开始支持**原生类型标注**，无需导入 typing 模块也能写复杂注解，进一步简化(替代了一部分)

```python
# Python 3.9+ 写法（替代 typing.List[int]）
def func1(lst: list[int]) -> None:
    pass

# 替代 typing.Dict[str, int]
def func4(d: dict[str, int]) -> int:
    return sum(d.values())
```

### 装饰器

**装饰器是给函数 / 类 “加功能” 的工具，不修改原代码，还能复用逻辑**。

假设现在有两个函数，需要在函数执行前加一个打印的功能

```python
# 普通函数

def add(a,b):
    return a+b

def multiply(a,b):
    return a*b

def log():
    print('function begin...')
    
# 添加功能
def add(a,b):
    log() #侵入
    return a+b

def multiply(a,b):
    log() #侵入
    return a*b
```

```python
# 装饰器
from functools import wraps

def log(func):
    @wraps(func) #解决__name__指向问题：保留原函数的名称、文档字符串等元信息
    def wrapper(*args,**kwargs):
        print('function begin...')
        result = func(*args,**kwargs)
        return result
    return wrapper

@log
def add(a,b):
    return a+b

# add = log(add) 本质上等于装饰器函数执行该函数，并返回该函数
print(add(2,3))
```

- 执行`@log`的时候，本质上只执行了`return wrapper`，返回包裹后的函数
- 执行`print(add(2,3))`的时候，相当于执行`wrapper`函数，返回最终的值



### 参数

- `*args` = 多出来的**普通参数** → 元组
- `**kwargs` = 多出来的**名字 = 值参数** → 字典（keyword arguments）

`*args` = 多出来的**普通参数** → 元组

```
func(1,2,3) → args = (1,2,3)
```

`**kwargs`：接收**关键字参数** → 变成**字典**

```
func(a=1,b=2) → kwargs = {'a':1,'b':2}
```



## 相关库

### 服务器

web服务器类似于java中的tomcat：

| 服务器   | 协议 | 运行方式    | 适用场景                  |
| -------- | ---- | ----------- | ------------------------- |
| uvicorn  | ASGI | 同步 + 异步 | FastAPI/Starlette（异步） |
| Gunicorn | WSGI | 仅同步      | Flask/Django（同步）      |

### typing

`typing`是 Python 3.5 + 引入的**标准库模块**，核心作用是提供**类型注解相关的工具**

#### 变量类型

```python
# 基础类型（直接标注，无需导入 typing）
name: str = "张三"  # 字符串
age: int = 25       # 整数
score: float = 92.5 # 浮点数
is_vip: bool = True # 布尔值

# 空值（None）：必须用 Optional 或 None（3.10+）
from typing import Optional
# 方式1：用 Optional（表示变量可以是指定类型或 None）
addr: Optional[str] = None  # 等价于 str | None（3.10+）
# 方式2：Python 3.10+ 简化写法（无需导入）
addr: str | None = None
```

#### 函数类型

```python
# 基础示例：无复杂类型
def add(a: int, b: int) -> int:
    return a + b

# 说明：
# a: int → 参数a是整数
# b: int → 参数b是整数
# -> int → 返回值是整数

# 测试：传入非整数会被IDE提示（但运行时不报错，Python仍允许）
add(10, 20)  # ✅ 正常
add("10", "20")  # IDE标红提示，运行时会报错（字符串不能相加）
```

`typing` 主要解决「复杂类型」的注解问题（比如列表、字典、多类型可选等）

| 工具       | 作用                              | 基础示例                                                     | 3.10+ 简化写法                         |
| ---------- | --------------------------------- | ------------------------------------------------------------ | -------------------------------------- |
| `List`     | 标注列表（指定元素类型）          | `from typing import List`nums: List[int] = [1,2,3]           | `nums: list[int] = [1,2,3]`            |
| `Dict`     | 标注字典（指定键值类型）          | `from typing import Dict`user: Dict[str, int] = {"age":25}   | `user: dict[str, int] = {"age":25}`    |
| `Union`    | 标注多类型可选                    | `from typing import Union`value: Union[int, str] = 10        | `value:int | str = 10`                 |
| `Optional` | 标注可选类型（类型 + None）       | `from typing import Optional`name: Optional[str] = None      | `name:str | None = None`               |
| `Tuple`    | 标注元组（指定元素类型 / 长度）   | `from typing import Tuple`point: Tuple[int, int] = (10,20)   | `point: tuple[int, int] = (10,20)`     |
| `Callable` | 标注函数（指定入参 / 返回值类型） | `from typing import Callable`func: Callable[[int,int], int] = add | `func: callable[[int,int], int] = add` |

1、List：标注列表元素类型

```python
# 旧写法（需导入）
from typing import List
# 整数列表
nums: List[int] = [1, 2, 3]
# 字符串列表
names: List[str] = ["张三", "李四"]

# 3.10+ 简化写法（无需导入，直接用list）
nums: list[int] = [1, 2, 3]
names: list[str] = ["张三", "李四"]
```

2、Dict：标注字典键值类型

```python
# 旧写法
from typing import Dict
# 键是字符串，值是整数
user_info: Dict[str, int] = {"age": 25, "score": 90}
# 键是字符串，值是任意类型（用Any）
from typing import Any
data: Dict[str, Any] = {"name": "张三", "age": 25, "is_vip": True}

# 3.10+ 简化写法
user_info: dict[str, int] = {"age": 25, "score": 90}
data: dict[str, Any] = {"name": "张三", "age": 25, "is_vip": True}

```

3、Union：多类型可选

```python
# 旧写法
from typing import Union
# 变量可以是int或str
value: Union[int, str] = 10
value = "hello"  # ✅ 合法

# 函数参数示例
def print_value(value: Union[int, str]) -> None:
    print(f"值：{value}")

# 3.10+ 简化写法（用 | 分隔）
def print_value(value: int | str) -> None:
    print(f"值：{value}")
```

4、Optional：类型 + None（Union 的特例）

```python
# 旧写法
from typing import Optional
# 变量可以是str或None（等价于 Union[str, None]）
addr: Optional[str] = None
addr = "北京市"  # ✅ 合法

# 函数返回值示例
def get_name() -> Optional[str]:
    return None  # 或返回字符串

# 3.10+ 简化写法
addr: str | None = None
```

5、自定义

```python
# Python 3.10+ 推荐用 TypeAlias
from typing import TypeAlias

# 定义别名：UserList 代表「元素是字典的列表」
UserList: TypeAlias = list[dict[str, int]]

# 使用别名
users: UserList = [{"age":25}, {"age":30}]

# 旧写法（3.9及以下）
UserList = list[dict[str, int]]
```



### pydantic

**pydantic**是是 Python 中**超实用的数据验证与数据解析库**

- 类型检验：接收用户 / API 传来的数据（比如字符串、数字），不知道类型对不对、值是否合法
- 数据校验：手动写一堆 `if` 判断验证数据（比如 “年龄必须是整数且大于 0”），代码又臭又长
- 类型转换：想把 JSON / 字典转换成结构化的 Python 对象，还要手动赋值。

**BaseModel：定义数据对象**

```python
# python普通类 普通对象
class AgentOutput:
    city:str
    weather:str
    temp_range:str
    suggestion:str

output = AgentOutput()
output.city = "北京"

print(output) # 原生python对象，打印出来的是一个地址

# pydantic基础类 基础对象（类似于JavaScript对象）
from pydantic import BaseModel
class Agent(BaseModel):
    city: str
    weather: str
    temp_range: str
    suggestion: str

agent = Agent(
    city="北京",
    weather="晴",
    temp_range="15-25°C",
    suggestion="适合外出，注意防晒")

print(agent) # 打印一个pydantic对象，类似于Javascript对象
print(agent.model_dump()) # 将pydantic对象转换成字典

```

**Field：字段控制**

- `...`必填字段
- `gt`：greater than
- `ge`:  greater than and equal
- `lt`:  less

```python
from pydantic import BaseModel,Field,ValidationError
from enum import StrEnum

# 模型控制：enum声明
class AgentModel(StrEnum):
    glm_4 = "glm-4"
    gpt_3_5 = "gpt-3.5"
    gpt_4 = "gpt-4"

class AgentConfig(BaseModel):
    model: AgentModel = Field(
        ...,
        description="Model of the agent"
    )
    temperature: float = Field(0.7, ge=0.0, le=1.0, description="Temperature for the agent's responses")
    max_tokens: int = Field(2048, ge=1, description="Maximum number of tokens in the agent's response")
    
valid_config = AgentConfig(**{"model": "glm-4", "temperature": 0.5, "max_tokens": 1000})
print(valid_config.model_dump())


try:
    invalid_config = AgentConfig(**{"model": "invalid-model", "temperature": 1.5, "max_tokens": -10})
except ValidationError as e:
    print(f'报错信息：{e}')
```



### 异步编程

Python异步编程的核心是`asyncio`库，搭配3个关键字：

- `async def`：定义**异步函数**
- `await`：放在异步操作前，告诉程序这里需要等待结果
- `asyncio.run()`：
  - 运行异步函数的主入口，是异步程序的'启动器'
  - 本身是同步操作
- `asyncio.create_task()`：包装接口
- `asyncio.gather()`：批量等待多个任务

```python
# 基本的异步调用
import asyncio
import time 

async def api(name):
    time.sleep(1) # 这里不使用time，在asyncio.wait中会阻塞整个event_loop执行
    return f'{name} api success'

async def main():
    start = time.time()

    task1 = asyncio.create_task(api("OpenAI"))
    task2 = asyncio.create_task(api("智谱AI"))
    task3 = asyncio.create_task(api('qwen'))

    res1 = await task1
    res2 = await task2
    res3 = await task3

    print(f'异步总耗时：{time.time() - start:.2f}秒')

asyncio.run(main())

```

`asyncio.gather()`：批量等待多个任务

```python
# gather
async def async_main():
    start = time.time()
    # 批量创建并等待所有任务，直接返回结果列表
    results = await asyncio.gather(
        api("OpenAI"),
        api("智谱AI"),
        api("百度文心")
    )
    result1,result2 = results # 这里还可以使用解构
    print(results)  # ['OpenAI 返回结果', '智谱AI 返回结果', '百度文心 返回结果']
    
    print(result1) 
    print(result2)
    
    print(f"异步总耗时：{time.time() - start:.2f}秒")
```

`asyncio.wait()`：灵活控制任务（完成一个处理一个）

- `asyncio.FIRST_COMPLETED`：**任意一个**完成就返回
- `asyncio.ALL_COMPLETED`：等**所有任务**都完成，才返回
- `asyncio.FIRST_EXCEPTION`：只要有**一个任务报错**，就立刻返回

```python
async def tools():
    tasks = {
        asyncio.create_task(api("搜索引擎")),
        asyncio.create_task(api("计算器")),
        asyncio.create_task(api("数据库"))
    }

    while tasks:
        done, pending = await asyncio.wait(tasks, return_when=asyncio.FIRST_COMPLETED)

        for task in done:
            result = task.result()
            print(f'工具调用结果: {result}')
            tasks.remove(task)

asyncio.run(tools())
```

### 服务器

vim：

- 输入`esc+wq`退出，

- 输入`i`编辑

ssh:

- 输入`exit`或快捷键`ctrl+d`退出
- 连接我的服务器`ssh aliyun-git`

### re

python中内置的正则regular表达式：

```python
import re

llm_output = 'abcdc'
match = re.search(
    r'a.*?c', # r = raw string匹配原始字符串：让正则里的斜杠 \ 只当普通字符用，不转义
    llm_output, re.DOTALL # DOTALL 匹配换行内容
)
print(match)
```

#### r的作用

r = raw string匹配原始字符串：让正则里的斜杠 \ 只当普通字符用，不转义

``````python
import re
string = 'abc123cdf'
match1 = re.search('\\d+',string) # 这里转义了，必须再加一个反斜杠
match2 = re.search(r'\d+',string) # 这里不转义

print('match1:',match1) # match1: <re.Match object; span=(3, 6), match='123'>
print('match2:',match2) # match2: <re.Match object; span=(3, 6), match='123'>
``````

#### 匹配什么

- `.` **任意一个字符**（除换行）

- `\d` **一个数字**（0-9）

- `\w` **一个字母 / 数字 / 下划线**

- `\s` **一个空格 / 换行 / 制表符**

- `[abc]` 只匹配 a 或 b 或 c

- `[^abc]` 不匹配 a/b/c（排除）

#### 匹配个数

- `*` **0 次或多次**（可有可无，越多越好）
- `+` **1 次或多次**（至少一个）
- `?` **0 次或 1 次**（要么没有，要么一个）
- `{3}` 正好 3 个
- `{2,5}` 2～5 个

#### 位置与边界

- `^` 开头
- `$` 结尾
- `|` 或者（A|B）

#### 分组提取

- `( )` 括号包起来 = **要提取的内容**
- `(?: )` 只分组不提取

#### 综合

`.*?`（最重要！）

- **非贪婪匹配：匹配任意内容，但匹配到就停，不贪多**

- 比如`a.*?c`  
- abcdc ==> 匹配abc

`(?=...)`

- **前瞻：匹配到这里就停止，不包含后面内容**

- 你代码里就是：

- 遇到下一个 `Thought/Action/Observation` 就停止。

 `\Z`

- **表示文本的末尾**

正则里面的分组：

`()`

- 要内容用

```python
import re

text = "Action: 查询订单"

match = re.search(r'(Action|Thought): (.*)', text)
# 所有正则语句会匹配一次，一个括号会匹配一次

print(match.group(0))  # 完整匹配
print(match.group(1))  # 分组1
print(match.group(2))  # 分组2
```

`(?=)`

- 不要内容只打包用
  - 这里为什么需要括号，主要用来做或判断，限定或判断范围

```python
import re

text = "Action: 查询订单"

# 把第1组换成 非捕获分组 (?: )
match = re.search(r'(?:Action|Thought): (.*)', text)

print(match.group(0))  # 完整匹配
print(match.group(1))  # 现在只有 1 个捕获组了！ === 最后一个括号
# print(match.group(2)) # 这行会报错！因为没有第2组了
```



cbcac dbabc dcbdb