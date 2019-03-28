# 配置

本章节将介绍 `composer.json` 配置文件中的 `config` 配置段，
另参考 [schema](04-schema.md) 文档。

## process-timeout

默认值为 `300`。 像 git clone 这种耗时的操作，
在 Composer 认定其操作失败之前会一直运行。如果你的网速较慢或者要下载的依赖包数量很大的话，
可以调高此配置项的值。

## use-include-path

默认值为 `false`。如果设置为 `true`，则 Composer 的自动加载器（autoloader）将会在 PHP 的 
include_path 路径中查找所需要的类（class）。

## preferred-install

默认值为 `auto` ，全部可选值为 `source`、`dist` 或 `auto`。此配置项
用于设置 Composer 安装依赖包时的首选安装方式。
还可以设置为一个包含模式匹配的键/值对表，能够细化到针对单个依赖包进行精确设置。

```json
{
    "config": {
        "preferred-install": {
            "my-organization/stable-package": "dist",
            "my-organization/*": "source",
            "partner-organization/*": "auto",
            "*": "dist"
        }
    }
}
```

> **注意：** 顺序很重要。更严格的模式匹配应该放在
> 更宽松的模式匹配前边（也就是说依赖包匹配的越精确，设置就越能生效）。
> 在全局和单个依赖包的设置中，如果将字符串标记值（source、dist 或 auto）和键/值对列表混合使用的话，
> 字符串标记值将被转换为 `*` 模式匹配。

## store-auths

认证框弹出后执行什么动作，可选值为：`true` （永久保存）、
`false` （不保存） 和 `"prompt"` （每次都询问），默认值为 `"prompt"`。

## github-protocols

默认值为 `["https", "ssh", "git"]`。从 github.com 请求克隆时可以使用的协议列表，
按优先级依次排列。默认情况下，只有当 [secure-http](#secure-http) 被关闭时才会默认采用 `git` 协议， 
这是因为 git 协议是未加密的。如果希望源站的远程
推送 URL 使用 https 而非 ssh 协议(`git@github.com:...`)，那么将协议
列表设置为 `["https"]` ，那么 Composer 将不会把推送 URL 替换为 ssh
URL。

## github-oauth

一个域名（domain name）和 oauth 密钥的列表。例如，使用 `{"github.com":
"oauthtoken"}` 作为此配置项的值，将使用 `oauthtoken` 访问
github 上的私有仓库，并绕过其基于 IP 地址
的 API 调用频率限制。[阅读
更多信息](articles/troubleshooting.md#api-rate-limit-and-oauth-tokens) 以
了解 GitHub 的 OAuth 令牌。

## gitlab-oauth

一个域名（domain name）和 oauth 密钥的列表。例如，使用 `{"gitlab.com":
"oauthtoken"}` 作为此配置项的值，将使用 `oauthtoken` 访问
gitlab 上的私有仓库。请注意：如果软件包（package）并未托管在 
gitlab.com 上，则必须通过 
[`gitlab-domains`](06-config.md#gitlab-domains) 配置项设置域名。

## gitlab-token

一个域名（domain name）和私有令牌（private token）组成的列表。例如，将 `{"gitlab.com":
"privatetoken"}` 作为此配置项的值，将使用 `privatetoken` 用于访问
gitlab 上的私有仓库。请注意：如果软件包（package）并非托管在 
gitlab.com 上，你还必须将实际所用的域名（domain name）设置到
[`gitlab-domains`](06-config.md#gitlab-domains) 配置项中。

## disable-tls

默认值为 `false`。如果设置为 true，所有 HTTPS 链接都将被替换为 HTTP 链接，
并且不会执行网络层加密。开启此选项将会带来
安全风险，因此强烈 **不推荐**。更好的方式是在 php.ini 配置文件中开启
php_openssl 扩展模块。

## secure-http

默认值为 `true`。如果设置为 true，只有 HTTPS 链接才被允许
经过 Composer 下载。如果你真的确定需要采用 HTTP 协议访问某些资源的话，
可以设置为 false，但是通过 [Let's Encrypt](https://letsencrypt.org/) 
获取一个免费的 SSL 证书才是一个更好的选择。

## bitbucket-oauth

一个域名（domain name）和 consumer 组成的列表。例如设置为 `{"bitbucket.org":
{"consumer-key": "myKey", "consumer-secret": "mySecret"}}`。请参考[此篇文章](https://confluence.atlassian.com/bitbucket/oauth-on-bitbucket-cloud-238027431.html)
以了解如何在 Bitbucket 上设置一个 consumer。

## cafile

认证证书（Certificate Authority）文件在本地文件系统中的位置。在 PHP 5.6+ 版本中，你
应当通过 php.ini 文件中的 openssl.cafile 配置项进行设置，即便 PHP 5.6+ 版本
能够自动检测到你的系统 CA 文件。

## capath

如果未设置 cafile 配置项，或者并未在 cafile 指定的位置找到证书，
则会在 capath 所指向的目录将中查找合适的证书。
capath 所指向的目录必须包含的是经过正确散列计算的证书。

## http-basic

用于进行身份验证的域名（domain name）和用户名/密码（username/passwords）列表。
例如，使用 `{"example.org": {"username": "alice", "password": "foo"}}` 作为
此配置项的值将允许 Composer 通过 example.org 的身份验证。

> **注意：** 与身份验证相关的配置信息，例如 `http-basic` 和
> `github-oauth`，也可以放在 `auth.json` 文件中，
> 而无需放在 `composer.json` 文件中。这样就可以让 git 忽略这些带有敏感信息的文件，并且每个
> 开发人员都可以在其中放置自己的验证信息。

## platform

用于伪造平台（PHP 及其扩展模块）信息，以便可以模拟
生产环境或者在配置文件中对目标运行平台进行指定。例如： `{"php":
"7.0.3", "ext-something": "4.0.3"}`。

## vendor-dir

默认值为 `vendor`。你可以将依赖包安装到你所指定的目录下。
`$HOME` 和 `~` 将会被替换为你的 home 目录路径，
这一替换规则对于 vendor-dir 及以下所有类似 `*-dir` 的配置项都适用。

## bin-dir

默认值为 `vendor/bin`。如果你的项目中包含可执行文件，那么这些文件都会被
在此目录中建立符号链接。

## data-dir

在 Windows 系统中的默认值为 `C:\Users\<user>\AppData\Roaming\Composer`，
在遵循 XDG Base Directory Specifications 标准的 unix 系统中的默认值为 `$XDG_DATA_HOME/composer`，
在其他 unix 系统中的默认值为 `$home` 。目前，此配置项的作用仅仅是
存储旧版本的 composer.phar 文件，以便能够回滚到旧
版本。另请参见 [COMPOSER_HOME](03-cli.md#composer-home)。

## cache-dir

在 Windows 系统中的默认值为 `C:\Users\<user>\AppData\Local\Composer`；
在遵循 XDG Base Directory Specifications 的 unix 系统中的默认值为 `$XDG_CACHE_HOME/composer`；
在其他 unix 系统中的默认值为 `$home/cache`。用于存放 
Composer 用到的所有缓存文件。详情请见 [COMPOSER_HOME](03-cli.md#composer-home)。

## cache-files-dir

默认值为 `$cache-dir/files`。用于存放软件包（package）的 zip 文件。

## cache-repo-dir

默认值为 `$cache-dir/repo`。存储 `composer` 类型的仓库
以及 `svn`、`fossil`、`github` 和 `bitbucket` 类型的 VCS 仓库的元数据。

## cache-vcs-dir

默认值为 `$cache-dir/vcs`。存放克隆的 VCS 仓库，用于加载
`git`/`hg` 类型的 VCS 仓库的元数据，并加快安装速度。

## cache-files-ttl

默认值为 `15552000` （即 6 个月）。Composer 默认会缓存所有下载的安装文件（zip、tar 等文件）。
默认情况下，6 个不使用就会被清除。
此配置项允许你调整这个时间周期（以秒为单位）或者
将其设置为 0 从而完全禁止。

## cache-files-maxsize

默认值为 `300MiB`。Composer 默认会缓存所有下载的安装文件（zip、tar 等文件）。
如果垃圾收集器定期执行回收任务，此设置项将
是这些缓存文件所能占用的最大空间值。旧（或不常用）的文件将首先被删除，
直到缓存所占用的空间不超过此值。

## bin-compat

默认值为 `auto`。用于确定需要安装的可执行文件的兼容性。
如果设置为 `auto`，那么 Composer 在 Windows 上只安装 .bat 文件。如果
设置为 `full`，那么用于 Windows 的 .bat 文件和用于类 Unix 系统的脚本文件
都将被安装。
如果是在 linux 虚拟机中运行 Composer，但仍希望 .bat 文件可以用于 Windows 宿主系统，
那么这将会非常有用。

## prepend-autoloader

默认值为 `true`。如果设置为 `false`，Composer 自动加载器（autoloader）将不会添加到
已有自动加载器的前边。有时需要这样做来修复
与其他自动加载器的互操作性问题。

## autoloader-suffix

默认值为 `null`。此配置项的值将被用作 Composer 自动加载器（autoloader）的后缀。
如果设置为 null，则会用一组随机字符串作为后缀。

## optimize-autoloader

默认值为 `false`。如果设置为 `true`，则每次导出自动加载器（autoloader）时都会进行优化。

## sort-packages

默认值为 `false`。如果设置为 `true`，那么在通过 `require` 命令添加一个新的软件包（package）时，
`composer.json` 中的软件包列表将按照字母顺序进行排列。

## classmap-authoritative

默认值为 `false`。如果设置为 `true`，Composer 自动加载器（autoloader）将只
加载 classmap 中的类（class）。. Implies `optimize-autoloader`.

## apcu-autoloader

默认值为 `false`。如果设置为 `true`，Composer 自动加载器（autoloader）将检查 APCu，
如果 APCu 扩展模块已经被开启，则用来缓存已找到或未找到的类（class）。

## github-domains

默认值为 `["github.com"]`。用于 github 模式的一个域名列表。此配置项用于
专用于 GitHub 企业版（Enterprise）。

## github-expose-hostname

默认值为 `true`。如果设置为 `false`，则用于访问 github API 而创建的 OAuth 令牌
将只包含日期而没有主机名。

## gitlab-domains

默认值为 `["gitlab.com"]`。GitLab 服务器域名列表。
用于 `gitlab` 仓库类型。

## use-github-api

默认值为 `true`。与针对特定仓库设置 `no-api` 类似，
将 `use-github-api` 设置为 `false` 将在全局定义
所有 GitHub 仓库的克隆行为，就像克隆任何其他 git 仓库一样，
而不是使用 GitHub API。但是与直接使用 `git` 工具
不同，Composer 仍将尝试使用 GitHub 上存储的 zip 文件。

## notify-on-install

默认值为 `true`。Composer 允许仓库定义一个通知 URL，
以便从此仓库安装软件包（package）后接收通知。
你可以通过此配置项禁止此行为。

## discard-changes

默认值为 `false`，全部可选值为 `true`、`false` 和 `"stash"`。此配置项
允许你设置在非交互模式下处理脏更新（dirty updates，也就是 vendor 目录下的内容被修改了）的默认行为。
如果设置为 `true`，则将总是丢弃 vendor 目录下的修改，如果设置为
`"stash"` 则将尝试暂存并恢复。对于 CI 服务器或部署脚本。
如果你有可能会修改 vendor 目录下的内容，可以使用此配置项。

## archive-format

默认值为 `tar`。当工作流程中需要创建一个专用归档文件时，
Composer 允许你设置默认的归档文件格式。

## archive-dir

默认值为 `.`。Composer 允许你通过此配置项设置归档文件的默认存储目录。
以便当工作流程中需要创建特定归档格式的文件时存放于此目录下。或者说是让模块间的开发
变得更容易。

例如：

```json
{
    "config": {
        "archive-dir": "/home/user/.composer/repo"
    }
}
```

## htaccess-protect

默认值为 `true`。如果设置为 `false`，Composer 将不会
在 composer 自己的主目录（home）、缓存目录和数据目录下创建 `.htaccess` 文件。

&larr; [Repositories](05-repositories.md)  |  [Community](07-community.md) &rarr;
