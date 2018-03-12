# Introduction

Composer is a tool for dependency management in PHP. It allows you to declare
the libraries your project depends on and it will manage (install/update) them
for you.

## Dependency management

Composer is **not** a package manager in the same sense as Yum or Apt are. Yes,
it deals with "packages" or libraries, but it manages them on a per-project
basis, installing them in a directory (e.g. `vendor`) inside your project. By
default it does not install anything globally. Thus, it is a dependency
manager. It does however support a "global" project for convenience via the
[global](03-cli.md#global) command.

This idea is not new and Composer is strongly inspired by node's
[npm](https://www.npmjs.com/) and ruby's [bundler](https://bundler.io/).

Suppose:

1. You have a project that depends on a number of libraries.
1. Some of those libraries depend on other libraries.

Composer:

1. Enables you to declare the libraries you depend on.
1. Finds out which versions of which packages can and need to be installed, and
   installs them (meaning it downloads them into your project).

See the [Basic usage](01-basic-usage.md) chapter for more details on declaring
dependencies.

## System Requirements

Composer requires PHP 5.3.2+ to run. A few sensitive php settings and compile
flags are also required, but when using the installer you will be warned about
any incompatibilities.

To install packages from sources instead of simple zip archives, you will need
git, svn, fossil or hg depending on how the package is version-controlled.

Composer is multi-platform and we strive to make it run equally well on Windows,
Linux and OSX.

## Installation - Linux / Unix / OSX

### Downloading the Composer Executable

Composer offers a convenient installer that you can execute directly from the
commandline. Feel free to [download this file](https://getcomposer.org/installer)
or review it on [GitHub](https://github.com/composer/getcomposer.org/blob/master/web/installer)
if you wish to know more about the inner workings of the installer. The source
is plain PHP.

There are in short, two ways to install Composer. Locally as part of your
project, or globally as a system wide executable.

#### Locally

Installing Composer locally is a matter of just running the installer in your
project directory. See [the Download page](https://getcomposer.org/download/)
for instructions.

The installer will just check a few PHP settings and then download
`composer.phar` to your working directory. This file is the Composer binary. It
is a PHAR (PHP archive), which is an archive format for PHP which can be run on
the command line, amongst other things.

Now just run `php composer.phar` in order to run Composer.

You can install Composer to a specific directory by using the `--install-dir`
option and additionally (re)name it as well using the `--filename` option. When
running the installer when following
[the Download page instructions](https://getcomposer.org/download/) add the
following parameters:

```sh
php composer-setup.php --install-dir=bin --filename=composer
```

Now just run `php bin/composer` in order to run Composer.

#### Globally

You can place the Composer PHAR anywhere you wish. If you put it in a directory
that is part of your `PATH`, you can access it globally. On unixy systems you
can even make it executable and invoke it without directly using the `php`
interpreter.

After running the installer following [the Download page instructions](https://getcomposer.org/download/)
you can run this to move composer.phar to a directory that is in your path:

```sh
mv composer.phar /usr/local/bin/composer
```

> **Note:** If the above fails due to permissions, you may need to run it again
> with sudo.

> **Note:** On some versions of OSX the `/usr` directory does not exist by
> default. If you receive the error "/usr/local/bin/composer: No such file or
> directory" then you must create the directory manually before proceeding:
> `mkdir -p /usr/local/bin`.

> **Note:** For information on changing your PATH, please read the
> [Wikipedia article](https://en.wikipedia.org/wiki/PATH_(variable)) and/or use Google.

Now just run `composer` in order to run Composer instead of `php composer.phar`.

## Installation - Windows

### Using the Installer

This is the easiest way to get Composer set up on your machine.

Download and run
[Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe). It will
install the latest Composer version and set up your PATH so that you can just
call `composer` from any directory in your command line.

> **Note:** Close your current terminal. Test usage with a new terminal: This is
> important since the PATH only gets loaded when the terminal starts.

### Manual Installation

Change to a directory on your `PATH` and run the installer following
[the Download page instructions](https://getcomposer.org/download/)
to download `composer.phar`.

Create a new `composer.bat` file alongside `composer.phar`:

```sh
C:\bin>echo @php "%~dp0composer.phar" %*>composer.bat
```

Add the directory to your PATH environment variable if it isn't already.
For information on changing your PATH variable, please see
[this article](https://www.computerhope.com/issues/ch000549.htm) and/or
use Google.

Close your current terminal. Test usage with a new terminal:

```sh
C:\Users\username>composer -V
Composer version 1.0.0 2016-01-10 20:34:53
```

## Using Composer

Now that you've installed Composer, you are ready to use it! Head on over to the
next chapter for a short and simple demonstration.

[Basic usage](01-basic-usage.md) &rarr;
