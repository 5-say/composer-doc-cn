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

> phpDocumentor 的特殊模板需要安装在 /vendor 以外的其它目录中。因此他们选择 `phpdocumentor-template` [安装类型][1] 并为此类型创建了一个插件，以便将他们的模板发送到正确的目录中。

在这样一个模板包的例子中 composer.json 将使用以下设置：

    {
        "name": "phpdocumentor/template-responsive",
        "type": "phpdocumentor-template",
        "require": {
            "phpdocumentor/template-installer-plugin": "*"
        }
    }

> **重要提示：** 为了确保这个模板安装程序在安装模板包之前就已存在，模板包必须写入对此安装程序包的依赖。

## 创建一个安装程序

一个自定义安装程序通常是以 Composer 插件的形式存在，并包含有一个类，它实现了 [`Composer\Installer\InstallerInterface`][3] 这个接口。

一个基本的安装程序插件必须由3个文件组成：

1. 包文件：composer.json
2. 插件类，例如：`My\Project\Composer\Plugin.php`，其中的类必须实现 `Composer\Plugin\PluginInterface` 接口。
3. 安装程序类，例如：`My\Project\Composer\Installer.php`，其中的类必须实现 `Composer\Installer\InstallerInterface` 接口。

### composer.json

The package file is the same as any other package file but with the following
requirements:

1. the [type][1] attribute must be `composer-plugin`.
2. the [extra][2] attribute must contain an element `class` defining the
   class name of the plugin (including namespace). If a package contains
   multiple plugins this can be array of class names.

Example:

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

### The Plugin class

The class defining the Composer plugin must implement the
[`Composer\Plugin\PluginInterface`][3]. It can then register the Custom
Installer in its `activate()` method.

The class may be placed in any location and have any name, as long as it is
autoloadable and matches the `extra.class` element in the package definition.

Example:

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

### The Custom Installer class

The class that executes the custom installation should implement the
[`Composer\Installer\InstallerInterface`][4] (or extend another installer that
implements that interface). It defines the [type][1] string as it will be
recognized by packages that will use this installer in the `supports()` method.

> **NOTE**: _choose your [type][1] name carefully, it is recommended to follow
> the format: `vendor-type`_. For example: `phpdocumentor-template`.

The InstallerInterface class defines the following methods (please see the
source for the exact signature):

* **supports()**, here you test whether the passed [type][1] matches the name
  that you declared for this installer (see the example).
* **isInstalled()**, determines whether a supported package is installed or not.
* **install()**, here you can determine the actions that need to be executed
  upon installation.
* **update()**, here you define the behavior that is required when Composer is
  invoked with the update argument.
* **uninstall()**, here you can determine the actions that need to be executed
  when the package needs to be removed.
* **getInstallPath()**, this method should return the location where the
  package is to be installed, _relative from the location of composer.json._

Example:

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

The example demonstrates that it is quite simple to extend the
[`Composer\Installer\LibraryInstaller`][5] class to strip a prefix
(`phpdocumentor/template-`) and use the remaining part to assemble a completely
different installation path.

> _Instead of being installed in `/vendor` any package installed using this
> Installer will be put in the `/data/templates/<stripped name>` folder._

[1]: ../04-schema.md#type
[2]: ../04-schema.md#extra
[3]: https://github.com/composer/composer/blob/master/src/Composer/Plugin/PluginInterface.php
[4]: https://github.com/composer/composer/blob/master/src/Composer/Installer/InstallerInterface.php
[5]: https://github.com/composer/composer/blob/master/src/Composer/Installer/LibraryInstaller.php
