<!--
    tagline: Script are callbacks that are called before/after installing packages
-->

# Scripts

## What is a script?

A script, in Composer's terms, can either be a PHP callback (defined as a
static method) or any command-line executable command. Scripts are useful
for executing a package's custom code or package-specific commands during
the Composer execution process.

> **Note:** Only scripts defined in the root package's `composer.json` are
> executed. If a dependency of the root package specifies its own scripts,
> Composer does not execute those additional scripts.

## Event names

Composer fires the following named events during its execution process:

### Command Events

- **pre-install-cmd**: occurs before the `install` command is executed with a
  lock file present.
- **post-install-cmd**: occurs after the `install` command has been executed
  with a lock file present.
- **pre-update-cmd**: occurs before the `update` command is executed, or before
  the `install` command is executed without a lock file present.
- **post-update-cmd**: occurs after the `update` command has been executed, or
  after the `install` command has been executed without a lock file present.
- **post-status-cmd**: occurs after the `status` command has been executed.
- **pre-archive-cmd**: occurs before the `archive` command is executed.
- **post-archive-cmd**: occurs after the `archive` command has been executed.
- **pre-autoload-dump**: occurs before the autoloader is dumped, either during
  `install`/`update`, or via the `dump-autoload` command.
- **post-autoload-dump**: occurs after the autoloader has been dumped, either
  during `install`/`update`, or via the `dump-autoload` command.
- **post-root-package-install**: occurs after the root package has been
  installed, during the `create-project` command.
- **post-create-project-cmd**: occurs after the `create-project` command has
  been executed.

### Installer Events

- **pre-dependencies-solving**: occurs before the dependencies are resolved.
- **post-dependencies-solving**: occurs after the dependencies have been resolved.

### Package Events

- **pre-package-install**: occurs before a package is installed.
- **post-package-install**: occurs after a package has been installed.
- **pre-package-update**: occurs before a package is updated.
- **post-package-update**: occurs after a package has been updated.
- **pre-package-uninstall**: occurs before a package is uninstalled.
- **post-package-uninstall**: occurs after a package has been uninstalled.

### Plugin Events

- **init**: occurs after a Composer instance is done being initialized.
- **command**: occurs before any Composer Command is executed on the CLI. It
  provides you with access to the input and output objects of the program.
- **pre-file-download**: occurs before files are downloaded and allows
  you to manipulate the `RemoteFilesystem` object prior to downloading files
  based on the URL to be downloaded.
- **pre-command-run**: occurs before a command is executed and allows you to
  manipulate the `InputInterface` object's options and arguments to tweak
  a command's behavior.

> **Note:** Composer makes no assumptions about the state of your dependencies
> prior to `install` or `update`. Therefore, you should not specify scripts
> that require Composer-managed dependencies in the `pre-update-cmd` or
> `pre-install-cmd` event hooks. If you need to execute scripts prior to
> `install` or `update` please make sure they are self-contained within your
> root package.

## Defining scripts

The root JSON object in `composer.json` should have a property called
`"scripts"`, which contains pairs of named events and each event's
corresponding scripts. An event's scripts can be defined as either a string
(only for a single script) or an array (for single or multiple scripts.)

For any given event:

- Scripts execute in the order defined when their corresponding event is fired.
- An array of scripts wired to a single event can contain both PHP callbacks
and command-line executable commands.
- PHP classes containing defined callbacks must be autoloadable via Composer's
autoload functionality.
- Callbacks can only autoload classes from psr-0, psr-4 and classmap
definitions. If a defined callback relies on functions defined outside of a
class, the callback itself is responsible for loading the file containing these
functions.

Script definition example:

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
        ],
        "post-autoload-dump": [
            "MyVendor\\MyClass::postAutoloadDump"
        ],
        "post-create-project-cmd": [
            "php -r \"copy('config/local-example.php', 'config/local.php');\""
        ]
    }
}
```

Using the previous definition example, here's the class `MyVendor\MyClass`
that might be used to execute the PHP callbacks:

```php
<?php

namespace MyVendor;

use Composer\Script\Event;
use Composer\Installer\PackageEvent;

class MyClass
{
    public static function postUpdate(Event $event)
    {
        $composer = $event->getComposer();
        // do stuff
    }

    public static function postAutoloadDump(Event $event)
    {
        $vendorDir = $event->getComposer()->getConfig()->get('vendor-dir');
        require $vendorDir . '/autoload.php';

        some_function_from_an_autoloaded_file();
    }

    public static function postPackageInstall(PackageEvent $event)
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

**Note:** During a composer install or update process, a variable named
`COMPOSER_DEV_MODE` will be added to the environment. If the command was run
with the `--no-dev` flag, this variable will be set to 0, otherwise it will be
set to 1.

## Event classes

When an event is fired, your PHP callback receives as first argument a
`Composer\EventDispatcher\Event` object. This object has a `getName()` method
that lets you retrieve the event name.

Depending on the [script types](#event-names) you will get various event
subclasses containing various getters with relevant data and associated
objects:

- Base class: [`Composer\EventDispatcher\Event`](https://getcomposer.org/apidoc/master/Composer/EventDispatcher/Event.html)
- Command Events: [`Composer\Script\Event`](https://getcomposer.org/apidoc/master/Composer/Script/Event.html)
- Installer Events: [`Composer\Installer\InstallerEvent`](https://getcomposer.org/apidoc/master/Composer/Installer/InstallerEvent.html)
- Package Events: [`Composer\Installer\PackageEvent`](https://getcomposer.org/apidoc/master/Composer/Installer/PackageEvent.html)
- Plugin Events:
  - init: [`Composer\EventDispatcher\Event`](https://getcomposer.org/apidoc/master/Composer/EventDispatcher/Event.html)
  - command: [`Composer\Plugin\CommandEvent`](https://getcomposer.org/apidoc/master/Composer/Plugin/CommandEvent.html)
  - pre-file-download: [`Composer\Plugin\PreFileDownloadEvent`](https://getcomposer.org/apidoc/master/Composer/Plugin/PreFileDownloadEvent.html)

## Running scripts manually

If you would like to run the scripts for an event manually, the syntax is:

```sh
composer run-script [--dev] [--no-dev] script
```

For example `composer run-script post-install-cmd` will run any
**post-install-cmd** scripts that have been defined.

You can also give additional arguments to the script handler by appending `--`
followed by the handler arguments. e.g.
`composer run-script post-install-cmd -- --check` will pass`--check` along to
the script handler. Those arguments are received as CLI arg by CLI handlers,
and can be retrieved as an array via `$event->getArguments()` by PHP handlers.

## Writing custom commands

If you add custom scripts that do not fit one of the predefined event name
above, you can either run them with run-script or also run them as native
Composer commands. For example the handler defined below is executable by
simply running `composer test`:

```json
{
    "scripts": {
        "test": "phpunit"
    }
}
```

Similar to the `run-script` command you can give additional arguments to scripts,
e.g. `composer test -- --filter <pattern>` will pass `--filter <pattern>` along
to the `phpunit` script.

> **Note:** Before executing scripts, Composer's bin-dir is temporarily pushed
> on top of the PATH environment variable so that binaries of dependencies
> are easily accessible. In this example no matter if the `phpunit` binary is
> actually in `vendor/bin/phpunit` or `bin/phpunit` it will be found and executed.

## Referencing scripts

To enable script re-use and avoid duplicates, you can call a script from another
one by prefixing the command name with `@`:

```json
{
    "scripts": {
        "test": [
            "@clearCache",
            "phpunit"
        ],
        "clearCache": "rm -rf cache/*"
    }
}
```

## Calling Composer commands

To call Composer commands, you can use `@composer` which will automatically
resolve to whatever composer.phar is currently being used:

```json
{
    "scripts": {
        "test": [
            "@composer install",
            "phpunit"
        ]
    }
}
```

One limitation of this is that you can not call multiple composer commands in
a row like `@composer install && @composer foo`. You must split them up in a
JSON array of commands.

## Executing PHP scripts

To execute PHP scripts, you can use `@php` which will automatically
resolve to whatever php process is currently being used:

```json
{
    "scripts": {
        "test": [
            "@php script.php",
            "phpunit"
        ]
    }
}
```

One limitation of this is that you can not call multiple commands in
a row like `@php install && @php foo`. You must split them up in a
JSON array of commands.

## Custom descriptions.

You can set custom script descriptions with the following in your `composer.json`:

```json
{
    "scripts-descriptions": {
        "test": "Run all tests!"
    }
}
```

> **Note:** You can only set custom descriptions of custom commands.
