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
所以默认使用 Packagist 上注册的 `Monolog/Monolog` 包（请参阅 [下文](#packagist) 关于 Packagist 的说明，
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
> 首先获取您请求的包的名称，并在 [`repositories`](04-schema.md#repositories) kye 注册的存储库中搜索该包。
> 如果您没有注册任何额外的存储库，或者在您指定的存储库中找不到具有该名称的包，
> 那么它将返回到 Packagist 上进行查找（更多信息请参阅 [下文](#packagist)）。
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

当 Composer 完成安装后，它会将下载到的所有包及其确切版本写入 `composer.lock` 文件中，并将项目锁定到这些特定版本。
您应该将 `composer.lock` 文件提交到您的项目中，以便所有处理该项目的人员都被锁定到相同版本的依赖（下文会有更详细的介绍）。

### 安装时已存在 `composer.lock` 文件

让我们进入第二个场景。如果在运行 `composer install` 时已经有一个 `composer.lock` 文件以及一个 `composer.json` 文件，
则表示你之前已经运行过 `install` 命令，或者项目中的其他人运行了 `install` 命令，并将 `composer.lock` 文件提交给了项目（这很好）。

不管怎样，当存在 `composer.lock` 文件时运行 `install` 可以解析并安装您在 `composer.json` 中列出的所有依赖项，
但是 Composer 会使用在 `composer.lock` 中列出的准确版本，以确保包版本对于项目中的每个人都是一致的。
因此，您将拥有 `composer.json` 文件请求的所有依赖项，但它们可能并非都是最新的可用版本
（在 `composer.lock` 文件中列出的某些依赖项可能在文件创建后发布了较新的版本）。
这是预先设计好的，它可以确保项目不会因为依赖项中的某个意外更改而中断。

### 提交你的 `composer.lock` 文件到版本库

将 `composer.lock` 文件提交到版本库非常重要，因为它会让参与项目的所有人都使用相同版本的依赖项。
您的 CI 服务器、生产环境、团队中的其他开发人员、所有平台和所有人都在相同的依赖项上运行，
这减少了影响部署的某些部分的错误的可能性。即使您是单独开发的，在重新安装项目的六个月内，
即使您的依赖项从那时起发布了许多新版本，您仍然可以确信所安装的依赖项仍在正常工作。
（更多内容，请参阅下面有关使用 `update` 命令的说明。）

## 将依赖项更新到其最新版本

如上所述，`composer.lock` 文件会阻止程序自动获取依赖项的最新版本。如需更新到最新版本，请使用[`update`](03-cli.md#update) 命令。
这将获取最新的匹配版本（根据您的 `composer.json` 文件），并将新的版本号更新进锁文件。
（这相当于删除 `composer.lock` 文件并再次运行 `install`。）

```sh
php composer.phar update
```

> **注意：** 如果对 `composer.json` 进行了可能会影响依赖项解析的更改后，未更新 `composer.lock` 文件
> 则在执行 `install` 命令时，Composer 将显示一条警告。

如果只想安装或更新一个依赖项，可以以白名单的方式显示的指定它：

```sh
php composer.phar update monolog/monolog [...]
```

> **注意：** 当前项目作为其它项目的依赖项时，可以不必提交锁文件，详情请参见：[库-锁文件](02-libraries.md#lock-file)。

## Packagist

[Packagist](https://packagist.org/) 是主要的 Composer 存储库。Composer 存储库是最基础的包的来源：
一个你可以从那里得到依赖包的地方。Packagist 的目标是成为每个使用者的中央存储库。
这意味着您可以简单的 `require` 任何可用的包，而无需进一步指定 Composer 应在何处查找该依赖包。

当您访问 [Packagist 网站](https://packagist.org/)（packagist.org），您就可以浏览和搜索依赖包。

建议使用 Composer 的任何开源项目都在 Packagist 上发布其依赖包。
虽然不需要在 Packagist 上供 Composer 使用，但它可以更快地被其他开发人员发现和采用。

## 平台包

Composer 具有平台包，这些包是用于系统上安装但 Composer 实际上无法安装的内容的虚拟包。
这包括 PHP 本身、PHP 扩展和一些系统库。

* `php` 表示用户的 PHP 版本，允许您应用版本约束，例如 `^7.1`。
如果需要 64 位版本的 PHP，您可以 require `php-64bit` 包。

* `hhvm` 表示 HHVM 运行时的版本并允许您应用约束，例如 `^2.3`.

* `ext-<name>` 允许您指定 PHP 扩展（包括核心扩展）。
版本控制在这里可能非常不一致，所以将约束设置为 `*` 通常是一个好主意。
扩展包名称的一个示例是 `ext-gd`。

* `lib-<name>` 允许对 PHP 使用的库版本进行约束。
以下内容可用：`curl`, `iconv`, `icu`, `libxml`, `openssl`, `pcre`, `uuid`, `xsl`.

您可以使用 [`show --platform`](03-cli.md#show) 获取本地可用平台包的列表。

## 自动加载

对于指定自动加载信息的库，Composer 生成一个 `vendor/autoload.php` 文件。
您只需要引用此文件就可以开始使用这些库提供的类，而不需要做任何额外的工作：

```php
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('name');
$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));
$log->addWarning('Foo');
```

您甚至可以通过向 `composer.json` 添加一个[`autoload`](04-schema.md#autoload) 字段来向自动加载程序添加您自己的代码。

```json
{
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```

Composer 将为 `Acme` 命名空间注册一个 [PSR-4](http://www.php-fig.org/psr/psr-4/) 自动加载程序。

定义从命名空间到目录的映射。`src` 目录将位于项目根目录中，与 `vendor` 目录处于同一级别。
例如，文件名为 `src/Foo.php`，则表示其包含一个 `Acme\Foo` 类。

添加 [`autoload`](04-schema.md#autoload) 字段后，必须重新运行 [`dump-autoload`](03-cli.md#dump-autoload) 以重新生成 `vendor/autoload.php` 文件。

引入该文件还将返回 autoloader 实例，因此可以将 include 调用的返回值存储在变量中，
并添加更多的命名空间。这对于自动加载测试套件中的类很有用。例如：

```php
$loader = require __DIR__ . '/vendor/autoload.php';
$loader->addPsr4('Acme\\Test\\', __DIR__);
```

除了 PSR-4 自动加载之外，Composer 还支持 PSR-0、类映射和文件自动加载。
相关信息，请参阅 [`autoload`](04-schema.md#autoload)。

另请参阅文档中有关[优化自动加载器](articles/autoloader-optimization.md)的章节。

> **注意：** Composer 提供了自己的自动加载器。如果不想使用该文件，
> 还可以引用 `vendor/composer/autoload_*.php` 文件，
> 这些文件返回的关联数组，允许您配置自己的自动加载程序。

&larr; [Intro](00-intro.md)  |  [Libraries](02-libraries.md) &rarr;
