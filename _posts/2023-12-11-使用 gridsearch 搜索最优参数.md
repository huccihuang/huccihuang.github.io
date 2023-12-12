---
title: 使用 gridsearch 搜索最优参数
date: 2023-12-11 21:00:00 +0800
categories: [dataScience]
tags: [python, dataScience, sklearn]
---

## gridsearch 介绍

在使用模型的时候会迷茫要使用什么参数，比如 learning_rate, max_depth 等等，gridsearch 就可以用来搜索最佳参数。

`sklearn.model_selection.GridSearchCV` 会穷举所有可能的参数组合，进行评估，找出最优的参数组合。

## 一个简单的例子

gridsearch 需要三个参数，使用的模型、需要搜索的参数、评估方法。

```python
param_grid = {'C': [0.1, 1, 10], 'kernel': ['linear', 'rbf']}
clf = svm.SVC()
grid_search = GridSearchCV(clf, param_grid, scoring='accuracy')

grid_search.fit(X, y)
print(grid_search.best_params_) # 找到的最优参数组合
print(grid_search.best_score_) # 找到的最优参数组合下的性能评分
```

## 和 pipeline 结合使用

pipeline 是一个包含了许多步骤的管道，如数据标准化，数据填充等等，将 pipeline 和 gridsearch 结合可以省很多步骤，pipeline 与单一模型不同的地方在于参数的命名。

一般的参数直接填写参数名称就可以了，而 pipeline 需要写明是哪个步骤的哪个参数，格式为 `步骤__参数` 。

```python
pipe = Pipeline([
    ('scaler', StandardScaler()),  # 预处理步骤 1: 标准化特征
    ('svc', svm.SVC())  # 分类器
])

param_grid = {
    'scaler__with_mean': [True, False],  # 预处理步骤 1 的超参数
    'svc__C': [0.1, 1, 10],  # SVC 的 C 参数
    'svc__kernel': ['linear', 'rbf']  # SVC 的 kernel 参数
}
```