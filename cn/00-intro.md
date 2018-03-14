- [简介](#%E7%AE%80%E4%BB%8B)
    - [依赖关系管理](#%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB%E7%AE%A1%E7%90%86)
    - [系统需求](#%E7%B3%BB%E7%BB%9F%E9%9C%80%E6%B1%82)
    - [安装方式 - Linux / Unix / OSX](#%E5%AE%89%E8%A3%85%E6%96%B9%E5%BC%8F---linux-unix-osx)
        - [下载 Composer 可执行文件](#%E4%B8%8B%E8%BD%BD-composer-%E5%8F%AF%E6%89%A7%E8%A1%8C%E6%96%87%E4%BB%B6)
            - [局部安装](#%E5%B1%80%E9%83%A8%E5%AE%89%E8%A3%85)
            - [全局安装](#%E5%85%A8%E5%B1%80%E5%AE%89%E8%A3%85)
    - [安装方式 - Windows](#%E5%AE%89%E8%A3%85%E6%96%B9%E5%BC%8F---windows)
        - [使用安装程序](#%E4%BD%BF%E7%94%A8%E5%AE%89%E8%A3%85%E7%A8%8B%E5%BA%8F)
        - [手动安装](#%E6%89%8B%E5%8A%A8%E5%AE%89%E8%A3%85)
    - [使用 Composer](#%E4%BD%BF%E7%94%A8-composer)

# 简介

Composer 是 PHP 中的依赖关系管理工具。
它允许您声明您的项目所依赖的库，并且它将为您管理（安装/更新）它们。

## 依赖关系管理

Composer **并不是** 一个像 Yum 或 Apt 那样的包管理器。
是的，它处理的是“包”或者库，
但是它在每个项目的基础上管理它们，
将它们安装在您项目的一个目录中（例如：`vendor`)。
默认情况下，它不会在全局范围内安装任何东西。
因此，它是一个依赖关系管理器。
然而为了方便，它确实支持通过 [global](03-cli.md#global) 命令来操作一个名为“global”的全局项目。

这个想法并不新鲜，Composer 的灵感来源于 node 的 [npm](https://www.npmjs.com/) 和 ruby 的 [bundler](https://bundler.io/)。

假设：

1. 您的项目依赖于大量的库。
2. 其中一些库依赖于其他库。

Composer：

1. 允许您声明所依赖的库。
2. 找出哪些包可以被安装，以及需要安装哪些版本，并且安装它们（这意味着将它们下载到您的项目中）。

请参阅 [基本用法](01-basic-usage.md) 一章，了解关于声明依赖关系的更多细节。

## 系统需求

Composer 需要 PHP 5.3.2+ 来运行。
还需要一些敏感的 php 设置和编译标志，
当你使用安装程序时，有任何不兼容的情况你都将收到对应的警告信息。

要从源文件而不是简单的 zip 文件中安装依赖包，
你将需要 git、svn、fossil 或者 hg 版本管理系统，这取决于目标包是如何进行版本控制的。

Composer 是多平台的，并且我们努力让它在 Windows、Linux 和 OSX 上运行得同样出色。

## 安装方式 - Linux / Unix / OSX

### 下载 Composer 可执行文件

Composer 提供了一个便捷的安装程序，您可以直接从命令行执行。请直接 [下载此文件](https://getcomposer.org/installer)。
或者如果您想了解更多关于安装程序的内部工作原理，可以在 [GitHub](https://github.com/composer/getcomposer.org/blob/master/web/installer) 上查看它，源代码是使用普通的 PHP 进行编写的。

简而言之，有两种方式来安装 Composer。
作为您的项目的一部分，或者作为一个系统范围的全局可执行文件。

#### 局部安装

在局部安装 Composer 只是在您的项目目录中运行安装程序。
请参阅 [下载页面](https://getcomposer.org/download/) 以获得更多指导。


安装程序会检查一些PHP设置，然后下载 `composer.phar` 到您的工作目录。
这是 Composer 的二进制文件。它是一个 PHAR（PHP archive）文件，这是 PHP 的一种存档格式，可以在命令行中运行，也可以在其他地方运行。

现在为了运行 Composer 我们需要执行 `php composer.phar` 。

您可以使用 `--install-dir` 选项将 Composer 安装到特定的目录，另外也可以使用 `--filename` 选项来将它重命名。
当运行安装程序时，请遵循 [下载页面的说明](https://getcomposer.org/download/)，添加以下参数：

```sh
php composer-setup.php --install-dir=bin --filename=composer
```

现在您只需要执行 `php bin/composer` 命令就可以运行 Composer 了。

#### 全局安装

您可以把 Composer PHAR 放置在任何你想要的地方。
如果您将它放在环境变量 `PATH` 的某个目录中，那么您可以在全局中访问它。
在类 Unix 的操作系统中，您甚至可以让它成为可执行文件，在不直接使用 `php` 解释器的情况下调用它。

在运行了 [下载页面的指令](https://getcomposer.org/download/) 进行安装后，您可以使用下面的命令来将 composer.phar 移动到您环境变量 `PATH` 的一个目录中：

```sh
mv composer.phar /usr/local/bin/composer
```

> **注意：** 如果上述命令由于权限不足执行失败，您可能需要使用 sudo 再次运行它。

> **注意：** 在 OSX 的某些版本中，`/usr` 目录在默认情况下并不存在。  
> 如果您接收到错误 "/usr/local/bin/composer: No such file or directory"  
> 那么您必须在继续之前手动创建该目录：`mkdir -p /usr/local/bin`。

> **注意：** 有关更改您的 PATH 环境变量的资料，请阅读 [维基百科](https://en.wikipedia.org/wiki/PATH_(variable)) 或上网搜索相关资料。

现在您只需要执行 `composer` 命令就可以运行 Composer 了，而不是繁琐的 `php composer.phar`。

## 安装方式 - Windows

### 使用安装程序

这是让 Composer 在您机器上运行的最简单的方法。

下载并且执行 [Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe)。
它将安装最新版本的 Composer 并设置好 PATH 环境变量，因此在任何目录中您都可以在命令行中直接使用 `composer` 命令。

> **注意：** 关闭当前命令行窗口，重新打开新的终端测试使用：这很重要，因为 PATH 环境变量只在终端启动时才重新加载。

### 手动安装

在任意命令行窗口切换到 `PATH` 环境变量中的一个目录，并且运行 [下载页面中给出的指令](https://getcomposer.org/download/) 以下载 `composer.phar` 文件。

在 `composer.phar` 文件旁创建一个新的 `composer.bat` 文件：

```sh
C:\bin>echo @php "%~dp0composer.phar" %*>composer.bat
```

如果该目录还没有添加进 PATH 环境变量中，请手动添加。
有关更改您的 PATH 环境变量的资料，请阅读 [这篇文章](https://www.computerhope.com/issues/ch000549.htm) 或上网搜索相关资料。

关闭当前命令行窗口，打开新的命令行窗口进行测试：

```sh
C:\Users\username>composer -V
Composer version 1.0.0 2016-01-10 20:34:53
```

## 使用 Composer

现在您已经安装了 Composer，并可以开始使用它了！在接下来的一章里，我们将做一个简短而简单的演示。

[基本用法](01-basic-usage.md) &rarr;
