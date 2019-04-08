# 简介

Composer 是 PHP 中的依赖关系管理工具。
它允许你声明你的项目所依赖的库，并且它将为你管理（安装/更新）它们。

## 依赖关系管理

Composer **并不是** 一个像 Yum 或 Apt 那样的包管理器。
是的，它处理的是“包”或者库，但是它在每个项目的基础上管理它们，将它们安装在你项目的一个目录中（例如：`vendor`)。
默认情况下，它不会在全局范围内安装任何东西。
因此，它是一个依赖关系管理器。
然而为了方便起见，它支持通过 [global](03-cli.md#global) 命令来操作一个名为“global”的全局项目。

这个想法并不新鲜，Composer 的灵感来源于 node 的 [npm](https://www.npmjs.com/) 和 ruby 的 [bundler](https://bundler.io/)。

假设：

1. 你有一个依赖于多个库的项目。
2. 其中一些库又依赖于其他库。

Composer：

1. 允许你声明所依赖的库。
2. 找出哪些版本的软件包可以和需要安装，并安装它们（意味着它会将它们下载到项目中）。

请参阅 [基本用法](01-basic-usage.md) 一章，了解关于声明依赖关系的更多细节。

## 系统要求

Composer 需要运行于 PHP 5.3.2+。
一些敏感的 PHP 设置和编译标志也是必需的，当你使用安装程序时，有任何不兼容的情况你都将收到对应的警告信息。

要从源代码而不是简单的 zip 文件安装软件包，你将需要 git、svn、fossil 或 hg，这取决于软件包的版本控制方式。

Composer 是多平台的，我们努力使它在 Windows、Linux 和 MacOS 上运行得同样出色。

## 安装 - Linux / Unix / macOS

### 下载 Composer 可执行文件

Composer 提供了一个方便的安装程序，可以直接从命令行执行。
如果你想了解更多关于安装程序内部工作的信息，请 [下载此文件](https://getcomposer.org/installer) 或在 [GitHub](https://github.com/composer/getcomposer.org/blob/master/web/installer) 上查看此文件。
源码是使用纯 PHP 进行编写的。

简而言之，安装 Composer 有两种方法。
局部安装作为项目的一部分，或者全局安装作为一个系统范围的可执行文件。

#### 局部安装

要在局部安装 Composer，请在项目目录中运行安装程序。
相关说明，请参阅[下载页面](https://getcomposer.org/download/)。

安装程序将检查一些 PHP 设置，然后将 `composer.phar` 下载到你的工作目录。
它是 Composer 的二进制文件。
此外它还是一个 PHAR（PHP archive）文件，这是一种 PHP 的存档格式，可以在命令行中执行。

现在，执行 `php composer.phar` 命令就可以运行 Composer 了。

你可以使用 `--install dir` 选项将 Composer 安装到特定的目录中，并使用 `--filename` 选项来将它重命名。
当运行安装程序时请参考 [下载页说明](https://getcomposer.org/download/) 添加以下参数：

```sh
php composer-setup.php --install-dir=bin --filename=composer
```

现在，执行 `php bin/composer` 命令就可以运行 Composer 了。

#### 全局安装

你可以把 Composer PHAR 放在任何你想放的地方。
如果你将它放在属于你的 `PATH` 指向的目录中，你就可以全局访问它。
在 UNIX 系统上，你甚至可以使其可执行并直接调用它，而无需使用 `php` 解释器。

按照 [下载页说明](https://getcomposer.org/download/) 运行安装程序后，你可以使用下面的命令将 composer.phar 移动到 `PATH` 指向的目录中：

```sh
mv composer.phar /usr/local/bin/composer
```

如果你只想为你的当前用户安装它，并且避免使用 root 权限，那么你可以使用 `~/.local/bin`，这在某些 Linux 发行版上是默认的。

> **注意：** 如果由于权限原因导致上述操作失败，你可能需要使用 sudo 再次运行。

**注意：** 在某些版本的 MacOS 上，默认情况下不存在 `/usr` 目录。
> 如果收到错误提示 "/usr/local/bin/composer: No such file or directory"，则必须手动创建目录，然后才能继续执行以下操作：
> `mkdir-p/usr/local/bin`。

> **注意：** For information on changing your PATH, please read the
> [Wikipedia article](https://en.wikipedia.org/wiki/PATH_(variable)) and/or use Google.

现在你只需要执行 `composer` 命令就可以运行 Composer 了，而不是繁琐的 `php composer.phar`。

## 安装 - Windows

### 使用安装程序

这是让 Composer 在你机器上运行的最简单的方法。

下载并且执行 [Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe)。
它将安装最新版本的 Composer 并设置好 PATH 环境变量，以便你可以从命令行中的任何目录调用 `composer` 命令。

> **注意：** 关闭当前终端。测试新终端的使用情况：
> 这非常重要，因为 PATH 环境变量只在终端启动时才重新加载。

### 手动安装

在命令行窗口切换到 `PATH` 环境变量中的一个目录，并运行 [下载页说明](https://getcomposer.org/download/) 中给出的指令，以下载 `composer.phar` 文件。

在 `composer.phar` 文件旁创建一个新的 `composer.bat` 文件：

```sh
C:\bin>echo @php "%~dp0composer.phar" %*>composer.bat
```

将目录添加到 PATH 环境变量（如果尚未添加）。
有关更改 PATH 变量的信息，请参考 [这篇文章](https://www.computerhope.com/issues/ch000549.htm) 或上网搜索相关资料。

关闭当前命令行窗口，打开新的命令行窗口进行测试：

```sh
C:\Users\username>composer -V
Composer version 1.0.0 2016-01-10 20:34:53
```

## 使用 Composer

现在您已经安装了 Composer，并可以开始使用它了！在接下来的一章里，我们将做一个简短而简单的演示。

[Basic usage](01-basic-usage.md) &rarr;
