---
layout: post
title: 使用 mypy 来改善你的 Python 项目
date: 2018-07-31 11:46:43 +0800
categories: python
---

> 本文翻译自 Clément Verna 的文章 [Improve your Python projects with mypy](https://fedoramagazine.org/improve-python-projects-mypy/)。

*mypy* 实用工具是一款针对 Python 的静态类型检查程序。它结合了动态类型和静态类型的优点。想必各位读者也知道，Python 是动态类型编程语言。那么 Python 的静态类型检查有何意义呢？且听细细分解。

### 什么是类型？

系统在存储数据之前，需要知道程序使用的数据需要多少内存空间。为此，编程语言会使用 *类型* 来进行确定。类型会匹配必须为程序分配用以存储数据的内存大小。几种最常见的类型包括整数、浮点数和字符串。

动态类型语言会在运行时检查程序中的类型。Python 即为动态类型语言（原文中写道：Python 有一个「弱」类型系统。为避免歧义，译者进行了修改。——译者注），这意味着解释器不强制进行类型检查。

静态类型语言在程序运行之前通过对源代码的分析来检查类型。如果程序可以通过静态类型检查，那就可以确保其满足某些类型安全性。静态类型检查会在运行前检测应用程序代码中可能的错误。这便是 *mypy*（Python 应用程序的静态类型检查）的价值所在。

### 安装并运行 mypy

Fedora 仓库中已经有了 *mypy* 的包了，所以安装起来很简单：

```
$ dnf install python3-mypy
```

现在，我们创建一个简单的 Python 应用程序来测试和理解 *mypy* 的工作原理。

```
class Person():
    def __init__(self, surname, firstname, age, job):
        self.surname = surname
        self.firstname = firstname
        self.age = age
        self.job = job


def display_doctors(persons):
    for person in persons:
        if person.job.lower()in['gp', 'dentist', 'cardiologist']:
            print(f'{person.surname} {person.name}')


mike = Person('Davis', 'Mike', '45', 'dentist')
john = Person('Roberts', 'John', 21, 'teacher')
lee = Person('Willams', 'Lee', 'gp', 56)

display_doctors(mike)
display_doctors([mike, john, 'lee'])
```

将以上这段代码保存到名为 *testing_mypy.py* 的文件中。接下来，针对测试代码运行 *mypy*：

```
$ mypy testing_mypy.py
```

需要注意的是，默认情况下，*mypy* 是宽容的（permissive）。运行以上示例时，不会返回错误。此默认设置对于具有较大代码库的应用程序非常有用，因为你可以逐步将 *mypy* 引入项目中。

### 添加类型提示

如果你使用的是 Fedora 27 或更高版本中的默认 Python 3.6，就可以使用类型提示来标注代码。然后 *mypy* 可以根据这些类型提示来检查应用程序。接下来，使用编辑器给示例程序中的 `display_doctors` 函数添加类型提示：

```
from typing import List


def display_doctors(persons: List[Person]) -> None:
    for person in persons:
        if person.job.lower()in['gp', 'dentist', 'cardiologist']:
            print(f'{person.surname} {person.name}')
```

该示例添加了以下提示：

- `List[Person]`——该语法表示 `display_doctors` 函数需要一个 `Person` 对象列表作为参数。
- `-> None`——此语法指定该函数将返回 `None` 值。

现在，再次运行 *mypy*：

```
$ mypy testing_mypy.py
test_mypy.py:19: error: "Person" has no attribute "name"
test_mypy.py:26: error: Argument 1 to "display_doctors" has incompatible type "Person"; expected "List[Person]"
test_mypy.py:27: error: List item 2 has incompatible type "str"; expected "Person"
```

这会导致一些错误，你可以按如下方式修复：

- 首先，在 `print` 语句中，程序尝试访问不存在的 `person.name`。其实，程序应使用 `person.firstname`。
- 当程序首次调用 `display_doctors` 时，则会发生第二个错误。其期望的参数是 `Person` 的列表，但该示例只传递了 `Person`。
- 最后一个错误则是由于 `Person` 列表中的错误引起的。 示例应用程序添加了字符串 `'lee'`，而没有将 `Person` 对象 `lee` 添加到列表中。

以下是示例程序的相关修补程序：

```
def display_doctors(persons: List[Person]) -> None:
    for person in persons:
        if person.job.lower()in['gp', 'dentist', 'cardiologist']:
            print(f'{person.surname} {person.firstname}')
...
```

```
display_doctors([mike])
display_doctors([mike, john, lee])
```

编辑上面的代码，然后再次运行 *mypy*。

程序中还有其他错误。各位读者可以试着从中找找看。可以针对 `Person` 这个类使用类型提示。先自己尝试一下。想要检查一下的话，可以在 [GitHub](https://github.com/cverna/testing_mypy) 上参考这个没有错误的程序。

### 结语

希望本篇 *mypy* 简短介绍可以对你的代码库有所禆益。 *mypy* 让我们可以更轻松地维护代码以及阅读应用程序代码。还可以让我们在代码运行之前发现错别字和错误。值得一提的是，*mypy* 也可用于 Python 2.7 应用程序。只不过，它在注释方面的语法会有不同。
