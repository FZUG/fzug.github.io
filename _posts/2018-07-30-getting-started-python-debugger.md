---
layout: post
title: 轻松上手 Python 调试器
date: 2018-07-30 15:03:00 +0800
categories: python
---

> 本文翻译自 Clément Verna 的文章 [Getting started with the Python debugger](https://fedoramagazine.org/getting-started-python-debugger/)。

Python 生态中包含了丰富的工具和库，可以极大地改善开发体验。例如，我们之前已经介绍了如何[使用交互式 shell 来改善 Python 编程体验]({% post_url 2018-07-29-enhance-python-interactive-shell %})。本文重点介绍另一种可以节省时间并提高 Python 技能的工具：Python 调试器。

## Python 调试器

Python 标准库中提供了一款调试器，名为 *pdb*。这款调试器提供调试所需的大多数功能，如断点、单行步进、堆栈帧检查等等。

了解一下 *pdb* 的基本知识还是很有用的，因为它是标准库的一部分。在我们无法安装其他增强型调试器的环境下我们便可以使用它来调试。

### 运行 pdb

运行 *pdb* 最简单的方法，是在命令行中将程序作为参数传递给调试器。以下面这个脚本为例：

```
# pdb_test.py
#!/usr/bin/python3

from time import sleep

def countdown(number):
    for i in range(number, 0, -1):
        print(i)
        sleep(1)

if __name__ == "__main__":
    seconds = 10
    countdown(seconds)
```

我们便可以从命令行中这样运行 *pdb*：

```
$ python3 -m pdb pdb_test.py
> /tmp/pdb_test.py(1)<module>()
-> from time import sleep
(Pdb)
```

另一种使用 *pdb* 的方法是在程序中设置断点。这时，需要先导入 *pdb* 模块并使用 *set_trace* 函数：

```
1 # pdb_test.py
2 #!/usr/bin/python3
3
4 from time import sleep
5 
6
7 def countdown(number):
8     for i in range(number, 0, -1):
9         import pdb; pdb.set_trace()
10         print(i)
11        sleep(1)
12
13
14 if __name__ == "__main__":
15     seconds = 10
16     countdown(seconds)
```

```
$ python3 pdb_test.py
> /tmp/pdb_test.py(6)countdown()
-> print(i)
(Pdb)
```

脚本会在断点处停止，*pdb* 会显示脚本中的下一行。你也可以在失败后执行调试器。这种方式又叫 *事后* 调试（postmortem debugging）。

### 指引执行堆栈

调试中的一个常见用例是指引执行堆栈。Python 调试器运行过程中，以下命令很有用：

- w(here) ：显示当前执行的行以及执行堆栈的位置。

```
$ python3 test_pdb.py 
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) w
/tmp/test_pdb.py(16)<module>()
-> countdown(seconds)
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb)
```

- l(ist) ：显示当前位置前后更多的上下文（代码）。

```
$ python3 test_pdb.py 
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) l
5 
6 
7 def countdown(number):
8 for i in range(number, 0, -1):
9 import pdb; pdb.set_trace()
10 -> print(i)
11 sleep(1)
12 
13 
14 if __name__ == "__main__":
15 seconds = 10
(Pdb)
```

- u(p)/d(own)：向上或向下指引调用堆栈。

```
$ py3 test_pdb.py 
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) up
> /tmp/test_pdb.py(16)<module>()
-> countdown(seconds)
(Pdb) down
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb)
```

### 单步执行程序

*pdb* 提供了以下命令便于我们执行或单步执行代码：

- n(ext)：继续执行，直至当前函数的下一行，或函数有返回值。
- s(tep)：执行当前行并在第一个可能的场合停止（在被调用的函数或当前函数中）。
- c(ontinue)：继续执行，仅在断点处停止。

```
$ py3 test_pdb.py 
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) n
10
> /tmp/test_pdb.py(11)countdown()
-> sleep(1)
(Pdb) n
> /tmp/test_pdb.py(8)countdown()
-> for i in range(number, 0, -1):
(Pdb) n
> /tmp/test_pdb.py(9)countdown()
-> import pdb; pdb.set_trace()
(Pdb) s
--Call--
> /usr/lib64/python3.6/pdb.py(1584)set_trace()
-> def set_trace():
(Pdb) c
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) c
9
> /tmp/test_pdb.py(9)countdown()
-> import pdb; pdb.set_trace()
(Pdb)
```

这个例子中便可看出 *next* 和 *step* 的区别。实际上，当使用 *step* 时，调试器会进入 *pdb* 模块源代码，而 *next* 则会执行 *set_trace* 函数。

### 检查变量内容

*pdb* 非常有用的一种场景，是检查执行堆栈中存储的变量的内容。例如，*a(rgs)* 命令会打印当前函数的变量，如下所示：

```
py3 test_pdb.py 
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) where
/tmp/test_pdb.py(16)<module>()
-> countdown(seconds)
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) args
number = 10
(Pdb)
```

*pdb* 打印了变量 *number* 的值，在本例中为10。

另一个可用于打印变量值的命令是 *p(rint)*。

```
$ py3 test_pdb.py 
> /tmp/test_pdb.py(10)countdown()
-> print(i)
(Pdb) list
5 
6 
7 def countdown(number):
8 for i in range(number, 0, -1):
9 import pdb; pdb.set_trace()
10 -> print(i)
11 sleep(1)
12 
13 
14 if __name__ == "__main__":
15 seconds = 10
(Pdb) print(seconds)
10
(Pdb) p i
10
(Pdb) p number - i
0
(Pdb)
```

如示例中最后一行命令所示，*print* 可以在显示结果之前计算表达式。

[Python 文档](https://docs.python.org/3/library/pdb.html)中有 *pdb* 各种命令的参考和示例。对于刚开始使用 Python 调试器的朋友们来说，读一读会很有帮助。

## 增强型调试器

一些增强型调试器可提供更好的用户体验。大部分都为 *pdb* 添加了有用的额外功能，例如语法高亮显示、更好的回溯以及内省（introspection，可以自动提示相关参数、对象方法等——译者注）等。增强型调试器的常用选择有 [IPython 的 ipdb](https://github.com/gotcha/ipdb) 和 [pdb++](https://github.com/antocuni/pdb)。

这些示例将展示如何在虚拟环境中安装这两个调试器。这些示例中都使用了新的虚拟环境，不过在调试应用程序的情况下，应使用应用程序的虚拟环境。

### 安装 IPython 的 ipdb

在虚拟环境中使用 *pip* 来安装 IPython *ipdb*。

```
$ python3 -m venv .test_pdb
$ source .test_pdb/bin/activate
(test_pdb)$ pip install ipdb
```

使用以下命令在脚本中调用 *ipdb*。请注意，该模块名为 *ipdb* 而不是 *pdb*：

```
import ipdb; ipdb.set_trace()
```

IPython ipdb 同样也进入了 Fedora 软件包仓库，因而也可以使用 Fedora 的包管理器 *dnf* 来安装：

```
$ sudo dnf install python3-ipdb
```

### 安装 pdb++

安装 *pdb++* 的方式比较类似：

```
$ python3 -m venv .test_pdb
$ source .test_pdb/bin/activate
(test_pdb)$ pip install pdbp
```

*pdb++* 会覆盖 *pdb* 模块，因此可以使用相同的语法在程序中添加断点：

```
import pdb; pdb.set_trace()
```

## 结语

学会使用 Python 调试器可以节省我们排查应用程序问题的时间。同时也能帮助我们了解应用程序或某些库的复杂部分如何运作，从而提高 Python 开发能力。
