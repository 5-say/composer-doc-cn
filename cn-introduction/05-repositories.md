# 资源库

本章将解释包和库的概念，什么样的存储库是可用的，以及它们如何工作。

## 概述

在此之前，我们看到存在不同类型的资源库，我们需要了解一些基本概念，以理解 Composer 是如何构建于其上的。

### 包

Composer 是一个依赖管理工具。它在本地安装一些资源包。一个包本质上就是一个包含东西的目录。通常情况下它存储 PHP 代码，但在理论上它可以是任何东西。并且它包含一个描述，其中有一个名称和一个版本号，这个名称和版本号用于识别该包。

事实上，在 composer 内部将每一个版本都视为一个单独的包。尽管在你使用 composer 时这种区别无关紧要，但当你想改变它时，这就显得至关重要。

除了名称和版本号，还存放了有用的元数据。与安装关系最密切的是 source 信息，它申明了在哪里可以获得资源包的内容。包数据指向包内容，并有两种指向方式：dist 和 source。

**Dist:** dist 指向一个存档，该存档是对一个资源包的某个版本的数据进行的打包。通常是已经发行的稳定版本。

**Source:** source 指向一个开发中的源。这通常是一个源代码仓库，例如 git。当你想要对下载下来的资源包进行修改时，可以这样获取。

你可以使用其中任意一个，或者同时使用。这取决于其它的一些因素，比如“user-supplied 选项”和“包的稳定性”，前者将会被优先考虑。

### 资源库

一个资源库是一个包的来源。它是一个 packages/versions 的列表。Composer 将查看所有你定义的 repositories 以找到你项目需要的资源包。

默认情况下已经将 Packagist.org 注册到 Composer。你可以在 `composer.json` 中申明更多的资源库，把它们加入你的项目中。

资源库的定义仅可用于“root 包”，而在你依赖的包中定义的资源库将不会被加载。如果你想了解其中的原因，请阅读 [FAQ entry](faqs/why-can't-composer-load-repositories-recursively.md)。

## Types

### Composer

主资源库的类型为 `composer`。它使用一个单一的 `packages.json` 文件，包含了所有的资源包元数据。

这也是 packagist.org 所使用的资源类型。要引用一个 `composer` 资源库，只需要提供一个存放 `packages.json` 文件的 **目录路径**。比如要引用 `packagist.org` 下的 `/packages.json`，它的 URL 就应该是 `packagist.org`。而 `example.org/packages.json` 的 URL 应该是 `example.org`。

#### packages

唯一必须的字段是 `packages`。它的 JSON 结构如下：

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

`@composer.json` 标记将会从此包的指定版本中读取 `composer.json` 的内容，其内至少应包含以下信息：

* name
* version
* dist or source

这是一个最简单的包定义：

    {
        "name": "smarty/smarty",
        "version": "3.1.7",
        "dist": {
            "url": "http://www.smarty.net/files/Smarty-3.1.7.zip",
            "type": "zip"
        }
    }

它还可以包含任何在 [架构](04-schema.md) 中介绍的字段。

#### notify-batch

`notify-batch` 字段允许你指定一个 URL，它将会在用户安装每一个包时被调用。该 URL 可以是（与其资源库相同域名的）绝对路径或者一个完整的 URL 地址。

例如使用下面的值：

    {
        "notify-batch": "/downloads/"
    }

对于 `example.org/packages.json` 包含的 `monolog/monolog` 包，它将会发送一个 `POST` 请求到 `example.org/downloads/`，使用下面的 JSON request body：

    {
        "downloads": [
            {"name": "monolog/monolog", "version": "1.2.1.0"},
        ]
    }

`version` 字段将包含标准化的版本号。

`notify-batch` 字段是可选的。

#### includes

对于较大的资源库，可以拆分 `packages.json` 为多个文件。`includes` 字段允许你引用这些额外的文件。

例：

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

文件的 SHA-1 码允许它被缓存，仅在 hash 值改变时重新请求。

此字段是可选的。你也许并不需要它来自定义存储库。

#### provider-includes and providers-url

的对于非常大的资源库，像 packagist.org 使用 so-called provider 文件是首选方法。`provider-includes` 字段允许你设置一个列表，来申明这个资源库提供的包名称。在这种情况下文件的哈希算法必须使用 sha256。

`providers-url` 描述了如何在服务器上找到这些 provider 文件。它是以资源库的根目录为起点的绝对路径。

例：

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

这些文件包含资源包的名称以及哈希值，以验证文件的完整性，例如：

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

上述文件申明了 `acme/foo` 和 `acme/bar` 可以在这个资源库找到，通过加载由 `providers-url` 引用的文件，替换 `%name%` 为包名并且替换 `%hash%` 为 sha256 的值。这些文件本身只包含上文提到的 [packages](#packages) 的定义。

这些字段是可选的。你也许并不需要它们来自定义存储库。

#### stream options

`packages.json` 文件是用一个 PHP 流加载的。你可以使用 `options` 参数来设定额外的流信息。你可以设置任何有效的PHP 流上下文选项。更多相关信息请查看 [Context options and parameters](http://php.net/manual/en/context.php)。

### VCS

VCS 表示版本控制系统。这包括像 git、svn 或 hg 这样的版本管理系统。Composer 有一个资源类型可以从这些系统安装软件包。

#### 从 VCS 资源库加载一个包

这里有几个用例。最常见的是维护自己 fork 的第三方库。如果你在项目中使用某些库，并且你决定改变这些库内的某些东西，你会希望你项目中使用的是你自己的修正版本。如果这个库是在 GitHub 上（这种情况经常出现），你可以简单的 fork 它并 push 你的变更到这个 fork 里。在这之后你更新项目的 `composer.json` 文件，添加你的 fork 作为一个资源库，变更版本约束来指向你的自定义分支。关于版本约束的命名约定请查看 [库（资源包）](02-libraries.md)。

例如，假设你 fork 了 monolog，在 `bugfix` 分支修复了一个 bug：

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

当你运行 `php composer.phar update` 时，你应该得到你修改的版本，而不是 packagist.org 上的 `monolog/monolog`。

注意，你不应该对包进行重命名，除非你真的打算摆脱原来的包，并长期的使用你自己的 fork。这样 Composer 就会正确获取你的包了。如果你确定要重命名这个包，你应该在默认分支（通常是 master 分支）上操作，而不是特性分支，因为包的名字取自默认分支。

如果其它包依赖你 fork 的这个分支，可能要对它做版本号的行内别名设置，才能够准确的识别版本约束。更多相关信息请查看 [别名](articles/aliases.md)。

#### 使用私有资源库

完全相同的解决方案，也可以让你使用你 GitHub 和 BitBucket 上的私人代码库进行工作：

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

唯一的要求是为一个 git 客户端安装 SSH 秘钥。

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

#### Subversion 选项

由于 Subversion 没有原生的分支和标签的概念，Composer 假设在默认情况下该代码位于 `$url/trunk`、`$url/branches` 和 `$url/tags` 内。如果你的存储库使用了不同的布局，你可以更改这些值。例如，如果你使用大写的名称，你可以像这样配置资源库：

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

如果你的存储库目录中没有任何分支或标签文件夹，你可以将 `branches-path` 或 `tags-path` 设置为 `false`。

如果是一个位于子目录的包，例如， `/trunk/foo/bar/composer.json` 和 `/tags/1.0/foo/bar/composer.json`，那么你可以让 composer 通过 `"package-path"` 选项设置的子目录进行访问，在这个例子中可以将其设置为 `"package-path": "foo/bar/"`。

### PEAR

`pear` 类型资源库，使得从任何 PEAR 渠道安装资源包成为可能。Composer 将为所有此类型的包增加前缀（类似于 `pear-{渠道名称}/`）以避免冲突。而在之后使用别名时也增加前缀（如 `pear-{渠道别名}/`）。

例如使用 `pear2.php.net`：

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

在这种情况下渠道的简称（别名）是 `pear2`，因此 `PEAR2_HTTP_Request` 包的名称应该写作 `pear-pear2/PEAR2_HTTP_Request`。

> **注意：** `pear` 类型的资源库对每个 requires 都要做完整的请求，因此可能大大降低安装速度。

#### 自定义供应商别名

It is possible to alias PEAR channel packages with a custom vendor name.

例：

假设你有一个私人 PEAR 库，并希望使用 Composer 从 VCS 集成依赖。你的 PEAR 库包含以下资源包：

 * `BasePackage`
 * `IntermediatePackage`, which depends on `BasePackage`
 * `TopLevelPackage1` and `TopLevelPackage2` which both depend on `IntermediatePackage`

如果没有一个供应商别名，Composer 将使用 PEAR 渠道名称作为包名的一部分：

 * `pear-pear.foobar.repo/BasePackage`
 * `pear-pear.foobar.repo/IntermediatePackage`
 * `pear-pear.foobar.repo/TopLevelPackage1`
 * `pear-pear.foobar.repo/TopLevelPackage2`

假设之后的某个时间，你希望将你的 PEAR 包迁移，使用 Composer 资源库和命名方案，并且采用 `foobar` 作为供应商名称。使用 PEAR 包的项目将不会看到更新的资源包，因为它们有不同的供应商名称（`foobar/IntermediatePackage` 与 `pear-pear.foobar.repo/IntermediatePackage`）。

你可以通过从一开始就为 PEAR 资源库指定 `vendor-alias` 来避免这种情况的发生，以得到一个不会过时的包名。

为了说明这一点，下面的例子会从你的 PEAR 资源库中得到 `BasePackage`、`TopLevelPackage1` 和 `TopLevelPackage2` 资源包，并从 Github 资源库中获取 `IntermediatePackage` 资源包：

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

### Package

If you want to use a project that does not support composer through any of the
means above, you still can define the package yourself by using a `package`
repository.

Basically, you define the same information that is included in the `composer`
repository's `packages.json`, but only for a single package. Again, the
minimum required fields are `name`, `version`, and either of `dist` or
`source`.

Here is an example for the smarty template engine:

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

Typically you would leave the source part off, as you don't really need it.

> **Note**: This repository type has a few limitations and should be avoided
> whenever possible:
>
> - Composer will not update the package unless you change the `version` field.
> - Composer will not update the commit references, so if you use `master` as
>   reference you will have to delete the package to force an update, and will
>   have to deal with an unstable lock file.

## Hosting your own

While you will probably want to put your packages on packagist most of the time,
there are some use cases for hosting your own repository.

* **Private company packages:** If you are part of a company that uses composer
  for their packages internally, you might want to keep those packages private.

* **Separate ecosystem:** If you have a project which has its own ecosystem,
  and the packages aren't really reusable by the greater PHP community, you
  might want to keep them separate to packagist. An example of this would be
  wordpress plugins.

For hosting your own packages, a native `composer` type of repository is 
recommended, which provides the best performance.

There are a few tools that can help you create a `composer` repository.

### Packagist

The underlying application used by packagist is open source. This means that you
can just install your own copy of packagist, re-brand, and use it. It's really
quite straight-forward to do. However due to its size and complexity, for most
small and medium sized companies willing to track a few packages will be better
off using Satis.

Packagist is a Symfony2 application, and it is [available on
GitHub](https://github.com/composer/packagist). It uses composer internally and
acts as a proxy between VCS repositories and the composer users. It holds a list
of all VCS packages, periodically re-crawls them, and exposes them as a composer
repository.

To set your own copy, simply follow the instructions from the [packagist
github repository](https://github.com/composer/packagist).

### Satis

Satis is a static `composer` repository generator. It is a bit like an ultra-
lightweight, static file-based version of packagist.

You give it a `composer.json` containing repositories, typically VCS and
package repository definitions. It will fetch all the packages that are
`require`d and dump a `packages.json` that is your `composer` repository.

Check [the satis GitHub repository](https://github.com/composer/satis) and
the [Satis article](articles/handling-private-packages-with-satis.md) for more
information.

### Artifact

There are some cases, when there is no ability to have one of the previously
mentioned repository types online, even the VCS one. Typical example could be
cross-organisation library exchange through built artifacts. Of course, most
of the times they are private. To simplify maintenance, one can simply use a
repository of type `artifact` with a folder containing ZIP archives of those
private packages:

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

Each zip artifact is just a ZIP archive with `composer.json` in root folder:

    $ unzip -l acme-corp-parser-10.3.5.zip
    composer.json
    ...

If there are two archives with different versions of a package, they are both
imported. When an archive with a newer version is added in the artifact folder
and you run `update`, that version will be imported as well and Composer will
update to the latest version.

## Disabling Packagist

You can disable the default Packagist repository by adding this to your
`composer.json`:

    {
        "repositories": [
            {
                "packagist": false
            }
        ]
    }


&larr; [Schema](04-schema.md)  |  [Community](06-community.md) &rarr;
