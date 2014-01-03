# 命令行

你已经学会了如何使用命令行界面做一些事情。本章将向你介绍所有可用的命令。

为了从命令行获得帮助信息，请运行 `composer` 或者 `composer list` 命令，然后结合 `--help` 命令来获得更多的帮助信息。

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

## 进程退出代码

* **0:** 正常
* **1:** 通用/未知错误
* **2:** 依赖关系处理错误

## 初始化 `init`

在 [“库”](02-libraries.md) 那一章我们看到了如何手动创建 `composer.json` 文件。实际上还有一个 `init` 命令可以更容易的做到这一点。

当您运行该命令，它会以交互方式要求您填写一些信息，同时聪明的使用一些默认值。

    $ php composer.phar init

### 初始化参数

* **--name:** 包的名称。
* **--description:** 包的描述。
* **--author:** 包的作者。
* **--homepage:** 包的主页。
* **--require:** 需要依赖的其它包，必须要有一个版本约束。并且应该遵循 `foo/bar:1.0.0` 这样的格式。
* **--require-dev:** 开发版的依赖包，内容格式与 **--require** 相同。
* **--stability (-s):** `minimum-stability` 字段的值。

## 安装 `install`

`install` 命令从当前目录读取 `composer.json` 文件，处理了依赖关系，并把其安装到 `vendor` 目录下。

    $ php composer.phar install

如果当前目录下存在 `composer.lock` 文件，它会从此文件读取依赖版本，而不是根据 `composer.json` 文件去获取依赖。这确保了该库的每个使用者都能得到相同的依赖版本。

如果没有 `composer.lock` 文件，composer 将在处理完依赖关系后创建它。

### 安装参数

* **--prefer-source:** 下载包的方式有两种： `source`
  和 `dist`。对于稳定版本 composer 将默认使用 `dist` 方式。而 `source` 表示版本控制源 。如果 `--prefer-source` 是被启用的，composer 将从 `source` 安装（如果有的话）。如果想要使用一个 bugfix 到你的项目，这是非常有用的。并且可以直接从本地的版本库直接获取依赖关系。
* **--prefer-dist:** 与 `--prefer-source` 相反，composer 将尽可能的从 `dist` 获取，这将大幅度的加快在 build servers 上的安装。这也是一个回避 git 问题的途径，如果你不清楚如何正确的设置。
* **--dry-run:** 如果你只是想演示而并非实际安装一个包，你可以运行 `--dry-run` 命令，它将模拟安装并显示将会发生什么。
* **--dev:** 安装 `require-dev` 字段中列出的包（这是一个默认值）。
* **--no-dev:** 跳过 `require-dev` 字段中列出的包。
* **--no-scripts:** 跳过 `composer.json` 文件中定义的脚本。
* **--no-plugins:** 关闭 plugins。
* **--no-progress:** 移除进度信息，这可以避免一些不处理换行的终端或脚本出现混乱的显示。
* **--optimize-autoloader (-o):** 转换 PSR-0 autoloading 到 classmap 可以获得更快的加载支持。特别是在生产环境下建议这么做，但由于运行需要一些时间，因此并没有作为默认值。

## 更新 `update`

为了获取依赖的最新版本，并且升级 `composer.lock` 文件，你应该使用 `update` 命令。

    $ php composer.phar update

这将解决项目的所有依赖，并将确切的版本号写入 `composer.lock`。

如果你只是想更新几个包，你可以像这样分别列出它们：

    $ php composer.phar update vendor/package vendor/package2

你还可以使用通配符进行批量更新：

    $ php composer.phar update vendor/*

### 更新参数

* **--prefer-source:** 当有可用的包时，从 `source` 安装。
* **--prefer-dist:** 当有可用的包时，从 `dist` 安装。
* **--dry-run:** 模拟命令，并没有做实际的操作。
* **--dev:** 安装 `require-dev` 字段中列出的包（这是一个默认值）。
* **--no-dev:** 跳过 `require-dev` 字段中列出的包。
* **--no-scripts:** 跳过 `composer.json` 文件中定义的脚本。
* **--no-plugins:** 关闭 plugins。
* **--no-progress:** 移除进度信息，这可以避免一些不处理换行的终端或脚本出现混乱的显示。
* **--optimize-autoloader (-o):** 转换 PSR-0 autoloading 到 classmap 可以获得更快的加载支持。特别是在生产环境下建议这么做，但由于运行需要一些时间，因此并没有作为默认值。
* **--lock:** 仅更新 lock 文件的 hash，取消有关 lock 文件过时的警告。
* **--with-dependencies** 同时更新白名单内包的依赖关系，这将进行递归更新。

## 申明依赖 `require`

`require` 命令增加新的依赖包到当前目录的 `composer.json` 文件中。

    $ php composer.phar require

在添加或改变依赖时， 修改后的依赖关系将被安装或者更新。

如果你不希望通过交互来指定依赖包，你可以在这条令中直接指明依赖包。

    $ php composer.phar require vendor/package:2.* vendor/package2:dev-master

### 申明依赖的参数

* **--prefer-source:** 当有可用的包时，从 `source` 安装。
* **--prefer-dist:** 当有可用的包时，从 `dist` 安装。
* **--dev:** 安装 `require-dev` 字段中列出的包。
* **--no-update:** 禁用依赖关系的自动更新。
* **--no-progress:** 移除进度信息，这可以避免一些不处理换行的终端或脚本出现混乱的显示。

## 全局执行 `global`

`global` 命令允许你在 [COMPOSER_HOME](#composer-home) 目录下执行其它命令，像 `install`、`require` 或 `update`。

并且如果你将 `$COMPOSER_HOME/vendor/bin` 加入到了 `$PATH` 环境变量中，你就可以用它在命令行中安装全局应用，下面是一个例子：

    $ php composer.phar global require fabpot/php-cs-fixer:dev-master

现在 `php-cs-fixer` 就可以在全局范围使用了（假设你已经设置了你的 PATH）。如果稍后你想更新它，你只需要运行 `global update`：

    $ php composer.phar global update

## 搜索 `search`

`search` 命令允许你为当前项目搜索依赖包，通常它只搜索 packagist.org 上的包，你可以简单的输入你的搜索条件。

    $ php composer.phar search monolog

您也可以通过传递多个参数来进行多条件搜索。

### 搜索参数

* **--only-name (-N):** 仅针对指定的名称搜索（完全匹配）。

## 展示 `show`

列出所有可用的软件包，你可以使用 `show` 命令。

    $ php composer.phar show

如果你想看到一个包的详细信息，你可以输入一个包名称。

    $ php composer.phar show monolog/monolog

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

你甚至可以输入一个软件包的版本号，来显示该版本的详细信息。

    $ php composer.phar show monolog/monolog 1.0.2

### 展示的参数

* **--installed (-i):** 列出已安装的依赖包。
* **--platform (-p):** 仅列出平台软件包（PHP 与它的扩展）。
* **--self (-s):** 仅列出当前项目信息。

## 依赖性检测 `depends`

`depends` 命令可以查出已安装在你项目中的某个包，是否正在被其它的包所依赖，并列出他们。

    $ php composer.phar depends --link-type=require monolog/monolog

    nrk/monolog-fluent
    poc/poc
    propel/propel
    symfony/monolog-bridge
    symfony/symfony

### 依赖性检测的参数

* **--link-type:** 检测的类型，默认为 `require` 也可以是 `require-dev`。

## 有效性检测 `validate`

在提交 `composer.json` 文件，和创建 tag 前，你应该始终运行 `validate` 命令。它将检测你的 `composer.json` 文件是否是有效的

    $ php composer.phar validate

## status

If you often need to modify the code of your dependencies and they are
installed from source, the `status` command allows you to check if you have
local changes in any of them.

    $ php composer.phar status

With the `--verbose` option you get some more information about what was
changed:

    $ php composer.phar status -v
    You have changes in the following dependencies:
    vendor/seld/jsonlint:
        M README.mdown

## self-update

To update composer itself to the latest version, just run the `self-update`
command. It will replace your `composer.phar` with the latest version.

    $ php composer.phar self-update

If you would like to instead update to a specific release simply specify it:

    $ composer self-update 1.0.0-alpha7

If you have installed composer for your entire system (see [global installation](00-intro.md#globally)),
you may have to run the command with `root` privileges

    $ sudo composer self-update

### Options

* **--rollback (-r):** Rollback to the last version you had installed.
* **--clean-backups:** Delete old backups during an update. This makes the current version of composer the only backup available after the update.

## config

The `config` command allows you to edit some basic composer settings in either
the local composer.json file or the global config.json file.

    $ php composer.phar config --list

### Usage

`config [options] [setting-key] [setting-value1] ... [setting-valueN]`

`setting-key` is a configuration option name and `setting-value1` is a
configuration value.  For settings that can take an array of values (like
`github-protocols`), more than one setting-value arguments are allowed.

See the [config schema section](04-schema.md#config) for valid configuration
options.

### Options

* **--global (-g):** Operate on the global config file located at
`$COMPOSER_HOME/config.json` by default.  Without this option, this command
affects the local composer.json file or a file specified by `--file`.
* **--editor (-e):** Open the local composer.json file using in a text editor as
defined by the `EDITOR` env variable.  With the `--global` option, this opens
the global config file.
* **--unset:** Remove the configuration element named by `setting-key`.
* **--list (-l):** Show the list of current config variables.  With the `--global`
 option this lists the global configuration only.
* **--file="..." (-f):** Operate on a specific file instead of composer.json. Note
 that this cannot be used in conjunction with the `--global` option.

### Modifying Repositories

In addition to modifying the config section, the `config` command also supports making
changes to the repositories section by using it the following way:

    $ php composer.phar config repositories.foo vcs http://github.com/foo/bar

## create-project

You can use Composer to create new projects from an existing package. This is
the equivalent of doing a git clone/svn checkout followed by a composer install
of the vendors.

There are several applications for this:

1. You can deploy application packages.
2. You can check out any package and start developing on patches for example.
3. Projects with multiple developers can use this feature to bootstrap the
   initial application for development.

To create a new project using composer you can use the "create-project" command.
Pass it a package name, and the directory to create the project in. You can also
provide a version as third argument, otherwise the latest version is used.

If the directory does not currently exist, it will be created during installation.

    php composer.phar create-project doctrine/orm path 2.2.*

It is also possible to run the command without params in a directory with an
existing `composer.json` file to bootstrap a project.

By default the command checks for the packages on packagist.org.

### Options

* **--repository-url:** Provide a custom repository to search for the package,
  which will be used instead of packagist. Can be either an HTTP URL pointing
  to a `composer` repository, or a path to a local `packages.json` file.
* **--stability (-s):** Minimum stability of package. Defaults to `stable`.
* **--prefer-source:** Install packages from `source` when available.
* **--prefer-dist:** Install packages from `dist` when available.
* **--dev:** Install packages listed in `require-dev`.
* **--no-install:** Disables installation of the vendors.
* **--no-plugins:** Disables plugins.
* **--no-scripts:** Disables the execution of the scripts defined in the root
  package.
* **--no-progress:** Removes the progress display that can mess with some
  terminals or scripts which don't handle backspace characters.
* **--keep-vcs:** Skip the deletion of the VCS metadata for the created
  project. This is mostly useful if you run the command in non-interactive
  mode.

## dump-autoload

If you need to update the autoloader because of new classes in a classmap
package for example, you can use "dump-autoload" to do that without having to
go through an install or update.

Additionally, it can dump an optimized autoloader that converts PSR-0 packages
into classmap ones for performance reasons. In large applications with many
classes, the autoloader can take up a substantial portion of every request's
time. Using classmaps for everything is less convenient in development, but
using this option you can still use PSR-0 for convenience and classmaps for
performance.

### Options

* **--optimize (-o):** Convert PSR-0 autoloading to classmap to get a faster
  autoloader. This is recommended especially for production, but can take
  a bit of time to run so it is currently not done by default.

## licenses

Lists the name, version and license of every package installed. Use
`--format=json` to get machine readable output.

## run-script

To run [scripts](articles/scripts.md) manually you can use this command,
just give it the script name and optionally --no-dev to disable the dev mode.

## diagnose

If you think you found a bug, or something is behaving strangely, you might
want to run the `diagnose` command to perform automated checks for many common
problems.

    $ php composer.phar diagnose

## archive

This command is used to generate a zip/tar archive for a given package in a
given version. It can also be used to archive your entire project without
excluded/ignored files.

    $ php composer.phar archive vendor/package 2.0.21 --format=zip

### Options

* **--format (-f):** Format of the resulting archive: tar or zip (default:
  "tar")
* **--dir:** Write the archive to this directory (default: ".")

## help

To get more information about a certain command, just use `help`.

    $ php composer.phar help install

## Environment variables

You can set a number of environment variables that override certain settings.
Whenever possible it is recommended to specify these settings in the `config`
section of `composer.json` instead. It is worth noting that the env vars will
always take precedence over the values specified in `composer.json`.

### COMPOSER

By setting the `COMPOSER` env variable it is possible to set the filename of
`composer.json` to something else.

For example:

    $ COMPOSER=composer-other.json php composer.phar install

### COMPOSER_ROOT_VERSION

By setting this var you can specify the version of the root package, if it can
not be guessed from VCS info and is not present in `composer.json`.

### COMPOSER_VENDOR_DIR

By setting this var you can make composer install the dependencies into a
directory other than `vendor`.

### COMPOSER_BIN_DIR

By setting this option you can change the `bin` ([Vendor Binaries](articles/vendor-binaries.md))
directory to something other than `vendor/bin`.

### http_proxy or HTTP_PROXY

If you are using composer from behind an HTTP proxy, you can use the standard
`http_proxy` or `HTTP_PROXY` env vars. Simply set it to the URL of your proxy.
Many operating systems already set this variable for you.

Using `http_proxy` (lowercased) or even defining both might be preferable since
some tools like git or curl will only use the lower-cased `http_proxy` version.
Alternatively you can also define the git proxy using
`git config --global http.proxy <proxy url>`.

### no_proxy

If you are behind a proxy and would like to disable it for certain domains, you
can use the `no_proxy` env var. Simply set it to a comma separated list of
domains the proxy should *not* be used for.

The env var accepts domains, IP addresses, and IP address blocks in CIDR
notation. You can restrict the filter to a particular port (e.g. `:80`). You
can also set it to `*` to ignore the proxy for all HTTP requests.

### HTTP_PROXY_REQUEST_FULLURI

If you use a proxy but it does not support the request_fulluri flag, then you
should set this env var to `false` or `0` to prevent composer from setting the
request_fulluri option.

### HTTPS_PROXY_REQUEST_FULLURI

If you use a proxy but it does not support the request_fulluri flag for HTTPS
requests, then you should set this env var to `false` or `0` to prevent composer
from setting the request_fulluri option.

### COMPOSER_HOME

The `COMPOSER_HOME` var allows you to change the composer home directory. This
is a hidden, global (per-user on the machine) directory that is shared between
all projects.

By default it points to `/home/<user>/.composer` on \*nix,
`/Users/<user>/.composer` on OSX and
`C:\Users\<user>\AppData\Roaming\Composer` on Windows.

#### COMPOSER_HOME/config.json

You may put a `config.json` file into the location which `COMPOSER_HOME` points
to. Composer will merge this configuration with your project's `composer.json`
when you run the `install` and `update` commands.

This file allows you to set [configuration](04-schema.md#config) and
[repositories](05-repositories.md) for the user's projects.

In case global configuration matches _local_ configuration, the _local_
configuration in the project's `composer.json` always wins.

### COMPOSER_CACHE_DIR

The `COMPOSER_CACHE_DIR` var allows you to change the composer cache directory,
which is also configurable via the [`cache-dir`](04-schema.md#config) option.

By default it points to $COMPOSER_HOME/cache on \*nix and OSX, and
`C:\Users\<user>\AppData\Local\Composer` (or `%LOCALAPPDATA%/Composer`) on Windows.

### COMPOSER_PROCESS_TIMEOUT

This env var controls the time composer waits for commands (such as git
commands) to finish executing. The default value is 300 seconds (5 minutes).

### COMPOSER_DISCARD_CHANGES

This env var controls the discard-changes [config option](04-schema.md#config).

### COMPOSER_NO_INTERACTION

If set to 1, this env var will make composer behave as if you passed the
`--no-interaction` flag to every command. This can be set on build boxes/CI.

&larr; [Libraries](02-libraries.md)  |  [Schema](04-schema.md) &rarr;
