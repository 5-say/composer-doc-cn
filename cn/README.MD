## 文档

- [简介](./00-intro.md)
- [基本用法](./01-basic-usage.md)
- [库](./02-libraries.md)
- [命令行界面 / 命令](./03-cli.md)
- [composer.json 文件结构](./04-schema.md)
- [资源仓库](./05-repositories.md)
- [配置](./06-config.md)
- [社区](./07-community.md)


## 文章

- [别名](./articles/aliases.md) 
版本分支别名
- [自动加载优化](./articles/autoloader-optimization.md) 
如何减少自动加载机制的性能影响
- [设置和使用自定义安装程序](./articles/custom-installers.md) 
修改某些类型的包的安装方式
- [处理私人包](./articles/handling-private-packages-with-satis.md) 
托管和安装私人的 Composer packages
- [HTTP 基础认证](./articles/http-basic-authentication.md) 
访问私有包
- [设置和使用插件](./articles/plugins.md) 
修改并扩展 Composer 的功能
- [脚本](./articles/scripts.md) 
脚本是在安装包之前或之后调用的回调
- [排除故障](./articles/troubleshooting.md) 
解决问题
- [Vendor 二进制文件与 `vendor/bin` 目录](./articles/vendor-binaries.md) 
从包中暴露命令行脚本
- [版本与约束](./articles/versions.md) 
版本详解


## 常见问题

- [如何将一个包安装到我的框架的自定义路径中？](./faqs/how-do-i-install-a-package-to-a-custom-path-for-my-framework.md)
- [如何以编程方式安装 Composer？](./faqs/how-to-install-composer-programmatically.md)
- [如何安全地安装不受信任的包？使用超级用户或 root 用户运行 Composer 是安全的吗？](./faqs/how-to-install-untrusted-packages-safely.md)
- [我应该提交我的 vendor 目录里的依赖项吗？](./faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md)
- [为什么不绑定版本约束是个坏主意？](./faqs/why-are-unbound-version-constraints-a-bad-idea.md)
- [为什么在版本约束中将比较符号与通配符结合使用是个坏主意？](./faqs/why-are-version-constraints-combining-comparisons-and-wildcards-a-bad-idea.md)
- [为什么 Composer 不能递归加载依赖项？](./faqs/why-can't-composer-load-repositories-recursively.md)