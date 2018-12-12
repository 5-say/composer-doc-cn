<a name="Command-line-interface"></a>
# 命令行

你已经学会了如何使用命令行界面做一些事情。本章将向你介绍所有可用的命令。

为了从命令行获得帮助信息，请运行 `composer` 或者 `composer list` 命令，然后结合 `--help` 命令来获得更多的帮助信息。

---

- [命令行](#Command-line-interface)
- [全局参数](#Global-Options)
  - [进程退出代码](#Process-Exit-Codes)
  - [初始化 `init`](#init) - - [参数](#init-Options)
  - [安装 `install`](#install) - - [参数](#install-Options)
  - [更新 `update`](#update) - - [参数](#update-Options)
  - [申明依赖 `require`](#require) - - [参数](#require-Options)
  - [全局执行 `global`](#global)
  - [搜索 `search`](#search) - - [参数](#search-Options)
  - [展示 `show`](#show) - - [参数](#show-Options)
  - [依赖性检测 `depends`](#depends) - - [参数](#depends-Options)
  - [有效性检测 `validate`](#validate)
  - [依赖包状态检测 `status`](#status)
  - [自我更新 `self-update`](#self-update) - - [参数](#self-update-Options)
  - [更改配置 `config`](#config) - - [使用方法](#config-Usage) - - [参数](#config-Options) - - [修改包来源](#Modifying-Repositories)
  - [创建项目 `create-project`](#create-project) - - [参数](#create-project-Options)
  - [打印自动加载索引 `dump-autoload`](#dump-autoload) - - [参数](#dump-autoload-Options)
  - [查看许可协议 `licenses`](#licenses)
  - [执行脚本 `run-script`](#run-script)
  - [诊断 `diagnose`](#diagnose)
  - [归档 `archive`](#archive) - - [参数](#archive-Options)
  - [获取帮助信息 `help`](#help)
  - [环境变量](#Environment-variables)
     - [COMPOSER](#COMPOSER)
     - [COMPOSER_ROOT_VERSION](#COMPOSER_ROOT_VERSION)
     - [COMPOSER_VENDOR_DIR](#COMPOSER_VENDOR_DIR)
     - [COMPOSER_BIN_DIR](#COMPOSER_BIN_DIR)
     - [http_proxy or HTTP_PROXY](#http_proxy-or-HTTP_PROXY)
     - [no_proxy](#no_proxy)
     - [HTTP_PROXY_REQUEST_FULLURI](#HTTP_PROXY_REQUEST_FULLURI)
     - [HTTPS_PROXY_REQUEST_FULLURI](#HTTPS_PROXY_REQUEST_FULLURI)
     - [COMPOSER_HOME](#COMPOSER_HOME)
         - [COMPOSER_HOME/config.json](#COMPOSER_HOME-config.json)
     - [COMPOSER_CACHE_DIR](#COMPOSER_CACHE_DIR)
     - [COMPOSER_PROCESS_TIMEOUT](#COMPOSER_PROCESS_TIMEOUT)
     - [COMPOSER_DISCARD_CHANGES](#COMPOSER_DISCARD_CHANGES)
     - [COMPOSER_NO_INTERACTION](#COMPOSER_NO_INTERACTION)

---
<a name="Global-Options"></a>
## 全局参数

下列参数可与每一个命令结合使用：

* **--verbose (-v):** 增加反馈信息的详细度。
  * -v 表示正常输出。
  * -vv 表示更详细的输出。
  * -vvv 则是为了 debug。
* **--help (-h):** 显示帮助信息。
* **--quiet (-q):** 禁止输出任何信息。
* **--no-interaction (-n):** 不要询问任何交互问题。
* **--working-dir (-d):** 如果指定的话，使用给定的目录作为工作目录。
* **--profile:** 显示时间和内存使用信息。
* **--ansi:** 强制 ANSI 输出。
* **--no-ansi:** 关闭 ANSI 输出。
* **--version (-V):** 显示当前应用程序的版本信息。

<a name="Process-Exit-Codes"></a>
## 进程退出代码

* **0:** 正常
* **1:** 通用/未知错误
* **2:** 依赖关系处理错误

<a name="init"></a>
## 初始化 `init`

在 [“库”](02-libraries.md) 那一章我们看到了如何手动创建 `composer.json` 文件。实际上还有一个 `init` 命令可以更容易的做到这一点。

当您运行该命令，它会以交互方式要求您填写一些信息，同时聪明的使用一些默认值。

```sh
php composer.phar init
```

<a name="init-Options"></a>
### 初始化-参数

* **--name:** 包的名称。
* **--description:** 包的描述。
* **--author:** 包的作者。
* **--homepage:** 包的主页。
* **--require:** 需要依赖的其它包，必须要有一个版本约束。并且应该遵循 `foo/bar:1.0.0` 这样的格式。
* **--require-dev:** 开发版的依赖包，内容格式与 **--require** 相同。
* **--stability (-s):** `minimum-stability` 字段的值。

<a name="install"></a>
## 安装 `install`

`install` 命令从当前目录读取 `composer.json` 文件，处理了依赖关系，并把其安装到 `vendor` 目录下。

```sh
php composer.phar install
```

如果当前目录存在 `composer.lock` 文件，它会从此文件读取依赖版本，而不是根据 `composer.json` 文件去获取依赖。这确保了该库的每个使用者都能得到相同的依赖版本。

如果没有 `composer.lock` 文件，composer 将在处理完依赖关系后创建它。

<a name="install-Options"></a>
### 安装-参数

* **--prefer-source:** 下载包的方式有两种： `source`
  和 `dist`。对于稳定版本 composer 将默认使用 `dist` 方式。而 `source` 表示版本控制源 。如果 `--prefer-source` 是被启用的，composer 将从 `source` 安装（如果有的话）。如果想要使用一个 bugfix 到你的项目，这是非常有用的。并且可以直接从本地的版本库直接获取依赖关系。
* **--prefer-dist:** 与 `--prefer-source` 相反，composer 将尽可能的从 `dist` 获取，这将大幅度的加快在 build servers 上的安装。这也是一个回避 git 问题的途径，如果你不清楚如何正确的设置。
* **--dry-run:** 如果你只是想演示而并非实际安装一个包，你可以运行 `--dry-run` 命令，它将模拟安装并显示将会发生什么。
* **--dev:** 安装 `require-dev` 字段中列出的包（这是一个默认值）。
* **--no-dev:** 跳过 `require-dev` 字段中列出的包。
* **--no-scripts:** 跳过 `composer.json` 文件中定义的脚本。
* **--no-plugins:** 关闭 plugins。
* **--no-progress:** 移除进度信息，这可以避免一些不处理换行的终端或脚本出现混乱的显示。
* **--optimize-autoloader (-o):** 转换 PSR-0/4 autoloading 到 classmap 可以获得更快的加载支持。特别是在生产环境下建议这么做，但由于运行需要一些时间，因此并没有作为默认值。

<a name="update"></a>
## 更新 `update`

为了获取依赖的最新版本，并且升级 `composer.lock` 文件，你应该使用 `update` 命令。

```sh
php composer.phar update
```

这将解决项目的所有依赖，并将确切的版本号写入 `composer.lock`。

如果你只是想更新几个包，你可以像这样分别列出它们：

```sh
php composer.phar update vendor/package vendor/package2
```

你还可以使用通配符进行批量更新：

```sh
php composer.phar update vendor/*
```

<a name="update-Options"></a>
### 更新-参数

* **--prefer-source:** 当有可用的包时，从 `source` 安装。
* **--prefer-dist:** 当有可用的包时，从 `dist` 安装。
* **--dry-run:** 模拟命令，并没有做实际的操作。
* **--dev:** 安装 `require-dev` 字段中列出的包（这是一个默认值）。
* **--no-dev:** 跳过 `require-dev` 字段中列出的包。
* **--no-scripts:** 跳过 `composer.json` 文件中定义的脚本。
* **--no-plugins:** 关闭 plugins。
* **--no-progress:** 移除进度信息，这可以避免一些不处理换行的终端或脚本出现混乱的显示。
* **--optimize-autoloader (-o):** 转换 PSR-0/4 autoloading 到 classmap 可以获得更快的加载支持。特别是在生产环境下建议这么做，但由于运行需要一些时间，因此并没有作为默认值。
* **--lock:** 仅更新 lock 文件的 hash，取消有关 lock 文件过时的警告。
* **--with-dependencies** 同时更新白名单内包的依赖关系，这将进行递归更新。

<a name="require"></a>
## 申明依赖 `require`

`require` 命令增加新的依赖包到当前目录的 `composer.json` 文件中。

```sh
php composer.phar require
```

在添加或改变依赖时， 修改后的依赖关系将被安装或者更新。

如果你不希望通过交互来指定依赖包，你可以在这条令中直接指明依赖包。

```sh
php composer.phar require vendor/package:2.* vendor/package2:dev-master
```

<a name="require-Options"></a>
### 申明依赖-参数

* **--prefer-source:** 当有可用的包时，从 `source` 安装。
* **--prefer-dist:** 当有可用的包时，从 `dist` 安装。
* **--dev:** 安装 `require-dev` 字段中列出的包。
* **--no-update:** 禁用依赖关系的自动更新。
* **--no-progress:** 移除进度信息，这可以避免一些不处理换行的终端或脚本出现混乱的显示。
* **--update-with-dependencies** 一并更新新装包的依赖。

<a name="global"></a>
## 全局执行 `global`

`global` 命令允许你在 [COMPOSER_HOME](#COMPOSER_HOME) 目录下执行其它命令，像 `install`、`require` 或 `update`。

并且如果你将 `$COMPOSER_HOME/vendor/bin` 加入到了 `$PATH` 环境变量中，你就可以用它在命令行中安装全局应用，下面是一个例子：

```sh
php composer.phar global require fabpot/php-cs-fixer:dev-master
```

现在 `php-cs-fixer` 就可以在全局范围使用了（假设你已经设置了你的 PATH）。如果稍后你想更新它，你只需要运行 `global update`：

```sh
php composer.phar global update
```

<a name="search"></a>
## 搜索 `search`

`search` 命令允许你为当前项目搜索依赖包，通常它只搜索 packagist.org 上的包，你可以简单的输入你的搜索条件。

```sh
php composer.phar search monolog
```

您也可以通过传递多个参数来进行多条件搜索。

<a name="search-Options"></a>
### 搜索-参数

* **--only-name (-N):** 仅针对指定的名称搜索（完全匹配）。

<a name="show"></a>
## 展示 `show`

列出所有可用的软件包，你可以使用 `show` 命令。

```sh
php composer.phar show
```

如果你想看到一个包的详细信息，你可以输入一个包名称。

```sh
php composer.phar show monolog/monolog

name     : monolog/monolog
versions : master-dev, 1.0.2, 1.0.1, 1.0.0, 1.0.0-RC1
type     : library
names    : monolog/monolog
source   : [git] http://github.com/Seldaek/monolog.git 3d4e60d0cbc4b888fe5ad223d77964428b1978da
dist     : [zip] http://github.com/Seldaek/monolog/zipball/3d4e60d0cbc4b888fe5ad223d77964428b1978da 3d4e60d0cbc4b888fe5ad223d77964428b1978da
license  : MIT

autoload
psr-0
Monolog : src/

requires
php >=5.3.0
```

你甚至可以输入一个软件包的版本号，来显示该版本的详细信息。

```sh
php composer.phar show monolog/monolog 1.0.2
```

<a name="show-Options"></a>
### 展示-参数

* **--installed (-i):** 列出已安装的依赖包。
* **--platform (-p):** 仅列出平台软件包（PHP 与它的扩展）。
* **--self (-s):** 仅列出当前项目信息。

<a name="depends"></a>
## 依赖性检测 `depends`

`depends` 命令可以查出已安装在你项目中的某个包，是否正在被其它的包所依赖，并列出他们。

```sh
php composer.phar depends --link-type=require monolog/monolog

nrk/monolog-fluent
poc/poc
propel/propel
symfony/monolog-bridge
symfony/symfony
```

<a name="depends-Options"></a>
### 依赖性检测-参数

* **--link-type:** 检测的类型，默认为 `require` 也可以是 `require-dev`。

<a name="validate"></a>
## 有效性检测 `validate`

在提交 `composer.json` 文件，和创建 tag 前，你应该始终运行 `validate` 命令。它将检测你的 `composer.json` 文件是否是有效的

```sh
php composer.phar validate
```

### 有效性检测参数

* **--no-check-all:** Composer 是否进行完整的校验。

<a name="status"></a>
## 依赖包状态检测 `status`

如果你经常修改依赖包里的代码，并且它们是从 source（自定义源）进行安装的，那么 `status` 命令允许你进行检查，如果你有任何本地的更改它将会给予提示。

```sh
php composer.phar status
```

你可以使用 `--verbose` 系列参数（-v|vv|vvv）来获取更详细的详细：

```sh
php composer.phar status -v

You have changes in the following dependencies:
vendor/seld/jsonlint:
    M README.mdown
```

<a name="self-update"></a>
## 自我更新 `self-update`

将 Composer 自身升级到最新版本，只需要运行 `self-update` 命令。它将替换你的 `composer.phar` 文件到最新版本。

```sh
php composer.phar self-update
```

如果你想要升级到一个特定的版本，可以这样简单的指定它：

```sh
php composer.phar self-update 1.0.0-alpha7
```

如果你已经为整个系统安装 Composer（参见 [全局安装](00-intro.md#全局安装)），你可能需要在 `root` 权限下运行它：

```sh
sudo composer self-update
```

<a name="self-update-Options"></a>
### 自我更新-参数

* **--rollback (-r):** 回滚到你已经安装的最后一个版本。
* **--clean-backups:** 在更新过程中删除旧的备份，这使得更新过后的当前版本是唯一可用的备份。

<a name="config"></a>
## 更改配置 `config`

`config` 命令允许你编辑 Composer 的一些基本设置，无论是本地的 `composer.json` 或者全局的 `config.json` 文件。

```sh
php composer.phar config --list
```

<a name="config-Usage"></a>
### 更改配置-使用方法

`config [options] [setting-key] [setting-value1] ... [setting-valueN]`

`setting-key` 是一个配置选项的名称，`setting-value1` 是一个配置的值。可以使用数组作为配置的值（像 `github-protocols`），多个 `setting-value` 是允许的。

有效的配置选项，请查看“架构”章节的 [config](04-schema.md#config) 。

<a name="config-Options"></a>
### 更改配置-参数

* **--global (-g):** 操作位于 `$COMPOSER_HOME/config.json` 的全局配置文件。如果不指定该参数，此命令将影响当前项目的 composer.json 文件，或 `--file` 参数所指向的文件。
* **--editor (-e):** 使用文本编辑器打开 composer.json 文件。默认情况下始终是打开当前项目的文件。当存在 `--global` 参数时，将会打开全局 composer.json 文件。
* **--unset:** 移除由 `setting-key` 指定名称的配置选项。
* **--list (-l):** 显示当前配置选项的列表。当存在 `--global` 参数时，将会显示全局配置选项的列表。
* **--file="..." (-f):** 在一个指定的文件上操作，而不是 composer.json。注意：不能与 `--global` 参数一起使用。

<a name="Modifying-Repositories"></a>
### 修改包来源

除了修改配置选项， `config` 命令还支持通过以下方法修改来源信息：

```sh
php composer.phar config repositories.foo vcs http://github.com/foo/bar
```

<a name="create-project"></a>
## 创建项目 `create-project`

你可以使用 Composer 从现有的包中创建一个新的项目。这相当于执行了一个 `git clone` 或 `svn checkout` 命令后将这个包的依赖安装到它自己的 vendor 目录。

此命令有几个常见的用途：

1. 你可以快速的部署你的应用。
2. 你可以检出任何资源包，并开发它的补丁。
3. 多人开发项目，可以用它来加快应用的初始化。

要创建基于 Composer 的新项目，你可以使用 "create-project" 命令。传递一个包名，它会为你创建项目的目录。你也可以在第三个参数中指定版本号，否则将获取最新的版本。

如果该目录目前不存在，则会在安装过程中自动创建。

```sh
php composer.phar create-project doctrine/orm path 2.2.*
```

此外，你也可以无需使用这个命令，而是通过现有的 `composer.json` 文件来启动这个项目。

默认情况下，这个命令会在 packagist.org 上查找你指定的包。

<a name="create-project-Options"></a>
### 创建项目-参数

* **--repository-url:** 提供一个自定义的储存库来搜索包，这将被用来代替 packagist.org。可以是一个指向 `composer` 资源库的 HTTP URL，或者是指向某个 `packages.json` 文件的本地路径。
* **--stability (-s):** 资源包的最低稳定版本，默认为 `stable`。
* **--prefer-source:** 当有可用的包时，从 `source` 安装。
* **--prefer-dist:** 当有可用的包时，从 `dist` 安装。
* **--dev:** 安装 `require-dev` 字段中列出的包。
* **--no-install:** 禁止安装包的依赖。
* **--no-plugins:** 禁用 plugins。
* **--no-scripts:** 禁止在根资源包中定义的脚本执行。
* **--no-progress:** 移除进度信息，这可以避免一些不处理换行的终端或脚本出现混乱的显示。
* **--keep-vcs:** 创建时跳过缺失的 VCS 。如果你在非交互模式下运行创建命令，这将是非常有用的。

<a name="dump-autoload"></a>
## 打印自动加载索引 `dump-autoload`

某些情况下你需要更新 autoloader，例如在你的包中加入了一个新的类。你可以使用 `dump-autoload` 来完成，而不必执行 `install` 或 `update` 命令。

此外，它可以打印一个优化过的，符合 PSR-0/4 规范的类的索引，这也是出于对性能的可考虑。在大型的应用中会有许多类文件，而 autoloader 会占用每个请求的很大一部分时间，使用 classmaps 或许在开发时不太方便，但它在保证性能的前提下，仍然可以获得 PSR-0/4 规范带来的便利。

<a name="dump-autoload-Options"></a>
### 打印自动加载索引-参数

* **--optimize (-o):** 转换 PSR-0/4 autoloading 到 classmap 获得更快的载入速度。这特别适用于生产环境，但可能需要一些时间来运行，因此它目前不是默认设置。
* **--no-dev:** 禁用 autoload-dev 规则。

<a name="licenses"></a>
## 查看许可协议 `licenses`

列出已安装的每个包的名称、版本、许可协议。可以使用 `--format=json` 参数来获取 JSON 格式的输出。

<a name="run-script"></a>
## 执行脚本 `run-script`

你可以运行此命令来手动执行 [脚本](articles/scripts.md)，只需要指定脚本的名称，可选的 `--no-dev` 参数允许你禁用开发者模式。

<a name="diagnose"></a>
## 诊断 `diagnose`

如果你觉得发现了一个 bug 或是程序行为变得怪异，你可能需要运行 `diagnose` 命令，来帮助你检测一些常见的问题。

```sh
php composer.phar diagnose
```

<a name="archive"></a>
## 归档 `archive`

此命令用来对指定包的指定版本进行 zip/tar 归档。它也可以用来归档你的整个项目，不包括 excluded/ignored（排除/忽略）的文件。

```sh
php composer.phar archive vendor/package 2.0.21 --format=zip
```

<a name="archive-Options"></a>
### 归档-参数

* **--format (-f):** 指定归档格式：tar 或 zip（默认为 tar）。
* **--dir:** 指定归档存放的目录（默认为当前目录）。

<a name="help"></a>
## 获取帮助信息 `help`

使用 `help` 可以获取指定命令的帮助信息。

```sh
php composer.phar help install
```

<a name="Environment-variables"></a>
## 环境变量

你可以设置一些环境变量来覆盖默认的配置。建议尽可能的在 `composer.json` 的 `config` 字段中设置这些值，而不是通过命令行设置环境变量。值得注意的是环境变量中的值，将始终优先于 `composer.json` 中所指定的值。

<a name="COMPOSER"></a>
### COMPOSER

环境变量 `COMPOSER` 可以为 `composer.json` 文件指定其它的文件名。

例如：

```sh
COMPOSER=composer-other.json php composer.phar install
```

<a name="COMPOSER_ROOT_VERSION"></a>
### COMPOSER_ROOT_VERSION

通过设置这个环境变量，你可以指定 root 包的版本，如果程序不能从 VCS 上猜测出版本号，并且未在 `composer.json` 文件中申明。

<a name="COMPOSER_VENDOR_DIR"></a>
### COMPOSER_VENDOR_DIR

通过设置这个环境变量，你可以指定 composer 将依赖安装在 `vendor` 以外的其它目录中。

<a name="COMPOSER_BIN_DIR"></a>
### COMPOSER_BIN_DIR

通过设置这个环境变量，你可以指定 `bin`（[Vendor Binaries](articles/vendor-binaries.md)）目录到 `vendor/bin` 以外的其它目录。

<a name="http_proxy-or-HTTP_PROXY"></a>
### http_proxy or HTTP_PROXY

如果你是通过 HTTP 代理来使用 Composer，你可以使用 `http_proxy` 或 `HTTP_PROXY` 环境变量。只要简单的将它设置为代理服务器的 URL。许多操作系统已经为你的服务设置了此变量。

建议使用 `http_proxy`（小写）或者两者都进行定义。因为某些工具，像 git 或 curl 将使用 `http_proxy` 小写的版本。另外，你还可以使用 `git config --global http.proxy <proxy url>` 来单独设置 git 的代理。

<a name="no_proxy"></a>
### no_proxy

如果你是使用代理服务器，并且想要对某些域名禁用代理，就可以使用 `no_proxy` 环境变量。只需要输入一个逗号相隔的域名 *排除* 列表。

此环境变量接受域名、IP 以及 CIDR地址块。你可以将它限制到一个端口（例如：`:80`）。你还可以把它设置为 `*` 来忽略所有的 HTTP 代理请求。

<a name="HTTP_PROXY_REQUEST_FULLURI"></a>
### HTTP_PROXY_REQUEST_FULLURI

如果你使用了 HTTP 代理，但它不支持 `request_fulluri` 标签，那么你应该设置这个环境变量为 `false` 或 `0` ，来防止 composer 从 `request_fulluri` 读取配置。

<a name="HTTPS_PROXY_REQUEST_FULLURI"></a>
### HTTPS_PROXY_REQUEST_FULLURI

如果你使用了 HTTPS 代理，但它不支持 `request_fulluri` 标签，那么你应该设置这个环境变量为 `false` 或 `0` ，来防止 composer 从 `request_fulluri` 读取配置。

<a name="COMPOSER_HOME"></a>
### COMPOSER_HOME

`COMPOSER_HOME` 环境变量允许你改变 Composer 的主目录。这是一个隐藏的、所有项目共享的全局目录（对本机的所有用户都可用）。

它在各个系统上的默认值分别为：
- \*nix `/home/<user>/.composer`。
- OSX `/Users/<user>/.composer`。
- Windows `C:\Users\<user>\AppData\Roaming\Composer`。

<a name="COMPOSER_HOME-config.json"></a>
#### COMPOSER_HOME/config.json

你可以在 `COMPOSER_HOME` 目录中放置一个 `config.json` 文件。在你执行 `install` 和 `update` 命令时，Composer 会将它与你项目中的 `composer.json` 文件进行合并。

该文件允许你为用户的项目设置 [配置信息](04-schema.md#config) 和 [资源库](05-repositories.md)。

若 _全局_ 和 _项目_ 存在相同配置项，那么项目中的 `composer.json` 文件拥有更高的优先级。

<a name="COMPOSER_CACHE_DIR"></a>
### COMPOSER_CACHE_DIR

`COMPOSER_CACHE_DIR` 环境变量允许你设置 Composer 的缓存目录，这也可以通过 [`cache-dir`](04-schema.md#config) 进行配置。

它在各个系统上的默认值分别为：
- \*nix and OSX `$COMPOSER_HOME/cache`。
- Windows `C:\Users\<user>\AppData\Local\Composer` 或 `%LOCALAPPDATA%/Composer`。

<a name="COMPOSER_PROCESS_TIMEOUT"></a>
### COMPOSER_PROCESS_TIMEOUT

这个环境变量控制着 Composer 执行命令的等待时间（例如：git 命令）。默认值为300秒（5分钟）。

<a name="COMPOSER_DISCARD_CHANGES"></a>
### COMPOSER_DISCARD_CHANGES

这个环境变量控制着 discard-changes [config option](04-schema.md#config)。

<a name="COMPOSER_NO_INTERACTION"></a>
### COMPOSER_NO_INTERACTION

如果设置为1，这个环境变量将使 Composer 在执行每一个命令时都放弃交互，相当于对所有命令都使用了 `--no-interaction`。可以在搭建 *虚拟机/持续集成服务器* 时这样设置。

&larr; [库（资源包）](02-libraries.md)  |  [架构](04-schema.md) &rarr;
