# composer.json

本章将解释所有在 `composer.json` 中可用的字段。

## JSON schema

我们有一个 [JSON schema](http://json-schema.org) 格式化文档，它也可以被用来验证你的 `composer.json` 文件。事实上，它已经被 `validate` 命令所使用。 你可以在这里找到它： [`res/composer-schema.json`](https://github.com/composer/composer/blob/master/res/composer-schema.json).

## Root 包

“root 包”是指由 `composer.json` 定义的在你项目根目录的包。这是 `composer.json` 定义你项目所需的主要条件。（简单的说，你自己的项目就是一个 root 包）

某些字段仅适用于“root 包”上下文。 `config` 字段就是其中一个例子。只有“root 包”可以定义。在依赖包中定义的 `config` 字段将被忽略，这使得 `config` 字段只有“root 包”可用（`root-only`）。

如果你克隆了其中的一个依赖包，直接在其上开始工作，那么它就变成了“root 包”。与作为他人的依赖包时使用相同的 `composer.json` 文件，但上下文发生了变化。

> **注意：** 一个资源包是不是“root 包”，取决于它的上下文。
> 例如：如果你的项目依赖 `monolog` 库，那么你的项目就是“root 包”。
> 但是，如果你从 GitHub 上克隆了 `monolog` 为它修复 bug，
> 那么此时 `monolog` 就是“root 包”。

## 属性

### 包名 `name`

包的名称，它包括供应商名称和项目名称，使用 `/` 分隔。

例如：

* monolog/monolog
* igorw/event-source

对于需要发布的包（库），这是必须填写的。

### 描述 `description`

一个包的简短描述。通常这个最长只有一行。

对于需要发布的包（库），这是必须填写的。

### 版本 `version`

`version` 不是必须的，并且建议忽略（见下文）。

它应该符合 'X.Y.Z' 或者 'vX.Y.Z' 的形式， `-dev`、`-patch`、`-alpha`、`-beta` 或 `-RC` 这些后缀是可选的。在后缀之后也可以再跟上一个数字。

例如：

    1.0.0
    1.0.2
    1.1.0
    0.2.5
    1.0.0-dev
    1.0.0-alpha3
    1.0.0-beta2
    1.0.0-RC5

通常，我们能够从 VCS (git, svn, hg) 的信息推断出包的版本号，在这种情况下，我们建议忽略 `version`。

> **注意：** Packagist 使用 VCS 仓库，
> 因此 `version` 定义的版本号必须是真实准确的。
> 自己手动指定的 `version`，最终有可能在某个时候因为人为错误造成问题。

### 安装类型 `type`

包的安装类型，默认为 `library`。

包的安装类型，用来定义安装逻辑。如果你有一个包需要一个特殊的逻辑，你可以设定一个自定义的类型。这可以是一个 `symfony-bundle`，一个 `wordpress-plugin` 或者一个 `typo3-module`。这些类型都将是具体到某一个项目，而对应的项目将要提供一种能够安装该类型包的安装程序。

composer 原生支持以下4种类型：

- **library:** 这是默认类型，它会简单的将文件复制到 `vendor` 目录。
- **project:** 这表示当前包是一个项目，而不是一个库。例如：框架应用程序 [Symfony standard edition](https://github.com/symfony/symfony-standard)，内容管理系统 [SilverStripe installer](https://github.com/silverstripe/silverstripe-installer) 或者完全成熟的分布式应用程序。使用 IDE 创建一个新的工作区时，这可以为其提供项目列表的初始化。
- **metapackage:** 当一个空的包，包含依赖并且需要触发依赖的安装，这将不会对系统写入额外的文件。因此这种安装类型并不需要一个 dist 或 source。
- **composer-plugin:** 一个安装类型为 `composer-plugin` 的包，它有一个自定义安装类型，可以为其它包提供一个 installler。详细请查看 [自定义安装类型](articles/custom-installers.md)。

仅在你需要一个自定义的安装逻辑时才使用它。建议忽略这个属性，采用默认的 `library`。

### 关键字 `keywords`

该包相关的关键词的数组。这些可用于搜索和过滤。

例如：

    logging
    events
    database
    redis
    templating

可选。

### 项目主页 `homepage`

该项目网站的 URL 地址。

可选。

### 版本发布时间 `time`

版本发布时间。

必须符合 `YYYY-MM-DD` 或 `YYYY-MM-DD HH:MM:SS` 格式。

可选。

### 许可协议 `license`

包的许可协议，它可以是一个字符串或者字符串数组。

最常见的许可协议的推荐写法（按字母排序）：

    Apache-2.0
    BSD-2-Clause
    BSD-3-Clause
    BSD-4-Clause
    GPL-2.0
    GPL-2.0+
    GPL-3.0
    GPL-3.0+
    LGPL-2.1
    LGPL-2.1+
    LGPL-3.0
    LGPL-3.0+
    MIT

可选，但强烈建议提供此内容。更多许可协议的标识符请参见 [SPDX Open Source License Registry](http://www.spdx.org/licenses/)。

对于闭源软件，你必须使用 `"proprietary"` 协议标识符。

一个例子：

    {
        "license": "MIT"
    }

对于一个包，当允许在多个许可协议间进行选择时（"disjunctive license"），这些协议标识符可以被指定为数组。

多协议的一个例子：

    {
        "license": [
           "LGPL-2.1",
           "GPL-3.0+"
        ]
    }

另外它们也可以由 "or" 分隔，并写在括号中：

    {
        "license": "(LGPL-2.1 or GPL-3.0+)"
    }

同样，当有多个许可协议需要结合使用时（"conjunctive license"），它们应该被 "and" 分隔，并写在括号中。

### 作者 `authors`

包的作者。这是一个对象数组。

这个对象必须包含以下属性：

* **name:** 作者的姓名，通常使用真名。
* **email:** 作者的 email 地址。
* **homepage:** 作者主页的 URL 地址。
* **role:** 该作者在此项目中担任的角色（例如：开发人员 或 翻译）。

一个例子：

    {
        "authors": [
            {
                "name": "Nils Adermann",
                "email": "naderman@naderman.de",
                "homepage": "http://www.naderman.de",
                "role": "Developer"
            },
            {
                "name": "Jordi Boggiano",
                "email": "j.boggiano@seld.be",
                "homepage": "http://seld.be",
                "role": "Developer"
            }
        ]
    }

可选，但强烈建议提供此内容。

### 支持 `support`

获取项目支持的向相关信息对象。

这个对象必须包含以下属性：

* **email:** 项目支持 email 地址。
* **issues:** 跟踪问题的 URL 地址。
* **forum:** 论坛地址。
* **wiki:** Wiki 地址。
* **irc:** IRC 聊天频道地址，类似于 irc://server/channel。
* **source:** 网址浏览或下载源。

一个例子：

    {
        "support": {
            "email": "support@example.org",
            "irc": "irc://irc.freenode.org/composer"
        }
    }

可选。

### Package links

下面提到的所有对象，都应该是 包名 到 [版本](01-basic-usage.md#包版本) 的映射对象。

例如：

    {
        "require": {
            "monolog/monolog": "1.0.*"
        }
    }

所有的这些都是可选的。

`require` 和 `require-dev` 还支持稳定性标签（@，仅针对“root 包”）。这允许你在 [minimum-stability](#minimum-stability) 设定的范围外做进一步的限制或扩展。例如：如果你想允许依赖一个不稳定的包，你可以在一个包的版本约束后使用它，或者是一个空的版本约束内使用它。

例子：

    {
        "require": {
            "monolog/monolog": "1.0.*@beta",
            "acme/foo": "@dev"
        }
    }

如果你的依赖之一，有对另一个不稳定包的依赖，你最好在 require 中显示的定义它，并带上足够详细的稳定性标识。

例子：

    {
        "require": {
            "doctrine/doctrine-fixtures-bundle": "dev-master",
            "doctrine/data-fixtures": "@dev"
        }
    }

`require` 和 `require-dev` 还支持对 dev（开发）版本的明确引用（即：版本控制系统中的提交编号 commit），以确保它们被锁定到一个给定的状态，即使你运行了更新命令。你只需要明确一个开发版本号，并带上诸如 `#<ref>` 的标识。

Example:

    {
        "require": {
            "monolog/monolog": "dev-master#2eb0c0978d290a1c45346a1955188929cb4e5db7",
            "acme/foo": "1.0.x-dev#abc123"
        }
    }

> **注意：** 虽然这有时很方便，但不应该长期在你的包中使用，因为它有一个技术上的限制。
> composer.json 将仍然在哈希值之前指定的分支名称读取元数据，
> 正因为如此，在某些情况下，它不会是一个实用的解决方法，
> 如果可能，你应该总是尝试切换到拥有标签的版本。

它也可以应用于行内别名，这样它将匹配一个约束，否则不会。更多信息请参考 [别名](articles/aliases.md).

#### require

Lists packages required by this package. The package will not be installed
unless those requirements can be met.

#### require-dev <span>(root-only)</span>

Lists packages required for developing this package, or running
tests, etc. The dev requirements of the root package are installed by default.
Both `install` or `update` support the `--no-dev` option that prevents dev
dependencies from being installed.

#### conflict

Lists packages that conflict with this version of this package. They
will not be allowed to be installed together with your package.

Note that when specifying ranges like `<1.0, >= 1.1` in a `conflict` link,
this will state a conflict with all versions that are less than 1.0 *and* equal
or newer than 1.1 at the same time, which is probably not what you want. You
probably want to go for `<1.0 | >= 1.1` in this case.

#### replace

Lists packages that are replaced by this package. This allows you to fork a
package, publish it under a different name with its own version numbers, while
packages requiring the original package continue to work with your fork because
it replaces the original package.

This is also useful for packages that contain sub-packages, for example the main
symfony/symfony package contains all the Symfony Components which are also
available as individual packages. If you require the main package it will
automatically fulfill any requirement of one of the individual components,
since it replaces them.

Caution is advised when using replace for the sub-package purpose explained
above. You should then typically only replace using `self.version` as a version
constraint, to make sure the main package only replaces the sub-packages of
that exact version, and not any other version, which would be incorrect.

#### provide

List of other packages that are provided by this package. This is mostly
useful for common interfaces. A package could depend on some virtual
`logger` package, any library that implements this logger interface would
simply list it in `provide`.

### suggest

Suggested packages that can enhance or work well with this package. These are
just informational and are displayed after the package is installed, to give
your users a hint that they could add more packages, even though they are not
strictly required.

The format is like package links above, except that the values are free text
and not version constraints.

Example:

    {
        "suggest": {
            "monolog/monolog": "Allows more advanced logging of the application flow"
        }
    }

### autoload

Autoload mapping for a PHP autoloader.

Currently [`PSR-0`](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md)
autoloading, `classmap` generation and `files` are supported. PSR-0 is the recommended way though
since it offers greater flexibility (no need to regenerate the autoloader when you add
classes).

#### PSR-0

Under the `psr-0` key you define a mapping from namespaces to paths, relative to the
package root. Note that this also supports the PEAR-style non-namespaced convention.

Please note namespace declarations should end in `\\` to make sure the autoloader
responds exactly. For example `Foo` would match in `FooBar` so the trailing
backslashes solve the problem: `Foo\\` and `FooBar\\` are distinct.

The PSR-0 references are all combined, during install/update, into a single key => value
array which may be found in the generated file `vendor/composer/autoload_namespaces.php`.

Example:

    {
        "autoload": {
            "psr-0": {
                "Monolog\\": "src/",
                "Vendor\\Namespace\\": "src/",
                "Vendor_Namespace_": "src/"
            }
        }
    }

If you need to search for a same prefix in multiple directories,
you can specify them as an array as such:

    {
        "autoload": {
            "psr-0": { "Monolog\\": ["src/", "lib/"] }
        }
    }

The PSR-0 style is not limited to namespace declarations only but may be
specified right down to the class level. This can be useful for libraries with
only one class in the global namespace. If the php source file is also located
in the root of the package, for example, it may be declared like this:

    {
        "autoload": {
            "psr-0": { "UniqueGlobalClass": "" }
        }
    }

If you want to have a fallback directory where any namespace can be, you can
use an empty prefix like:

    {
        "autoload": {
            "psr-0": { "": "src/" }
        }
    }

#### Classmap

The `classmap` references are all combined, during install/update, into a single
key => value array which may be found in the generated file
`vendor/composer/autoload_classmap.php`. This map is built by scanning for
classes in all `.php` and `.inc` files in the given directories/files.

You can use the classmap generation support to define autoloading for all libraries
that do not follow PSR-0. To configure this you specify all directories or files
to search for classes.

Example:

    {
        "autoload": {
            "classmap": ["src/", "lib/", "Something.php"]
        }
    }

#### Files

If you want to require certain files explicitly on every request then you can use
the 'files' autoloading mechanism. This is useful if your package includes PHP functions
that cannot be autoloaded by PHP.

Example:

    {
        "autoload": {
            "files": ["src/MyLibrary/functions.php"]
        }
    }

### include-path

> **DEPRECATED**: This is only present to support legacy projects, and all new code
> should preferably use autoloading. As such it is a deprecated practice, but the
> feature itself will not likely disappear from Composer.

A list of paths which should get appended to PHP's `include_path`.

Example:

    {
        "include-path": ["lib/"]
    }

Optional.

### target-dir

Defines the installation target.

In case the package root is below the namespace declaration you cannot
autoload properly. `target-dir` solves this problem.

An example is Symfony. There are individual packages for the components. The
Yaml component is under `Symfony\Component\Yaml`. The package root is that
`Yaml` directory. To make autoloading possible, we need to make sure that it
is not installed into `vendor/symfony/yaml`, but instead into
`vendor/symfony/yaml/Symfony/Component/Yaml`, so that the autoloader can load
it from `vendor/symfony/yaml`.

To do that, `autoload` and `target-dir` are defined as follows:

    {
        "autoload": {
            "psr-0": { "Symfony\\Component\\Yaml\\": "" }
        },
        "target-dir": "Symfony/Component/Yaml"
    }

Optional.

### minimum-stability <span>(root-only)</span>

This defines the default behavior for filtering packages by stability. This
defaults to `stable`, so if you rely on a `dev` package, you should specify
it in your file to avoid surprises.

All versions of each package are checked for stability, and those that are less
stable than the `minimum-stability` setting will be ignored when resolving
your project dependencies. Specific changes to the stability requirements of
a given package can be done in `require` or `require-dev` (see
[package links](#package-links)).

Available options (in order of stability) are `dev`, `alpha`, `beta`, `RC`,
and `stable`.

### prefer-stable <span>(root-only)</span>

When this is enabled, Composer will prefer more stable packages over unstable
ones when finding compatible stable packages is possible. If you require a
dev version or only alphas are available for a package, those will still be
selected granted that the minimum-stability allows for it.

Use `"prefer-stable": true` to enable.

### repositories <span>(root-only)</span>

Custom package repositories to use.

By default composer just uses the packagist repository. By specifying
repositories you can get packages from elsewhere.

Repositories are not resolved recursively. You can only add them to your main
`composer.json`. Repository declarations of dependencies' `composer.json`s are
ignored.

The following repository types are supported:

* **composer:** A composer repository is simply a `packages.json` file served
  via the network (HTTP, FTP, SSH), that contains a list of `composer.json`
  objects with additional `dist` and/or `source` information. The `packages.json`
  file is loaded using a PHP stream. You can set extra options on that stream
  using the `options` parameter.
* **vcs:** The version control system repository can fetch packages from git,
  svn and hg repositories.
* **pear:** With this you can import any pear repository into your composer
  project.
* **package:** If you depend on a project that does not have any support for
  composer whatsoever you can define the package inline using a `package`
  repository. You basically just inline the `composer.json` object.

For more information on any of these, see [Repositories](05-repositories.md).

Example:

    {
        "repositories": [
            {
                "type": "composer",
                "url": "http://packages.example.com"
            },
            {
                "type": "composer",
                "url": "https://packages.example.com",
                "options": {
                    "ssl": {
                        "verify_peer": "true"
                    }
                }
            },
            {
                "type": "vcs",
                "url": "https://github.com/Seldaek/monolog"
            },
            {
                "type": "pear",
                "url": "http://pear2.php.net"
            },
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
                    }
                }
            }
        ]
    }

> **Note:** Order is significant here. When looking for a package, Composer
will look from the first to the last repository, and pick the first match.
By default Packagist is added last which means that custom repositories can
override packages from it.

### config <span>(root-only)</span>

A set of configuration options. It is only used for projects.

The following options are supported:

* **process-timeout:** Defaults to `300`. The duration processes like git clones
  can run before Composer assumes they died out. You may need to make this
  higher if you have a slow connection or huge vendors.
* **use-include-path:** Defaults to `false`. If true, the Composer autoloader
  will also look for classes in the PHP include path.
* **preferred-install:** Defaults to `auto` and can be any of `source`, `dist` or
  `auto`. This option allows you to set the install method Composer will prefer to
  use.
* **github-protocols:** Defaults to `["git", "https"]`. A list of protocols to
  use when cloning from github.com, in priority order. You can reconfigure it to
  prioritize the https protocol if you are behind a proxy or have somehow bad
  performances with the git protocol.
* **github-oauth:** A list of domain names and oauth keys. For example using
  `{"github.com": "oauthtoken"}` as the value of this option will use `oauthtoken`
  to access private repositories on github and to circumvent the low IP-based
  rate limiting of their API.
* **vendor-dir:** Defaults to `vendor`. You can install dependencies into a
  different directory if you want to.
* **bin-dir:** Defaults to `vendor/bin`. If a project includes binaries, they
  will be symlinked into this directory.
* **cache-dir:** Defaults to `$home/cache` on unix systems and
  `C:\Users\<user>\AppData\Local\Composer` on Windows. Stores all the caches
  used by composer. See also [COMPOSER_HOME](03-cli.md#composer-home).
* **cache-files-dir:** Defaults to `$cache-dir/files`. Stores the zip archives
  of packages.
* **cache-repo-dir:** Defaults to `$cache-dir/repo`. Stores repository metadata
  for the `composer` type and the VCS repos of type `svn`, `github` and `bitbucket`.
* **cache-vcs-dir:** Defaults to `$cache-dir/vcs`. Stores VCS clones for
  loading VCS repository metadata for the `git`/`hg` types and to speed up installs.
* **cache-files-ttl:** Defaults to `15552000` (6 months). Composer caches all
  dist (zip, tar, ..) packages that it downloads. Those are purged after six
  months of being unused by default. This option allows you to tweak this
  duration (in seconds) or disable it completely by setting it to 0.
* **cache-files-maxsize:** Defaults to `300MiB`. Composer caches all
  dist (zip, tar, ..) packages that it downloads. When the garbage collection
  is periodically ran, this is the maximum size the cache will be able to use.
  Older (less used) files will be removed first until the cache fits.
* **prepend-autoloader:** Defaults to `true`. If false, the composer autoloader
  will not be prepended to existing autoloaders. This is sometimes required to fix
  interoperability issues with other autoloaders.
* **autoloader-suffix:** Defaults to `null`. String to be used as a suffix for
  the generated Composer autoloader. When null a random one will be generated.
* **github-domains:** Defaults to `["github.com"]`. A list of domains to use in
  github mode. This is used for GitHub Enterprise setups.
* **notify-on-install:** Defaults to `true`. Composer allows repositories to
  define a notification URL, so that they get notified whenever a package from
  that repository is installed. This option allows you to disable that behaviour.
* **discard-changes:** Defaults to `false` and can be any of `true`, `false` or
  `"stash"`. This option allows you to set the default style of handling dirty
  updates when in non-interactive mode. `true` will always discard changes in
  vendors, while `"stash"` will try to stash and reapply. Use this for CI
  servers or deploy scripts if you tend to have modified vendors.

Example:

    {
        "config": {
            "bin-dir": "bin"
        }
    }

### scripts <span>(root-only)</span>

Composer allows you to hook into various parts of the installation process
through the use of scripts.

See [Scripts](articles/scripts.md) for events details and examples.

### extra

Arbitrary extra data for consumption by `scripts`.

This can be virtually anything. To access it from within a script event
handler, you can do:

    $extra = $event->getComposer()->getPackage()->getExtra();

Optional.

### bin

A set of files that should be treated as binaries and symlinked into the `bin-dir`
(from config).

See [Vendor Binaries](articles/vendor-binaries.md) for more details.

Optional.

### archive

A set of options for creating package archives.

The following options are supported:

* **exclude:** Allows configuring a list of patterns for excluded paths. The
  pattern syntax matches .gitignore files. A leading exclamation mark (!) will
  result in any matching files to be included even if a previous pattern
  excluded them. A leading slash will only match at the beginning of the project
  relative path. An asterisk will not expand to a directory separator.

Example:

    {
        "archive": {
            "exclude": ["/foo/bar", "baz", "/*.test", "!/foo/bar/baz"]
        }
    }

The example will include `/dir/foo/bar/file`, `/foo/bar/baz`, `/file.php`,
`/foo/my.test` but it will exclude `/foo/bar/any`, `/foo/baz`, and `/my.test`.

Optional.

&larr; [Command-line interface](03-cli.md)  |  [Repositories](05-repositories.md) &rarr;
