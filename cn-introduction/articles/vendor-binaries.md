<!--
    tagline: Expose command-line scripts from packages
-->

# 二进制供应库和 `vendor/bin` 目录

## 什么是二进制供应库？

一个 Composer 资源包，想要传递给安装它的用户的任何命令行脚本，
都应该被列入 `二进制供应库`。

如果一个资源包，含有不被用户所需要的其他脚本（比如构建或编译脚本）
那么这些代码不应该被列入二进制供应库。


## 如何定义？

它是通过在项目的 `composer.json` 里添加一个 `bin` 键定义的。
它是以一种文件的数组的形式定义的，这样任何给定的项目
都可以添加多个二进制文件。

```json
{
    "bin": ["bin/my-script", "bin/my-other-script"]
}
```

## 在 composer.json 里定义二进制供应库的作用是？

对于任何 **依赖于** 该资源包的项目，
它将引导 Composer 安装资源包里的二进制文件到 `vendor/bin` 目录。

这是一个便捷的途径来暴露有用的脚本，
否则它们将会被隐藏在 `vendor/` 目录的深处。


## 当 Composer 运行到定义了二进制供应库的 composer.json 文件时发生了什么？

对于被某个资源包直接定义的二进制供应库，什么也不会发生。


## 当 Composer 运行到，列出了二进制供应库依赖关系的 composer.json 文件时发生了什么？

Composer 会检查所有依赖包里定义的二进制文件。
并为每一个依赖的二进制库设立一个指向 `vendor/bin` 的软连接。

比如 `my-vendor/project-a` 资源包的二进制库就是这样安装的：

```json
{
    "name": "my-vendor/project-a",
    "bin": ["bin/project-a-bin"]
}
```

在该 `composer.json` 上执行 `composer install` 命令，
不会对 `bin/project-a-bin` 造成任何影响。

再比如项目 `my-vendor/project-b` 有这样的 require 定义：

```json
{
    "name": "my-vendor/project-b",
    "require": {
        "my-vendor/project-a": "*"
    }
}
```

在该 `composer.json` 上执行 `composer install` 命令时，
将会检查 project-b 的所有依赖包，并安装它们的二进制库到 `vendor/bin` 目录。

这种情况下，Composer 将使 `vendor/bin/project-a-bin` 可以作为
`vendor/my-vendor/project-a/bin/project-a-bin` 的访问路径。在类 Unix 平台上，
这是通过创建软链接实现的。


## 对于 Windows 环境和 .bat 文件呢？

完全由 Composer 管理的包并不 *需要* 包含任何用以兼容 Windows 的
`.bat` 文件。在 Windows 环境下运行时，Composer 会用一种特殊的方式处理
二进制文件的安装：

 * 一个用以引用此二进制文件的 `.bat` 文件会自动生成
 * 一个与该二进制文件同名的 Unix 风格的代理文件也会自动生成
 （方便 Cygwin 或 Git Bash 使用）

有时资源包需要维护自定义的 `.bat` 文件来支持工作流，它可以不包含在 Composer 中。
这种情况下，该资源包 **不** 应该把它们作为二进制文件列出，因为 Composer 不需要知道它们。


## 二进制供应库可以安装在 vendor/bin 以外的其它地方么？

当然，这里有两种指定二进制供应库的其他可选位置的方法：

 1. 在 `composer.json` 文件中的 `bin-dir` 配置属性处设置
 2. 设置环境变量 `COMPOSER_BIN_DIR`

前者的实例如下：

```json
{
    "config": {
        "bin-dir": "scripts"
    }
}
```

在这个 `composer.json` 上运行 `composer install` 会把所有的二进制供应库
都安装在 `scripts/` 目录，而不是缺省的 `vendor/bin/` 目录。
