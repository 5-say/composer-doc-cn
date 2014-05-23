<!--
    tagline: Modify and extend Composer's functionality
-->

# 设置和使用插件

## 概述

你可能需要改变和扩展 Composer 加入自定义的功能。例如：如果你的环境对 Composer 的行为有特殊要求，而这并不适用于普通用户。又或者，你想使用 Composer 完成某些事情，而又不希望对普通用户造成影响。

在这些情况下，你可以考虑创建一个插件来处理特定的逻辑。

## 创建一个插件

一个插件就是一个普通的 Composer 资源包，插件的主要代码都位于这个包中，并且还可以依赖更多其它的资源包。

### 插件包

插件包内的文件和普通资源包相同，但必须满足下列要求：

1. [type][1] 属性必须是 `composer-plugin`。
2. [extra][2] 属性必须包含一个元素 `class`，它定义了插件类的名称（包含命名空间）。如果一个包中含有多个插件，则可以使用数组来定义类名。

此外，你必须 require 一个特殊的资源包 `composer-plugin-api`，定义与你的插件相兼容的 composer plugin API 版本。目前 composer plugin API 的版本为1.0.0。

实例：

```json
{
    "name": "my/plugin-package",
    "type": "composer-plugin",
    "require": {
        "composer-plugin-api": "1.0.0"
    }
}
```

### 插件类

每一个插件都必须提供一个实现了 [`Composer\Plugin\PluginInterface`][3] 接口的类。类中的 `activate()` 方法在插件载入后被调用，并接收两个类的实例：[`Composer\Composer`][4] 和 [`Composer\IO\IOInterface`][5]。使用这两个对象可以读取所有的配置，操作所有的内部对象和状态。

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

## 事件处理程序

此外，插件还可以实现 [`Composer\EventDispatcher\EventSubscriberInterface`][6] 接口，为了在插件载入后自动注册事件处理程序到 `EventDispatcher`。

插件可用事件如下：

* **COMMAND** 在载入插件的所有命令前被调用。它为你提供了访问程序输入输出的对象。
* **PRE_FILE_DOWNLOAD** 在文件下载前被触发，它允许你在下载前根据目标 URL 操作 `RemoteFilesystem` 对象。

> 一个插件也可以订阅 [脚本事件][7]。

实例：

```php
<?php

namespace Naderman\Composer\AWS;

use Composer\Composer;
use Composer\EventDispatcher\EventSubscriberInterface;
use Composer\IO\IOInterface;
use Composer\Plugin\PluginInterface;
use Composer\Plugin\PluginEvents;
use Composer\Plugin\PreFileDownloadEvent;

class AwsPlugin implements PluginInterface, EventSubscriberInterface
{
    protected $composer;
    protected $io;

    public function activate(Composer $composer, IOInterface $io)
    {
        $this->composer = $composer;
        $this->io = $io;
    }

    public static function getSubscribedEvents()
    {
        return array(
            PluginEvents::PRE_FILE_DOWNLOAD => array(
                array('onPreFileDownload', 0)
            ),
        );
    }

    public function onPreFileDownload(PreFileDownloadEvent $event)
    {
        $protocol = parse_url($event->getProcessedUrl(), PHP_URL_SCHEME);

        if ($protocol === 's3') {
            $awsClient = new AwsClient($this->io, $this->composer->getConfig());
            $s3RemoteFilesystem = new S3RemoteFilesystem($this->io, $event->getRemoteFilesystem()->getOptions(), $awsClient);
            $event->setRemoteFilesystem($s3RemoteFilesystem);
        }
    }
}
```

## 使用插件

插件包是优先被安装和加载的，在 Composer 启动时，如果在已安装资源包的当前项目列表中被找到，它们将被自动加载。此外，所有的插件包将被安装在 `COMPOSER_HOME` 目录，以便在 Composer 全局命令前载入，在操作本地项目前插件就已经被载入。

> 你可以通过 `--no-plugins` 选项让 composer 的命令禁用所有已经安装的插件。这或许是特别有用的，如果任何插件会导致错误，而你想要升级或卸载它。

[1]: ../04-schema.md#type
[2]: ../04-schema.md#extra
[3]: https://github.com/composer/composer/blob/master/src/Composer/Plugin/PluginInterface.php
[4]: https://github.com/composer/composer/blob/master/src/Composer/Composer.php
[5]: https://github.com/composer/composer/blob/master/src/Composer/IO/IOInterface.php
[6]: https://github.com/composer/composer/blob/master/src/Composer/EventDispatcher/EventSubscriberInterface.php
[7]: ./scripts.md#event-names
