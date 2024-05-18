---
title: "Match Case: 让 80% 的 if 惨遭失业"
date: 2024-1-4 17:00:00 +0800
categories: [Python Features]
tags: [Python]
---

在这个文章里，我会分享 Python 中的 match case 语句，它可以用来处理多个条件分支，而不需要一个又一个的 `if` 和 `elif`。match case 是 Python 3.10 引入的特性，如果你想尝试，需要先确保你的 python 版本在 3.10 或以上。

## 使用方法

### 基本语法

先通过一个简单的例子，让我们理解 `match case` 的语法。

假设我们需要判断红绿灯的颜色，用 if 语句可以这样写。

```python
def if_traffic_light(color):
	# 如果 color 是红色，return 一个字符串 "stop"
    if color == 'red':
        return 'Stop'
    # 如果是黄色或者绿色，也返回相应的字符串
    elif color == 'yellow':
        return 'Caution'
    elif color == 'green':
        return 'Go'
    # 如果不是这三种颜色，就返回颜色无效
    else:
        return 'Invalid color'

print(if_traffic_light('red'))  # Output: Stop
```

运行看看。

再让我们试试用 match case 改写。

```python
def match_traffic_light(color):
	#　先用 match 表示需要匹配的变量，这个例子中是 color
    match color:
        # 再用 case 表示需要匹配到的东西
        case 'red':
        # 和上面一样，如果是红色就return Stop
            return 'Stop'
        # 接下来是黄色和绿色
        case 'yellow':
            return 'Caution'
        case 'green':
            return 'Go'
        # 与 if 相同，匹配从上到下进行,前面的 case 匹配上了，就不会匹配后面的 case
        # 最后使用 case _
        case _:
        # 它与 if 语句最后的else相同，当前面的 case 都没匹配上，就会进入 `_` 分支。这里的 _ 是一个通配符。
            return 'Invalid color'

print(match_traffic_light('red'))  # Output: Stop
```

这样我们就完成了改写，写法比 `if` 语句更清爽，也更结构化。运行看看，和使用 if 的结果一样。

如果你曾经接触过 Java 或者其他语言，看到这里你也许会觉得「嗯～不过是 `switch case` 的另一个名字罢了」。但其实两者有很大的差别，`switch case` 只能做到字符串或者是数值的字面量匹配，就像我们在这个例子中看到的。

而 `match case` 能做到结构匹配，而且支持析构。简单来说，match case 可以在匹配后解包，并进行变量绑定。

让我们通过例子来感受一下。

### 匹配序列

以序列为例，现在我用长度为 2 的元组来表示二维点的坐标。使用一个函数判断它是否在原点，如果是在原点就打印出 *Origin*，不在就打印出坐标。

```python
def match_point(point):
	# 那么我们要做的就是，match point
    match point:
	    # 如果是 `(0,0)`
        case (0,0):
	        # 就打印 Origin
            print('Origin')
        # 如果是其他长度为 2 的元组
        case (x,y):
		    # 就打印出坐标
            print(f'Point is at ({x},{y})')
        # 这里会匹配长度为 2 的序列，并将 x 和 y 绑定为这个序列的两个元素。

# 定义两个点来进行测试
p1 = (2,2)
p2 = (0,0)

match_point(p1)
match_point(p2)
```

试试看，和我们预期一致。

我想再加一个 case，当传进来的不是长度为2的序列时，也能有相应的动作。

```python 
case others:
	print(f'{others} is not a valid point!')
```

当 case 后只有一个变量名，无论 match 的是什么，都会进入这个分支并绑定变量，所以这个分支只能放在最后。听起来和 `_` 很像，所以也有人用这个写法来代替下划线。

来定义一个长度为 3 的序列试试看，和预期一致。

```python
p3 = (1,2,3)
match_point(p3)
```

这个例子中的 x 和 y 都绑定了值，实际上我们可以只绑定一个值。

比如现在我希望分别显示点在 x 轴上或者 y 轴上，就可以这样写。

```python
def match_point(point):
    match point:
        case (0,0):
            print('Origin')
        # 当点的坐标是 x,0 时说明在 x 轴上
        case (x, 0):
            print(f'On x-axis, x={x}')
        # 当点的坐标是 0,y 时说明在 y 轴上
        case (0, y):
            print(f'On y-axis, y={y}')
        case (x,y):
            print(f'x={x}, y={y}')
        case others:
            raise TypeError(f"{default} is not a point!")

# 再修改一下点的坐标试试看
p1 = (0,2)
p2 = (2,0)

match_point(p1)
match_point(p2)
```

通过这个例子，相信你应该能够理解 match case 的结构匹配和支持析构的意思了。

我们再继续探索一下 match case 还有什么更好用的方法。

### 或模式

我们知道点在 (x, 0) 或者 (y,0) 上都是在坐标轴上，因此我们可以使用或符号，或者说竖杠来将这两种情况并列。

```python
def match_point(point):
match point:
    case (0,0):
        print('Origin')
    case (x, 0) | (0, y):
        print(f'On axis, y={y}')
    case (x,y):
        print(f'x={x}, y={y}')
    case others:
        raise TypeError(f"{default} is not a point!")
```

直接这样运行会报错。因为同一个 case 下，使用 `|` 连接的结构，应该绑定同一个变量，所以这里不能是 (0,y)，而是要写 (0, x)。这样，两个结构就都绑定了变量 x。同理，(0,0) 没有 x 也不能放进来。

再运行试试，与预期一致。

### 使用 `*` 应对长度不确定的情况

目前我们的例子是二维点，所以序列的长度只能是 2。在实际应用中可能会遇到我们不确定序列长度，只需要确定序列包含某几个元素的情况，这时可以用 `*`。

比如说我想要匹配一个 `[1,2]` 开头的序列，序列的长度无所谓。我就可以写了 1 和 2 后，加上 `*rest`，这样就能匹配到 `[1,2]` 开头，长度至少为 2 的序列。

```python
def match_first_two_elements(arr):
	match arr:
		case [1,2, *rest]:
			print('match!')
		case default:
			print(f'{default} not matched!')

match_first_two_elements([1,2,3,4,5])
```

如果这里 `*` 的出现让你迷惑，可以参考我往期关于 `*` 用法的文章。你会了解到 `*` 可以用于序列的解包。

### 序列对类型不做区分

对于序列的匹配，我还想提醒一点。`match case` 不会区分序列的类型。对元组和列表一视同仁，方括号，圆括号，或者没有括号，都是一样的。我们可以测试一下。

```python
# 定义一个元组 t 和一个列表 l
t = (0, 0)
l = [0, 0]

# match t 时使用方括号
match t:
    case [0, 0]:
        print("Matched tuple t with square brackets.")
# match l 时使用圆括号
match l:
    case (0, 0):
        print("Matched list l with parentheses.")
# 再 match l 的时候不带括号
match l:
	case 0,0:
		print("Matched list l with no brackets.")
```

运行后你会发现，都能匹配成功。

所以你在匹配序列的时候要注意，如果想要区分类型，要在 case 后接上你想要的类型。

```python
match t:
    case tuple([0,0]):
        print("t is tuple (0,0)")
```

如果你只关心类型而不关心里面的值，直接写类型也是可以的。

```python
match t:
    case tuple:
        print("t is a tuple.")
```

这样就比使用 if 语句要省下一个 isinstance 了。

### 守卫模式

说到 if，if 也可以和 case 结合使用，通过在 case 后接 if，可以让我们在匹配时对结构进一步限制。官方文档把这个 if 叫做 guard，守卫。

这个守卫会在前面的结构已经匹配，并且变量绑定后，再检查。

举个例子，以刚刚的用元组表示点举例，现在我想要知道点在哪个象限。我就可以在变量绑定后，再使用 if 来判断 x 和 y 坐标的范围。

```python
def match_quadrant(point):
	match p3:
		case (x,y) if x > 0 and y > 0:
			print('First quadrant')
		case (x,y) if x < 0 and y > 0:
			print('Second quadrant')
		case (x,y) if x < 0 and y < 0:
			print('Third quadrant')
		case (x,y) if x > 0 and y < 0:
			print('Fourth quadrant')
	    case (x,y):
	        print('On axis')
```

### 匹配字典

再让我们看看如何将 `match case` 用于字典。

现在我用字典来表示点，字典的 key 是 `'x'` 和 `'y'`，value 是坐标的值。看看 `match case` 会如何匹配字典。

```python
p2 = {'x': 20, 'y': 30}

match p2:  
    case {'x': 0, 'y': 0}:  
        print("Origin")  
    case {'x': x, 'y': 0}:  
        print(f"X-axis, x={x}")  
    case {'x': 0, 'y': y}:  
        print(f"Y-axis, y={y}")  
    case {'x': x, 'y': y}:  
        print(f'x={x}, y={y}')
    case _:  
        raise ValueError("Not a point")
```

匹配字典与序列不同一个地方在于，匹配序列时会要求序列的长度，而匹配字典时仅看 key。即使你不确定要匹配的长度，也可以很好地匹配上。

比如我定义了一个字典，匹配时只写了一个键值对。一样可以匹配成功。

```python
d = {'a': 1, 'b': 2, 'c': 3}

match d:
    case {'a' : x}:
        print(f'value of key a is {x}')
```

### 匹配自定义类

`match case` 同样适用于自定义类，依然以点举例。现在我定义了一个 Point 类来表示点。

此时的 `case` 后接的也需要是 Point 类。

```python
class Point:
    def __init__(self,x,y):
        self.x = x
        self.y = y
          
p3 = Point(0,0)

match p3:
    case Point(x=0,y=0):
        print("Origin")
    case Point(x=0,y=y):
        print(f"Y-axis, y = {y}")
    case Point(x=x,y=0):
        print(f"X-axis, x = {x}")
    case Point(x=x,y=y):
        print(f"({x},{y})")
    case _:
        raise ValueError("Not a point")
```

#### `__match_args__`

你可以看到，当我们构造实例 `p3` 时，是用的位置参数而不是关键字参数，不需要指定哪个是 x 哪个是 y。这样可以少打几个字，很方便。但我们在 `case` 后面使用的是关键字参数，却不能用位置参数，不然会报错。

```python
match p3:
	case Point(x, y):
		print(f'{x},{y}')
# TypeError: Point() accepts 0 positional sub-patterns (2 given)
```

如果想要在 `case` 后也使用位置参数，我们需要添加一个类变量 `__match_args__` 来指定在 match case 中位置表示的参数。

```python
class Point:
    __match_args__ = ('x','y')

    def __init__(self,x,y):
        self.x = x
        self.y = y
```

## 最后

通过今天的文章，你会发现 `match case` 使用起来并不难，只是因为 `match case` 的用法很灵活，会让人觉得它不够明确，而很少尝试。我看到大部分人仅将它用于字面量的匹配。

我希望这个文章可以帮助你更好地理解怎么使用 `match case`，让你能够在自己的代码中使用它来提高效率。

如果你有任何疑问或意见，可以在下方的评论区留言。

感谢阅读。