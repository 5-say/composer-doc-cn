<a name="Libraries"></a>
# 库（资源包）

本章将告诉你如何通过 Composer 来安装你的库。

---

- [库（资源包）](#Libraries)
  - [每一个项目都是一个包](#Every-project-is-a-package)
  - [平台软件包](#Platform-packages)
  - [指明版本](#Specifying-the-version)
    - [标签](#Tags)
    - [分支](#Branches)
    - [别名](#Aliases)
  - [锁文件](#Lock-file)
  - [发布到 VCS（线上版本控制系统）](#Publishing-to-a-VCS)
  - [发布到 packagist](#Publishing-to-packagist)

---

<a name="Every-project-is-a-package"></a>
## 每一个项目都是一个包

只要你有一个 `composer.json` 文件在目录中，那么整个目录就是一个包。当你添加一个 `require` 到项目中，你就是在创建一个依赖于其它库的包。你的项目和库之间唯一的区别是，你的项目是一个没有名字的包。

为了使它成为一个可安装的包，你需要给它一个名称。你可以通过 `composer.json` 中的 `name` 来定义：

```json
{
    "name": "acme/hello-world",
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

在这种情况下项目的名称为 `acme/hello-world`，其中 `acme` 是供应商的名称。供应商的名称是必须填写的。

> **注意：** 如果你不知道拿什么作为供应商的名称，
> 那么使用你 github 上的用户名通常是不错的选择。
> 虽然包名不区分大小写，但惯例是使用小写字母，并用连字符作为单词的分隔。

<a name="Platform-packages"></a>
## 平台软件包

Composer 将那些已经安装在系统上，但并不是由 Composer 安装的包视为一个虚拟的平台软件包。这包括PHP本身，PHP扩展和一些系统库。

* `php` 表示用户的 PHP 版本要求，你可以对其做出限制。例如 `>=5.4.0`。如果需要64位版本的 PHP，你可以使用 `php-64bit` 进行限制。

* `hhvm` 代表的是 HHVM（也就是 HipHop Virtual
  Machine） 运行环境的版本，并且允许你设置一个版本限制，例如，'>=2.3.3'。

* `ext-<name>` 可以帮你指定需要的 PHP 扩展（包括核心扩展）。通常 PHP 拓展的版本可以是不一致的，将它们的版本约束为 `*` 是一个不错的主意。一个 PHP 扩展包的例子：包名可以写成 `ext-gd`。

* `lib-<name>` 允许对 PHP 库的版本进行限制。  
以下是可供使用的名称：`curl`、`iconv`、`icu`、`libxml`、`openssl`、`pcre`、`uuid`、`xsl`。

你可以使用 `composer show --platform` 命令来获取可用的平台软件包的列表。

<a name="Specifying-the-version"></a>
## 指明版本

你需要一些方法来指明自己开发的包的版本，当你在 Packagist 上发布自己的包，它能够从 VCS (git, svn,
hg) 的信息推断出包的版本，因此你不必手动指明版本号，并且也不建议这样做。请查看 [标签](#Tags) 和 [分支](#Branches) 来了解版本号是如何被提取的。

如果你想要手动创建并且真的要明确指定它，你只需要添加一个 `version` 字段：

```json
{
    "version": "1.0.0"
}
```

> **注意：** 你应该尽量避免手动设置版本号，因为标签的值必须与标签名相匹配。

<a name="Tags"></a>
### 标签

对于每一个看起来像版本号的标签，都会相应的创建一个包的版本。它应该符合 'X.Y.Z' 或者 'vX.Y.Z' 的形式，`-patch`、`-alpha`、`-beta` 或 `-RC` 这些后缀是可选的。在后缀之后也可以再跟上一个数字。

下面是有效的标签名称的几个例子：

- 1.0.0
- v1.0.0
- 1.10.5-RC1
- v4.4.4beta2
- v2.0.0-alpha
- v2.0.4-p1

> **注意：** 即使你的标签带有前缀 `v`，
> 由于在需要 `require` 一个[版本的约束](01-basic-usage.md#Package-Versions)时是不允许这种前缀的，
> 因此 `v` 将被省略（例如标签 `V1.0.0` 将创建 `1.0.0` 版本）。

<a name="Branches"></a>
### 分支

对于每一个分支，都会相应的创建一个包的开发版本。如果分支名看起来像一个版本号，那么将创建一个如同 `{分支名}-dev` 的包版本号。例如一个分支 `2.0` 将产生一个 `2.0.x-dev` 包版本（加入了 `.x` 是出于技术的原因，以确保它被识别为一个分支，而 `2.0.x` 的分支名称也是允许的，它同样会被转换为 `2.0.x-dev`）。如果分支名看起来不像一个版本号，它将会创建 `dev-{分支名}` 形式的版本号。例如 `master` 将产生一个 `dev-master` 的版本号。

下面是版本分支名称的一些示例：

- 1.x
- 1.0 (equals 1.0.x)
- 1.1.x

> **注意：** 当你安装一个新的版本时，将会自动从它 `source` 中拉取。
> 详细请查看 [`install`](03-cli.md#install) 命令。

<a name="Aliases"></a>
### 别名

它表示一个包版本的别名。例如，你可以为 `dev-master` 设置别名 `1.0.x-dev`，这样就可以通过 require `1.0.x-dev` 来得到 `dev-master` 版本的包。

详细请查看[“别名”](articles/aliases.md)。

<a name="Lock-file"></a>
## 锁文件

如果你愿意，可以在你的项目中提交 `composer.lock` 文件。他将帮助你的团队始终针对同一个依赖版本进行测试。任何时候，这个锁文件都只对于你的项目产生影响。

如果你不想提交锁文件，并且你正在使用 Git，那么请将它添加到 `.gitignore` 文件中。

<a name="Publishing-to-a-VCS"></a>
## 发布到 VCS（线上版本控制系统）

一旦你有一个包含 `composer.json` 文件的库存储在线上版本控制系统（例如：Git），你的库就可以被 Composer 所安装。在这个例子中，我们将 `acme/hello-world` 库发布在 GitHub 上的 `github.com/username/hello-world` 中。

现在测试这个 `acme/hello-world` 包，我们在本地创建一个新的项目。我们将它命名为 `acme/blog`。此博客将依赖 `acme/hello-world`，而后者又依赖 `monolog/monolog`。我们可以在某处创建一个新的 `blog` 文件夹来完成它，并且需要包含 `composer.json` 文件：

```json
{
    "name": "acme/blog",
    "require": {
        "acme/hello-world": "dev-master"
    }
}
```

在这个例子中 `name` 不是必须的，因为我们并不想将它发布为一个库。在这里为 `composer.json` 文件添加描述。

现在我们需要告诉我们的应用，在哪里可以找到 `hello-world` 的依赖。为此我们需要在 `composer.json` 中添加 `repositories` 来源申明：

```json
{
    "name": "acme/blog",
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/username/hello-world"
        }
    ],
    "require": {
        "acme/hello-world": "dev-master"
    }
}
```

更多关于包的来源是如何工作的，以及还有什么其他的类型可供选择，请查看[资源库](05-repositories.md)。

这就是全部了。你现在可以使用 Composer 的 `install` 命令来安装你的依赖包了！

**小结：** 任何含有 `composer.json` 的 `GIT`、`SVN`、`HG` 存储库，都可以通过 `require` 字段指定“包来源”和“声明依赖”来添加到你的项目中。

<a name="Publishing-to-packagist"></a>
## 发布到 packagist

好的，你现在可以发布你的包了，但你不会希望你的用户每次都这样繁琐的指定包的来源。

你可能注意到了另一件事，我们并没有指定 `monolog/monolog` 的来源。它是怎么工作的？答案是 packagist。

[Packagist](https://packagist.org/) 是 Composer 主要的一个包信息存储库，它默认是启用的。任何在 packagist 上发布的包都可以直接被 Composer 使用。就像 monolog
它被 [发布在 packagist 上](https://packagist.org/packages/monolog/monolog)，我们可以直接使用它，而不必指定任何额外的来源信息。

如果我们想与世界分享我们的 `hello-world`，我们最好将它发布到 packagist 上。这样做是很容易的。

你只需要点击那个大大的 "Submit Package" 按钮并注册。接着提交你库的来源地址，此时 packagist 就开始了抓取。一旦完成，你的包将可以提供给任何人使用。

&larr; [基本用法](01-basic-usage.md) |  [命令行](03-cli.md) &rarr;
