<!--
    tagline: Alias branch names to versions
-->

# 别名

## 为什么使用别名？

当你使用 VCS 资源库，你将只会得到类似于这样的版本号：从分支发布的标签获取，它看起来像 `2.0`。比较特殊的是，对于你的 `master` 分支，你会得到一个最新提交的 `dev-master` 版本。对于你的 `bugfix` 分支，你会得到一个最新提交的 `dev-bugfix` 版本。以此类推，这些特殊的版本标识可以用来获取最新的分支源码。

如果你的 `master` 分支使用标签发布了 `1.0` 系列版本，即 `1.0.1`、`1.0.2`、`1.0.3` 等等，任何依赖它的资源包都可能会使用 `1.0.*` 这个版本约束。

如果有人想要最新的 `dev-master` 版本，他们将会碰到一个问题：另一些依赖它的包可能使用了 `1.0.*` 这个版本约束，因此在 require 这个开发版本时将会产生冲突，因为 `dev-master` 不符合 `1.0.*` 的约束。

这时，就可以使用别名。

## 分支别名

`dev-master` 指向一个在你 VCS 项目上的主分支。有些用户会想要使用最新的开发版本，这是相当常见的情况。因此，Composer 允许你别名你的 `dev-master` 版本为一个 `1.0.x-dev` 的版本号。这是通过在 `composer.json` 文件中的 `extra` 下指定 `branch-alias` 字段来完成的：

    {
        "extra": {
            "branch-alias": {
                "dev-master": "1.0.x-dev"
            }
        }
    }

此处的分支版本必须以 `dev-` 开头（不可比较的版本名称），对应的别名必须是可比较的开发版本名称（即，以数字开头，并以 `.x-dev` 结束）。`branch-alias` 所引用的分支必须是存在的。对于 `dev-master` 你需要在 `master` 分支上提交它。

其结果是，任何人都可以使用 `1.0.*` 版本约束来得到 `dev-master` 版本。

为了定义分支别名，你必须是需要别名的包的所有者。如果你想别名一个第三方包，而又不想 fork 它到自己的版本库，可以使用行内别名，我们在接下来就会提到它。

## 使用行内别名

分支别名是非常适合用于主开发分支的。但为了使用它们，你需要拥有对源码的控制权，并且你需要提交别名修改到你控制的版本库。

当你只想在本地项目中尝试一些依赖库的 bug 修正时，这并不是最好的方式。

出于这个原因，你可以在 `require` 和 `require-dev` 字段中直接别名你需要的包。比方说那你找到了 `monolog/monolog` 的一个 bug。你在 GitHub 上克隆了 [Monolog](https://github.com/Seldaek/monolog) 并在名为 `bugfix` 的分支上修正了一个问题。现在你想安装这个版本到你的本地项目。

你所使用的 `symfony/monolog-bundle` require 了 `monolog/monolog` 并约束了版本 `1.*`. 因此你需要让你的 `dev-bugfix` 满足该版本约束。

只要在你项目根目录的 `composer.json` 文件中加入以下内容：

    {
        "repositories": [
            {
                "type": "vcs",
                "url": "https://github.com/you/monolog"
            }
        ],
        "require": {
            "symfony/monolog-bundle": "2.0",
            "monolog/monolog": "dev-bugfix as 1.0.x-dev"
        }
    }

它将会在你的 GitHub 上获取 `monolog/monolog` 的 `dev-bugfix` 版本并将其版本别名为 `1.0.x-dev`。

> **Note:** If a package with inline aliases is required, the alias (right of
> the `as`) is used as the version constraint. The part left of the `as` is
> discarded. As a consequence, if A requires B and B requires `monolog/monolog`
> version `dev-bugfix as 1.0.x-dev`, installing A will make B require
> `1.0.x-dev`, which may exist as a branch alias or an actual `1.0` branch. If
> it does not, it must be re-inline-aliased in A's `composer.json`.

> **Note:** Inline aliasing should be avoided, especially for published
> packages. If you found a bug, try and get your fix merged upstream. This
> helps to avoid issues for users of your package.
