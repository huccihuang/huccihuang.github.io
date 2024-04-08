---
title: "Python Features: Special Methods - Make custom classes behave like build-in classes"
date: 2024-4-4 12:00 +0800
categories: [Python Features]
tags: [Python, special methods]
---

Hey guys, today I am going to talk about special methods.

Special methods, also known as magic methods. They are usually written inside a class definition, used to specify how a class interacts with Python's built-in functions and operators. By defining magic methods within our own class, we can make our class operate like built-in Python classes.

I will explain in the video what magic methods are, how we can define them in our own classes, and some common magic methods.

Let's get started!

## What is magic methods

You may have used the plus sign in the code to process numbers and strings. You will find that although they are both plus signs, their logic is different. The plus sign for numbers adds the values, while the plus sign for strings concatenates them.

```python
print(f'{1+2 = }')
print(f"{'a'+'b' = }")
```

The difference is because string types and numeric types behave differently towards the plus sign. The behavior of treating the plus sign is defined in the magic method add.

When we run these two lines of code, what actually happens is that the integer object and the string object call __add__  , like this.

```python
(x:=1).__add__(2) 
'a'.__add__('b')
```

The result is same as our direct use of the plus sign. So I would say that when we use the plus sign on an object, we are actually calling the object's __add__ magic method.

Methods like this, which define how a class treats operators and Python built-in functions, are magic methods. Because it often has two underscores before and after, it is also called the dunder method. dunder is the abbreviation of double underscore.

## How to use

Knowing this, we can well control the behavior of the class when we define it ourselves. For example, now I define a shopping cart ShoppingCart class.

```python
from typing import List

class ShoppingCart:
    def __init__(self, items: List[str]):
        self.items = items
```

I've already written the constructor, which by the way is also a magic method.

The ShoppingCart has an attribute items, which is a list indicating what is in the shopping cart.

Now I want to define a dunder add function to add two shopping carts. So the dunder add functions parameters are self and another_cart 。

```python
def __add__(self, another_cart):
	new_cart = ShoppingCart(self.items + another_cart.items)
	return new_cart
```
    
After adding, I want to return a new shopping cart with everything in both shopping carts.

So, here is the dunder add method.

Now let's run the addition. Define two shopping carts and add them together.

```python
cart1 = ShoppingCart(["apple", "banana"])
cart2 = ShoppingCart(["orange", "pear"])
new_cart = cart1 + cart2
print(new_cart.items)
```

With this, we have completed the behavior of our custom class when faced with a plus sign.

## What other magic methods are there

More magic methods and uses we can find on this website.

[https://diveintopython3.net/special-method-names.html](https://diveintopython3.net/special-method-names.html)

Now, I would like to introduce a few more important and common magic methods.  

### `__str__`

If we were to print the cart1 we just defined, that would be a memory address.

But I hope to get a string expression that we humans can understand, such as what is in the shopping cart.

Then I need to define a str magic method. This magic method can define how our class is expressed.

```python
def __str__(self):
	return f"Cart({self.items})"
```

This way, when I print again, the contents of the shopping cart will be displayed.

`__str__` is such a method that can define a typed string representation.

I would like to mention here by the way, though it is not the focus today, many people often confused about the difference between `__str__` and `__repr__`. Their difference is that one is for users and the other is for developers. We can directly see their differences from the `__repr__` and `__str__` of the `datetime.datetime` object. 

```python
>>> import datetime
>>> dt = datetime.datetime(2024,1,1)
>>> str(dt)
'2024-01-01 00:00:00'
>>> repr(dt)
'datetime.datetime(2024, 1, 1, 0, 0)'
```

I will choose str instead of repr for demonstration here because str is preferred when executing print statements. In our actual project, if we have to choose one between str and repr, repr should be chosen first.

### `__len__`

I want to see how many items are in the shopping cart by calling the len function.

The result of running now will be an error saying that this class does not define the len method.

```python
def __len__(self):
	return len(self.items)
```

Then we define the magic method `__len__` internally, which defines what will be returned when call the len function.

Check how many items are in the shopping cart and return the length of items as items is list.

### `__call__`

Sometimes we will hope that the class can be called directly like a function. For example, through `cart('orange')` we can directly put oranges in the shopping cart. At this time, you can define a `__call__`.

The `__call__` function allows us to call an instance like a function. Here I want to put something into the shopping cart, so I just append it to items.

```python
def __call__(self, item: str):
    self.items.append(item)
```

## Question

I want to show you a question I encountered before. You can try it in your head or on the editor to verify your understanding of the magic method just mentioned.

Define a class `add` so that it can achieve the following effects.

```
addTwo = add(2)
addTwo ## 2
addTwo + 5 ## 7
addTwo(3) ## 5
addTwo(3)(5) ## 10
```

Here is my solution:

```Python
class add:
	def __init__(self, value):
		self.value = value 
	def __call__(self, num): 
		return add(self.value + num) 
	def __add__(self, num): 
		return self.value+num 
	def __str__(self): 
		return str(self.value)
```

And if you are familiar with class inheritance, the solution can be much shorter:

```python
class add(int): 
	def __call__(self, n): 
		return add(self + n)
```

## At Last

I hope to give you an idea of what the magic method is and how we can take advantage of it.

If you have any questions, you can leave a message in the comment area below and I will try my best to reply. If you find this article helpful, please like it or share it with anyone you think may need it.

Thanks for your reading!

