# composer.json

This chapter will explain all of the fields available in `composer.json`.

## JSON schema

We have a [JSON schema](http://json-schema.org) that documents the format and
can also be used to validate your `composer.json`. In fact, it is used by the
`validate` command. You can find it at:
[`res/composer-schema.json`](https://github.com/composer/composer/blob/master/res/composer-schema.json).

## Root Package

The root package is the package defined by the `composer.json` at the root of
your project. It is the main `composer.json` that defines your project
requirements.

Certain fields only apply when in the root package context. One example of
this is the `config` field. Only the root package can define configuration.
The config of dependencies is ignored. This makes the `config` field
`root-only`.

If you clone one of those dependencies to work on it, then that package is the
root package. The `composer.json` is identical, but the context is different.

> **Note:** A package can be the root package or not, depending on the context.
> For example, if your project depends on the `monolog` library, your project
> is the root package. However, if you clone `monolog` from GitHub in order to
> fix a bug in it, then `monolog` is the root package.

## Properties

### name

The name of the package. It consists of vendor name and project name,
separated by `/`.

Examples:

* monolog/monolog
* igorw/event-source

Required for published packages (libraries).

### description

A short description of the package. Usually this is just one line long.

Required for published packages (libraries).

### version

The version of the package. In most cases this is not required and should
be omitted (see below).

This must follow the format of `X.Y.Z` or `vX.Y.Z` with an optional suffix
of `-dev`, `-patch`, `-alpha`, `-beta` or `-RC`. The patch, alpha, beta and
RC suffixes can also be followed by a number.

Examples:

    1.0.0
    1.0.2
    1.1.0
    0.2.5
    1.0.0-dev
    1.0.0-alpha3
    1.0.0-beta2
    1.0.0-RC5

Optional if the package repository can infer the version from somewhere, such
as the VCS tag name in the VCS repository. In that case it is also recommended
to omit it.

> **Note:** Packagist uses VCS repositories, so the statement above is very
> much true for Packagist as well. Specifying the version yourself will
> most likely end up creating problems at some point due to human error.

### type

The type of the package. It defaults to `library`.

Package types are used for custom installation logic. If you have a package
that needs some special logic, you can define a custom type. This could be a
`symfony-bundle`, a `wordpress-plugin` or a `typo3-module`. These types will
all be specific to certain projects, and they will need to provide an
installer capable of installing packages of that type.

Out of the box, composer supports four types:

- **library:** This is the default. It will simply copy the files to `vendor`.
- **project:** This denotes a project rather than a library. For example
  application shells like the [Symfony standard edition](https://github.com/symfony/symfony-standard),
  CMSs like the [SilverStripe installer](https://github.com/silverstripe/silverstripe-installer)
  or full fledged applications distributed as packages. This can for example
  be used by IDEs to provide listings of projects to initialize when creating
  a new workspace.
- **metapackage:** An empty package that contains requirements and will trigger
  their installation, but contains no files and will not write anything to the
  filesystem. As such, it does not require a dist or source key to be
  installable.
- **composer-plugin:** A package of type `composer-plugin` may provide an
  installer for other packages that have a custom type. Read more in the
  [dedicated article](articles/custom-installers.md).

Only use a custom type if you need custom logic during installation. It is
recommended to omit this field and have it just default to `library`.

### keywords

An array of keywords that the package is related to. These can be used for
searching and filtering.

Examples:

    logging
    events
    database
    redis
    templating

Optional.

### homepage

An URL to the website of the project.

Optional.

### time

Release date of the version.

Must be in `YYYY-MM-DD` or `YYYY-MM-DD HH:MM:SS` format.

Optional.

### license

The license of the package. This can be either a string or an array of strings.

The recommended notation for the most common licenses is (alphabetical):

    Apache-2.0
    BSD-2-Clause
    BSD-3-Clause
    BSD-4-Clause
    GPL-2.0
    GPL-2.0+
    GPL-3.0
    GPL-3.0+
    LGPL-2.1
    LGPL-2.1+
    LGPL-3.0
    LGPL-3.0+
    MIT

Optional, but it is highly recommended to supply this. More identifiers are
listed at the [SPDX Open Source License Registry](http://www.spdx.org/licenses/).

For closed-source software, you may use `"proprietary"` as the license identifier.

An Example:

    {
        "license": "MIT"
    }


For a package, when there is a choice between licenses ("disjunctive license"),
multiple can be specified as array.

An Example for disjunctive licenses:

    {
        "license": [
           "LGPL-2.1",
           "GPL-3.0+"
        ]
    }

Alternatively they can be separated with "or" and enclosed in parenthesis;

    {
        "license": "(LGPL-2.1 or GPL-3.0+)"
    }

Similarly when multiple licenses need to be applied ("conjunctive license"),
they should be separated with "and" and enclosed in parenthesis.

### authors

The authors of the package. This is an array of objects.

Each author object can have following properties:

* **name:** The author's name. Usually his real name.
* **email:** The author's email address.
* **homepage:** An URL to the author's website.
* **role:** The authors' role in the project (e.g. developer or translator)

An example:

    {
        "authors": [
            {
                "name": "Nils Adermann",
                "email": "naderman@naderman.de",
                "homepage": "http://www.naderman.de",
                "role": "Developer"
            },
            {
                "name": "Jordi Boggiano",
                "email": "j.boggiano@seld.be",
                "homepage": "http://seld.be",
                "role": "Developer"
            }
        ]
    }

Optional, but highly recommended.

### support

Various information to get support about the project.

Support information includes the following:

* **email:** Email address for support.
* **issues:** URL to the Issue Tracker.
* **forum:** URL to the Forum.
* **wiki:** URL to the Wiki.
* **irc:** IRC channel for support, as irc://server/channel.
* **source:** URL to browse or download the sources.

An example:

    {
        "support": {
            "email": "support@example.org",
            "irc": "irc://irc.freenode.org/composer"
        }
    }

Optional.

### Package links

All of the following take an object which maps package names to
[version constraints](01-basic-usage.md#package-versions).

Example:

    {
        "require": {
            "monolog/monolog": "1.0.*"
        }
    }

All links are optional fields.

`require` and `require-dev` additionally support stability flags (root-only).
These allow you to further restrict or expand the stability of a package beyond
the scope of the [minimum-stability](#minimum-stability) setting. You can apply
them to a constraint, or just apply them to an empty constraint if you want to
allow unstable packages of a dependency for example.

Example:

    {
        "require": {
            "monolog/monolog": "1.0.*@beta",
            "acme/foo": "@dev"
        }
    }

If one of your dependencies has a dependency on an unstable package you need to
explicitly require it as well, along with its sufficient stability flag.

Example:

    {
        "require": {
            "doctrine/doctrine-fixtures-bundle": "dev-master",
            "doctrine/data-fixtures": "@dev"
        }
    }

`require` and `require-dev` additionally support explicit references (i.e.
commit) for dev versions to make sure they are locked to a given state, even
when you run update. These only work if you explicitly require a dev version
and append the reference with `#<ref>`.

Example:

    {
        "require": {
            "monolog/monolog": "dev-master#2eb0c0978d290a1c45346a1955188929cb4e5db7",
            "acme/foo": "1.0.x-dev#abc123"
        }
    }

> **Note:** While this is convenient at times, it should not be how you use
> packages in the long term because it comes with a technical limitation. The
> composer.json metadata will still be read from the branch name you specify
> before the hash. Because of that in some cases it will not be a practical
> workaround, and you should always try to switch to tagged releases as soon
> as you can.

It is also possible to inline-alias a package constraint so that it matches
a constraint that it otherwise would not. For more information [see the
aliases article](articles/aliases.md).

#### require

Lists packages required by this package. The package will not be installed
unless those requirements can be met.

#### require-dev <span>(root-only)</span>

Lists packages required for developing this package, or running
tests, etc. The dev requirements of the root package are installed by default.
Both `install` or `update` support the `--no-dev` option that prevents dev
dependencies from being installed.

#### conflict

Lists packages that conflict with this version of this package. They
will not be allowed to be installed together with your package.

Note that when specifying ranges like `<1.0, >= 1.1` in a `conflict` link,
this will state a conflict with all versions that are less than 1.0 *and* equal
or newer than 1.1 at the same time, which is probably not what you want. You
probably want to go for `<1.0 | >= 1.1` in this case.

#### replace

Lists packages that are replaced by this package. This allows you to fork a
package, publish it under a different name with its own version numbers, while
packages requiring the original package continue to work with your fork because
it replaces the original package.

This is also useful for packages that contain sub-packages, for example the main
symfony/symfony package contains all the Symfony Components which are also
available as individual packages. If you require the main package it will
automatically fulfill any requirement of one of the individual components,
since it replaces them.

Caution is advised when using replace for the sub-package purpose explained
above. You should then typically only replace using `self.version` as a version
constraint, to make sure the main package only replaces the sub-packages of
that exact version, and not any other version, which would be incorrect.

#### provide

List of other packages that are provided by this package. This is mostly
useful for common interfaces. A package could depend on some virtual
`logger` package, any library that implements this logger interface would
simply list it in `provide`.

### suggest

Suggested packages that can enhance or work well with this package. These are
just informational and are displayed after the package is installed, to give
your users a hint that they could add more packages, even though they are not
strictly required.

The format is like package links above, except that the values are free text
and not version constraints.

Example:

    {
        "suggest": {
            "monolog/monolog": "Allows more advanced logging of the application flow"
        }
    }

### autoload

Autoload mapping for a PHP autoloader.

Currently [`PSR-0`](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md)
autoloading, `classmap` generation and `files` are supported. PSR-0 is the recommended way though
since it offers greater flexibility (no need to regenerate the autoloader when you add
classes).

#### PSR-0

Under the `psr-0` key you define a mapping from namespaces to paths, relative to the
package root. Note that this also supports the PEAR-style non-namespaced convention.

Please note namespace declarations should end in `\\` to make sure the autoloader
responds exactly. For example `Foo` would match in `FooBar` so the trailing
backslashes solve the problem: `Foo\\` and `FooBar\\` are distinct.

The PSR-0 references are all combined, during install/update, into a single key => value
array which may be found in the generated file `vendor/composer/autoload_namespaces.php`.

Example:

    {
        "autoload": {
            "psr-0": {
                "Monolog\\": "src/",
                "Vendor\\Namespace\\": "src/",
                "Vendor_Namespace_": "src/"
            }
        }
    }

If you need to search for a same prefix in multiple directories,
you can specify them as an array as such:

    {
        "autoload": {
            "psr-0": { "Monolog\\": ["src/", "lib/"] }
        }
    }

The PSR-0 style is not limited to namespace declarations only but may be
specified right down to the class level. This can be useful for libraries with
only one class in the global namespace. If the php source file is also located
in the root of the package, for example, it may be declared like this:

    {
        "autoload": {
            "psr-0": { "UniqueGlobalClass": "" }
        }
    }

If you want to have a fallback directory where any namespace can be, you can
use an empty prefix like:

    {
        "autoload": {
            "psr-0": { "": "src/" }
        }
    }

#### Classmap

The `classmap` references are all combined, during install/update, into a single
key => value array which may be found in the generated file
`vendor/composer/autoload_classmap.php`. This map is built by scanning for
classes in all `.php` and `.inc` files in the given directories/files.

You can use the classmap generation support to define autoloading for all libraries
that do not follow PSR-0. To configure this you specify all directories or files
to search for classes.

Example:

    {
        "autoload": {
            "classmap": ["src/", "lib/", "Something.php"]
        }
    }

#### Files

If you want to require certain files explicitly on every request then you can use
the 'files' autoloading mechanism. This is useful if your package includes PHP functions
that cannot be autoloaded by PHP.

Example:

    {
        "autoload": {
            "files": ["src/MyLibrary/functions.php"]
        }
    }

### include-path

> **DEPRECATED**: This is only present to support legacy projects, and all new code
> should preferably use autoloading. As such it is a deprecated practice, but the
> feature itself will not likely disappear from Composer.

A list of paths which should get appended to PHP's `include_path`.

Example:

    {
        "include-path": ["lib/"]
    }

Optional.

### target-dir

Defines the installation target.

In case the package root is below the namespace declaration you cannot
autoload properly. `target-dir` solves this problem.

An example is Symfony. There are individual packages for the components. The
Yaml component is under `Symfony\Component\Yaml`. The package root is that
`Yaml` directory. To make autoloading possible, we need to make sure that it
is not installed into `vendor/symfony/yaml`, but instead into
`vendor/symfony/yaml/Symfony/Component/Yaml`, so that the autoloader can load
it from `vendor/symfony/yaml`.

To do that, `autoload` and `target-dir` are defined as follows:

    {
        "autoload": {
            "psr-0": { "Symfony\\Component\\Yaml\\": "" }
        },
        "target-dir": "Symfony/Component/Yaml"
    }

Optional.

### minimum-stability <span>(root-only)</span>

This defines the default behavior for filtering packages by stability. This
defaults to `stable`, so if you rely on a `dev` package, you should specify
it in your file to avoid surprises.

All versions of each package are checked for stability, and those that are less
stable than the `minimum-stability` setting will be ignored when resolving
your project dependencies. Specific changes to the stability requirements of
a given package can be done in `require` or `require-dev` (see
[package links](#package-links)).

Available options (in order of stability) are `dev`, `alpha`, `beta`, `RC`,
and `stable`.

### prefer-stable <span>(root-only)</span>

When this is enabled, Composer will prefer more stable packages over unstable
ones when finding compatible stable packages is possible. If you require a
dev version or only alphas are available for a package, those will still be
selected granted that the minimum-stability allows for it.

Use `"prefer-stable": true` to enable.

### repositories <span>(root-only)</span>

Custom package repositories to use.

By default composer just uses the packagist repository. By specifying
repositories you can get packages from elsewhere.

Repositories are not resolved recursively. You can only add them to your main
`composer.json`. Repository declarations of dependencies' `composer.json`s are
ignored.

The following repository types are supported:

* **composer:** A composer repository is simply a `packages.json` file served
  via the network (HTTP, FTP, SSH), that contains a list of `composer.json`
  objects with additional `dist` and/or `source` information. The `packages.json`
  file is loaded using a PHP stream. You can set extra options on that stream
  using the `options` parameter.
* **vcs:** The version control system repository can fetch packages from git,
  svn and hg repositories.
* **pear:** With this you can import any pear repository into your composer
  project.
* **package:** If you depend on a project that does not have any support for
  composer whatsoever you can define the package inline using a `package`
  repository. You basically just inline the `composer.json` object.

For more information on any of these, see [Repositories](05-repositories.md).

Example:

    {
        "repositories": [
            {
                "type": "composer",
                "url": "http://packages.example.com"
            },
            {
                "type": "composer",
                "url": "https://packages.example.com",
                "options": {
                    "ssl": {
                        "verify_peer": "true"
                    }
                }
            },
            {
                "type": "vcs",
                "url": "https://github.com/Seldaek/monolog"
            },
            {
                "type": "pear",
                "url": "http://pear2.php.net"
            },
            {
                "type": "package",
                "package": {
                    "name": "smarty/smarty",
                    "version": "3.1.7",
                    "dist": {
                        "url": "http://www.smarty.net/files/Smarty-3.1.7.zip",
                        "type": "zip"
                    },
                    "source": {
                        "url": "http://smarty-php.googlecode.com/svn/",
                        "type": "svn",
                        "reference": "tags/Smarty_3_1_7/distribution/"
                    }
                }
            }
        ]
    }

> **Note:** Order is significant here. When looking for a package, Composer
will look from the first to the last repository, and pick the first match.
By default Packagist is added last which means that custom repositories can
override packages from it.

### config <span>(root-only)</span>

A set of configuration options. It is only used for projects.

The following options are supported:

* **process-timeout:** Defaults to `300`. The duration processes like git clones
  can run before Composer assumes they died out. You may need to make this
  higher if you have a slow connection or huge vendors.
* **use-include-path:** Defaults to `false`. If true, the Composer autoloader
  will also look for classes in the PHP include path.
* **preferred-install:** Defaults to `auto` and can be any of `source`, `dist` or
  `auto`. This option allows you to set the install method Composer will prefer to
  use.
* **github-protocols:** Defaults to `["git", "https"]`. A list of protocols to
  use when cloning from github.com, in priority order. You can reconfigure it to
  prioritize the https protocol if you are behind a proxy or have somehow bad
  performances with the git protocol.
* **github-oauth:** A list of domain names and oauth keys. For example using
  `{"github.com": "oauthtoken"}` as the value of this option will use `oauthtoken`
  to access private repositories on github and to circumvent the low IP-based
  rate limiting of their API.
* **vendor-dir:** Defaults to `vendor`. You can install dependencies into a
  different directory if you want to.
* **bin-dir:** Defaults to `vendor/bin`. If a project includes binaries, they
  will be symlinked into this directory.
* **cache-dir:** Defaults to `$home/cache` on unix systems and
  `C:\Users\<user>\AppData\Local\Composer` on Windows. Stores all the caches
  used by composer. See also [COMPOSER_HOME](03-cli.md#composer-home).
* **cache-files-dir:** Defaults to `$cache-dir/files`. Stores the zip archives
  of packages.
* **cache-repo-dir:** Defaults to `$cache-dir/repo`. Stores repository metadata
  for the `composer` type and the VCS repos of type `svn`, `github` and `bitbucket`.
* **cache-vcs-dir:** Defaults to `$cache-dir/vcs`. Stores VCS clones for
  loading VCS repository metadata for the `git`/`hg` types and to speed up installs.
* **cache-files-ttl:** Defaults to `15552000` (6 months). Composer caches all
  dist (zip, tar, ..) packages that it downloads. Those are purged after six
  months of being unused by default. This option allows you to tweak this
  duration (in seconds) or disable it completely by setting it to 0.
* **cache-files-maxsize:** Defaults to `300MiB`. Composer caches all
  dist (zip, tar, ..) packages that it downloads. When the garbage collection
  is periodically ran, this is the maximum size the cache will be able to use.
  Older (less used) files will be removed first until the cache fits.
* **prepend-autoloader:** Defaults to `true`. If false, the composer autoloader
  will not be prepended to existing autoloaders. This is sometimes required to fix
  interoperability issues with other autoloaders.
* **autoloader-suffix:** Defaults to `null`. String to be used as a suffix for
  the generated Composer autoloader. When null a random one will be generated.
* **github-domains:** Defaults to `["github.com"]`. A list of domains to use in
  github mode. This is used for GitHub Enterprise setups.
* **notify-on-install:** Defaults to `true`. Composer allows repositories to
  define a notification URL, so that they get notified whenever a package from
  that repository is installed. This option allows you to disable that behaviour.
* **discard-changes:** Defaults to `false` and can be any of `true`, `false` or
  `"stash"`. This option allows you to set the default style of handling dirty
  updates when in non-interactive mode. `true` will always discard changes in
  vendors, while `"stash"` will try to stash and reapply. Use this for CI
  servers or deploy scripts if you tend to have modified vendors.

Example:

    {
        "config": {
            "bin-dir": "bin"
        }
    }

### scripts <span>(root-only)</span>

Composer allows you to hook into various parts of the installation process
through the use of scripts.

See [Scripts](articles/scripts.md) for events details and examples.

### extra

Arbitrary extra data for consumption by `scripts`.

This can be virtually anything. To access it from within a script event
handler, you can do:

    $extra = $event->getComposer()->getPackage()->getExtra();

Optional.

### bin

A set of files that should be treated as binaries and symlinked into the `bin-dir`
(from config).

See [Vendor Binaries](articles/vendor-binaries.md) for more details.

Optional.

### archive

A set of options for creating package archives.

The following options are supported:

* **exclude:** Allows configuring a list of patterns for excluded paths. The
  pattern syntax matches .gitignore files. A leading exclamation mark (!) will
  result in any matching files to be included even if a previous pattern
  excluded them. A leading slash will only match at the beginning of the project
  relative path. An asterisk will not expand to a directory separator.

Example:

    {
        "archive": {
            "exclude": ["/foo/bar", "baz", "/*.test", "!/foo/bar/baz"]
        }
    }

The example will include `/dir/foo/bar/file`, `/foo/bar/baz`, `/file.php`,
`/foo/my.test` but it will exclude `/foo/bar/any`, `/foo/baz`, and `/my.test`.

Optional.

&larr; [Command-line interface](03-cli.md)  |  [Repositories](05-repositories.md) &rarr;
