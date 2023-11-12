---
title: 使用 translate 方法进行字符串替换
date: 2023-9-28 21:00:00 +0800
categories: [Python]
tags: [str]
pin: false
---

说到字符串中的替换，一般都会想到 `replace` 方法，但是如果遇到像是把一个字符串的 `a` 替换成 `e`，把 `e` 替换成 `a`，这样互相替换的需求，用 `replace` 就会过于麻烦了，需要使用中间变量。

所以这里提供另一种方法，使用 `translate` 实现。先创建一个映射，再来翻译。

```python
a = "I am the king of the world!"
trans = str.maketrans("ae", "ea")  # 建立映射关系
print(a.translate(trans))  # 输出"I em tha king of tha world!"

```

需要注意的地方是第二行，maketrans 两个参数的字符串长度需要一致。