---
title: [Advanced features of Python] Lambda function: simple one-time function
date: 2024-2-28 12:00 +0800
categories: [Python 高级特性]
tags: [Python, Lambda function]
---

Today I am gonna show you lambda function, also know as anonymous function. When we need to build some simple, one-time function, it is the show time of lambda function.

Through this article, you will learn how to create and use lambda functions and see how useful they can be.

Let’s go ahead!

# How to construct a lambda function

This is a function, to add 2 numbers.

```python
def add(x, y):
    return x + y
```

Although this is a very simple function, we need 2 lines of code. But what about lambda function?

```python
add = lambda x, y: x + y
```

Just 1 line.

Let’s take a look at the construction of this lambda function. The add at the beginning of the statement represents the function name. The lambda keyword indicates that this is a lambda function, a and b are the parameters of the function. And then a colon, followed by the colon is what the lambda function returns. If the lambda function does not require parameters, just add a colon after lambda.

Easy to understand, right? Now Let’s see how to use the lambda function in practical code.

# How to use lambda function

## Basic usage

The method of using lambda function is same as ordinary functions, function name and arguments.

```python
add = lambda a,b: a+b
print(add(1,2))
```

## Use lambda function as a parameter

An important use of lambda functions is to use them as parameters of other functions, such as map and sorted functions that require functions as parameters.

Here is an example that use lambda in map.

```python
my_lst = [1,2,3,4,5]
print(list(map(lambda x: x**2, my_lst)))
```

I am giving a brief explanation of map function here. It takes a function an iterable object as parameters, then applied the function to each element in the iterable object, returning a new iterator.

In this example, we passed in lambda function could square numbers. So what we get is the square of each value in `my_lst`.

```plaintext
[1, 4, 9, 16, 25]
```

## Use with dictionary

I want to share my favorite way to use lambda function, and that is with dictionary. When we need to write multiple conditional branches, we need to write multiple `elif`, which is not Pythonic. But using lambda function with dictionary could solve this problem very well.

For example, now we need a function that can return different strings based on students' scores.

```python
def stu_score_map(x):
    if x > 85:
        return "Good"
    elif 60 <= x <= 85:
        return "Average"
    else:
        return "Bad"
```

If there were more conditions, this function would be longer.

Now I'm going to use a lambda with a dictionary.

```python
stu_score_map = lambda x: {
    x > 85: "Good",
    60 <= x <= 85: "Average",
    x < 60: "Bad"
}[True]

print(stu_score_map(75))
```

It is shorter than before, and more readable.

The lambda function will return the value with key True. And the keys of dictionary are bool expression that become True or False depending on the input parameter x.

If you want different operations based on the student's score, you can change value to a function name or an lambda function.

```python
stu_score_map = lambda x: {
    x > 85: "Good",
    60 <= x <= 85: lambda y: y+10, # add 10 to score between 60 and 85
    x < 60: "Bad"
}[True]

print(stu_score_map(70)(70))
```

# At last

After reading this blog, you may now be able to take advantages of lambda function in practical code, you can check your previous code to see if there are any areas that can be optimized.

If you have any questions, feel free to ask in the comment section below, I will do my best to answer. If you think the article is helpful, you can like and share it with anyone who you think would find it useful. You can also subscribed my channel for future posts. Thanks for reading.

