<!--
    tagline: Modify the way certain types of packages are installed
-->

# 设置和使用自定义安装类型

## 概要

有时需要在包的安装过程中执行其它的动作，例如：将它安装在默认的 `vendor` 以外的其它目录。

在这些情况下，你可以考虑创建一个自定义安装程序来处理特定的逻辑。

## 调用自定义安装程序

假设你的项目已经有了一个自定义的安装模块，那么如何根据 [安装类型][1] 正确调用你包文件中的安装程序就成为了一个问题。

> _参见见下一章，如何通过指令创建自定义安装程序。_

任何自定义安装程序都要通过 [type][1] 属性来识别。一旦被确认，它将完全覆盖默认的安装程序，并执行自己的安装逻辑。

一个实际用例：

> phpDocumentor 的特殊模板需要安装在 /vendor 以外的其它目录中。
> 因此他们选择 `phpdocumentor-template` [安装类型][1] 并为此类型创建了一个插件，以便将他们的模板发送到正确的目录中。

在这样一个模板包的例子中 composer.json 将使用以下设置：

```json
{
    "name": "phpdocumentor/template-responsive",
    "type": "phpdocumentor-template",
    "require": {
        "phpdocumentor/template-installer-plugin": "*"
    }
}
```

> **重要提示：** 为了确保这个模板安装程序在安装模板包之前就已存在，模板包必须写入对此安装程序包的依赖。

## 创建一个安装程序

一个自定义安装程序通常是以 Composer 插件的形式存在，并包含有一个类，它实现了 [`Composer\Installer\InstallerInterface`][3] 这个接口。

一个基本的安装程序插件必须由3个文件组成：

1. 包文件：composer.json
2. 插件类，例如：`My\Project\Composer\Plugin.php`，其中的类必须实现 `Composer\Plugin\PluginInterface` 接口。
3. 安装程序类，例如：`My\Project\Composer\Installer.php`，其中的类必须实现 `Composer\Installer\InstallerInterface` 接口。

### composer.json

此处的包文件和普通资源包是相同的，但需要满足以下条件：

1. [type][1] 属性必须是 `composer-plugin`。
2. [extra][2] 属性必须包含 `class` 元素，它定义了插件类的名称（包含命名空间）。如果这个包有多个插件类，可以使用数组的形式进行定义。

实例：

```json
{
    "name": "phpdocumentor/template-installer-plugin",
    "type": "composer-plugin",
    "license": "MIT",
    "autoload": {
        "psr-0": {"phpDocumentor\\Composer": "src/"}
    },
    "extra": {
        "class": "phpDocumentor\\Composer\\TemplateInstallerPlugin"
    },
    "require": {
        "composer-plugin-api": "1.0.0"
    }
}
```

### 插件类

这个类定义了 Composer 的插件，它必须实现 [`Composer\Plugin\PluginInterface`][3] 这个接口。它可以在 `activate()` 方法中注册自定义安装程序。

这个类可以被放在任何位置、使用任何名字，只要能够根据 `extra.class` 中的定义被自动加载即可。

实例：

```php
<?php

namespace phpDocumentor\Composer;

use Composer\Composer;
use Composer\IO\IOInterface;
use Composer\Plugin\PluginInterface;

class TemplateInstallerPlugin implements PluginInterface
{
    public function activate(Composer $composer, IOInterface $io)
    {
        $installer = new TemplateInstaller($io, $composer);
        $composer->getInstallationManager()->addInstaller($installer);
    }
}
```

### 自定义安装程序类

这个类用于执行自定义的安装过程，它必须实现 [`Composer\Installer\InstallerInterface`][4] 这个接口（或者继承了另一个实现此接口的安装程序类）。它将会对 [安装类型][1] 中定义的字符串执行 `supports()` 方法验证，一旦通过就采用对应的安装程序。

> **注意：** _请慎重选择你的 [安装类型][1] 名称，建议遵循这样的格式：`vendor-type`_。例如：`phpdocumentor-template`。

InstallerInterface 类定义了以下方法（请查阅源码以获得更详细的信息）：

* **supports()** 在这里测试你发布的这个安装程序名称是否通过 [安装类型][1] 匹配（参见示例）。只有正确匹配的资源包才会使用此安装程序进行安装。
* **isInstalled()** 确定支持的资源包是否已安装。
* **install()** 这里你可以定义在安装时需要执行的动作。
* **update()** 这里你可以定义在更新时需要执行的动作。当 Composer 调用更新参数时这是必须的。
* **uninstall()** 这里你可以定义在移除一个包时需要执行的动作。
* **getInstallPath()** 这个方法需要返回一个资源包将要安装的位置。_相对于 composer.json 文件的位置。_

实例：

```php
<?php

namespace phpDocumentor\Composer;

use Composer\Package\PackageInterface;
use Composer\Installer\LibraryInstaller;

class TemplateInstaller extends LibraryInstaller
{
    /**
     * {@inheritDoc}
     */
    public function getPackageBasePath(PackageInterface $package)
    {
        $prefix = substr($package->getPrettyName(), 0, 23);
        if ('phpdocumentor/template-' !== $prefix) {
            throw new \InvalidArgumentException(
                'Unable to install template, phpdocumentor templates '
                .'should always start their package name with '
                .'"phpdocumentor/template-"'
            );
        }

        return 'data/templates/'.substr($package->getPrettyName(), 23);
    }

    /**
     * {@inheritDoc}
     */
    public function supports($packageType)
    {
        return 'phpdocumentor-template' === $packageType;
    }
}
```

这个例子演示了，简单的继承 [`Composer\Installer\LibraryInstaller`][5] 类来剥离 `phpdocumentor/template-` 前缀，并用剩余的部分重新组装了一个完全不同的安装路径。

> _并非安装在 `/vendor` 目录，任何使用这个安装程序的资源包，将被放置在 `/data/templates/<stripped name>` 目录中。_

[1]: ../04-schema.md#type
[2]: ../04-schema.md#extra
[3]: https://github.com/composer/composer/blob/master/src/Composer/Plugin/PluginInterface.php
[4]: https://github.com/composer/composer/blob/master/src/Composer/Installer/InstallerInterface.php
[5]: https://github.com/composer/composer/blob/master/src/Composer/Installer/LibraryInstaller.php
