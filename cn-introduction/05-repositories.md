<a name="Repositories"></a>
# 资源库

本章将解释包和库的概念，什么样的存储库是可用的，以及它们如何工作。

---

- [资源库](#Repositories)
  - [概述](#Concepts)
    - [包](#Package)
    - [资源库](#Repository)
  - [Types](#Types)
    - [Composer](#Composer)
      - [packages](#packages)
      - [notify-batch](#notify-batch)
      - [includes](#includes)
      - [provider-includes and providers-url](#provider-includes)
      - [stream options](#stream-options)
    - [VCS](#VCS)
      - [从 VCS 资源库加载一个包](#from-a-VCS)
      - [使用私有资源库](#Using-private)
      - [Git 的备选方案](#Git-alternatives)
      - [Subversion 选项](#Subversion-Options)
    - [PEAR](#PEAR)
      - [自定义供应商别名](#Custom-vendor-alias)
    - [Package](#Package)
  - [Hosting your own](#Hosting-your-own)
    - [Packagist](#Packagist)
    - [Satis](#Satis)
    - [Artifact](#Artifact)
  - [禁用 Packagist](#Disabling-Packagist)

---

<a name="Concepts"></a>
## 概述

在此之前，我们看到存在不同类型的资源库，我们需要了解一些基本概念，以理解 Composer 是如何构建于其上的。

<a name="Package"></a>
### 包

Composer 是一个依赖管理工具。它在本地安装一些资源包。一个包本质上就是一个包含东西的目录。通常情况下它存储 PHP 代码，但在理论上它可以是任何东西。并且它包含一个描述，其中有一个名称和一个版本号，这个名称和版本号用于识别该包。

事实上，在 composer 内部将每一个版本都视为一个单独的包。尽管在你使用 composer 时这种区别无关紧要，但当你想改变它时，这就显得至关重要。

除了名称和版本号，还存放了有用的元数据。与安装关系最密切的是 source 信息，它申明了在哪里可以获得资源包的内容。包数据指向包内容，并有两种指向方式：dist 和 source。

**Dist:** dist 指向一个存档，该存档是对一个资源包的某个版本的数据进行的打包。通常是已经发行的稳定版本。

**Source:** source 指向一个开发中的源。这通常是一个源代码仓库，例如 git。当你想要对下载下来的资源包进行修改时，可以这样获取。

你可以使用其中任意一个，或者同时使用。这取决于其它的一些因素，比如“user-supplied 选项”和“包的稳定性”，前者将会被优先考虑。

<a name="Repository"></a>
### 资源库

一个资源库是一个包的来源。它是一个 packages/versions 的列表。Composer 将查看所有你定义的 repositories 以找到你项目需要的资源包。

默认情况下已经将 Packagist.org 注册到 Composer。你可以在 `composer.json` 中申明更多的资源库，把它们加入你的项目中。

资源库的定义仅可用于“root 包”，而在你依赖的包中定义的资源库将不会被加载。如果你想了解其中的原因，请阅读 [FAQ entry](faqs/why-can't-composer-load-repositories-recursively.md)。

<a name="Types"></a>
## Types

<a name="Composer"></a>
### Composer

主资源库的类型为 `composer`。它使用一个单一的 `packages.json` 文件，包含了所有的资源包元数据。

这也是 packagist.org 所使用的资源类型。要引用一个 `composer` 资源库，只需要提供一个存放 `packages.json` 文件的 **目录路径**。比如要引用 `packagist.org` 下的 `/packages.json`，它的 URL 就应该是 `packagist.org`。而 `example.org/packages.json` 的 URL 应该是 `example.org`。

<a name="packages"></a>
#### packages

唯一必须的字段是 `packages`。它的 JSON 结构如下：

```json
{
    "packages": {
        "vendor/package-name": {
            "dev-master": { @composer.json },
            "1.0.x-dev": { @composer.json },
            "0.0.1": { @composer.json },
            "1.0.0": { @composer.json }
        }
    }
}
```

`@composer.json` 标记将会从此包的指定版本中读取 `composer.json` 的内容，其内至少应包含以下信息：

* name
* version
* dist or source

这是一个最简单的包定义：

```json
{
    "name": "smarty/smarty",
    "version": "3.1.7",
    "dist": {
        "url": "http://www.smarty.net/files/Smarty-3.1.7.zip",
        "type": "zip"
    }
}
```

它还可以包含任何在 [composer.json 架构](04-schema.md) 中介绍的字段。

<a name="notify-batch"></a>
#### notify-batch

`notify-batch` 字段允许你指定一个 URL，它将会在用户安装每一个包时被调用。该 URL 可以是（与其资源库相同域名的）绝对路径或者一个完整的 URL 地址。

例如使用下面的值：

```json
{
    "notify-batch": "/downloads/"
}
```

对于 `example.org/packages.json` 包含的 `monolog/monolog` 包，它将会发送一个 `POST` 请求到 `example.org/downloads/`，使用下面的 JSON request body：

```json
{
    "downloads": [
        {"name": "monolog/monolog", "version": "1.2.1.0"},
    ]
}
```

`version` 字段将包含标准化的版本号。

`notify-batch` 字段是可选的。

<a name="includes"></a>
#### includes

对于较大的资源库，可以拆分 `packages.json` 为多个文件。`includes` 字段允许你引用这些额外的文件。

实例：

```json
{
    "includes": {
        "packages-2011.json": {
            "sha1": "525a85fb37edd1ad71040d429928c2c0edec9d17"
        },
        "packages-2012-01.json": {
            "sha1": "897cde726f8a3918faf27c803b336da223d400dd"
        },
        "packages-2012-02.json": {
            "sha1": "26f911ad717da26bbcac3f8f435280d13917efa5"
        }
    }
}
```

文件的 SHA-1 码允许它被缓存，仅在 hash 值改变时重新请求。

此字段是可选的。你也许并不需要它来自定义存储库。

<a name="provider-includes"></a>
#### provider-includes and providers-url

的对于非常大的资源库，像 packagist.org 使用 so-called provider 文件是首选方法。`provider-includes` 字段允许你设置一个列表，来申明这个资源库提供的包名称。在这种情况下文件的哈希算法必须使用 sha256。

`providers-url` 描述了如何在服务器上找到这些 provider 文件。它是以资源库的根目录为起点的绝对路径。

实例：

```json
{
    "provider-includes": {
        "providers-a.json": {
            "sha256": "f5b4bc0b354108ef08614e569c1ed01a2782e67641744864a74e788982886f4c"
        },
        "providers-b.json": {
            "sha256": "b38372163fac0573053536f5b8ef11b86f804ea8b016d239e706191203f6efac"
        }
    },
    "providers-url": "/p/%package%$%hash%.json"
}
```

这些文件包含资源包的名称以及哈希值，以验证文件的完整性，例如：

```json
{
    "providers": {
        "acme/foo": {
            "sha256": "38968de1305c2e17f4de33aea164515bc787c42c7e2d6e25948539a14268bb82"
        },
        "acme/bar": {
            "sha256": "4dd24c930bd6e1103251306d6336ac813b563a220d9ca14f4743c032fb047233"
        }
    }
}
```

上述文件申明了 `acme/foo` 和 `acme/bar` 可以在这个资源库找到，通过加载由 `providers-url` 引用的文件，替换 `%package%` 为包名并且替换 `%hash%` 为 sha256 的值。这些文件本身只包含上文提到的 [packages](#packages) 的定义。

这些字段是可选的。你也许并不需要它们来自定义存储库。

<a name="stream-options"></a>
#### stream options

`packages.json` 文件是用一个 PHP 流加载的。你可以使用 `options` 参数来设定额外的流信息。你可以设置任何有效的PHP 流上下文选项。更多相关信息请查看 [Context options and parameters](http://php.net/manual/en/context.php)。

<a name="VCS"></a>
### VCS

VCS 表示版本控制系统。这包括像 git、svn 或 hg 这样的版本管理系统。Composer 有一个资源类型可以从这些系统安装软件包。

<a name="from-a-VCS"></a>
#### 从 VCS 资源库加载一个包

这里有几个用例。最常见的是维护自己 fork 的第三方库。如果你在项目中使用某些库，并且你决定改变这些库内的某些东西，你会希望你项目中使用的是你自己的修正版本。如果这个库是在 GitHub 上（这种情况经常出现），你可以简单的 fork 它并 push 你的变更到这个 fork 里。在这之后你更新项目的 `composer.json` 文件，添加你的 fork 作为一个资源库，变更版本约束来指向你的自定义分支。关于版本约束的命名约定请查看 [库（资源包）](02-libraries.md)。

例如，假设你 fork 了 monolog，在 `bugfix` 分支修复了一个 bug：

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/igorw/monolog"
        }
    ],
    "require": {
        "monolog/monolog": "dev-bugfix"
    }
}
```

当你运行 `php composer.phar update` 时，你应该得到你修改的版本，而不是 packagist.org 上的 `monolog/monolog`。

注意，你不应该对包进行重命名，除非你真的打算摆脱原来的包，并长期的使用你自己的 fork。这样 Composer 就会正确获取你的包了。如果你确定要重命名这个包，你应该在默认分支（通常是 master 分支）上操作，而不是特性分支，因为包的名字取自默认分支。

如果其它包依赖你 fork 的这个分支，可能要对它做版本号的行内别名设置，才能够准确的识别版本约束。更多相关信息请查看 [别名](articles/aliases.md)。

<a name="Using-private"></a>
#### 使用私有资源库

完全相同的解决方案，也可以让你使用你 GitHub 和 BitBucket 上的私人代码库进行工作：

```json
{
    "require": {
        "vendor/my-private-repo": "dev-master"
    },
    "repositories": [
        {
            "type": "vcs",
            "url":  "git@bitbucket.org:vendor/my-private-repo.git"
        }
    ]
}
```

唯一的要求是为一个 git 客户端安装 SSH 秘钥。

<a name="Git-alternatives"></a>
#### Git 的备选方案

Git 并不是 VCS 资源库唯一支持的版本管理系统。

以下几种都是被支持的：

* **Git:** [git-scm.com](http://git-scm.com)
* **Subversion:** [subversion.apache.org](http://subversion.apache.org)
* **Mercurial:** [mercurial.selenic.com](http://mercurial.selenic.com)

为了从这些系统获取资源包，你必须安装对应的客户端，这可能是不方便的。基于这个原因，这里提供了 GitHub 和 BitBucket 的 API 的特殊支持，以便在无需安装版本控制系统的情况下获取资源包。在 VCS 资源库提供的 `dist` 中获取 zip 存档。

* **GitHub:** [github.com](https://github.com) (Git)
* **BitBucket:** [bitbucket.org](https://bitbucket.org) (Git and Mercurial)

VCS 驱动将基于 URL 自动检测版本库类型。但如果可能，你需要明确的指定一个 `git`、`svn` 或 `hg` 作为资源库类型，而不是 `vcs`。

If you set the `no-api` key to `true` on a github repository it will clone the
repository as it would with any other git repository instead of using the
GitHub API. But unlike using the `git` driver directly, composer will still
attempt to use github's zip files.

<a name="Subversion-Options"></a>
#### Subversion 选项

由于 Subversion 没有原生的分支和标签的概念，Composer 假设在默认情况下该代码位于 `$url/trunk`、`$url/branches` 和 `$url/tags` 内。如果你的存储库使用了不同的布局，你可以更改这些值。例如，如果你使用大写的名称，你可以像这样配置资源库：

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "http://svn.example.org/projectA/",
            "trunk-path": "Trunk",
            "branches-path": "Branches",
            "tags-path": "Tags"
        }
    ]
}
```

如果你的存储库目录中没有任何分支或标签文件夹，你可以将 `branches-path` 或 `tags-path` 设置为 `false`。

如果是一个位于子目录的包，例如， `/trunk/foo/bar/composer.json` 和 `/tags/1.0/foo/bar/composer.json`，那么你可以让 composer 通过 `"package-path"` 选项设置的子目录进行访问，在这个例子中可以将其设置为 `"package-path": "foo/bar/"`。

<a name="PEAR"></a>
### PEAR

`pear` 类型资源库，使得从任何 PEAR 渠道安装资源包成为可能。Composer 将为所有此类型的包增加前缀（类似于 `pear-{渠道名称}/`）以避免冲突。而在之后使用别名时也增加前缀（如 `pear-{渠道别名}/`）。

例如使用 `pear2.php.net`：

```json
{
    "repositories": [
        {
            "type": "pear",
            "url": "http://pear2.php.net"
        }
    ],
    "require": {
        "pear-pear2.php.net/PEAR2_Text_Markdown": "*",
        "pear-pear2/PEAR2_HTTP_Request": "*"
    }
}
```

在这种情况下渠道的简称（别名）是 `pear2`，因此 `PEAR2_HTTP_Request` 包的名称应该写作 `pear-pear2/PEAR2_HTTP_Request`。

> **注意：** `pear` 类型的资源库对每个 requires 都要做完整的请求，因此可能大大降低安装速度。

<a name="Custom-vendor-alias"></a>
#### 自定义供应商别名

通过自定义供应商名称，对 PEAR 渠道包进行别名是允许的。

例：

假设你有一个私人 PEAR 库，并希望使用 Composer 从 VCS 集成依赖。你的 PEAR 库包含以下资源包：

 * `BasePackage`。
 * `IntermediatePackage` 依赖于 `BasePackage`。
 * `TopLevelPackage1` 和 `TopLevelPackage2` 都依赖于 `IntermediatePackage`。

如果没有一个供应商别名，Composer 将使用 PEAR 渠道名称作为包名的一部分：

 * `pear-pear.foobar.repo/BasePackage`
 * `pear-pear.foobar.repo/IntermediatePackage`
 * `pear-pear.foobar.repo/TopLevelPackage1`
 * `pear-pear.foobar.repo/TopLevelPackage2`

假设之后的某个时间，你希望将你的 PEAR 包迁移，使用 Composer 资源库和命名方案，并且采用 `foobar` 作为供应商名称。这样之前使用 PEAR 包的项目将不会看到更新的资源包，因为它们有不同的供应商名称（`foobar/IntermediatePackage` 与 `pear-pear.foobar.repo/IntermediatePackage`）。

你可以通过从一开始就为 PEAR 资源库指定 `vendor-alias` 来避免这种情况的发生，以得到一个不会过时的包名。

为了说明这一点，下面的例子会从你的 PEAR 资源库中得到 `BasePackage`、`TopLevelPackage1` 和 `TopLevelPackage2` 资源包，并从 Github 资源库中获取 `IntermediatePackage` 资源包：

```json
{
    "repositories": [
        {
            "type": "git",
            "url": "https://github.com/foobar/intermediate.git"
        },
        {
            "type": "pear",
            "url": "http://pear.foobar.repo",
            "vendor-alias": "foobar"
        }
    ],
    "require": {
        "foobar/TopLevelPackage1": "*",
        "foobar/TopLevelPackage2": "*"
    }
}
```

<a name="Package"></a>
### Package

如果你想使用一个项目，它无法通过上述任何一种方式支持 composer，你仍然可以使用 `package` 类型定义资源库。

基本上，你可以定义与 `packages.json` 中 `composer` 类型资源库相同的信息，但需要为每个这样的资源包分别定义。同样，至少应该包含以下信息：`name`、`version`、（`dist` 或 `source`）。

这是一个 smarty 模板引擎的例子：

```json
{
    "repositories": [
        {
            "type": "package",
            "package": {
                "name": "smarty/smarty",
                "version": "3.1.7",
                "dist": {
                    "url": "http://www.smarty.net/files/Smarty-3.1.7.zip",
                    "type": "zip"
                },
                "source": {
                    "url": "http://smarty-php.googlecode.com/svn/",
                    "type": "svn",
                    "reference": "tags/Smarty_3_1_7/distribution/"
                },
                "autoload": {
                    "classmap": ["libs/"]
                }
            }
        }
    ],
    "require": {
        "smarty/smarty": "3.1.*"
    }
}
```

通常你不需要去定义 `source`，因为你并不是真的需要它。

> **注意：** 该资源库类型存在以下限制，因此应尽可能避免使用：
>
> - Composer 将不会更新资源包，除非你修改了 `version` 字段。
> - Composer 将不会更新 commit references，因此如果你使用 `master` reference，将不得不删除该程序包以强制更新，并且将不得不面对一个不稳定的 lock 文件。

<a name="Hosting-your-own"></a>
## Hosting your own

尽管大部分的时间，你大概都会把资源包放在 packagist.org 上，但这里还将告诉你一些用例，以便你可以自行托管资源库。

* **Private company packages:** 如果你是一个公司的职员，对公司内部的资源包使用 composer，你可能会想让这些包保持私有的状态。

* **Separate ecosystem:** 如果你的项目有自己的生态系统，并且自己的资源包不需要被其它项目所复用，你可能会想将它们从 packagist.org 上分离出来。其中一个例子就是 wordpress 的插件。

对于自行托管的软件包，建议使用 `composer` 类型资源库设置，它将提供最佳的性能。

这里有一些工具，可以帮助你创建 `composer` 类型的资源库。

<a name="Packagist"></a>
### Packagist

packagist 的底层是开源的。这意味着你可以只安装你自己拷贝的 packagist，改造并使用它。这真的是很直接简单的事情。然而，由于其规模和复杂性，对于大多数中小型企业还是建议使用 Satis。

Packagist 是一个 Symfony2 应用程序，并且托管在 GitHub 上 [github.com/composer/packagist](https://github.com/composer/packagist)。它内部使用了 composer 并作为 VCS 资源库与 composer 用户之间的代理。它拥有所有 VCS 资源包的列表，定期重新抓取它们，并将其作为一个 composer 资源库。

要设置你的副本，只需要按照 [github.com/composer/packagist](https://github.com/composer/packagist) 的说明进行操作。

<a name="Satis"></a>
### Satis

Satis 是一个静态的 `composer` 资源库生成器。它像是一个超轻量级的、基于静态文件的 packagist 版本。

你给它一个包含 `composer.json` 的存储库，定义好 VCS 和 资源库。它会获取所有你列出的包，并打印 `packages.json` 文件，作为 `composer` 类型的资源库。

更多详细信息请查看 [github.com/composer/satis](https://github.com/composer/satis) 和 [Satis article](articles/handling-private-packages-with-satis.md)。

<a name="Artifact"></a>
### Artifact

在某些情况下，或许没有能力拥有之前提到的任何一种线上资源库。Typical example could be cross-organisation library exchange through built artifacts。当然大部分的时间他们都是私有的。为了简化维护，可以简单的使用 `artifact` 资源库类型，来引用一个包含那些私有包的 ZIP 存档的文件夹：

```json
{
    "repositories": [
        {
            "type": "artifact",
            "url": "path/to/directory/with/zips/"
        }
    ],
    "require": {
        "private-vendor-one/core": "15.6.2",
        "private-vendor-two/connectivity": "*",
        "acme-corp/parser": "10.3.5"
    }
}
```

每个 zip artifact 都只是一个 ZIP 存档，放置在 `composer.json` 所在的根目录：

```sh
unzip -l acme-corp-parser-10.3.5.zip

composer.json
...
```

如果有两个不同版本的资源包，它们都会被导入。当有一个新版本的存档被添加到 artifact 文件夹，并且你运行了 `update` 命令，该版本就会被导入，并且 Composer 将更新到最新版本。

<a name="Disabling-Packagist"></a>
## 禁用 Packagist

你可以在 `composer.json` 中禁用默认的 Packagist 资源库。

```json
{
    "repositories": [
        {
            "packagist": false
        }
    ]
}
```


&larr; [架构](04-schema.md)  |  [社区](06-community.md) &rarr;
