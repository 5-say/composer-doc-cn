<a name="composer.json"></a>
# composer.json 架构

本章将解释所有在 `composer.json` 中可用的字段。

---

- [composer.json](#composer.json)
  - [JSON schema](#JSON-schema)
  - [Root 包](#Root-Package)
  - [属性](#Properties)
    - [包名 `name`](#package-name)
    - [描述 `description`](#description)
    - [版本 `version`](#version)
    - [安装类型 `type`](#type)
    - [关键字 `keywords`](#keywords)
    - [项目主页 `homepage`](#homepage)
    - [版本发布时间 `time`](#time)
    - [许可协议 `license`](#license)
    - [作者 `authors`](#authors)
    - [支持 `support`](#support)
    - [Package links](#Package-links)
      - [require](#require)
      - [require-dev <span>(root-only)</span>](#require-dev)
      - [conflict](#conflict)
      - [replace](#replace)
      - [provide](#provide)
    - [suggest](#suggest)
    - [autoload](#autoload)
      - [PSR-0](#PSR-0)
      - [Classmap](#Classmap)
      - [Files](#Files)
    - [include-path](#include-path)
    - [target-dir](#target-dir)
    - [minimum-stability <span>(root-only)</span>](#minimum-stability)
    - [prefer-stable <span>(root-only)</span>](#prefer-stable)
    - [repositories <span>(root-only)</span>](#repositories)
    - [config <span>(root-only)</span>](#config)
    - [scripts <span>(root-only)</span>](#scripts)
    - [extra](#extra)
    - [bin](#bin)
    - [archive](#archive)

---

<a name="JSON-schema"></a>
## JSON schema

我们有一个 [JSON schema](http://json-schema.org) 格式化文档，它也可以被用来验证你的 `composer.json` 文件。事实上，它已经被 `validate` 命令所使用。 你可以在这里找到它： [`res/composer-schema.json`](https://github.com/composer/composer/blob/master/res/composer-schema.json).

<a name="Root-Package"></a>
## Root 包

“root 包”是指由 `composer.json` 定义的在你项目根目录的包。这是 `composer.json` 定义你项目所需的主要条件。（简单的说，你自己的项目就是一个 root 包）

某些字段仅适用于“root 包”上下文。 `config` 字段就是其中一个例子。只有“root 包”可以定义。在依赖包中定义的 `config` 字段将被忽略，这使得 `config` 字段只有“root 包”可用（`root-only`）。

如果你克隆了其中的一个依赖包，直接在其上开始工作，那么它就变成了“root 包”。与作为他人的依赖包时使用相同的 `composer.json` 文件，但上下文发生了变化。

> **注意：** 一个资源包是不是“root 包”，取决于它的上下文。
> 例：如果你的项目依赖 `monolog` 库，那么你的项目就是“root 包”。
> 但是，如果你从 GitHub 上克隆了 `monolog` 为它修复 bug，
> 那么此时 `monolog` 就是“root 包”。

<a name="Properties"></a>
## 属性

<a name="package-name"></a>
### 包名 `name`

包的名称，它包括供应商名称和项目名称，使用 `/` 分隔。

例：

* monolog/monolog
* igorw/event-source

对于需要发布的包（库），这是必须填写的。

<a name="description"></a>
### 描述 `description`

一个包的简短描述。通常这个最长只有一行。

对于需要发布的包（库），这是必须填写的。

<a name="version"></a>
### 版本 `version`

`version` 不是必须的，并且建议忽略（见下文）。

它应该符合 'X.Y.Z' 或者 'vX.Y.Z' 的形式， `-dev`、`-patch`、`-alpha`、`-beta` 或 `-RC` 这些后缀是可选的。在后缀之后也可以再跟上一个数字。

例：

- 1.0.0
- 1.0.2
- 1.1.0
- 0.2.5
- 1.0.0-dev
- 1.0.0-alpha3
- 1.0.0-beta2
- 1.0.0-RC5

通常，我们能够从 VCS (git, svn, hg) 的信息推断出包的版本号，在这种情况下，我们建议忽略 `version`。

> **注意：** Packagist 使用 VCS 仓库，
> 因此 `version` 定义的版本号必须是真实准确的。
> 自己手动指定的 `version`，最终有可能在某个时候因为人为错误造成问题。

<a name="type"></a>
### 安装类型 `type`

包的安装类型，默认为 `library`。

包的安装类型，用来定义安装逻辑。如果你有一个包需要一个特殊的逻辑，你可以设定一个自定义的类型。这可以是一个 `symfony-bundle`，一个 `wordpress-plugin` 或者一个 `typo3-module`。这些类型都将是具体到某一个项目，而对应的项目将要提供一种能够安装该类型包的安装程序。

composer 原生支持以下4种类型：

- **library:** 这是默认类型，它会简单的将文件复制到 `vendor` 目录。
- **project:** 这表示当前包是一个项目，而不是一个库。例：框架应用程序 [Symfony standard edition](https://github.com/symfony/symfony-standard)，内容管理系统 [SilverStripe installer](https://github.com/silverstripe/silverstripe-installer) 或者完全成熟的分布式应用程序。使用 IDE 创建一个新的工作区时，这可以为其提供项目列表的初始化。
- **metapackage:** 当一个空的包，包含依赖并且需要触发依赖的安装，这将不会对系统写入额外的文件。因此这种安装类型并不需要一个 dist 或 source。
- **composer-plugin:** 一个安装类型为 `composer-plugin` 的包，它有一个自定义安装类型，可以为其它包提供一个 installler。详细请查看 [自定义安装类型](articles/custom-installers.md)。

仅在你需要一个自定义的安装逻辑时才使用它。建议忽略这个属性，采用默认的 `library`。

<a name="keywords"></a>
### 关键字 `keywords`

该包相关的关键词的数组。这些可用于搜索和过滤。

实例：

- logging
- events
- database
- redis
- templating

可选。

<a name="homepage"></a>
### 项目主页 `homepage`

该项目网站的 URL 地址。

可选。

<a name="time"></a>
### 版本发布时间 `time`

版本发布时间。

必须符合 `YYYY-MM-DD` 或 `YYYY-MM-DD HH:MM:SS` 格式。

可选。

<a name="license"></a>
### 许可协议 `license`

包的许可协议，它可以是一个字符串或者字符串数组。

最常见的许可协议的推荐写法（按字母排序）：

- Apache-2.0
- BSD-2-Clause
- BSD-3-Clause
- BSD-4-Clause
- GPL-2.0
- GPL-2.0+
- GPL-3.0
- GPL-3.0+
- LGPL-2.1
- LGPL-2.1+
- LGPL-3.0
- LGPL-3.0+
- MIT

可选，但强烈建议提供此内容。更多许可协议的标识符请参见 [SPDX Open Source License Registry](http://www.spdx.org/licenses/)。

对于闭源软件，你必须使用 `"proprietary"` 协议标识符。

一个例：

```json
{
    "license": "MIT"
}
```

对于一个包，当允许在多个许可协议间进行选择时（"disjunctive license"），这些协议标识符可以被指定为数组。

多协议的一个例：

```json
{
    "license": [
       "LGPL-2.1",
       "GPL-3.0+"
    ]
}
```

另外它们也可以由 "or" 分隔，并写在括号中：

```json
{
    "license": "(LGPL-2.1 or GPL-3.0+)"
}
```

同样，当有多个许可协议需要结合使用时（"conjunctive license"），它们应该被 "and" 分隔，并写在括号中。

<a name="authors"></a>
### 作者 `authors`

包的作者。这是一个对象数组。

这个对象必须包含以下属性：

* **name:** 作者的姓名，通常使用真名。
* **email:** 作者的 email 地址。
* **homepage:** 作者主页的 URL 地址。
* **role:** 该作者在此项目中担任的角色（例：开发人员 或 翻译）。

一个实例：

```json
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
```

可选，但强烈建议提供此内容。

<a name="support"></a>
### 支持 `support`

获取项目支持的向相关信息对象。

这个对象必须包含以下属性：

* **email:** 项目支持 email 地址。
* **issues:** 跟踪问题的 URL 地址。
* **forum:** 论坛地址。
* **wiki:** Wiki 地址。
* **irc:** IRC 聊天频道地址，类似于 irc://server/channel。
* **source:** 网址浏览或下载源。

一个实例：

```json
{
    "support": {
        "email": "support@example.org",
        "irc": "irc://irc.freenode.org/composer"
    }
}
```

可选。

<a name="Package-links"></a>
### Package links

下面提到的所有对象，都应该是 包名 到 [版本](01-basic-usage.md#Package-Versions) 的映射对象。

实例：

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

所有的这些都是可选的。

`require` 和 `require-dev` 还支持稳定性标签（@，仅针对“root 包”）。这允许你在 [minimum-stability](#minimum-stability) 设定的范围外做进一步的限制或扩展。例：如果你想允许依赖一个不稳定的包，你可以在一个包的版本约束后使用它，或者是一个空的版本约束内使用它。

实例：

```json
{
    "require": {
        "monolog/monolog": "1.0.*@beta",
        "acme/foo": "@dev"
    }
}
```

如果你的依赖之一，有对另一个不稳定包的依赖，你最好在 require 中显示的定义它，并带上足够详细的稳定性标识。

实例：

```json
{
    "require": {
        "doctrine/doctrine-fixtures-bundle": "dev-master",
        "doctrine/data-fixtures": "@dev"
    }
}
```

`require` 和 `require-dev` 还支持对 dev（开发）版本的明确引用（即：版本控制系统中的提交编号 commit），以确保它们被锁定到一个给定的状态，即使你运行了更新命令。你只需要明确一个开发版本号，并带上诸如 `#<ref>` 的标识。

实例：

```json
{
    "require": {
        "monolog/monolog": "dev-master#2eb0c0978d290a1c45346a1955188929cb4e5db7",
        "acme/foo": "1.0.x-dev#abc123"
    }
}
```

> **注意：** 虽然这有时很方便，但不应该长期在你的包中使用，因为它有一个技术上的限制。
> composer.json 将仍然在哈希值之前指定的分支名称读取元数据，
> 正因为如此，在某些情况下，它不会是一个实用的解决方法，
> 如果可能，你应该总是尝试切换到拥有标签的版本。

它也可以应用于行内别名，这样它将匹配一个约束，否则不会。更多信息请参考 [别名](articles/aliases.md)。

<a name="require"></a>
#### require

必须的软件包列表，除非这些依赖被满足，否则不会完成安装。

<a name="require-dev"></a>
#### require-dev <span>(root-only)</span>

这个列表是为开发或测试等目的，额外列出的依赖。“root 包”的 require-dev 默认是会被安装的。然而 `install` 或 `update` 支持使用 `--no-dev` 参数来跳过 `require-dev` 字段中列出的包。

<a name="conflict"></a>
#### conflict

此列表中的包与当前包的这个版本冲突。它们将不允许同时被安装。

请注意，在 `conflict` 中指定类似于 `<1.0, >= 1.1` 的版本范围时，这表示它与小于1.0 *并且* 同时大等于1.1的版本冲突，这很可能不是你想要的。在这种情况下你可能想要表达的是 `<1.0 | >= 1.1` 。

<a name="replace"></a>
#### replace

这个列表中的包将被当前包取代。这使你可以 fork 一个包，以不同的名称和版本号发布，同时要求依赖于原包的其它包，在这之后依赖于你 fork 的这个包，因为它取代了原来的包。

这对于创建一个内部包含子包的主包也非常的有用。例如 symfony/symfony 这个主包，包含了所有 Symfony 的组件，而这些组件又可以作为单独的包进行发布。如果你 require 了主包，那么它就会自动完成其下各个组件的任务，因为主包取代了子包。

注意，在使用上述方法取代子包时，通常你应该只对子包使用 `self.version` 这一个版本约束，以确保主包仅替换掉子包的准确版本，而不是任何其他版本。

<a name="provide"></a>
#### provide

List of other packages that are provided by this package. This is mostly useful for common interfaces. A package could depend on some virtual `logger` package, any library that implements this logger interface would simply list it in `provide`.

<a name="suggest"></a>
### suggest

建议安装的包，它们增强或能够与当前包良好的工作。这些只是信息，并显示在依赖包安装完成之后，给你的用户一个建议，他们可以添加更多的包。

格式如下，版本约束变成了描述信息。

实例：

```json
{
    "suggest": {
        "monolog/monolog": "Allows more advanced logging of the application flow"
    }
}
```

<a name="autoload"></a>
### autoload

PHP autoloader 的自动加载映射。

Currently [`PSR-0`](http://www.php-fig.org/psr/psr-0/) autoloading,
[`PSR-4`](http://www.php-fig.org/psr/psr-4/) autoloading, `classmap` generation and
`files` includes are supported. PSR-4 is the recommended way though since it offers
greater ease of use (no need to regenerate the autoloader when you add classes).

#### PSR-4

Under the `psr-4` key you define a mapping from namespaces to paths, relative to the
package root. When autoloading a class like `Foo\\Bar\\Baz` a namespace prefix
`Foo\\` pointing to a directory `src/` means that the autoloader will look for a
file named `src/Bar/Baz.php` and include it if present. Note that as opposed to
the older PSR-0 style, the prefix (`Foo\\`) is **not** present in the file path.

Namespace prefixes must end in `\\` to avoid conflicts between similar prefixes.
For example `Foo` would match classes in the `FooBar` namespace so the trailing
backslashes solve the problem: `Foo\\` and `FooBar\\` are distinct.

The PSR-4 references are all combined, during install/update, into a single
key => value array which may be found in the generated file
`vendor/composer/autoload_psr4.php`.

Example:

```json
{
    "autoload": {
        "psr-4": {
            "Monolog\\": "src/",
            "Vendor\\Namespace\\": ""
        }
    }
}
```

If you need to search for a same prefix in multiple directories,
you can specify them as an array as such:

```json
{
    "autoload": {
        "psr-4": { "Monolog\\": ["src/", "lib/"] }
    }
}
```

If you want to have a fallback directory where any namespace will be looked for,
you can use an empty prefix like:

```json
{
    "autoload": {
        "psr-4": { "": "src/" }
    }
}
```

<a name="PSR-0"></a>
#### PSR-0

在 `psr-0` key 下你定义了一个命名空间到实际路径的映射（相对于包的根目录）。注意，这里同样支持 PEAR-style 方式的约定（与命名空间不同，PEAR 类库在类名上采用了下划线分隔）。

请注意，命名空间的申明应该以 `\\` 结束，以确保 autoloader 能够准确响应。例： `Foo` 将会与 `FooBar` 匹配，然而以反斜杠结束就可以解决这样的问题， `Foo\\` 和 `FooBar\\` 将会被区分开来。

在 install/update 过程中，PSR-0 引用都将被结合为一个单一的键值对数组，存储至 `vendor/composer/autoload_namespaces.php` 文件中。

实例：

```json
{
    "autoload": {
        "psr-0": {
            "Monolog\\": "src/",
            "Vendor\\Namespace\\": "src/",
            "Vendor_Namespace_": "src/"
        }
    }
}
```

如果你需要搜索多个目录中一个相同的前缀，你可以将它们指定为一个数组，例：

```json
{
    "autoload": {
        "psr-0": { "Monolog\\": ["src/", "lib/"] }
    }
}
```

PSR-0 方式并不仅限于申明命名空间，也可以是精确到类级别的指定。这对于只有一个类在全局命名空间的类库是非常有用的（如果 php 源文件也位于包的根目录）。例如，可以这样申明：

```json
{
    "autoload": {
        "psr-0": { "UniqueGlobalClass": "" }
    }
}
```

如果你想设置一个目录作为任何命名空间的备用目录，你可以使用空的前缀，像这样：

```json
{
    "autoload": {
        "psr-0": { "": "src/" }
    }
}
```

<a name="Classmap"></a>
#### Classmap

`classmap` 引用的所有组合，都会在 install/update 过程中生成，并存储到 `vendor/composer/autoload_classmap.php` 文件中。这个 map 是经过扫描指定目录（同样支持直接精确到文件）中所有的 `.php` 和 `.inc` 文件里内置的类而得到的。

你可以用 classmap 生成支持支持自定义加载的不遵循 PSR-0/4 规范的类库。要配置它指向需要的目录，以便能够准确搜索到类文件。

实例：

```json
{
    "autoload": {
        "classmap": ["src/", "lib/", "Something.php"]
    }
}
```

<a name="Files"></a>
#### Files

如果你想要明确的指定，在每次请求时都要载入某些文件，那么你可以使用 'files' autoloading。通常作为函数库的载入方式（而非类库）。

实例：

```json
{
    "autoload": {
        "files": ["src/MyLibrary/functions.php"]
    }
}
```

### autoload-dev <span>(root-only)</span>

This section allows to define autoload rules for development purposes.

Classes needed to run the test suite should not be included in the main autoload
rules to avoid polluting the autoloader in production and when other people use
your package as a dependency.

Therefore, it is a good idea to rely on a dedicated path for your unit tests
and to add it within the autoload-dev section.

Example:

```json
{
    "autoload": {
        "psr-4": { "MyLibrary\\": "src/" }
    },
    "autoload-dev": {
        "psr-4": { "MyLibrary\\Tests\\": "tests/" }
    }
}
```

<a name="include-path"></a>
### include-path

> **不建议**：这是目前唯一支持传统项目的做法，所有新的代码都建议使用自动加载。
> 这是一个过时的做法，但 Composer 将仍然保留这个功能。

一个追加到 PHP `include_path` 中的列表。

实例：

```json
{
    "include-path": ["lib/"]
}
```

可选。

<a name="target-dir"></a>
### target-dir

> **DEPRECATED**: This is only present to support legacy PSR-0 style autoloading,
> and all new code should preferably use PSR-4 without target-dir and projects
> using PSR-0 with PHP namespaces are encouraged to migrate to PSR-4 instead.

定义当前包安装的目标文件夹。

若某个包的根目录，在它申明的命名空间之下，将不能正确的使用自动加载。而 `target-dir` 解决了这个问题。

Symfony 就是一个例子。它有一些独立的包作为组件。Yaml 组件就放在 `Symfony\Component\Yaml` 目录下，然而这个包的根目录实际上是 `Yaml`。为了使自动加载成为可能，我们需要确保它不会被安装到 `vendor/symfony/yaml`，而是安装到 `vendor/symfony/yaml/Symfony/Component/Yaml`，从而使 Symfony 定义的 autoloader 可以从 `vendor/symfony/yaml` 加载它。

要做到这一点 `autoload` 和 `target-dir` 应该定义如下：

```json
{
    "autoload": {
        "psr-0": { "Symfony\\Component\\Yaml\\": "" }
    },
    "target-dir": "Symfony/Component/Yaml"
}
```

可选。

<a name="minimum-stability"></a>
### minimum-stability <span>(root-only)</span>

这定义了通过稳定性过滤包的默认行为。默认为 `stable`（稳定）。因此如果你依赖于一个 `dev`（开发）包，你应该明确的进行定义。

对每个包的所有版本都会进行稳定性检查，而低于 `minimum-stability` 所设定的最低稳定性的版本，将在解决依赖关系时被忽略。对于个别包的特殊稳定性要求，可以在 `require` 或 `require-dev` 中设定（请参考 [Package links](#Package-links)）。

可用的稳定性标识（按字母排序）：`dev`、`alpha`、`beta`、`RC`、`stable`。

<a name="prefer-stable"></a>
### prefer-stable <span>(root-only)</span>

当此选项被激活时，Composer 将优先使用更稳定的包版本。

使用 `"prefer-stable": true` 来激活它。

<a name="repositories"></a>
### repositories <span>(root-only)</span>

使用自定义的包资源库。

默认情况下 composer 只使用 packagist 作为包的资源库。通过指定资源库，你可以从其他地方获取资源包。

Repositories 并不是递归调用的，只能在“Root包”的 `composer.json` 中定义。附属包中的 `composer.json` 将被忽略。

支持以下类型的包资源库：

* **composer:** 一个 composer 类型的资源库，是一个简单的网络服务器（HTTP、FTP、SSH）上的 `packages.json` 文件，它包含一个 `composer.json` 对象的列表，有额外的 `dist` 和/或 `source` 信息。这个 `packages.json` 文件是用一个 PHP 流加载的。你可以使用 `options` 参数来设定额外的流信息。
* **vcs:** 从 git、svn 和 hg 取得资源。
* **pear:** 从 pear 获取资源。
* **package:** 如果你依赖于一个项目，它不提供任何对 composer 的支持，你就可以使用这种类型。你基本上就只需要内联一个 `composer.json` 对象。

更多相关内容，请查看 [资源库](05-repositories.md)。

实例：

```json
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
```

> **注意：** 顺序是非常重要的，当 Composer 查找资源包时，它会按照顺序进行。默认情况下 Packagist 是最后加入的，因此自定义设置将可以覆盖 Packagist 上的包。

<a name="config"></a>
### config <span>(root-only)</span>

下面的这一组选项，仅用于项目。

支持以下选项：

* **process-timeout:** 默认为 `300`。处理进程结束时间，例如：git 克隆的时间。Composer 将放弃超时的任务。如果你的网络缓慢或者正在使用一个巨大的包，你可能要将这个值设置的更高一些。
* **use-include-path:** 默认为 `false`。如果为 true，Composer autoloader 还将在 PHP include path 中继续查找类文件。
* **preferred-install:** 默认为 `auto`。它的值可以是 `source`、`dist` 或 `auto`。这个选项允许你设置 Composer 的默认安装方法。
* **github-protocols:** 默认为 `["git", "https", "ssh"]`。从 github.com 克隆时使用的协议优先级清单，因此默认情况下将优先使用 git 协议进行克隆。你可以重新排列它们的次序，例如，如果你的网络有代理服务器或 git 协议的效率很低，你就可以提升 https 协议的优先级。
* **github-oauth:** 一个域名和 oauth keys 的列表。
  例如：使用 `{"github.com": "oauthtoken"}` 作为此选项的值，
  将使用 `oauthtoken` 来访问 github 上的私人仓库，并绕过 low IP-based rate 的 API 限制。
  [关联知识](articles/troubleshooting.md#api-rate-limit-and-oauth-tokens)
  关于如何获取 GitHub 的 OAuth token。
* **vendor-dir:** 默认为 `vendor`。通过设置你可以安装依赖到不同的目录。
* **bin-dir:** 默认为 `vendor/bin`。如果一个项目包含二进制文件，它们将被连接到这个目录。
* **cache-dir:** unix 下默认为 `$home/cache`，Windows 下默认为 `C:\Users\<user>\AppData\Local\Composer`。用于存储 composer 所有的缓存文件。相关信息请查看 [COMPOSER_HOME](03-cli.md#composer-home)。
* **cache-files-dir:** 默认为 `$cache-dir/files`。存储包 zip 存档的目录。
* **cache-repo-dir:** 默认为 `$cache-dir/repo`。存储 `composer` 类型的 VCS（`svn`、`github`、`bitbucket`） repos 目录。
* **cache-vcs-dir:** 默认为 `$cache-dir/vcs`。此目录用于存储 VCS 克隆的 `git`/`hg` 类型的元数据，并加快安装速度。
* **cache-files-ttl:** 默认为 `15552000`（6个月）。默认情况下 Composer 缓存的所有数据都将在闲置6个月后被删除，这个选项允许你来调整这个时间，你可以将其设置为0以禁用缓存。
* **cache-files-maxsize:** 默认为 `300MiB`。Composer 缓存的最大容量，超出后将优先清除旧的缓存数据，直到缓存量低于这个数值。
* **prepend-autoloader:** 默认为 `true`。如果设置为 false，composer autoloader 将不会附加到现有的自动加载机制中。这有时候用来解决与其它自动加载机制产生的冲突。
* **autoloader-suffix:** 默认为 `null`。Composer autoloader 的后缀，当设置为空时将会产生一个随机的字符串。
* **optimize-autoloader** Defaults to `false`. Always optimize when dumping
  the autoloader.
* **github-domains:** 默认为 `["github.com"]`。一个 github mode 下的域名列表。这是用于GitHub的企业设置。
* **notify-on-install:** 默认为 `true`。Composer 允许资源仓库定义一个用于通知的 URL，以便有人从其上安装资源包时能够得到一个反馈通知。此选项允许你禁用该行为。
* **discard-changes:** 默认为 `false`，它的值可以是 `true`、`false` 或 `stash`。这个选项允许你设置在非交互模式下，当处理失败的更新时采用的处理方式。`true` 表示永远放弃更改。`"stash"` 表示继续尝试。Use this for CI servers or deploy scripts if you tend to have modified vendors.

实例：

```json
{
    "config": {
        "bin-dir": "bin"
    }
}
```

<a name="scripts"></a>
### scripts <span>(root-only)</span>

Composer 允许你在安装过程中的各个阶段挂接脚本。

更多细节和案例请查看 [脚本](articles/scripts.md)。

<a name="extra"></a>
### extra

任意的，供 `scripts` 使用的额外数据。.

这可以是几乎任何东西。若要从脚本事件访问处理程序，你可以这样做：

```php
$extra = $event->getComposer()->getPackage()->getExtra();
```

可选。

<a name="bin"></a>
### bin

该属性用于标注一组应被视为二进制脚本的文件，他们会被软链接到（config 对象中的）`bin-dir` 属性所标注的目录，以供其他依赖包调用。

详细请查看 [Vendor Binaries](articles/vendor-binaries.md)。

可选。

<a name="archive"></a>
### archive

这些选项在创建包存档时使用。

支持以下选项：

* **exclude:** 允许设置一个需要被排除的路径的列表。使用与 .gitignore 文件相同的语法。一个前导的（!）将会使其变成白名单而无视之前相同目录的排除设定。前导斜杠只会在项目的相对路径的开头匹配。星号为通配符。

实例：

```json
{
    "archive": {
        "exclude": ["/foo/bar", "baz", "/*.test", "!/foo/bar/baz"]
    }
}
```

在这个例子中我们 include `/dir/foo/bar/file`、`/foo/bar/baz`、`/file.php`、`/foo/my.test` 但排除了 `/foo/bar/any`、`/foo/baz`、`/my.test`。

可选。

&larr; [命令行](03-cli.md)  |  [资源库](05-repositories.md) &rarr;
