- [简介](#%E7%AE%80%E4%BB%8B)
    - [依赖关系管理](#%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB%E7%AE%A1%E7%90%86)
    - [系统需求](#%E7%B3%BB%E7%BB%9F%E9%9C%80%E6%B1%82)
    - [Installation - Linux / Unix / OSX](#installation---linux-unix-osx)
        - [Downloading the Composer Executable](#downloading-the-composer-executable)
            - [Locally](#locally)
            - [Globally](#globally)
    - [Installation - Windows](#installation---windows)
        - [Using the Installer](#using-the-installer)
        - [Manual Installation](#manual-installation)
    - [Using Composer](#using-composer)

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
1. 其中一些库依赖于其他库。

Composer：

1. 允许您声明所依赖的库。
1. 找出哪些包可以被安装，以及需要安装哪些版本，并且安装它们（这意味着将它们下载到您的项目中）。

请参阅 [基本用法](01-basic-usage.md) 一章，了解关于声明依赖关系的更多细节。

## 系统需求

Composer 需要 PHP 5.3.2+ 来运行。
还需要一些敏感的 php 设置和编译标志，
当你使用安装程序时，有任何不兼容的情况你都将收到对应的警告信息。

To install packages from sources instead of simple zip archives, you will need
git, svn, fossil or hg depending on how the package is version-controlled.

Composer is multi-platform and we strive to make it run equally well on Windows,
Linux and OSX.

## Installation - Linux / Unix / OSX

### Downloading the Composer Executable

Composer offers a convenient installer that you can execute directly from the
commandline. Feel free to [download this file](https://getcomposer.org/installer)
or review it on [GitHub](https://github.com/composer/getcomposer.org/blob/master/web/installer)
if you wish to know more about the inner workings of the installer. The source
is plain PHP.

There are in short, two ways to install Composer. Locally as part of your
project, or globally as a system wide executable.

#### Locally

Installing Composer locally is a matter of just running the installer in your
project directory. See [the Download page](https://getcomposer.org/download/)
for instructions.

The installer will just check a few PHP settings and then download
`composer.phar` to your working directory. This file is the Composer binary. It
is a PHAR (PHP archive), which is an archive format for PHP which can be run on
the command line, amongst other things.

Now just run `php composer.phar` in order to run Composer.

You can install Composer to a specific directory by using the `--install-dir`
option and additionally (re)name it as well using the `--filename` option. When
running the installer when following
[the Download page instructions](https://getcomposer.org/download/) add the
following parameters:

```sh
php composer-setup.php --install-dir=bin --filename=composer
```

Now just run `php bin/composer` in order to run Composer.

#### Globally

You can place the Composer PHAR anywhere you wish. If you put it in a directory
that is part of your `PATH`, you can access it globally. On unixy systems you
can even make it executable and invoke it without directly using the `php`
interpreter.

After running the installer following [the Download page instructions](https://getcomposer.org/download/)
you can run this to move composer.phar to a directory that is in your path:

```sh
mv composer.phar /usr/local/bin/composer
```

> **Note:** If the above fails due to permissions, you may need to run it again
> with sudo.

> **Note:** On some versions of OSX the `/usr` directory does not exist by
> default. If you receive the error "/usr/local/bin/composer: No such file or
> directory" then you must create the directory manually before proceeding:
> `mkdir -p /usr/local/bin`.

> **Note:** For information on changing your PATH, please read the
> [Wikipedia article](https://en.wikipedia.org/wiki/PATH_(variable)) and/or use Google.

Now just run `composer` in order to run Composer instead of `php composer.phar`.

## Installation - Windows

### Using the Installer

This is the easiest way to get Composer set up on your machine.

Download and run
[Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe). It will
install the latest Composer version and set up your PATH so that you can just
call `composer` from any directory in your command line.

> **Note:** Close your current terminal. Test usage with a new terminal: This is
> important since the PATH only gets loaded when the terminal starts.

### Manual Installation

Change to a directory on your `PATH` and run the installer following
[the Download page instructions](https://getcomposer.org/download/)
to download `composer.phar`.

Create a new `composer.bat` file alongside `composer.phar`:

```sh
C:\bin>echo @php "%~dp0composer.phar" %*>composer.bat
```

Add the directory to your PATH environment variable if it isn't already.
For information on changing your PATH variable, please see
[this article](https://www.computerhope.com/issues/ch000549.htm) and/or
use Google.

Close your current terminal. Test usage with a new terminal:

```sh
C:\Users\username>composer -V
Composer version 1.0.0 2016-01-10 20:34:53
```

## Using Composer

Now that you've installed Composer, you are ready to use it! Head on over to the
next chapter for a short and simple demonstration.

[Basic usage](01-basic-usage.md) &rarr;
