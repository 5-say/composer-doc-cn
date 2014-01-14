<!--
    tagline: Alias branch names to versions
-->

# 别名

## 为什么使用别名？

当你使用 VCS 资源库，你将只会得到类似于这样的版本号：从分支发布的标签获取，它看起来像 `2.0`。比较特殊的是，对于你的 `master` 分支，你会得到一个最新提交的 `dev-master` 版本。对于你的 `bugfix` 分支，你会得到一个最新提交的 `dev-bugfix` 版本。以此类推，这些特殊的版本标识可以用来获取最新的分支源码。

如果你的 `master` 分支使用标签发布了 `1.0` 系列版本，即 `1.0.1`、`1.0.2`、`1.0.3` 等等，任何依赖它的资源包都可能会 require `1.0.*` 这个版本约束。

如果有人想要最新的 `dev-master` 版本，他们将会碰到一个问题：其它的包可能还使用了 `1.0.*` 这个版本约束，因此在 require 这个开发版本时将会产生冲突，因为 `dev-master` 不符合 `1.0.*` 的约束。

这时，就可以使用别名。

## 分支别名

`dev-master` 分支是一个在你 VCS 项目上的主分支。有些用户会想要使用最新的开发版本，这是相当常见的情况。因此，Composer 允许你别名你的 `dev-master` 分支为一个 `1.0.x-dev` 的版本。这是通过在 `composer.json` 文件中的 `extra` 下指定 `branch-alias` 字段来完成的：

    {
        "extra": {
            "branch-alias": {
                "dev-master": "1.0.x-dev"
            }
        }
    }

此处的分支版本必须以 `dev-` 开头（不可比较的版本名称），对应的别名必须是可比较的开发版本名称（即，以数字开头，并以 `.x-dev` 结束）。`branch-alias` 所引用的分支必须是存在的。对于 `dev-master` 你需要在 `master` 分支上提交它。

其结果是，任何人都可以 require `1.0.*` 版本，并且他们实际将会得到 `dev-master` 版本。

为了定义分支别名，你必须是需要别名的包的所有者。如果你想别名一个第三方包，而又不想 fork 它到自己的版本库，可以使用行内别名，我们在接下来就会提到它。

## Require 中使用行内别名

Branch aliases are great for aliasing main development lines. But in order to
use them you need to have control over the source repository, and you need to
commit changes to version control.

This is not really fun when you just want to try a bugfix of some library that
is a dependency of your local project.

For this reason, you can alias packages in your `require` and `require-dev`
fields. Let's say you found a bug in the `monolog/monolog` package. You cloned
[Monolog](https://github.com/Seldaek/monolog) on GitHub and fixed the issue in 
a branch named `bugfix`. Now you want to install that version of monolog in your 
local project.

You are using `symfony/monolog-bundle` which requires `monolog/monolog` version
`1.*`. So you need your `dev-bugfix` to match that constraint.

Just add this to your project's root `composer.json`:

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

That will fetch the `dev-bugfix` version of `monolog/monolog` from your GitHub
and alias it to `1.0.x-dev`.

> **Note:** If a package with inline aliases is required, the alias (right of
> the `as`) is used as the version constraint. The part left of the `as` is
> discarded. As a consequence, if A requires B and B requires `monolog/monolog`
> version `dev-bugfix as 1.0.x-dev`, installing A will make B require
> `1.0.x-dev`, which may exist as a branch alias or an actual `1.0` branch. If
> it does not, it must be re-inline-aliased in A's `composer.json`.

> **Note:** Inline aliasing should be avoided, especially for published
> packages. If you found a bug, try and get your fix merged upstream. This
> helps to avoid issues for users of your package.
