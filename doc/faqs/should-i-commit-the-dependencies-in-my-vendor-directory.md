# 我应该提交 vendor 目录下的依赖包到源码仓库吗？

通常 **不** 建议这么做。vendor 目录（或任何用于安装依赖包的目录）
都应该添加到 `.gitignore`/`svn:ignore`/等文件中并被忽略。

最佳实践是让所有开发人员使用 Composer 来安装
依赖包。同样，构建服务器、CI（持续集成工具）、部署工具等也用该
进行调整，以便将使用 Composer 作为项目前期准备阶段的一部分。

虽然在某些环境中将其提交到源码仓库很有诱惑力，但是容易带来以下几个
问题：

- 更新代码时，VCS 仓库和 diff 太大。
- 在你自己的 VCS 中将会保存所有依赖包的历史副本。
- 通过 git 安装的依赖包将被添加到仓库中并被视作
  子模块。这会带来很多问题和麻烦，
  因为它们并非真正的子模块。

如果你真的觉得有必要这么做，你有以下几个选择：

1. 限制自己只安装打过 tag 的发布版（不是 dev 版本），这样的话
   只会安装 zip 文件，从而避免出现 git "子模块" 的问题。
2. 使用 --prefer-dist 或设置 `preferred-install` 为 `dist` 到你的
   [config](../04-schema.md#config) 文件中。
3. 安装后删除每个依赖包的 `.git` 目录，然后
   将其添加到你的 git 仓库中。你可以在 ZSH 中执行 `rm -rf vendor/**/.git` 命令，
   或者在 Bash 中执行 `find vendor/ -type d -name ".git" -exec rm -rf {} \;` 命令。
   但是这意味着在你运行 composer update 之前，必须删除这些依赖包。
4. 添加 .gitignore 规则 (`/vendor/**/.git`) 以忽略所有 `.git` 目录。
   此方法不要求你在运行 composer update 之前删除这些依赖包。
