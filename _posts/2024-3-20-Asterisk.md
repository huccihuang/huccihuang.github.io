---
title: "Python Features: Asterisk - small shape, versatile functionality."
date: 2024-2-28 12:00 +0800
categories: [Python Features]
tags: [Python]
---

Hey there, Welcome to my blog.

Today I want to share a simple content, the function of `*` in Python. In addition to the mathematical operations we are familiar with, `*` can also be used for repetition, packaging and unpacking.

In this article, you will see how to flexibly apply `*` in actual code to achieve the three purposes of repetition, packaging, and unpacking, and improve our work efficiency.

Let us start!

# Repetition

For numbers, `*` could be used for multiplication, as we all know before.

```python
	print(2 * 3)  # Output: 6
```

But for lists and strings, `*` will also work.

When we need a string containing multiple repeating characters, or a list containing multiple identical sequences, we can use `*` .

For example, if I need to write a string of hahaha, I can multiply the string 'ha' by 3; or if I need a list of [1,2,1,2,1,2], I You can also multiply the list [1,2] by 3.

```python
print('ha' * 3)  # Output: 'hahaha'
print([1, 2] * 3)  # Output: [1, 2, 1, 2, 1, 2]
```

This is the repeated function of `*`. Now let's talk about packaging and unpacking.

# Packing

What does packing and unpacking mean?

- Unpacking is the opposite of packing. It involves taking a collection of values (like a list or tuple) and “unpacking” them into individual variables.
- Packing means bundling multiple values into a single container, such as a tuple or list.

Now let's see how to use `*` to pack and unpack. The usage of `*` and `**` is slightly different, I will show them separately.

`*` can pack a sequence. When multiple values are passed into a variable with *, the variable will become a list containing all the passed values.

Let's look at a piece of code.

First define numbers as a list containing 5 values, and then assign numbers to two variables first and rest respectively, with * added before rest. Let's print these two variables and see what happens.

```python
numbers = [1, 2, 3, 4, 5]
first, *rest = numbers
print(first)  # Output: 1
print(rest)  # Output: [2, 3, 4, 5]
```

The first value is passed into first , which is still an integer; and the remaining values are passed into rest as a list. This is because rest is preceded by *, and no matter how many values are passed in, they will be packed into a list.

Using * to pack is also common in formal parameters of functions. Let’s look at an example.

Define a function print_values whose parameters are *args, which means that no matter how many values are passed into this function, they will be packed into a sequence. The function then iterates through args and prints out each value inside.

```python
def print_values(*args):
    for arg in args:
			print(args)

sum_values(1, 2, 3, 4)  # Output: 10
```

This is the function of * when used as a function parameter, to pack multiple incoming values into a sequence.

If you want to use ** when using function parameters, it will be different from *. The difference is that ** packs the keyword arguments into a dictionary. Let's look at an example.

Define a function example with parameters **kwargs. Just mentioned ** will pack keyword arguments into a dictionary, so this kwargs will be a dictionary. Then iterate through kwargs in the function and print out the key-value pairs in the form of key = value. Let's call this function and pass in a few keyword arguments.

```python
def example(**kwargs):
    for key, value in kwargs.items():
        print(f"{key} = {value}")

example(a=1, b=2, c=3)  # Output: a = 1, b = 2, c = 3
```

After talking about packing, let’s look at unpacking.

# Unpacking

`*` can unpack a sequence and unpack a sequence into several items.

Through this, we can pass in a list with `*` when passing parameters to the function to achieve the purpose of passing in multiple parameters.

We define a function called greet which takes two parameters. Then we define a tuple person containing two variables, and use * to unpack person and send it to greet.

```python
def greet(name, age):
    print(f"Hello {name}, you are {age} years old.")

person = ("Alice", 30)
greet(*person)  # Output: Hello Alice, you are 30 years old.
```

When we want to merge two sequences, this can also be done flexibly using *.

We define a list list1, and a tuple tuple 1. Then define a new list to merge them. We can use * in the new list to unpack the list and tuple.

```python
list1 = [1, 2, 3]
tuple1 = (4, 5, 6)
merged = [*list1, *tuple1]  # Result: [1, 2, 3, 4, 5, 6]
```

In terms of unpacking, the difference between ** and * is that * operates on sequences, while ** operates on dictionaries.

Let's look at an example of unpacking a dictionary into keyword arguments to a function.

We define a function that will print out the parameter names and parameter values in sequence. Then define a dictionary containing these parameter names, and then use ** to unpack the dictionary and pass it into the function. What is actually passed into the function becomes multiple keyword parameters.

```python
def create_profile(name, age, email, profession):
    print(f"Name: {name}")
    print(f"Age: {age}")
    print(f"Email: {email}")
    print(f"Profession: {profession}")

options = {
    "name": "John Doe",
    "age": 30,
    "email": "john.doe@example.com",
    "profession": "Engineer"
}

create_profile(**options)
```

Merging multiple dictionaries can also use ** .

We define two dictionaries and then define a new dictionary. Use ** to unpack these two dictionaries in the new dictionary. The result is that the new dictionary contains all key-value pairs of these two dictionaries.

```python
dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}
merged = {**dict1, **dict2}
print(merged)  # Output: {'a': 1, 'b': 3, 'c': 4}
```

## The Last

Finally, I would like to make a brief summary.

Generally speaking, in addition to mathematical operations, * can also do three things: repetition, packaging, and unpacking. By packaging and unpacking in different scenarios, we can achieve the purpose of processing function parameters, merging sequences, and merging dictionaries.

After this video, you should now be able to use * flexibly in actual code. You can check your previous code to see if there are ways to optimize it.

If you have any questions, please leave a message in the comment area and I will reply as soon as possible.

Thanks for reading!
