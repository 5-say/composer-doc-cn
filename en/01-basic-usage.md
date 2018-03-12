# Basic usage

## Introduction

For our basic usage introduction, we will be installing `monolog/monolog`,
a logging library. If you have not yet installed Composer, refer to the
[Intro](00-intro.md) chapter.

> **Note:** for the sake of simplicity, this introduction will assume you
> have performed a [local](00-intro.md#locally) install of Composer.

## `composer.json`: Project Setup

To start using Composer in your project, all you need is a `composer.json`
file. This file describes the dependencies of your project and may contain
other metadata as well.

### The `require` Key

The first (and often only) thing you specify in `composer.json` is the
[`require`](04-schema.md#require) key. You are simply telling Composer which
packages your project depends on.

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

As you can see, [`require`](04-schema.md#require) takes an object that maps
**package names** (e.g. `monolog/monolog`) to **version constraints** (e.g.
`1.0.*`).

Composer uses this information to search for the right set of files in package
"repositories" that you register using the [`repositories`](04-schema.md#repositories)
key, or in Packagist, the default package repository. In the above example,
since no other repository has been registered in the `composer.json` file, it is
assumed that the `monolog/monolog` package is registered on Packagist. (See more
about Packagist [below](#packagist), or read more about repositories
[here](05-repositories.md)).

### Package Names

The package name consists of a vendor name and the project's name. Often these
will be identical - the vendor name just exists to prevent naming clashes. For
example, it would allow two different people to create a library named `json`.
One might be named `igorw/json` while the other might be `seldaek/json`.

Read more about publishing packages and package naming [here](02-libraries.md).
(Note that you can also specify "platform packages" as dependencies, allowing
you to require certain versions of server software. See
[platform packages](#platform-packages) below.)

### Package Version Constraints

In our example, we are requesting the Monolog package with the version constraint
[`1.0.*`](https://semver.mwl.be/#?package=monolog%2Fmonolog&version=1.0.*).
This means any version in the `1.0` development branch, or any version that is
greater than or equal to 1.0 and less than 1.1 (`>=1.0 <1.1`).

Please read [versions](articles/versions.md) for more in-depth information on
versions, how versions relate to each other, and on version constraints.

> **How does Composer download the right files?** When you specify a dependency in
> `composer.json`, Composer first takes the name of the package that you have requested
> and searches for it in any repositories that you have registered using the
> [`repositories`](04-schema.md#repositories) key. If you have not registered
> any extra repositories, or it does not find a package with that name in the
> repositories you have specified, it falls back to Packagist (more [below](#packagist)).
>
> When Composer finds the right package, either in Packagist or in a repo you have specified,
> it then uses the versioning features of the package's VCS (i.e., branches and tags)
> to attempt to find the best match for the version constraint you have specified. Be sure to read
> about versions and package resolution in the [versions article](articles/versions.md).

> **Note:** If you are trying to require a package but Composer throws an error
> regarding package stability, the version you have specified may not meet your
> default minimum stability requirements. By default only stable releases are taken
> into consideration when searching for valid package versions in your VCS.
>
> You might run into this if you are trying to require dev, alpha, beta, or RC
> versions of a package. Read more about stability flags and the `minimum-stability`
> key on the [schema page](04-schema.md).

## Installing Dependencies

To install the defined dependencies for your project, just run the
[`install`](03-cli.md#install) command.

```sh
php composer.phar install
```

When you run this command, one of two things may happen:

### Installing Without `composer.lock`

If you have never run the command before and there is also no `composer.lock` file present,
Composer simply resolves all dependencies listed in your `composer.json` file and downloads
the latest version of their files into the `vendor` directory in your project. (The `vendor`
directory is the conventional location for all third-party code in a project). In our
example from above, you would end up with the Monolog source files in
`vendor/monolog/monolog/`. If Monolog listed any dependencies, those would also be in
folders under `vendor/`.

> **Tip:** If you are using git for your project, you probably want to add
> `vendor` in your `.gitignore`. You really don't want to add all of that
> third-party code to your versioned repository.

When Composer has finished installing, it writes all of the packages and the exact versions
of them that it downloaded to the `composer.lock` file, locking the project to those specific
versions. You should commit the `composer.lock` file to your project repo so that all people
working on the project are locked to the same versions of dependencies (more below).

### Installing With `composer.lock`

This brings us to the second scenario. If there is already a `composer.lock` file as well as a
`composer.json` file when you run `composer install`, it means either you ran the
`install` command before, or someone else on the project ran the `install` command and
committed the `composer.lock` file to the project (which is good).

Either way, running `install` when a `composer.lock` file is present resolves and installs
all dependencies that you listed in `composer.json`, but Composer uses the exact versions listed
in `composer.lock` to ensure that the package versions are consistent for everyone
working on your project. As a result you will have all dependencies requested by your
`composer.json` file, but they may not all be at the very latest available versions
(some of the dependencies listed in the `composer.lock` file may have released newer versions since
the file was created). This is by design, it ensures that your project does not break because of
unexpected changes in dependencies.

### Commit Your `composer.lock` File to Version Control

Committing this file to VC is important because it will cause anyone who sets
up the project to use the exact same
versions of the dependencies that you are using. Your CI server, production
machines, other developers in your team, everything and everyone runs on the
same dependencies, which mitigates the potential for bugs affecting only some
parts of the deployments. Even if you develop alone, in six months when
reinstalling the project you can feel confident the dependencies installed are
still working even if your dependencies released many new versions since then.
(See note below about using the `update` command.)

## Updating Dependencies to their Latest Versions

As mentioned above, the `composer.lock` file prevents you from automatically getting
the latest versions of your dependencies. To update to the latest versions, use the
[`update`](03-cli.md#update) command. This will fetch the latest matching
versions (according to your `composer.json` file) and update the lock file
with the new versions. (This is equivalent to deleting the `composer.lock` file
and running `install` again.)

```sh
php composer.phar update
```
> **Note:** Composer will display a Warning when executing an `install` command
> if `composer.lock` and `composer.json` are not synchronized.

If you only want to install or update one dependency, you can whitelist them:

```sh
php composer.phar update monolog/monolog [...]
```

> **Note:** For libraries it is not necessary to commit the lock
> file, see also: [Libraries - Lock file](02-libraries.md#lock-file).

## Packagist

[Packagist](https://packagist.org/) is the main Composer repository. A Composer
repository is basically a package source: a place where you can get packages
from. Packagist aims to be the central repository that everybody uses. This
means that you can automatically `require` any package that is available there,
without further specifying where Composer should look for the package.

If you go to the [Packagist website](https://packagist.org/) (packagist.org),
you can browse and search for packages.

Any open source project using Composer is recommended to publish their packages
on Packagist. A library does not need to be on Packagist to be used by Composer,
but it enables discovery and adoption by other developers more quickly.

## Platform packages

Composer has platform packages, which are virtual packages for things that are
installed on the system but are not actually installable by Composer. This
includes PHP itself, PHP extensions and some system libraries.

* `php` represents the PHP version of the user, allowing you to apply
  constraints, e.g. `>=5.4.0`. To require a 64bit version of php, you can
  require the `php-64bit` package.

* `hhvm` represents the version of the HHVM runtime and allows you to apply
  a constraint, e.g., `>=2.3.3`.

* `ext-<name>` allows you to require PHP extensions (includes core
  extensions). Versioning can be quite inconsistent here, so it's often
  a good idea to just set the constraint to `*`.  An example of an extension
  package name is `ext-gd`.

* `lib-<name>` allows constraints to be made on versions of libraries used by
  PHP. The following are available: `curl`, `iconv`, `icu`, `libxml`,
  `openssl`, `pcre`, `uuid`, `xsl`.

You can use [`show --platform`](03-cli.md#show) to get a list of your locally
available platform packages.

## Autoloading

For libraries that specify autoload information, Composer generates a
`vendor/autoload.php` file. You can simply include this file and start
using the classes that those libraries provide without any extra work:

```php
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('name');
$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));
$log->addWarning('Foo');
```

You can even add your own code to the autoloader by adding an
[`autoload`](04-schema.md#autoload) field to `composer.json`.

```json
{
    "autoload": {
        "psr-4": {"Acme\\": "src/"}
    }
}
```

Composer will register a [PSR-4](http://www.php-fig.org/psr/psr-4/) autoloader
for the `Acme` namespace.

You define a mapping from namespaces to directories. The `src` directory would
be in your project root, on the same level as `vendor` directory is. An example
filename would be `src/Foo.php` containing an `Acme\Foo` class.

After adding the [`autoload`](04-schema.md#autoload) field, you have to re-run
[`dump-autoload`](03-cli.md#dump-autoload) to re-generate the
`vendor/autoload.php` file.

Including that file will also return the autoloader instance, so you can store
the return value of the include call in a variable and add more namespaces.
This can be useful for autoloading classes in a test suite, for example.

```php
$loader = require __DIR__ . '/vendor/autoload.php';
$loader->addPsr4('Acme\\Test\\', __DIR__);
```

In addition to PSR-4 autoloading, Composer also supports PSR-0, classmap and
files autoloading. See the [`autoload`](04-schema.md#autoload) reference for
more information.

See also the docs on [optimizing the autoloader](articles/autoloader-optimization.md).

> **Note:** Composer provides its own autoloader. If you don't want to use that
> one, you can just include `vendor/composer/autoload_*.php` files, which return
> associative arrays allowing you to configure your own autoloader.

&larr; [Intro](00-intro.md)  |  [Libraries](02-libraries.md) &rarr;
