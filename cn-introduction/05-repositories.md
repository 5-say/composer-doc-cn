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

`@composer.json` 标记将会从 作为最低版本： would be the contents of the `composer.json` from that package version including as a minimum:

* name
* version
* dist or source

这是一个最小的包定义：

    {
        "name": "smarty/smarty",
        "version": "3.1.7",
        "dist": {
            "url": "http://www.smarty.net/files/Smarty-3.1.7.zip",
            "type": "zip"
        }
    }

它可以包含任何在 [架构](04-schema.md) 中介绍的字段。

#### notify-batch

The `notify-batch` field allows you to specify an URL that will be called
every time a user installs a package. The URL can be either an absolute path
(that will use the same domain as the repository) or a fully qualified URL.

An example value:

    {
        "notify-batch": "/downloads/"
    }

For `example.org/packages.json` containing a `monolog/monolog` package, this
would send a `POST` request to `example.org/downloads/` with following
JSON request body:

    {
        "downloads": [
            {"name": "monolog/monolog", "version": "1.2.1.0"},
        ]
    }

The version field will contain the normalized representation of the version
number.

This field is optional.

#### includes

For larger repositories it is possible to split the `packages.json` into
multiple files. The `includes` field allows you to reference these additional
files.

An example:

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

The SHA-1 sum of the file allows it to be cached and only re-requested if the
hash changed.

This field is optional. You probably don't need it for your own custom
repository.

#### provider-includes and providers-url

For very large repositories like packagist.org using the so-called provider
files is the preferred method. The `provider-includes` field allows you to
list a set of files that list package names provided by this repository. The
hash should be a sha256 of the files in this case.

The `providers-url` describes how provider files are found on the server. It
is an absolute path from the repository root.

An example:

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

Those files contain lists of package names and hashes to verify the file
integrity, for example:

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

The file above declares that acme/foo and acme/bar can be found in this
repository, by loading the file referenced by `providers-url`, replacing
`%name%` by the package name and `%hash%` by the sha256 field. Those files
themselves just contain package definitions as described [above](#packages).

This field is optional. You probably don't need it for your own custom
repository.

#### stream options

The `packages.json` file is loaded using a PHP stream. You can set extra options
on that stream using the `options` parameter. You can set any valid PHP stream
context option. See [Context options and parameters](http://php.net/manual/en/context.php)
for more information.

### VCS

VCS stands for version control system. This includes versioning systems like
git, svn or hg. Composer has a repository type for installing packages from
these systems.

#### Loading a package from a VCS repository

There are a few use cases for this. The most common one is maintaining your
own fork of a third party library. If you are using a certain library for your
project and you decide to change something in the library, you will want your
project to use the patched version. If the library is on GitHub (this is the
case most of the time), you can simply fork it there and push your changes to
your fork. After that you update the project's `composer.json`. All you have
to do is add your fork as a repository and update the version constraint to
point to your custom branch. For version constraint naming conventions see
[Libraries](02-libraries.md) for more information.

Example assuming you patched monolog to fix a bug in the `bugfix` branch:

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

When you run `php composer.phar update`, you should get your modified version
of `monolog/monolog` instead of the one from packagist.

Note that you should not rename the package unless you really intend to fork
it in the long term, and completely move away from the original package.
Composer will correctly pick your package over the original one since the
custom repository has priority over packagist. If you want to rename the
package, you should do so in the default (often master) branch and not in a
feature branch, since the package name is taken from the default branch.

If other dependencies rely on the package you forked, it is possible to
inline-alias it so that it matches a constraint that it otherwise would not.
For more information [see the aliases article](articles/aliases.md).

#### Using private repositories

Exactly the same solution allows you to work with your private repositories at
GitHub and BitBucket:

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

The only requirement is the installation of SSH keys for a git client.

#### Git alternatives

Git is not the only version control system supported by the VCS repository.
The following are supported:

* **Git:** [git-scm.com](http://git-scm.com)
* **Subversion:** [subversion.apache.org](http://subversion.apache.org)
* **Mercurial:** [mercurial.selenic.com](http://mercurial.selenic.com)

To get packages from these systems you need to have their respective clients
installed. That can be inconvenient. And for this reason there is special
support for GitHub and BitBucket that use the APIs provided by these sites, to
fetch the packages without having to install the version control system. The
VCS repository provides `dist`s for them that fetch the packages as zips.

* **GitHub:** [github.com](https://github.com) (Git)
* **BitBucket:** [bitbucket.org](https://bitbucket.org) (Git and Mercurial)

The VCS driver to be used is detected automatically based on the URL. However,
should you need to specify one for whatever reason, you can use `git`, `svn` or
`hg` as the repository type instead of `vcs`.

#### Subversion Options

Since Subversion has no native concept of branches and tags, Composer assumes
by default that code is located in `$url/trunk`, `$url/branches` and
`$url/tags`. If your repository has a different layout you can change those
values. For example if you used capitalized names you could configure the
repository like this:

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

If you have no branches or tags directory you can disable them entirely by
setting the `branches-path` or `tags-path` to `false`.

If the package is in a sub-directory, e.g. `/trunk/foo/bar/composer.json` and
`/tags/1.0/foo/bar/composer.json`, then you can make composer access it by
setting the `"package-path"` option to the sub-directory, in this example it
would be `"package-path": "foo/bar/"`.

### PEAR

It is possible to install packages from any PEAR channel by using the `pear`
repository. Composer will prefix all package names with `pear-{channelName}/` to
avoid conflicts. All packages are also aliased with prefix `pear-{channelAlias}/`

Example using `pear2.php.net`:

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

In this case the short name of the channel is `pear2`, so the
`PEAR2_HTTP_Request` package name becomes `pear-pear2/PEAR2_HTTP_Request`.

> **Note:** The `pear` repository requires doing quite a few requests per
> package, so this may considerably slow down the installation process.

#### Custom vendor alias

It is possible to alias PEAR channel packages with a custom vendor name.

Example:

Suppose you have a private PEAR repository and wish to use Composer to
incorporate dependencies from a VCS. Your PEAR repository contains the
following packages:

 * `BasePackage`
 * `IntermediatePackage`, which depends on `BasePackage`
 * `TopLevelPackage1` and `TopLevelPackage2` which both depend on `IntermediatePackage`

Without a vendor alias, Composer will use the PEAR channel name as the
vendor portion of the package name:

 * `pear-pear.foobar.repo/BasePackage`
 * `pear-pear.foobar.repo/IntermediatePackage`
 * `pear-pear.foobar.repo/TopLevelPackage1`
 * `pear-pear.foobar.repo/TopLevelPackage2`

Suppose at a later time you wish to migrate your PEAR packages to a
Composer repository and naming scheme, and adopt the vendor name of `foobar`.
Projects using your PEAR packages would not see the updated packages, since
they have a different vendor name (`foobar/IntermediatePackage` vs
`pear-pear.foobar.repo/IntermediatePackage`).

By specifying `vendor-alias` for the PEAR repository from the start, you can
avoid this scenario and future-proof your package names.

To illustrate, the following example would get the `BasePackage`,
`TopLevelPackage1`, and `TopLevelPackage2` packages from your PEAR repository
and `IntermediatePackage` from a Github repository:

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
