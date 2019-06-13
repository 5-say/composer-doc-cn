# 基本用法

## 简介

为了进行基本用法的介绍，我们将安装一个日志库 `monolog/monolog`。
如果你还没有安装 Composer，请参考 [简介](00-intro.md) 章节。

> **注意：** 为了简单起见，本章将假定你已经完成了一个 Composer 的 [局部安装](00-intro.md#locally)。

## `composer.json`: 项目设置

要在项目中开始使用 Composer，你只需要一个 `composer.json` 文件。
这个文件描述了项目的依赖关系，并且可能包含其他的元数据。

### The `require` key

[`require`](04-schema.md#require) 是第一个（通常也是唯一一个）你需要在 `composer.json` 中指定的东西。
你只需要简单地告诉 Composer 你的项目所依赖的包。

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

正如你所看到的，[`require`](04-schema.md#require) 是一个将 **包名**（例如 `monolog/monolog`）映射到 **版本约束**（例如`1.0.*`）的对象。

Composer 使用此信息在自定义的 [`repositories`](04-schema.md#repositories) "存储库"中搜索正确的依赖，
或在默认包存储库 Packageist 中搜索。
在上面的例子中，由于在 `composer.json` 文件中没有注册其他存储库，
所以默认使用 Packaginst 上注册的 `Monolog/Monolog` 包（请参阅 [下文](#packagist) 关于 Packagist 的说明，
或阅读更多关于 [存储库](05-repositories.md) 的文章）。

### 包名

包名由 供应商名称 和 项目名称 组成。通常情况下，这两个名称是相同的 - 供应商名称的存在只是为了防止命名冲突。
例如，它允许两个不同的人创建一个名为 `json` 的库。一个名为 `igorw/json`，另一个名为 `seldaek/json`。

请在 [这里](02-libraries.md) 阅读有关发布包和包命名的详细信息。（注意，你还可以将 "平台包" 指定为依赖项，
从而允许你指定某些服务器软件的版本。请参阅下文 [平台包](#platform-packages)。）

### 包版本约束

在我们的示例中，我们请求具有 [`1.0.*`](https://semver.mwl.be/#?package=monolog%2Fmonolog&version=1.0.*) 版本约束的 Monolog 包。
这表示 `1.0` 开发分支中的任何版本，或者大于或等于 1.0 小于 1.1 的任何版本（`>=1.0 <1.1`）。

请阅读 [版本](articles/versions.md) 章节，了解有关版本、版本如何相互关联以及版本约束的详细信息。

> **Composer 如何下载正确的文件？** 当我们在 `composer.json` 文件中指定依赖时，
> 首先获取您请求的包的名称，并在 [`repositories`](04-schema.md#repositories)kye 注册的存储库中搜索该包。
> 如果您没有注册任何额外的存储库，或者在您指定的存储库中找不到具有该名称的包，
> 那么它将返回到 Packaginst 上进行查找（更多信息请参阅 [下文](#packagist)）。
>
> 当 Composer 在 Packagist 或指定的存储库中找到正确的包时，
> 它将使用包的 VCS 版本控制功能（即分支和标记）尝试查找与指定的版本约束最匹配的包。
> 请务必阅读 [versions 章节](articles/versions.md) 中有关版本和包解析的内容。
>
> **注意：** 如果您试图引入一个包，但 Composer 在包稳定性方面抛出了一个错误，
> 那么您指定的版本可能不符合默认的最低稳定性要求。默认情况下，
> 在您的 VCS 中搜索有效的软件包版本时，只考虑稳定的版本。
>
> 如果您试图引入包的 dev、alpha、beta 或 RC 版本，可能会遇到这种情况。
> 请在 [架构页](04-schema.md) 阅读有关稳定性标志和 `minimum-stability` key 的详细信息。

## 安装依赖项

要为项目安装定义的依赖项，请运行 [`install`](03-cli.md#install) 命令。

```sh
php composer.phar install
```

运行该命令时，可能会存在以下两种情况：

### 安装时不存在 `composer.lock` 文件

如果之前从未运行过该命令，并且也没有 `composer.lock` 文件，Composer 只需解析 `composer.json` 文件中列出的所有依赖项，
并将其文件的最新版本下载到项目中的 `vendor` 目录中。（对于项目中的所有第三方代码，`vendor` 目录是默认的存放位置）。
在上面的示例中，您将获得 Monolog 的源文件，该文件位于 `vendor/monolog/monolog/` 中。
如果 Monolog 列出了其它依赖项，这些依赖项也将被存放于 `vendor/` 下的对应文件夹中。

> **提示：** 如果您在项目中使用 Git，建议将 `vendor` 目录添加到您的 `.gitignore` 文件中。
> 您通常不会希望将所有的这些第三方代码添加到你的版本控制的存储库中。

When Composer has finished installing, it writes all of the packages and the exact versions
of them that it downloaded to the `composer.lock` file, locking the project to those specific
versions. You should commit the `composer.lock` file to your project repo so that all people
working on the project are locked to the same versions of dependencies (more below).

### 安装时已存在 `composer.lock` 文件

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

### 提交你的 `composer.lock` 文件到版本库

Committing this file to VC is important because it will cause anyone who sets
up the project to use the exact same
versions of the dependencies that you are using. Your CI server, production
machines, other developers in your team, everything and everyone runs on the
same dependencies, which mitigates the potential for bugs affecting only some
parts of the deployments. Even if you develop alone, in six months when
reinstalling the project you can feel confident the dependencies installed are
still working even if your dependencies released many new versions since then.
(See note below about using the `update` command.)

## 将依赖项更新到其最新版本

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
> if the `composer.lock` has not been updated since changes were made to the
> `composer.json` that might affect dependency resolution.

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
  constraints, e.g. `^7.1`. To require a 64bit version of php, you can
  require the `php-64bit` package.

* `hhvm` represents the version of the HHVM runtime and allows you to apply
  a constraint, e.g., `^2.3`.

* `ext-<name>` allows you to require PHP extensions (includes core
  extensions). Versioning can be quite inconsistent here, so it's often
  a good idea to set the constraint to `*`.  An example of an extension
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
> one, you can include `vendor/composer/autoload_*.php` files, which return
> associative arrays allowing you to configure your own autoloader.

&larr; [Intro](00-intro.md)  |  [Libraries](02-libraries.md) &rarr;
