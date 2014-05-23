<!--
    tagline: Script are callbacks that are called before/after installing packages
-->

# 脚本

## 什么是脚本？

一个脚本，在 Composer 中，可以是一个 PHP 回调（定义为静态方法）或任何命令行可执行的命令。脚本对于在 Composer 运行过程中，执行一个资源包的自定义代码或包专用命令是非常有用的。

> **注意：**只有在根包的 `composer.json` 中定义的脚本才会被执行。即便根包的外部依赖定义了其自身的脚本，Composer 也不会去执行这些额外的脚本。

<a name="event-names"></a>
## 事件名称

Composer 在运行过程中将会触发以下事件：

|事件名称|详细说明
|---|---
| **pre-install-cmd** | 在 `install` 命令执行前触发。
| **post-install-cmd** | 在 `install` 命令执行后触发。
| **pre-update-cmd** | 在 `update` 命令执行前触发。
| **post-update-cmd** | 在 `update` 命令执行后触发。
| **pre-status-cmd** | 在 `status` 命令执行前触发。
| **post-status-cmd** | 在 `status` 命令执行后触发。
| **pre-package-install** | 在资源包安装前触发。
| **post-package-install** | 在资源包安装后触发。
| **pre-package-update** | 在资源包更新前触发。
| **post-package-update** | 在资源包更新后触发。
| **pre-package-uninstall** | 在资源包被卸载前触发。
| **post-package-uninstall** | 在资源包被卸载后触发。
| **pre-autoload-dump** | 在自动加载器被转储前触发，无论是 `install`/`update` 还是 `dump-autoload` 命令都会触发。
| **post-autoload-dump** | 在自动加载器被转储后触发，无论是 `install`/`update` 还是 `dump-autoload` 命令都会触发。
| **post-root-package-install** | 在 `create-project` 命令期间，根包安装完成后触发。
| **post-create-project-cmd** | 在 `create-project` 命令执行后触发。 
- **pre-archive-cmd**: occurs before the `archive` command is executed.
- **post-archive-cmd**: occurs after the `archive` command is executed.

> **注意：**Composer 不会去执行任何依赖包中定义的 `install` 或 `update` 相关脚本。因此你不应该在依赖包中申明 `pre-update-cmd` 或 `pre-install-cmd`。如果你需要在执行 `install` 或 `update` 命令前使用脚本，请确保它们已被定义在根包中。

## 定义脚本

在 `composer.json` 的根 JSON 对象中应该有一个名为 `"scripts"` 的属性，它包含有一系列的事件名称，以及对应的事件脚本。一个事件的脚本可以被定义为一个字符串（仅适用于单个脚本）或数组（单个或多个脚本）。

对于任何给定的事件：

- 脚本将按照事件和定义的顺序触发。
- 一个脚本数组可以包含 PHP 回调和命令行可执行命令。
- 由 PHP 类文件包含的回调，其存放的位置必须确保 Composer 能够正确的载入。

脚本定义实例：

```json
{
    "scripts": {
        "post-update-cmd": "MyVendor\\MyClass::postUpdate",
        "post-package-install": [
            "MyVendor\\MyClass::postPackageInstall"
        ],
        "post-install-cmd": [
            "MyVendor\\MyClass::warmCache",
            "phpunit -c app/"
        ]
    }
}
```

使用前面定义的例子，这里的 `MyVendor\MyClass` 类，就可以被使用来执行 PHP 的回调：

```php
<?php

namespace MyVendor;

use Composer\Script\Event;

class MyClass
{
    public static function postUpdate(Event $event)
    {
        $composer = $event->getComposer();
        // do stuff
    }

    public static function postPackageInstall(Event $event)
    {
        $installedPackage = $event->getOperation()->getPackage();
        // do stuff
    }

    public static function warmCache(Event $event)
    {
        // make cache toasty
    }
}
```

当一个事件被触发，Composer 的内部事件处理程序将接收一个 `Composer\Script\Event` 对象，这是传递给您的 PHP 回调的第一个参数。这个 `Event` 对象拥有一些 getter 方法来帮助你取得当前事件的上下文：

- `getComposer()`: 返回当前的 `Composer\Composer` 对象实例。
- `getName()`: 返回事件名称的字符串。
- `getIO()`: 返回当前的 输入\输出 流，它实现了 `Composer\IO\IOInterface` 接口，以便在控制台中使用。

## 手动运行脚本

如果你想手动运行事件脚本，可以使用下面的语法结构：

```sh
composer run-script [--dev] [--no-dev] script
```

例如 `composer run-script post-install-cmd` 将会运行所有 **post-install-cmd** 事件下定义的脚本。
