# 我应该提交 vendor 目录中的依赖包吗？

一般情况下 **不建议**。vendor 目录（或者你安装依赖的其它目录）都应该被添加进 `.gitignore`/`svn:ignore`/等等。

最好这么做，然后让所有开发人员使用 Composer 来安装依赖包。同样，build server、CI、deployment tools 等等，应进行修改，使运行 Composer 成为其项目引导的一部分。

虽然在某些环境下提交它是很让人心动的，但它将导致一些问题：

- 当你更新代码时，将极大的增加 VCS 仓库的体积和差异。
- 在你自己的 VCS 中将产生与你依赖的资源包重复的历史记录。
- 通过 git 的一个 git 仓库安装添加依赖，将把它们视作子模块。这是有问题的，因为它们并不是真正的子模块，并且你将会遇到这些问题。

如果你真的觉得你必须这样做，你有几个选择：

1. 限制自己安装标记版本（无 dev 版本），这样你只会得到 zip 压缩的安装，并避免 git“子模块”出现的问题。
2. 使用 --prefer-dist 或在 [config](../04-schema.md#config) 选项中设置 `preferred-install` 为 `dist`。
3. 在每一个依赖安装后删除其下的 `.git` 文件夹，然后你就可以添加它们到你的 git repo 中。你可以运行 `rm -rf vendor/**/.git` 命令快捷的操作，但这意味着你在运行 composer update 命令前需要先删除磁盘中的依赖文件。
4. 新增一个 .gitignore 规则（`vendor/.git`）来忽略 vendor 下所有 `.git` 目录。这种方法不需要你在运行 composer update 命令前删除你磁盘中的依赖文件。
