---
title: "Python Features: Decorators - Alter functions functionality without changing source code"
date: 2024-2-15 12:00 +0800
categories: [Python Features]
tags: [Python, decorators]
---

Hey guys, welcome to my blog.

Today I am going to share the concept decorators, which could dynamically alter functions functionality without changing source code.

In this article, I would show you how decorators work and why we need to use decorators.

Let us start!

## Principle of decorators: Take functions as arguments

In Python, functions are first class objects, if you have not heard of this concept before, never mind, all you need to know is that we can pass functions into other functions as arguments. While this may sound a bit abstract, let's look at a simple example and you will understand it soon.

```python
def square(x):
    return x * x

def print_running(f, x):
    print(f'"{f.__name__}" is running.')
    return f(x)

result = print_running(square, 2)
print(f'result = {result}')
```


In this example, `square` is a simple function that takes a number and returns its square.

And `print_running` function accepts a function object `f` and a number `x` as parameters. It then prints a string that including the name of function `f`, and returns the result of passing `x` into `f`.

When I pass `square` and number 2 into `print_running`, let's consider what happens.

First, "square is running" is printed, indicating that `square` is playing the role of `f`. Then, `square(2)` is executed, resulting in the number 4 being assigned to the variable `result`.

Through this example, you can grasp the concept of passing functions into other functions. Now, let's delve into today's topic—decorators.

## What is decorators


### The essence of decorators


Decorators are essentially functions that take another function as an argument. Inside the decorator function, a new function is defined based on the input function.

This new function not only contains the functionality of the input function but also adds additional features. The decorator then returns this new function, allowing us to replace the original input function with the returned new function in our code.

Although this may still sound abstract, let's look at a simple example.

```javascript
def decorator(func):
    def wrapper(*args,**kwargs):
        print(f'{func.__name__} is running')
        result = func(*args,**kwargs)    
        return result
    return wrapper
```


I want to define a decorator, which takes a function `func` as a parameter. Then, the decorator will define a function internally, as the function to be returned in the end, so here I define a function called `wrapper`.

This `wrapper` serves as the new function and needs to replace the original function `func`, so its parameters need to be the same as `func`. However, we don't know what the `func` parameter passed in is, and our decorator is not specifically for a single function; each function has different parameters. So, how do we define the parameters for `wrapper`?

The answer is `*args` and `**kwargs`. This way, `wrapper` can accept any parameters passed in.

Now, I can write the functionality of `wrapper`.

`wrapper` should execute the original functionality of `func`. So, I use `result` to store the original result of the function's execution.

The additional functionality I want is to print a line of string, indicating which function is running, just like what `print_running` did earlier.

Finally, I return `result`.

This functionality doesn't actually have much value; I'm writing it this way to keep the example simple and easy to understand.

And there you have it—a decorator is defined. This decorator is very simple, but it nicely reveals the essence of decorators. Now you should be able to understand how decorators work.

I want to rewrite this decorator to make it measure the running time of a function. This would be a more useful feature. 

To do this, we need to record the start time before the function runs and the end time after it runs. We can then obtain the running time by printing the difference between the two.

```python
import time

def decorator(func):
    def wrapper(*args,**kwargs):
        start_time = time.time()
        result = func(*args,**kwargs)
        end_time = time.time()
        print(f'{func.__name__} execution time: {end_time-start_time} seconds')
        return result
    return wrapper
```


### Usage


Let's take a look at how to use decorators.

We can directly pass the function to be decorated to the decorator, and then pass the returned new function to a newly defined variable. Now, this variable decorated_square is a function, which is the decorated square.

Now, calling `decorated_square` will print execution time.

```python
decorated_square = decorator(square)
result = decorated_square(10)
print(f'{result = }')
```


```other
execution time: 6.67572021484375e-06 seconds
result=100
```


As mentioned before, the decorated function is used to replace the original function. Therefore, we can be more direct and define it directly with the original name of the function.

```python
square = decorator(square)
result = square(10)
print(f'result = {result}')
```


In fact, Python provides a simpler way to use decorators, which is to "put a hat" on the function definition.

```python
@decorator
def square(x):
    return x * x
  
result = square(10)
print(f'result = {result}')
```


Putting on this hat is completely equivalent to defining the decorated function with the original name of the function.

### Decorators with parameters


Now, I want to increase the difficulty a bit.

I don't just want the decorator to record the running time of the function, I also need the decorator to measure whether the running time of the function exceeds a threshold. Different functions have different thresholds. For functions with a large amount of computation, the threshold is higher. For functions with a small amount of computation, the threshold is lower. This means that different functions require different decorators because they have different thresholds.

So do we need to define multiple decorators? No, we don't. We can define a function that defines decorators. Let's call it a decorator generator for now. The decorator generator will generate different decorators based on the parameters. It sounds a bit confusing, but you'll quickly understand it after looking at the example.

Here is an example of how to use a decorator generator:

```python
def timer(threshold):
    def decorator(func):
        def wrapper(*args, **kwargs):
            start_time = time.time()
            result = func(*args, **kwargs)
            end_time = time.time()
            if end_time - start_time > threshold:
                print(f'{func.__name__} took longer than {threshold} seconds to execute')
            return result
        return wrapper
    return decorator
```

Let's try using this decorator generator. Here is an example.

```python
@timer(threshold=0.5)
def sleep_06():
    time.sleep(0.6)
```


The above statement is equivalent to `sleep_06 = time_it(threshold=0.5)(sleep_06)`.

## wrapper attributes


Finally, I need to add a little bit about the attributes of decorated functions.

The wrapper returned by the decorator is a replacement for func. Of course, we hope that it will retain the metadata of func, such as doc and name.

Let's see what the name of the `sleep_06` is now.

```python
print(sleep_06.__name__)
```


It is undoubtedly the wrapper, because the wrapper does not inherit the meta-attributes of func.

Now let's copy the metadata of func to the wrapper by adding a line of `@functools.wraps(func)` on the wrapper.

```python
import functools
import time

def decorator(func):
    @functools.wraps(func)
    def wrapper(*args,**kwargs):
        start_time = time.time()
        result = func(*args,**kwargs)
        end_time = time.time()
        print(f'{func.__name__} took {end_time-start_time} seconds.')
        return result
    return wrapper

```


`@functools.wraps(func)` is also a decorator. It defines a decorator that takes func as an input parameter and allows wrapper to have func's metadata.

## Why use decorators


At this point, you may be wondering what the benefits of using decorators are. Even if you don't use decorators to add functionality, you can still achieve the same purpose by adding a few lines to the original function. For example, for the square function just mentioned, can't you just add a few more lines?

```python
def square(x):
    start_time = time.time()
    result = x*x
    end_time = time.time()
    print(f'square execution time: {end_time}-{start_time} seconds')
    return result
```


There are three reasons why we use decorators.

The first reason is that using decorators can improve code reuse and avoid repetitive redundant code. If I have multiple functions that need to measure execution time, I can directly apply the decorator to these functions instead of adding the same code to multiple functions. This code is both redundant and inconvenient for later maintenance.

The second reason is that using decorators can ensure the logic of the function is clear. If a function is already very complex, and I have to measure the execution time by modifying the internal code, it will blur the main logic of the function itself. At the same time, one of the principles of software development is single responsibility, which means that a function should only have one responsibility.

Third, through decorators, we can extend other people's functions. Imagine that we are using a function from a third-party library, but I want to add additional behavior, such as measuring execution time, then I can use a decorator to wrap it instead of going into the library to modify it.

## At Last


If you have any questions, please feel free to leave a comment below and I will reply as soon as I see it.

If you found this article helpful, please like and share it with anyone you think might find it useful.

Thank you for reading!

