---
title: "Python Common Mistakes: Why shouldn't we use a list as the default parameter of a function"
date: 2024-2-15 12:00 +0800
categories: [Python Features]
tags: [Python, decorators]
---

Hey guys, today I would like to discuss a topic of why we should not use a list as the default parameter of a function.

I will demonstrate the potential issues that arise from doing so, the reasons behind these problems, and alternative solutions. Let's get started!

## Example

Let's take a look at a simple function called `add_to`, which takes a number `num` and a list `target` as parameters, then append `num` to `target`. The default value for `target` is an empty list, which means that if we only pass in one argument, `target` will be an empty list.

```python
def add_to(num, target: List=[]):
    target.append(num)
    return target
```

This function may not be very useful in terms of its functionality, I wrote it this way in order to demonstrate the main topic of this discussion.

```python
print(add_to(1))
print(add_to(2))
```

Let's try passing in one argument and then calling the function twice. Can you guess what will happen?

You might think that if we pass in only one argument, `target` will be an empty list, and appending a number to an empty list will result in a list with a length of 1.

Therefore, it is possible that we will print two lists, each with a length of 1.

```
[1]
[2]
```

Let's actually run it and see what the result is.

```
[1]
[1, 2]
```

It is different from what we expected. The result of the first call is preserved when the function is called the second time.

Why?

## Reason

We can probably guess that when executing the `append` statement, the same object is being used as `target`. We can verify this by printing the ID of `target` inside the function.

The id of each object is unique, so using the id can help us check if two objects are the same object.

```python
def add_to(num, target=[]):
    print(id(target))
    target.append(num)
    return target

print(add_to(1))
print(add_to(2))
```

If you run it, you will see that the ID of `target` is the same for both function calls.

This is because default parameters are attributes of the function, and they are defined when the function is defined, not when the function is called. So, when we use a default parameter, we are actually using the same variable at the same memory address.

If our default parameter is a mutable object like a list or a dictionary, unintended modifications like the one we just observed can occur.

However, if our default parameter is an immutable object like a number, string, or tuple, such issues will not occur.

## Solution

So, we should avoid using mutable objects as default parameters.

In the example we just discussed, if we want to achieve the same result, we can make the following modification.

```python
def add_to(num, target=None):
    if not target:
        target = []
    target.append(num)
    return target
```

Set the default parameter `target` to `None`, and then inside the function, if `target` is `None`, define it as an empty list.

## At Last

That's all for now. I hope you have learned something from it.

If you have any questions, please feel free to leave a comment in the section below, and I'll be glad to respond.

Thank you for reading, see you next time.