# 如何为我的框架自定义一个资源包安装目录？

每个框架都可能有一个或多个不同的依赖包安装目录。Composer 可以通过使用 [composer/installers](https://github.com/composer/installers) 来配置，安装依赖到其它的目录，而不是默认的 `vendor` 目录。

如果你是一个 **包作者** 并且希望自己的资源包被安装到自定义的目录中，简单的 require `composer/installers` 依赖，并设置适当的 `type` 属性。这是常见的方式，如果你的资源包专门用于一个框架，如 CakePHP、Drupal 或 WordPress。这是一个 WordPress 主题的 composer.json 文件的例子：

```json
{
    "name": "you/themename",
    "type": "wordpress-theme",
    "require": {
        "composer/installers": "~1.0"
    }
}
```

现在当你用 Composer 安装这个主题时，它就会被放置在 `wp-content/themes/themename/` 目录。目前已被支持的 `type` 类型请查看 [current supported types](https://github.com/composer/installers#current-supported-types)。

对于一个 **包使用者** 你可以为一个包设置或覆盖安装路径，requires composer/installers 并在 extra 下设置 `installer-paths` 属性。Drupal 的多站点设置就是一个很好的例子，其中的资源包应该被安装到各自网站的子目录。在这里我们使用 composer/installers 来覆盖安装路径：

```json
{
    "extra": {
        "installer-paths": {
            "sites/example.com/modules/{$name}": ["vendor/package"]
        }
    }
}
```

现在该资源包将被安装到你指定的目录，并替换 `$name` 变量，而不是默认目录。

> **注意：** 你不能通过它改变所有包的安装目录。这仅适用于引入 `composer/installers` 依赖，自定义了 type 属性的资源包。
