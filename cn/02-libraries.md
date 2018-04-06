- [库](#%E5%BA%93)
    - [每个项目都是一个软件包](#%E6%AF%8F%E4%B8%AA%E9%A1%B9%E7%9B%AE%E9%83%BD%E6%98%AF%E4%B8%80%E4%B8%AA%E8%BD%AF%E4%BB%B6%E5%8C%85)
    - [库的版本控制](#%E5%BA%93%E7%9A%84%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)
        - [VCS Versioning](#vcs-versioning)
    - [Lock file](#lock-file)
    - [Publishing to a VCS](#publishing-to-a-vcs)
    - [Publishing to packagist](#publishing-to-packagist)

# 库

本章将告诉您，如何让您的库可以通过 Composer 进行安装。

## 每个项目都是一个软件包

只要目录中存在一个 `composer.json` 文件，该目录就是一个包。
当您通过 [`require`](04-schema.md#require) 向您的项目添加依赖时，您就是在制作一个依赖于其他软件包的软件包。
您的项目和一个库唯一的区别是，您的项目是一个没有名字的包。

为了使这个软件包可以安装，您需要给它一个名字。您可以通过在 `composer.json` 中添加 [`name`](04-schema.md#name) 属性来执行此操作：

```json
{
    "name": "acme/hello-world",
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

在这种情况下，项目名称是 `acme/hello-world`，而 `acme` 是所有者名称。提供所有者名称是强制性的。

> **注意：** 如果您不知道如何使用所有者名称，那么您的 GitHub 用户名通常是一个不错的选择。
> 虽然软件包名称不区分大小写，但惯例全部为小写字母并且为了分词而使用中横线。

## 库的版本控制

在绝大多数情况下，您将使用某种类型的版本控制系统（如 git，svn，hg 或 fossil）来维护您的库。
在这些情况下，Composer 会推断版本控制系统中的版本，并且您不应该在 `composer.json` 文件中直接指定版本。
（请参阅 [版本与约束](articles/versions.md) 一文，以了解 Composer 如何使用版本控制系统中的分支和标签来解析版本限制。）

如果您手动维护软件包（即没有版本控制系统），则需要通过 `version` 在 `composer.json` 文件中添加值来明确指定版本：

```json
{
    "version": "1.0.0"
}
```

> **注意：** 当您添加一个硬性的版本号到版本控制系统中时，版本将与标签名称冲突。Composer 将无法确定版本号。
> When you add a hardcoded version to a VCS, the version will conflict with tag names. Composer will not be able to determine the version number.

### VCS Versioning

Composer uses your VCS's branch and tag features to resolve the version
constraints you specify in your `require` field to specific sets of files.
When determining valid available versions, Composer looks at all of your tags
and branches and translates their names into an internal list of options that
it then matches against the version constraint you provided.

For more on how Composer treats tags and branches and how it resolves package
version constraints, read the [versions](articles/versions.md) article.

## Lock file

For your library you may commit the `composer.lock` file if you want to. This
can help your team to always test against the same dependency versions.
However, this lock file will not have any effect on other projects that depend
on it. It only has an effect on the main project.

If you do not want to commit the lock file and you are using git, add it to
the `.gitignore`.

## Publishing to a VCS

Once you have a VCS repository (version control system, e.g. git) containing a
`composer.json` file, your library is already composer-installable. In this
example we will publish the `acme/hello-world` library on GitHub under
`github.com/username/hello-world`.

Now, to test installing the `acme/hello-world` package, we create a new
project locally. We will call it `acme/blog`. This blog will depend on
`acme/hello-world`, which in turn depends on `monolog/monolog`. We can
accomplish this by creating a new `blog` directory somewhere, containing a
`composer.json`:

```json
{
    "name": "acme/blog",
    "require": {
        "acme/hello-world": "dev-master"
    }
}
```

The name is not needed in this case, since we don't want to publish the blog
as a library. It is added here to clarify which `composer.json` is being
described.

Now we need to tell the blog app where to find the `hello-world` dependency.
We do this by adding a package repository specification to the blog's
`composer.json`:

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

For more details on how package repositories work and what other types are
available, see [Repositories](05-repositories.md).

That's all. You can now install the dependencies by running Composer's
[`install`](03-cli.md#install) command!

**Recap:** Any git/svn/hg/fossil repository containing a `composer.json` can be
added to your project by specifying the package repository and declaring the
dependency in the [`require`](04-schema.md#require) field.

## Publishing to packagist

Alright, so now you can publish packages. But specifying the VCS repository
every time is cumbersome. You don't want to force all your users to do that.

The other thing that you may have noticed is that we did not specify a package
repository for `monolog/monolog`. How did that work? The answer is Packagist.

[Packagist](https://packagist.org/) is the main package repository for
Composer, and it is enabled by default. Anything that is published on
Packagist is available automatically through Composer. Since
[Monolog is on Packagist](https://packagist.org/packages/monolog/monolog), we
can depend on it without having to specify any additional repositories.

If we wanted to share `hello-world` with the world, we would publish it on
Packagist as well. Doing so is really easy.

You simply visit [Packagist](https://packagist.org) and hit the "Submit"
button. This will prompt you to sign up if you haven't already, and then
allows you to submit the URL to your VCS repository, at which point Packagist
will start crawling it. Once it is done, your package will be available to
anyone!

&larr; [Basic usage](01-basic-usage.md) |  [Command-line interface](03-cli.md) &rarr;
