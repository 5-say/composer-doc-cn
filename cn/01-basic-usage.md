- [基本用法](#%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95)
  - [简介](#%E7%AE%80%E4%BB%8B)
  - [`composer.json`：项目设置](#composerjson%EF%BC%9A%E9%A1%B9%E7%9B%AE%E8%AE%BE%E7%BD%AE)
    - [`require` 键](#require-%E9%94%AE)
    - [包名](#%E5%8C%85%E5%90%8D)
    - [包版本约束](#%E5%8C%85%E7%89%88%E6%9C%AC%E7%BA%A6%E6%9D%9F)
  - [安装依赖](#%E5%AE%89%E8%A3%85%E4%BE%9D%E8%B5%96)
    - [安装时 `composer.lock` 文件不存在](#%E5%AE%89%E8%A3%85%E6%97%B6-composerlock-%E6%96%87%E4%BB%B6%E4%B8%8D%E5%AD%98%E5%9C%A8)
    - [通过 `composer.lock` 文件安装](#%E9%80%9A%E8%BF%87-composerlock-%E6%96%87%E4%BB%B6%E5%AE%89%E8%A3%85)
    - [Commit Your `composer.lock` File to Version Control](#commit-your-composerlock-file-to-version-control)
  - [Updating Dependencies to their Latest Versions](#updating-dependencies-to-their-latest-versions)
  - [Packagist](#packagist)
  - [Platform packages](#platform-packages)
  - [Autoloading](#autoloading)

# 基本用法

## 简介

为了我们的基本用法介绍，我们将会安装 `monolog/monolog`，一个日志库。
如果您还没有安装 Composer，请参考 [简介](00-intro.md) 章节。

> **注意：** 为了简单起见，本介绍将假设您已经完成了一个 Composer 的 [局部安装](00-intro.md#%E5%B1%80%E9%83%A8%E5%AE%89%E8%A3%85)。

## `composer.json`：项目设置

在你的项目中开始使用 Composer，你所需要的只是一个 `composer.json` 文件。
这个文件描述了项目的依赖关系，并且可能包含其他元数据。

### `require` 键

[`require`](04-schema.md#require) 是第一个（通常也是唯一一个）您需要在 `composer.json` 中指定的东西。
您只需要简单地告诉 Composer 您的项目所依赖的包。

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

正如您所看到的，[`require`](04-schema.md#require) 需要一个映射 **包名**（例如 `monolog/monolog`）到 **版本限制** 的对象（例如`1.0.*`）。

Composer 使用这些信息在包的 “资源仓库”（使用 [`repositories`](04-schema.md#repositories) 键注册的信息中，或在默认的资源仓库 Packagist 中）搜索正确的文件。在上面的示例中，因为没有其他资源仓库在 `composer.json` 文件中被注册，那么我们假设 `monolog/monolog` 包是在 Packagist 上注册的。（请参阅章末关于 [packagist](#packagist) 的介绍，或在 [资源仓库](05-repositories.md) 一章中了解更多的信息）。

### 包名

包名由所有者的名称与项目的名称构成。通常它们都是相同的 —— 所有者名称的存在，只是为了防止命名冲突。例如，它允许两个不同的人创建一个名为 `json` 的库。一个可能命名为 `igorw/json`，另一个可能是 `seldaek/json`。

请在下一章“[库](02-libraries.md)”中阅读更多关于发布包和包命名的内容。
（注意：您还可以指定“平台包”作为依赖项，允许您依赖特定版本的服务器软件。请查看章末关于 [平台包](#platform-packages) 的介绍）

### 包版本约束

在我们的示例中，我们要求使用版本限制为 [`1.0.*`](https://semver.mwl.be/#?package=monolog%2Fmonolog&version=1.0.*) 的 Monolog 包。
这意味着在 `1.0` 开发分支中的任何版本，或任何大于等于 1.0 并且小于 1.1（`>=1.0 <1.1`）的版本。

请阅读有关 [版本](articles/versions.md) 的更深入的信息，了解不同版本之间的关系，以及版本约束。

> **Composer 如何下载正确的文件？** 当你在 `composer.json` 中指定一个依赖项时，
> 首先 Composer 获取您所需要的依赖包名称，并从您在 [`repositories`](04-schema.md#repositories) 键中注册的所有资源仓库中搜索它。
> 如果您没有额外注册任何资源仓库，或者它没有在您指定的资源仓库中找到对应的包名，那么就又回到了 Packagist 的问题上（更多信息请查看下文关于 [Packagist 平台](#packagist) 的介绍）。
>
> 当 Composer 在 Packagist 或您指定的资源仓库中找到了名称正确的依赖包，
> 它将使用依赖包的 VCS 版本控制特性（例如，分支和标签）来尝试找到与您指定的版本约束最接近的匹配项。请务必阅读 [版本与约束](articles/versions.md) 一文中有关版本和包解析的内容。

> **注意：** 如果您尝试使用 require 命令引入一个依赖包，但 Composer 抛出了一个关于包稳定性的错误，这说明您所指定的版本可能无法满足默认的最低稳定性要求。
> 默认情况下，在 VCS 中搜索有效的包版本时只有稳定的版本才会被考虑。
>
> 如果您试图引入一个包的 dev、alpha、beta 或 RC 版本，您可能会遇到这种情况。
> 请阅读 [composer.json 文件结构](04-schema.md) 一章中关于稳定性标志以及 `最低稳定性` 的相关内容。

## 安装依赖

要为您的项目安装已定义的依赖，只需要运行 [`install`](03-cli.md#install) 命令。

```sh
php composer.phar install
```

当您运行此命令时，有两件事情可能会发生：

### 安装时 `composer.lock` 文件不存在

如果您在此之前并没有运行过 `install` 命令，并且也不存在 `composer.lock` 文件，
Composer 将简单的解析您在 `composer.json` 文件中列出的依赖项，并将其最新版本的文件下载到您项目的 `vendor` 目录。
（`vendor` 目录是一个项目中存放所有第三方代码的常规位置）。在我们的例子中，您最终会在 `vendor/monolog/monolog/` 目录中得到 Monolog 的源文件。
如果 Monolog 列出了其它依赖，它们也将出现在 `vendor/` 目录下。

> **提示：** 如果您正在为您的项目使用 git，您或许会想要将 `vendor` 目录添加到 `.gitignore` 文件中。
> 而不是将所有的第三方代码添加到您的版本存储库中。

但 Composer 完成安装时，它将所有下载的包的确切版本写入 `composer.lock` 文件，将项目锁定到那些特定的版本。
您应该将 `composer.lock` 文件提交到您的项目仓库，因此，所有在项目上工作的人都将被锁定在相同的依赖项（下面会进行详细的介绍）。

### 通过 `composer.lock` 文件安装

This brings us to the second scenario. If there is already a `composer.lock` file as well as a
`composer.json` file when you run `composer install`, it means either you ran the
`install` command before, or someone else on the project ran the `install` command and
committed the `composer.lock` file to the project (which is good).

Either way, running `install` when a `composer.lock` file is present resolves and installs
all dependencies that you listed in `composer.json`, but Composer uses the exact versions listed
in `composer.lock` to ensure that the package versions are consistent for everyone
working on your project. As a result you will have all dependencies requested by your
`composer.json` file, but they may not all be at the very latest available versions
(some of the dependencies listed in the `composer.lock` file may have released newer versions since
the file was created). This is by design, it ensures that your project does not break because of
unexpected changes in dependencies.

### Commit Your `composer.lock` File to Version Control

Committing this file to VC is important because it will cause anyone who sets
up the project to use the exact same
versions of the dependencies that you are using. Your CI server, production
machines, other developers in your team, everything and everyone runs on the
same dependencies, which mitigates the potential for bugs affecting only some
parts of the deployments. Even if you develop alone, in six months when
reinstalling the project you can feel confident the dependencies installed are
still working even if your dependencies released many new versions since then.
(See note below about using the `update` command.)

## Updating Dependencies to their Latest Versions

As mentioned above, the `composer.lock` file prevents you from automatically getting
the latest versions of your dependencies. To update to the latest versions, use the
[`update`](03-cli.md#update) command. This will fetch the latest matching
versions (according to your `composer.json` file) and update the lock file
with the new versions. (This is equivalent to deleting the `composer.lock` file
and running `install` again.)

```sh
php composer.phar update
```
> **注意：** Composer will display a Warning when executing an `install` command
> if `composer.lock` and `composer.json` are not synchronized.

If you only want to install or update one dependency, you can whitelist them:

```sh
php composer.phar update monolog/monolog [...]
```

> **注意：** For libraries it is not necessary to commit the lock
> file, see also: [Libraries - Lock file](02-libraries.md#lock-file).

## Packagist

[Packagist](https://packagist.org/) is the main Composer repository. A Composer
repository is basically a package source: a place where you can get packages
from. Packagist aims to be the central repository that everybody uses. This
means that you can automatically `require` any package that is available there,
without further specifying where Composer should look for the package.

If you go to the [Packagist website](https://packagist.org/) (packagist.org),
you can browse and search for packages.

Any open source project using Composer is recommended to publish their packages
on Packagist. A library does not need to be on Packagist to be used by Composer,
but it enables discovery and adoption by other developers more quickly.

## Platform packages

Composer has platform packages, which are virtual packages for things that are
installed on the system but are not actually installable by Composer. This
includes PHP itself, PHP extensions and some system libraries.

* `php` represents the PHP version of the user, allowing you to apply
  constraints, e.g. `>=5.4.0`. To require a 64bit version of php, you can
  require the `php-64bit` package.

* `hhvm` represents the version of the HHVM runtime and allows you to apply
  a constraint, e.g., `>=2.3.3`.

* `ext-<name>` allows you to require PHP extensions (includes core
  extensions). Versioning can be quite inconsistent here, so it's often
  a good idea to just set the constraint to `*`.  An example of an extension
  package name is `ext-gd`.

* `lib-<name>` allows constraints to be made on versions of libraries used by
  PHP. The following are available: `curl`, `iconv`, `icu`, `libxml`,
  `openssl`, `pcre`, `uuid`, `xsl`.

You can use [`show --platform`](03-cli.md#show) to get a list of your locally
available platform packages.

## Autoloading

For libraries that specify autoload information, Composer generates a
`vendor/autoload.php` file. You can simply include this file and start
using the classes that those libraries provide without any extra work:

```php
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('name');
$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));
$log->addWarning('Foo');
```

You can even add your own code to the autoloader by adding an
[`autoload`](04-schema.md#autoload) field to `composer.json`.

```json
{
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```

Composer will register a [PSR-4](http://www.php-fig.org/psr/psr-4/) autoloader
for the `Acme` namespace.

You define a mapping from namespaces to directories. The `src` directory would
be in your project root, on the same level as `vendor` directory is. An example
filename would be `src/Foo.php` containing an `Acme\Foo` class.

After adding the [`autoload`](04-schema.md#autoload) field, you have to re-run
[`dump-autoload`](03-cli.md#dump-autoload) to re-generate the
`vendor/autoload.php` file.

Including that file will also return the autoloader instance, so you can store
the return value of the include call in a variable and add more namespaces.
This can be useful for autoloading classes in a test suite, for example.

```php
$loader = require __DIR__ . '/vendor/autoload.php';
$loader->addPsr4('Acme\\Test\\', __DIR__);
```

In addition to PSR-4 autoloading, Composer also supports PSR-0, classmap and
files autoloading. See the [`autoload`](04-schema.md#autoload) reference for
more information.

See also the docs on [optimizing the autoloader](articles/autoloader-optimization.md).

> **注意：** Composer provides its own autoloader. If you don't want to use that
> one, you can just include `vendor/composer/autoload_*.php` files, which return
> associative arrays allowing you to configure your own autoloader.

&larr; [Intro](00-intro.md)  |  [Libraries](02-libraries.md) &rarr;
