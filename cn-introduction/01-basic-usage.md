<a name="Basic-usage"></a>
# 基本用法

---

- [基本用法](#Basic-usage)
  - [安装](#Installation)
  - [`composer.json`：项目安装](#composer.json-Project-Setup)
    - [关于 `require` Key](#The-require-Key)
    - [包名称](#Package-Names)
    - [包版本](#Package-Versions)
    - [下一个重要版本（波浪号运算符）](#Next-Significant-Release)
    - [稳定性](#Stability)
  - [安装依赖包](#Installing-Dependencies)
  - [`composer.lock` - 锁文件](#composer.lock-The-Lock-File)
  - [Packagist](#Packagist)
  - [自动加载](#Autoloading)

---

<a name="Installation"></a>
## 安装

安装 Composer，你只需要下载 `composer.phar` 可执行文件。

```sh
curl -sS https://getcomposer.org/installer | php
```

详细请查看 [简介](00-intro.md) 章节。

要检查 Composer 是否正常工作，只需要通过 `php` 来执行 PHAR：

```sh
php composer.phar
```

这将返回给你一个可执行的命令列表。

> **注意：** 你也可以仅执行 `--check` 选项而无需下载 Composer。
> 要获取更多的信息请使用 `--help`。
>
> ```sh
> curl -sS https://getcomposer.org/installer | php -- --help
> ```

<a name="composer.json-Project-Setup"></a>
## `composer.json`：项目安装

要开始在你的项目中使用 Composer，你只需要一个 `composer.json` 文件。该文件包含了项目的依赖和其它的一些元数据。

这个 [JSON format](http://json.org/) 是很容易编写的。它允许你定义嵌套结构。

<a name="The-require-Key"></a>
### 关于 `require` Key

第一件事情（并且往往只需要做这一件事），你需要在 `composer.json` 文件中指定 `require` key 的值。你只需要简单的告诉 Composer 你的项目需要依赖哪些包。

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

你可以看到， `require` 需要一个 **包名称** （例如 `monolog/monolog`） 映射到 **包版本** （例如 `1.0.*`） 的对象。
同时你还可以声明需要开发支持特定版本的PHP和PHP扩展你的项目需要成功运行的参考（例下）
```json
{
    "require" : {
        "php" : "^5.5 || ^7.0",
        "ext-mbstring": "*"
    }
}
```
<a name="Package-Names"></a>
### 包名称

包名称由供应商名称和其项目名称构成。通常容易产生相同的项目名称，而供应商名称的存在则很好的解决了命名冲突的问题。它允许两个不同的人创建同样名为 `json` 的库，而之后它们将被命名为 `igorw/json` 和 `seldaek/json`。

这里我们需要引入 `monolog/monolog`，供应商名称与项目的名称相同，对于一个具有唯一名称的项目，我们推荐这么做。它还允许以后在同一个命名空间添加更多的相关项目。如果你维护着一个库，这将使你可以很容易的把它分离成更小的部分。

<a name="Package-Versions"></a>
### 包版本

在前面的例子中，我们引入的 monolog 版本指定为 `1.0.*`。这表示任何从 `1.0` 开始的开发分支，它将会匹配 `1.0.0`、`1.0.2` 或者 `1.0.20`。

版本约束可以用几个不同的方法来指定。

<table>
    <thead>
        <tr>
            <th>名称</th><th>实例</th><th>描述</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>确切的版本号</td>
            <td><code>1.0.2</code></td>
            <td>你可以指定包的确切版本。</td>
        </tr>
        <tr>
            <td>范围</td>
            <td>
                <code>&gt;=1.0</code>
                <code>&gt;=1.0,&lt;2.0</code>
                <code>&gt;=1.0,&lt;1.1|&gt;=1.2</code>
            </td>
            <td>通过使用比较操作符可以指定有效的版本范围。
            <br />有效的运算符：<code>&gt;</code>、<code>&gt;=</code>、<code>&lt;</code>、<code>&lt;=</code>、<code>!=</code>。
            <br />你可以定义多个范围，用逗号隔开，这将被视为一个<strong>逻辑AND</strong>处理。一个管道符号<code>|</code>将作为<strong>逻辑OR</strong>处理。
            <br />AND 的优先级高于 OR。</td>
        </tr>
        <tr>
            <td>通配符</td>
            <td><code>1.0.*</code></td>
            <td>你可以使用通配符<code>*</code>来指定一种模式。<code>1.0.*</code>与<code>&gt;=1.0,&lt;1.1</code>是等效的。</td>
        </tr>
        <tr>
            <td>赋值运算符</td>
            <td><code>~1.2</code></td>
            <td>这对于遵循语义化版本号的项目非常有用。<code>~1.2</code>相当于<code>&gt;=1.2,&lt;2.0</code>。想要了解更多，请阅读下一小节。</td>
        </tr>
        
        <tr>
            <td>插入符号</td>
            <td><code> ^1.2.3</code></td>
            <td>^操作符的行为很相似，但它更接近于语义，并将永远让不间断更新。例如<code>^1.2.3</code>相当于<code>>=1.2.3<2.0.0</code>如没有释放到2.0应该打破向后兼容性。同时也可以使用限制版本，如<code>^0.3</code>为<code>>=0.3.0<0.4.0 </code>。</td>
        </tr>
    </tbody>
</table>

<a name="Next-Significant-Release"></a>
### 下一个重要版本（波浪号运算符）

`~` 最好用例子来解释： `~1.2` 相当于 `>=1.2,<2.0`，而 `~1.2.3` 相当于 `>=1.2.3,<1.3`。正如你所看到的这对于遵循 [语义化版本号](http://semver.org/) 的项目最有用。一个常见的用法是标记你所依赖的最低版本，像 `~1.2` （允许1.2以上的任何版本，但不包括2.0）。由于理论上直到2.0应该都没有向后兼容性问题，所以效果很好。你还会看到它的另一种用法，使用 `~` 指定最低版本，但允许版本号的最后一位数字上升。

> **注意：** 虽然 `2.0-beta.1` 严格地说是早于 `2.0`，但是，根据版本约束条件，
> 例如 `~1.2` 却不会安装这个版本。就像前面所讲的 `~1.2` 只意味着 `.2`
> 部分可以改变，但是 `1.` 部分是固定的。

<a name="Stability"></a>
### 稳定性

默认情况下只有稳定的发行版才会被考虑在内。如果你也想获得 RC、beta、alpha 或 dev 版本，你可以使用 [稳定标志](04-schema.md#Package-links)。你可以对所有的包做 [最小稳定性](04-schema.md#minimum-stability) 设置，而不是每个依赖逐一设置。

<a name="Installing-Dependencies"></a>
## 安装依赖包

获取定义的依赖到你的本地项目，只需要调用 `composer.phar` 运行 `install` 命令。

```sh
php composer.phar install
```

接着前面的例子，这将会找到 `monolog/monolog` 的最新版本，并将它下载到 `vendor` 目录。
这是一个惯例把第三方的代码到一个指定的目录 `vendor`。如果是 monolog 将会创建 `vendor/monolog/monolog` 目录。

> **小技巧：** 如果你正在使用Git来管理你的项目，
> 你可能要添加 `vendor` 到你的 `.gitignore` 文件中。
> 你不会希望将所有的代码都添加到你的版本库中。

另一件事是 `install` 命令将创建一个 `composer.lock` 文件到你项目的根目录中。

<a name="composer.lock-The-Lock-File"></a>
## `composer.lock` - 锁文件

在安装依赖后，Composer 将把安装时确切的版本号列表写入 `composer.lock` 文件。这将锁定该项目的特定版本。

**请提交你应用程序的 `composer.lock` （包括 `composer.json`）到你的版本库中**

这是非常重要的，因为 `install` 命令将会检查锁文件是否存在，如果存在，它将下载指定的版本（忽略 `composer.json` 文件中的定义）。

这意味着，任何人建立项目都将下载与指定版本完全相同的依赖。你的持续集成服务器、生产环境、你团队中的其他开发人员、每件事、每个人都使用相同的依赖，从而减轻潜在的错误对部署的影响。即使你独自开发项目，在六个月内重新安装项目时，你也可以放心的继续工作，即使从那时起你的依赖已经发布了许多新的版本。

如果不存在 `composer.lock` 文件，Composer 将读取 `composer.json` 并创建锁文件。

这意味着如果你的依赖更新了新的版本，你将不会获得任何更新。此时要更新你的依赖版本请使用 `update` 命令。这将获取最新匹配的版本（根据你的 `composer.json` 文件）并将新版本更新进锁文件。

```sh
php composer.phar update
```

如果只想安装或更新一个依赖，你可以白名单它们：

```sh
php composer.phar update monolog/monolog [...]
```

> **注意：** 对于库，并不一定建议提交锁文件
> 请参考：[库的锁文件](02-libraries.md#Lock-file).

<a name="Packagist"></a>
## Packagist

[packagist](https://packagist.org/) 是 Composer 的主要资源库。 一个 Composer 的库基本上是一个包的源：记录了可以得到包的地方。Packagist 的目标是成为大家使用库资源的中央存储平台。这意味着你可以 `require` 那里的任何包。

当你访问 [packagist](https://packagist.org/) 网站 (packagist.org)，你可以浏览和搜索资源包。

任何支持 Composer 的开源项目应该发布自己的包在 packagist 上。虽然并不一定要发布在 packagist 上来使用 Composer，但它使我们的编程生活更加轻松。

<a name="Autoloading"></a>
## 自动加载

对于库的自动加载信息，Composer 生成了一个 `vendor/autoload.php` 文件。你可以简单的引入这个文件，你会得到一个免费的自动加载支持。

```php
require 'vendor/autoload.php';
```

这使得你可以很容易的使用第三方代码。例如：如果你的项目依赖 monolog，你就可以像这样开始使用这个类库，并且他们将被自动加载。

```php
$log = new Monolog\Logger('name');
$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));

$log->addWarning('Foo');
```

你可以在 `composer.json` 的 `autoload` 字段中增加自己的 autoloader。

```json
{
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```

Composer 将注册一个 [PSR-4](http://www.php-fig.org/psr/psr-4/) autoloader 到 `Acme` 命名空间。

你可以定义一个从命名空间到目录的映射。此时 `src` 会在你项目的根目录，与 `vendor` 文件夹同级。例如 `src/Foo.php` 文件应该包含 `Acme\Foo` 类。

添加 `autoload` 字段后，你应该再次运行 `install` 命令来生成 `vendor/autoload.php` 文件。

引用这个文件也将返回 autoloader 的实例，你可以将包含调用的返回值存储在变量中，并添加更多的命名空间。这对于在一个测试套件中自动加载类文件是非常有用的，例如。

```php
$loader = require 'vendor/autoload.php';
$loader->addPsr4('Acme\\Test\\', __DIR__);
```

除了 PSR-4 自动加载，Composer 同时支持PSR-0、classmap 和 files自动加载。详细请查看 [自动加载-参考](04-schema.md#autoload)。

> **注意：** Composer 提供了自己的 autoloader。如果你不想使用它，你可以仅仅引入 `vendor/composer/autoload_*.php` 文件，它返回一个关联数组，你可以通过这个关联数组配置自己的 autoloader。

&larr; [简介](00-intro.md)  |  [库（资源包）](02-libraries.md) &rarr;
