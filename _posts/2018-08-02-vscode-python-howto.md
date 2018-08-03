---
layout: post
title: 在 Fedora 上写 Python 不妨试试 VS Code
date: 2018-08-02 12:38:14 +0800
categories: python
---

> 本文翻译自 Clément Verna 的文章 [How to use VS Code for your Python projects](https://fedoramagazine.org/vscode-python-howto/)。
>
> 由于 VS Code 不在 Fedora 官方仓库中，所以需要读者自行添加第三方仓库。Fedora 并不为第三方仓库中的软件稳定性和安全性背书，请各位读者理解。

Visual Studio Code 或者叫 VS Code 是一款开源的代码编辑器，同时它还包含了可以用于构建和调试应用程序的工具。启用 Python 扩展后，*VS Code* 便可成为各位 Python 开发人员舒适的工作环境。本文会向大家展示一下都有哪些实用扩展，以及如何配置 VS Code 来充分利用这些扩展。

如果你还没有安装 VS Code，可以参考 Fedora Magazine 上的这篇文章 [Using Visual Studio Code on Fedora](https://fedoramagazine.org/using-visual-studio-code-fedora/)，或参考以下注解：

- 添加 VS Code 64 位稳定版 dnf 软件仓库
  ```
  sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
  sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
  ```
- Fedora 22 及以上版本使用 dnf 进行安装
  ```
  dnf check-update
  sudo dnf install code
  ```
（注解摘录自 [VS Code 官方文档](https://code.visualstudio.com/docs/setup/linux#_rhel-fedora-and-centos-based-distributions)）

### 安装 VS Code Python 扩展

首先，我们从扩展商店安装 Python 扩展，来让 VS Code 对 Python 更友好。

![](/assets/2018/08/02/Peek-2018-07-27-09-44.gif)

安装 Python 扩展后，现在我们可以来进行配置。

VS Code 以 JSON 文件的形式来管理其配置。我们会用到两个文件：

- 一个会作用于所有项目的全局设置
- 另一个是项目特定的设置

使用快捷键 **Ctrl+,**（逗号）来打开全局设置。

#### 设置 Python 路径

你可以通过配置让 VS Code 自动为每个项目选择最佳的 Python 解释器。方法很简单，只需要在全局设置中配置 `python.pythonPath` 这个键值即可。

```
// 将你的设置放在此文件中以覆盖默认设置和用户设置。
{
    "python.pythonPath":"${workspaceRoot}/.venv/bin/python",
}
```

这会告诉 VS Code 使用项目根目录中 `.venv` 虚拟环境目录下的 Python 解释器。

#### 使用环境变量

默认情况下，VS Code 会使用项目根目录中的 `.env` 文件所定义的环境变量。这对于设置环境变量很有用，例如：

```
PYTHONWARNINGS="once"
```

这行设置可确保程序在运行时显示警告。

如果想修改默认值，可以按如下所示来设置 `python.envFile` 这个配置键：

```
"python.envFile": "${workspaceFolder}/.env",
```

### 代码 lint

Python 扩展还支持不同的代码 lint 工具（`pep8`、`flake8`、`pylint`等）（可以用来检查代码的语法是否正确、风格是否符合要求等——译者注）。可以设置一些配置项来启用你所喜欢的、或者你当前项目所使用的 lint 工具。

默认情况下，会启用 `pylint`。但是在下面这个例子中，我们来配置一下 `flake8`。

```
"python.linting.pylintEnabled": false,
"python.linting.flake8Path": "${workspaceRoot}/.venv/bin/flake8",
"python.linting.flake8Enabled": true,
"python.linting.flake8Args": ["--max-line-length=90"],
```

在启用 lint 工具后，你的代码中如果有不符合 lint 工具标准的地方，会被下划线标示出来。哦，对了，在这个例子中，你还需要在项目的虚拟环境中安装 `flake8` 才能使 lint 检查生效。

![](/assets/2018/08/02/Peek-2018-07-27-12-05.gif)

### 代码格式化

VS Code 我们中还可以通过配置使代码自动格式化。该扩展目前支持 `autopep8`、`black` 和 `yapf`。`black` 的配置方法如下。

```
"python.formatting.provider": "black",
"python.formatting.blackPath": "${workspaceRoot}/.venv/bin/black"
"python.formatting.blackArgs": ["--line-length=90"],
"editor.formatOnSave": true,
```

如果你不想让编辑器在保存的时候自动格式化，可以将该选项设置为 `false`，然后在需要格式化当前文档的时候，使用 **Ctrl+Shift+I** 快捷键来操作。另外，在这个例子中，你还需要在项目的虚拟环境中安装 `black` 才能使格式化功能生效。

### 运行任务

VS Code 的另一个重要特性就是它可以运行任务。这些任务也是以 JSON 文件的形式来定义的，并且保存在项目根目录中。

#### 运行 Flask 开发服务器

在此示例中，你将创建一个任务来运行 Flask 开发服务器。使用可以运行外部命令的基本模板来创建新的 Build：

![](/assets/2018/08/02/Peek-2018-07-27-13-26.gif)

按如下所示编辑 `tasks.json` 文件，创建新任务来运行 `Flask` 开发服务器：

```
{
  // 参考 https://go.microsoft.com/fwlink/?LinkId=733558
  // 了解 tasks.json 格式的文档
  "version": "2.0.0",
  "tasks": [
    {

      "label": "Run Debug Server",
      "type": "shell",
      "command": "${workspaceRoot}/.venv/bin/flask run -h 0.0.0.0 -p 5000",
      "group": {
          "kind": "build",
          "isDefault": true
       }
    }
  ]
}
```

Flask 开发服务器使用环境变量来获取应用程序的入口点。可以使用 `.env` 文件来声明这些变量。例如：

```
FLASK_APP=wsgi.py
FLASK_DEBUG=True
```

现在可以使用快捷键 **Ctrl+Shift+B** 来执行任务了。

### 单元测试

VS Code 还整合了单元测试运行器 `pytest`、`unittest` 和 `nosetest`，全部都开箱即用。启用测试运行器后，VS Code 会发现单元测试，你可以按测试套件来单独运行，也可以直接运行全部测试。

例如，想启用 `pytest` 的话：

```
"python.unitTest.pyTestEnabled": true,
"python.unitTest.pyTestPath": "${workspaceRoot}/.venv/bin/pytest",
```

和前面一样，在这个例子中，你还需要在项目的虚拟环境中安装 `pytest` 才能使测试功能生效。

![](/assets/2018/08/02/Peek-2018-07-27-15-33.gif)
