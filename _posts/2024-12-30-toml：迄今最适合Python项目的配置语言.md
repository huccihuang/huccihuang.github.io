---
title: "toml：迄今最适合 Python 项目的配置语言"
date: 2024-12-30 8:00:00 +0800
categories: [Python Features]
tags: [Python, toml]
---


一些语言用于编写和运行程序，比如我们熟知的 Python Java 等等；还有些语言只用来传递信息，例如 JSON、YAML，和这次要介绍的 toml。

Toml 是目前最适合 Python 项目的配置语言。


## Toml 的语法

Toml 适合 Python主要体现在两点，一是语法非常相似，二是类型完美对应，随着后面的例子你会理解这两点。

首先，创建一个新的 toml 文件。

如果你和我一样使用 vscode，vscode 没有内置对 toml 的颜色高亮，我们需要通过插件实现。我使用的插件是 *Even Better Toml*。

### 注释

toml 的注释和 Python 一样，在行首使用 `#` 就可以了。

```toml
## This is a toml file.
```

### 键值对

创建键值对只需要等号就可定义，而且可以使用许多 Python 中的类型，先写一下比较熟悉的整数、字符串列表。

```toml
name = 'Hucci'
age = 59
sex = "Female"
hobbies = ['coding', 'reading', 'traveling']
birthdate = 1960-01-01T00:00:00Z
```

你可以看到我同时使用了单引号和双引号，这在 toml 中是合法的。不像 json 只认双引号。不过即使都合法，也会有细微的区别，如果要使用转义字符，就只能在双引号里面使用，不能在单引号里面使用。虽然转义字符不常用，但我们还是知道一下吧。

```toml
sex = "\"Female\""
```

除了这些常见类型，经常被解析为字符串的日期时间都也被正确解析。

这样我们就写好了一个简单的 toml 文件。

### 读取 toml

tomllib是 Python 3.11 中引入的标准库模块，用于解析 TOML 文件。如果你使用的是 Python 3.11 或更高版本，则不需要安装 tomllib，因为它已经包含在标准库中。

如果你使用的是 Python 3.10 或更低版本，就需要 `pip install toml`。

```python
## read_toml.py
import tomllib

with open('config.tml', 'br') as f:
    config = tomllib.load(f)
```

这里需要说明打开方式是以二进制仅读取的方式打开。

为了更方便地查看结果，我需要 `from pprint import pp`。

这样就将我们刚刚的 toml 读取为了一个字典。

```
{'name': 'Hucci',
'birthdate': datetime.datetime(1960, 1, 1, 0, 0, tzinfo=datetime.timezone.utc),
'age': 59,
'sex': 'Female',
'hobbies': ['coding', 'swimming']}
```

所有的类型都正确解析了，包括日期时间也正确解析为 datetime 类型。

### Inline_table

Toml 的类型和 Python 的类型几乎是一一对应，字符串对应字符串，整数对应整数，列表对应列表，时间对应时间。就连 Toml 中唯一一个听起来有点陌生的类型 table，也对应了 Python 的字典。

创建一个 table 很容易，就像字典一样用花括号，非常符合 Python 思维。比如我的 shape 是身高一米体重 50。

```toml
shape = {height = 1, weight = 50}
```

解析出来就是 shape 所对应的 value 是一个字典。

```
{'name': 'Hucci',
'birthdate': datetime.datetime(1960, 1, 1, 0, 0, tzinfo=datetime.timezone.utc),
'age': 59,
'sex': 'Female',
'hobbies': ['coding', 'swimming'],
'shape': {'height': 1, 'weight': 50}}
```

### 创建表

但大部分情况下，我们的配置是层层叠叠的嵌套表，如果用花括号这种写法就太复杂难看了。

所以会使用另一种写法，toml 中创建表的写法，用方括号括起 table 的名称。比如说我要写我的社交媒体。

```toml
[social]
bilibili = 'HucciWritingCode'
youtube = 'huccicoder'
```

这样解析的结果 social 所对应的 value 是一个字典，字典的 key 分别是 bilibili 和 youtube。

```
{'name': 'Hucci',
'birthdate': datetime.datetime(1960, 1, 1, 0, 0, tzinfo=datetime.timezone.utc),
'age': 59,
'sex': 'Female',
'hobbies': ['coding', 'swimming'],
'shape': {'height': 1, 'weight': 50},
'social': {'bilibili': 'HucciWritingCode', 'youtube': 'huccicoder'}}
```

### 在表中再嵌套表

如果说我想再在这个表里面嵌套，就需要再建立一张表。像这种表里面的表，我们可以用 `.` 来表示。比如说我再增加我的抖音账号，除了抖音账号的名称，我还要说明抖音账号的关注数。

```toml
[social]
bilibili = 'HucciWritingCode'
youtube = 'huccicoder'
tiktok.username = 'HucciWritingCode'
tiktok.followers = 100
```

这个 `.` 的写法也很符合 Python 的风格。

```
{'name': 'Hucci',
'birthdate': datetime.datetime(1960, 1, 1, 0, 0, tzinfo=datetime.timezone.utc),
'age': 59,
'sex': 'Female',
'hobbies': ['coding', 'swimming'],
'shape': {'height': 1, 'weight': 50},
'social': {'bilibili': 'HucciWritingCode',
           'youtube': 'huccicoder',
           'tiktok': {'username': 'HucciWritingCode', 'followers': 100}}}
```

这个嵌套还可以继续嵌套下去，只要愿意加足够的 `.`。

现在 social 这一项关于 tiktok 有两行，如果说 bilibili youtube 都是两行，那就不太好看，我们可以通过方括号的写法来合并同类项。

```toml
[social.tiktok]
username = 'HucciWritingCode'
followers = 100
```

这种写法和刚刚的写法完全等价。

### 字典组成的列表

现在假设要给每一个平台，都用这样的格式说明名称和关注数，用包含字典的列表来写会非常方便。

```toml
social = [
    {platform='bilibili', username='Hucci', followers=1000},
    {platform='youtube', username='Hucci', followers=100},
    {platform='tiktok', username='Hucci', followers=10},
]
```

更 toml 的写法，是用两个方括号来说明是列表中的字典。

```toml
[[social]]
platform = 'bilibili'
username = 'Hucci'
followers = 1000

[[social]]
platform = 'youtube'
username = 'Hucci'
followers = 100

[[social]]
platform = 'tiktok'
username = 'Hucci'
followers = 10
```

到这里，我们就介绍完了 toml 的所有基本语法。

## 为什么要用专门的配置语言

你可能会奇怪，明明可以直接用 python 写 config，为什么要用专门的配置语言呢？我之前在介绍 `.env` 文件存储环境变量时，也遇到了这样的疑问。

最主要是从安全性考虑。Python 文件是可执行代码，我们 import 时候会执行这些代码，存在一定安全隐患；而 YAML/JSON/TOML 等等是纯数据，避免了执行代码的风险。

## Outro

最后总结一下，我们介绍了配置语言 toml 的语法，说明为什么它适合 Python，并说明为什么要用专门的配置语言而不是用 Python 写 config。
