Hi 朋友们，我会用一系列文章展示 Python 高级特性，这篇文章是系列的第二篇。

上一篇文章中，我们讲了推导式，这篇文章我们来讲讲生成器。我会展示生成器相对于列表的优势，并且教导你如何创建和使用生成器。

# 生成器的函数写法

首先让我们看看下面的代码。

```python
def square_numbers(nums):
    result = []
    for i in nums:
        result.append(i * i)
    return result

my_nums = square_numbers([1,2,3,4,5])
print(my_nums)
```

在函数 `square_numbers` 中，它会接受一个列表，并循环其中的每个元素，将其平方后添加到一个新列表中。最后，将新列表返回。如果你运行了以上代码，你会发现它会打印出 `[1, 4, 9, 16, 25]`。

现在我们来看看如何将这个函数变为一个生成器函数。

```python
def square_numbers(nums):
    for i in nums:
        yield i * i

my_nums = square_numbers([1,2,3,4,5])
print(my_nums)
```

`yield` 关键字是生成器函数的关键。它会将函数变为一个生成器，每次调用这个函数时，都会从 `yield` 处返回。再次执行时，又从上次返回的 `yield` 处继续。这样，我们就不需要创建一个新列表，而是可以在需要的时候，生成一个新值。

如果你运行了上面的代码，你会发现结果和之前不同，返回的并不是一个列表，而是一个生成器对象。

生成器对象是一个可迭代对象，你可以使用 `for` 循环来遍历它，也可以使用 `next` 函数来获取下一个值。让我们尝试看看。

```python
print('首先使用 next 函数：')
print(next(my_nums))

print('接下来是使用 for 循环：')
for num in my_nums:
    print(num)
```

你会发现，在使用 `next` 函数返回第一个值 `1` 后，再从 `for` 中返回的是从第二个值开始的。这是因为生成器每次只会生成一个词，同时记住上一次的位置。所以，当生成第一个值后，下次生成的就是第二个值了。

# 生成器的表达式写法

现在，让我们展示生成器的另一种写法，生成器表达式。

在上次视频中，我们展示了列表推导式，它可以非常方便地创建一个新列表。如果要实现刚刚的列表平方功能，我们可以这样写：

```python
my_list = [i * i for i in [1,2,3,4,5]]
print(my_list)
```

生成器也存在着这样的方法，我们只需要将列表推导式中的方括号 `[]` 替换为圆括号 `()` 即可。就像这样：

```python
my_gen = (i * i for i in [1,2,3,4,5])
```

# 生成器的优势

生成器相对于列表的优势在于，它不会一次性生成所有的值，而是在需要的时候才生成。想象你需要处理一个非常大的数据集，如果使用列表，它会一次性生成所有的值，占用大量内存。而生成器则不会，它只会在需要的时候生成一个值，然后释放内存。

让我们来看一个例子，在这个例子中，我会分别使用列表和生成器来生成 1000000 个随机数，看看列表和生成器分别会占用多少时间和内存。

```python
import psutil, time, os, random

random_nums = [i for i in range(10)]

list_start_time, list_start_mem = time.time(), psutil.Process(os.getpid()).memory_info().rss/1024/1024
num_list = [random.choice(random_nums) for _ in range(1000000)]
list_end_time, list_end_mem = time.time(), psutil.Process(os.getpid()).memory_info().rss/1024/1024 
print(f'''Before build list, program takes memory: {list_start_mem} MB
      Took time: {list_end_time - list_start_time} seconds
      After build list, program takes memory: {list_end_mem} MB''')

gen_start_time, gen_start_mem = time.time(), psutil.Process(os.getpid()).memory_info().rss/1024/1024
num_gen = (random.choice(random_nums) for _ in range(1000000))
gen_end_time, gen_end_mem = time.time(), psutil.Process(os.getpid()).memory_info().rss/1024/1024
print(f'''Before build generator, program takes memory: {gen_start_mem} MB
      Took time: {gen_end_time - gen_start_time} seconds
      After build generator, program takes memory: {gen_end_mem} MB''')
```

在我的电脑上运行这个代码，我得到的结果是：

```
Before build list, program takes memory: 28.41796875 MB
Took time: 3.9176957607269287 seconds
After build list, program takes memory: 36.4765625 MB
Before build generator, program takes memory: 36.4765625 MB
Took time: 2.6226043701171875e-06 seconds
After build generator, program takes memory: 36.4765625 MB
```

你会发现，生成器在生成 1000000 个随机数时，所占用的内存几乎没有变化，而列表则占用了 8 MB 的内存。同时，生成器的生成时间也比列表快了许多。

所以，当你需要处理大量数据时，生成器是一个非常好的选择。

# 最后

我希望这篇文章能够对你有帮助，如果你有任何问题，欢迎在评论区留言。
如果你觉得这篇文章还不错，欢迎点赞分享。也欢迎关注我的频道，我会持续更新 Python 相关的内容。感谢你的阅读。