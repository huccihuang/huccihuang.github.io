---
title: "【设计模式 in Python】策略模式：不要再用一个类装下所有方法了！"
date: 2024-5-10 12:00 +0800
categories: [Design Pattern]
tags: [Python, design pattern, strategy pattern]
---

设计模式指的是对于某种问题的解决方案，在我们设计程序时，使用正确的设计模式将极大提高我们的代码效率。

我会在这篇文章中介绍设计模式中的策略模式，它常被用于需要实现不同策略的场景。比如使用不同策略处理文件，使用不同的交通工具导航，使用不同的支付方式进行支付等情况。

我们来看一个具体的例子。

## 不使用策略模式时

想象我们现在需要写一个处理文件的类，将不同类型的文件处理成统一的格式和类型。需要处理的文件类型会是 excel、csv、txt。

那么这个类我们会这样写：

```python
class FileProcessor:
    # 通过文件路径进行实例化
    def __init__(self, file_path): 
        self.file_path = file_path
        # 根据后缀名确定文件类型以使用不同的策略进行处理
        self.file_type = file_path.split('.')[-1]

	# 定义一个处理 excel 文件的函数
    def process_excel(self): 
        # 为了让例子尽可能简单易懂，具体的处理过程我用 print 代替，实际的处理可能会涉及多个函数
        print('Processing excel file')
    
    # 定义一个处理 csv 文件的函数
    def process_csv(self):
        print('Processing csv file')

	# 定义一个处理 txt 文件的函数
    def process_txt(self):
        print('Processing txt file')

	# 最后根据文件类型，使用不同的处理方法
	def process_file(self): 
        if self.file_type == 'xlsx':
            self.process_excel()
        elif self.file_type == 'csv':
            self.process_csv()
        elif self.file_type == 'txt':
            self.process_txt()
```

这个例子中，`FileProcessor` 只有五个方法，所以看起来不是很糟糕。但这是因为我将文件的处理过程都简化为了 `print` 语句，在实际应用中，这个类可能会有十个函数甚至更多，是一种比较糟糕的写法。

这样写有两个显著缺点：
1. 不利于增加功能。如果后期需要增加文件处理的类型，比如 word 或者 json 类型，这个类会变得越来越冗长。
2. 太多的功能放在了一个类里面，让这个类到底是干什么的变得模糊了。处理 excel、处理 csv 都是不同的功能，不应该放在一起。

## 策略模式出场

这时，就是策略模式出场的时候了！策略模式的使用场景就是这样，同样的一件事，用不同策略去做。

策略模式的基本思想是，将每个策略包装为一个单独的类，这些类拥有相同的接口，以便统一调用。

让我们来看看具体怎么写。

### 1. 一个策略的基类，定义策略类统一的接口

首先，为了让策略类都具有相同的接口，我要定义一个策略类的基类。所有策略类都会继承这个类，以达到规范接口的目的。

策略类都应该定义 `process_file` 方法作为接口，所以我在基类中写下这个方法。

```python
class ProcessStrategy:
    def process_file(self, filepath): 
        pass # 因为这个类只是作为基类使用，类里面的 process_file 不会真的实现，所以我们用 pass 表示它的功能就可以了。
```

为了让这个基类的子类必须实现 `process_file` 这个方法，我还需要 `from abc import abstractmethod` ，用 `abstractmethod` 修饰 `process_file`。这样可以约束子类必须实现 `process_file` 方法作为接口。

这里的 `abstractmethod` 是一个装饰器，如果你不太熟悉装饰器的概念，可以看我之前的文章。

另一方面，因为这个类仅仅作为基类使用，它不应该也不可以被实例化。所以我再 `import ABC` 来让 `ProcessStrategy` 继承于 ABC。

```python
class ProcessStrategy(ABC):
	@abstractmethod
	def process_file(self, filepath):
	    pass
```

ABC 的全称是 abstract base class，抽象基类，指的是仅作为基类，不应该被实例化的类。当我们需要定义这样的类时，就会继承 ABC。这样的效果是，如果一个继承自 `ABC` 的类，有被 `@abstractmethod` 装饰的方法，这个类就不能被实例化。

### 2. 策略类

定义好了基类，就可以定义具体的策略类了。

我们将策略类继承于刚刚定义的基类，重写 `process_file` 方法。

```python
class ExcelProcessStrategy(ProcessStrategy):
    def process_file(self, filepath):
        print('Processing excel file')

class CsvProcessStrategy(ProcessStrategy):
    def process_file(self, filepath):
        print('Processing csv file')

class TxtProcessStrategy(ProcessStrategy):
    def process_file(self, filepath):
        print('Processing txt file')
```

### 3. 在原本的类里面调用策略类

将原本的调用类内部方法，改为调用策略实例的方法。

```python
def process_file(self):
	if self.file_type == 'xlsx':
	    ExcelProcessStrategy().process_file(self.file_path)
	elif self.file_type == 'csv':
	    CsvProcessStrategy().process_file(self.file_path)
	elif self.file_type == 'txt':
	    TxtProcessStrategy().process_file(self.file_path)
```

这样我们就完成了策略模式的实现。

完整的代码如下。

```python
from abc import ABC, abstractmethod

class ProcessStrategy(ABC):
    @abstractmethod
    def process_file(self, filepath):
        pass

class ExcelProcessStrategy(ProcessStrategy):
    def process_file(self, filepath):
        print('Processing excel file')

class CsvProcessStrategy(ProcessStrategy):
    def process_file(self, filepath):
        print('Processing csv file')

class TxtProcessStrategy(ProcessStrategy):
    def process_file(self, filepath):
        print('Processing txt file')

class FileProcessor:
    def __init__(self, file_path):
        self.file_path = file_path
        self.file_type = file_path.split('.')[-1]

    def process_file(self):
        if self.file_type == 'xlsx':
            ExcelProcessStrategy().process_file(self.file_path)
        elif self.file_type == 'csv':
            CsvProcessStrategy().process_file(self.file_path)
        elif self.file_type == 'txt':
            TxtProcessStrategy().process_file(self.file_path)
```

也许你会觉得提升不明显，行数比之前还更多了。但正如我在文章开始说的，这个示例简化了文件处理步骤，实际一个策略类里可能会有多个函数，而不是例子中的一个。

让我们来尝试运行一下这个类。

```python
if __name__ == '__main__':
	file_processor = FileProcessor('file.xlsx')
	file_processor.process_file()
	file_processor = FileProcessor('file.csv')
	file_processor.process_file()
	file_processor = FileProcessor('file.txt')
	file_processor.process_file()
```

没有问题。

## 结尾

最后我想做个简短的总结。策略模式适用于，用不同方法干同一件事的场景。比如例子中的处理不同格式文件，或者导航时选择不同的交通工具，或者用不同的支付方式进行支付等等。

核心思想是将每个策略包装成一个类，为此需要先定义一个抽象基类作为策略类的基类，规范每个策略类的接口。

感谢你的阅读，我们下次再见。