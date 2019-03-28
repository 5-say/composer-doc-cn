# 如何安全地安装不受信任的软件包？以超级用户或 root 身份运行 Composr 安全吗？

某些 Composer 命令，包括 `exec`、`install` 和 `update` 允许第三方代码
在系统上执行。这源自于 Composer 的 "插件（plugins）" 和 "脚本（scripts）" 功能。插件和脚本拥有
运行 Composer 的账户的所有权限。因此，强烈建议 
**避免以超级用户或 root 用户的身份来运行 Composer**。

你可以在安装或更新软件包（package）期间禁用插件和脚本，
这样就只能执行 Composer 的代码而不执行第三方的代码了。方法如下：

```sh
composer install --no-plugins --no-scripts ...
composer update --no-plugins --no-scripts ...
```

`exec` 命令始终以运行 `composer` 的用户的身份执行第三方代码。

在某些情况下，比如 CI（持续集成） 系统中或需要安装不受信息的依赖包的情况下，
最安全的方法时执行上面的命令以禁止第三方插件和脚本的执行。
