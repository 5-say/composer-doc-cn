<a name="Introduction"></a>
# 简介

Composer 是 PHP 的一个依赖管理工具。它允许你申明项目所依赖的代码库，它会在你的项目中为你安装他们。

---

- [简介](#Introduction)
  - [依赖管理](#Dependency-management)
  - [声明依赖关系](#Declaring-dependencies)
  - [系统要求](#System-Requirements)
  - [安装 - *nix](#Installation-*nix)
    - [下载 Composer 的可执行文件](#Downloading-the-Composer-Executable)
      - [局部安装](#Locally)
      - [全局安装](#Globally)
      - [全局安装 (on OSX via homebrew)](#Globally-on-OSX-via-homebrew)
  - [安装 - Windows](#Installation-Windows)
    - [使用安装程序](#Using-the-Installer)
    - [手动安装](#Manual-Installation)
  - [使用 Composer](#Using-Composer)
  - [自动加载](#Autoloading)

---

<a name="Dependency-management"></a>
## 依赖管理

Composer 不是一个包管理器。是的，它涉及 "packages" 和 "libraries"，但它在每个项目的基础上进行管理，在你项目的某个目录中（例如 `vendor`）进行安装。默认情况下它不会在全局安装任何东西。因此，这仅仅是一个依赖管理。

这种想法并不新鲜，Composer 受到了 node 的 [npm](http://npmjs.org/)
和 ruby 的 [bundler](http://gembundler.com/) 的强烈启发。而当时 PHP 下并没有类似的工具。

Composer 将这样为你解决问题：

a) 你有一个项目依赖于若干个库。

b) 其中一些库依赖于其他库。

c) 你声明你所依赖的东西。

d) Composer 会找出哪个版本的包需要安装，并安装它们（将它们下载到你的项目中）。

<a name="Declaring-dependencies"></a>
## 声明依赖关系

比方说，你正在创建一个项目，你需要一个库来做日志记录。你决定使用 [monolog](https://github.com/Seldaek/monolog)。为了将它添加到你的项目中，你所需要做的就是创建一个 `composer.json` 文件，其中描述了项目的依赖关系。

```json
{
    "require": {
        "monolog/monolog": "1.2.*"
    }
}
```

我们只要指出我们的项目需要一些 `monolog/monolog` 的包，从 `1.2` 到 `1.3` 之间的最高版本。

<a name="System-Requirements"></a>
## 系统要求

运行 Composer 需要 PHP 5.3.2+ 以上版本。一些敏感的 PHP 设置和编译标志也是必须的，但对于任何不兼容项安装程序都会抛出警告。

我们将从包的来源直接安装，而不是简单的下载 zip 文件，你需要 git 、 svn 或者 hg ，这取决于你载入的包所使用的版本管理系统。

Composer 是多平台的，我们努力使它在 Windows 、 Linux 以及 OSX 平台上运行的同样出色。

<a name="Installation-*nix"></a>
## 安装 - *nix

<a name="Downloading-the-Composer-Executable"></a>
### 下载 Composer 的可执行文件

<a name="Locally"></a>
#### 局部安装

要真正获取 Composer，我们需要做两件事。首先安装 Composer （同样的，这意味着它将下载到你的项目中）：

```sh
curl -sS https://getcomposer.org/installer | php
```

> **注意：** 如果上述方法由于某些原因失败了，你还可以通过 `php` >下载安装器：

```sh
php -r "readfile('https://getcomposer.org/installer');" | php
```

这将检查一些 PHP 的设置，然后下载 `composer.phar` 到你的工作目录中。这是 Composer 的二进制文件。这是一个 PHAR 包（PHP 的归档），这是 PHP 的归档格式可以帮助用户在命令行中执行一些操作。

你可以通过 `--install-dir` 选项指定 Composer 的安装目录（它可以是一个绝对或相对路径）：

```sh
curl -sS https://getcomposer.org/installer | php -- --install-dir=bin
```

<a name="Globally"></a>
#### 全局安装

你可以将此文件放在任何地方。如果你把它放在系统的 `PATH` 目录中，你就能在全局访问它。 在类Unix系统中，你甚至可以在使用时不加 `php` 前缀。

你可以执行这些命令让 `composer` 在你的系统中进行全局调用：

```sh
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```

> **注意：** 如果上述命令因为权限执行失败，
> 请使用 sudo 再次尝试运行 `mv` 那行命令。

现在只需要运行 `composer` 命令就可以使用 Composer 而不需要输入 `php composer.phar`。

<a name="Globally-on-OSX-via-homebrew"></a>
#### 全局安装 (on OSX via homebrew)

Composer 是 homebrew-php 项目的一部分。

```sh
brew update
brew tap josegonzalez/homebrew-php
brew tap homebrew/versions
brew install php55-intl
brew install josegonzalez/php/composer
```

<a name="Installation-Windows"></a>
## 安装 - Windows

<a name="Using-the-Installer"></a>
### 使用安装程序

这是将 Composer 安装在你机器上的最简单的方法。

下载并且运行 [Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe)，它将安装最新版本的 Composer ，并设置好系统的环境变量，因此你可以在任何目录下直接使用 `composer` 命令。

<a name="Manual-Installation"></a>
### 手动安装

设置系统的环境变量 `PATH` 并运行安装命令下载 composer.phar 文件：

```sh
C:\Users\username>cd C:\bin
C:\bin>php -r "readfile('https://getcomposer.org/installer');" | php
```

> **注意：** 如果收到 readfile 错误提示，请使用 `http` 链接或者在 php.ini 中开启 php_openssl.dll 。

在 `composer.phar` 同级目录下新建文件 `composer.bat` ：

```sh
C:\bin>echo @php "%~dp0composer.phar" %*>composer.bat
```

关闭当前的命令行窗口，打开新的命令行窗口进行测试：

```sh
C:\Users\username>composer -V
Composer version 27d8904
```

<a name="Using-Composer"></a>
## 使用 Composer

现在我们将使用 Composer 来安装项目的依赖。如果在当前目录下没有一个 `composer.json` 文件，请查看[基本用法](01-basic-usage.md)章节。

要解决和下载依赖，请执行 `install` 命令：

```sh
php composer.phar install
```

如果你进行了全局安装，并且没有 phar 文件在当前目录，请使用下面的命令代替：

```sh
composer install
```

继续 [上面的例子](#Declaring-dependencies)，这里将下载 monolog 到 `vendor/monolog/monolog` 目录。

<a name="Autoloading"></a>
## 自动加载

除了库的下载，Composer 还准备了一个自动加载文件，它可以加载 Composer 下载的库中所有的类文件。使用它，你只需要将下面这行代码添加到你项目的引导文件中：

```php
require 'vendor/autoload.php';
```

现在我们就可以使用 monolog 了！想要学习更多关于 Composer 的知识，请查看“基本用法”章节。

[基本用法](01-basic-usage.md) &rarr;
