<!--
    tagline: Solving problems
-->
# Troubleshooting

This is a list of common pitfalls on using Composer, and how to avoid them.

## General

1. Before asking anyone, run [`composer diagnose`](../03-cli.md#diagnose) to check
   for common problems. If it all checks out, proceed to the next steps.

2. When facing any kind of problems using Composer, be sure to **work with the
   latest version**. See [self-update](../03-cli.md#self-update) for details.

3. Make sure you have no problems with your setup by running the installer's
   checks via `curl -sS https://getcomposer.org/installer | php -- --check`.

4. Ensure you're **installing vendors straight from your `composer.json`** via
   `rm -rf vendor && composer update -v` when troubleshooting, excluding any
   possible interferences with existing vendor installations or `composer.lock`
   entries.

5. Try clearing Composer's cache by running `composer clear-cache`.

## Package not found

1. Double-check you **don't have typos** in your `composer.json` or repository
   branches and tag names.

2. Be sure to **set the right
   [minimum-stability](../04-schema.md#minimum-stability)**. To get started or be
   sure this is no issue, set `minimum-stability` to "dev".

3. Packages **not coming from [Packagist](https://packagist.org/)** should
   always be **defined in the root package** (the package depending on all
   vendors).

4. Use the **same vendor and package name** throughout all branches and tags of
   your repository, especially when maintaining a third party fork and using
   `replace`.

5. If you are updating to a recently published version of a package, be aware that
   Packagist has a delay of up to 1 minute before new packages are visible to Composer.

6. If you are updating a single package, it may depend on newer versions itself.
   In this case add the `--with-dependencies` argument **or** add all dependencies which
   need an update to the command.

## Package not found on travis-ci.org

1. Check the ["Package not found"](#package-not-found) item above.

2. If the package tested is a dependency of one of its dependencies (cyclic
   dependency), the problem might be that Composer is not able to detect the version
   of the package properly. If it is a git clone it is generally alright and Composer
   will detect the version of the current branch, but travis does shallow clones so
   that process can fail when testing pull requests and feature branches in general.
   The best solution is to define the version you are on via an environment variable
   called COMPOSER_ROOT_VERSION. You set it to `dev-master` for example to define
   the root package's version as `dev-master`.
   Use: `before_script: COMPOSER_ROOT_VERSION=dev-master composer install` to export
   the variable for the call to composer.

## Package not found in a Jenkins-build

1. Check the ["Package not found"](#package-not-found) item above.
2. Reason for failing is similar to the problem which can occur on travis-ci.org: The
   git-clone / checkout within Jenkins leaves the branch in a "detached HEAD"-state. As
   a result, Composer is not able to identify the version of the current checked out branch
   and may not be able to resolve a cyclic dependency. To solve this problem, you can use
   the "Additional Behaviours" -> "Check out to specific local branch" in your Git-settings
   for your Jenkins-job, where your "local branch" shall be the same branch as you are
   checking out. Using this, the checkout will not be in detached state any more and cyclic
   dependency is recognized correctly.

## I have a dependency which contains a "repositories" definition in its composer.json, but it seems to be ignored.

The [`repositories`](../04-schema.md#repositories) configuration property is defined as [root-only](../04-schema.md#root-package). It is not inherited. You can read more about the reasons behind this in the "[why can't
composer load repositories recursively?](../faqs/why-can't-composer-load-repositories-recursively.md)" article.
The simplest work-around to this limitation, is moving or duplicating the `repositories` definition into your root
composer.json.

## I have locked a dependency to a specific commit but get unexpected results.

While Composer supports locking dependencies to a specific commit using the `#commit-ref` syntax, there are certain
caveats that one should take into account. The most important one is [documented](../04-schema.md#package-links), but
frequently overlooked:

> **Note:** While this is convenient at times, it should not be how you use
> packages in the long term because it comes with a technical limitation. The
> composer.json metadata will still be read from the branch name you specify
> before the hash. Because of that in some cases it will not be a practical
> workaround, and you should always try to switch to tagged releases as soon
> as you can.

There is no simple work-around to this limitation. It is therefore strongly recommended that you do not use it.

## Need to override a package version

Let's say your project depends on package A, which in turn depends on a specific
version of package B (say 0.1). But you need a different version of said package B (say 0.11).

You can fix this by aliasing version 0.11 to 0.1:

composer.json:

```json
{
    "require": {
        "A": "0.2",
        "B": "0.11 as 0.1"
    }
}
```

See [aliases](aliases.md) for more information.

## Memory limit errors

Composer may sometimes fail on some commands with this message:

`PHP Fatal error:  Allowed memory size of XXXXXX bytes exhausted <...>`

In this case, the PHP `memory_limit` should be increased.

> **Note:** Composer internally increases the `memory_limit` to `1.5G`.

To get the current `memory_limit` value, run:

```sh
php -r "echo ini_get('memory_limit').PHP_EOL;"
```

Try increasing the limit in your `php.ini` file (ex. `/etc/php5/cli/php.ini` for
Debian-like systems):

```ini
; Use -1 for unlimited or define an explicit value like 2G
memory_limit = -1
```

Or, you can increase the limit with a command-line argument:

```sh
php -d memory_limit=-1 composer.phar <...>
```

This issue can also happen on cPanel instances, when the shell fork bomb protection is activated. For more information, see the [documentation](https://documentation.cpanel.net/display/68Docs/Shell+Fork+Bomb+Protection) of the fork bomb feature on the cPanel site.

## Xdebug impact on Composer

To improve performance when the xdebug extension is enabled, Composer automatically restarts PHP without it.
You can override this behavior by using an environment variable: `COMPOSER_ALLOW_XDEBUG=1`.

Composer will always show a warning if xdebug is being used, but you can override this with an environment variable:
`COMPOSER_DISABLE_XDEBUG_WARN=1`. If you see this warning unexpectedly, then the restart process has failed:
please report this [issue](https://github.com/composer/composer/issues).

## "The system cannot find the path specified" (Windows)

1. Open regedit.
2. Search for an `AutoRun` key inside `HKEY_LOCAL_MACHINE\Software\Microsoft\Command Processor`,
   `HKEY_CURRENT_USER\Software\Microsoft\Command Processor`
   or `HKEY_LOCAL_MACHINE\Software\Wow6432Node\Microsoft\Command Processor`.
3. Check if it contains any path to non-existent file, if it's the case, just remove them.

## API rate limit and OAuth tokens

Because of GitHub's rate limits on their API it can happen that Composer prompts
for authentication asking your username and password so it can go ahead with its work.

If you would prefer not to provide your GitHub credentials to Composer you can
manually create a token using the following procedure:

1. [Create](https://github.com/settings/tokens) an OAuth token on GitHub.
[Read more](https://github.com/blog/1509-personal-api-tokens) on this.

2. Add it to the configuration running `composer config -g github-oauth.github.com <oauthtoken>`

Now Composer should install/update without asking for authentication.

## proc_open(): fork failed errors
If composer shows proc_open() fork failed on some commands:

`PHP Fatal error: Uncaught exception 'ErrorException' with message 'proc_open(): fork failed - Cannot allocate memory' in phar`

This could be happening because the VPS runs out of memory and has no Swap space enabled.

```sh
free -m

total used free shared buffers cached
Mem: 2048 357 1690 0 0 237
-/+ buffers/cache: 119 1928
Swap: 0 0 0
```

To enable the swap you can use for example:

```sh
/bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
/sbin/mkswap /var/swap.1
/sbin/swapon /var/swap.1
```
You can make a permanent swap file following this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-14-04).

## Degraded Mode

Due to some intermittent issues on Travis and other systems, we introduced a
degraded network mode which helps Composer finish successfully but disables
a few optimizations. This is enabled automatically when an issue is first
detected. If you see this issue sporadically you probably don't have to worry
(a slow or overloaded network can also cause those time outs), but if it
appears repeatedly you might want to look at the options below to identify
and resolve it.

If you have been pointed to this page, you want to check a few things:

- If you are using ESET antivirus, go in "Advanced Settings" and disable "HTTP-scanner"
  under "web access protection"
- If you are using IPv6, try disabling it. If that solves your issues, get in touch
  with your ISP or server host, the problem is not at the Packagist level but in the
  routing rules between you and Packagist (i.e. the internet at large). The best way to get
  these fixed is raise awareness to the network engineers that have the power to fix it.
  Take a look at the next section for IPv6 workarounds.

- If none of the above helped, please report the error.

## Operation timed out (IPv6 issues)

You may run into errors if IPv6 is not configured correctly. A common error is:

```
The "https://getcomposer.org/version" file could not be downloaded: failed to
open stream: Operation timed out
```

We recommend you fix your IPv6 setup. If that is not possible, you can try the
following workarounds:

**Workaround Linux:**

On linux, it seems that running this command helps to make ipv4 traffic have a
higher prio than ipv6, which is a better alternative than disabling ipv6 entirely:

```bash
sudo sh -c "echo 'precedence ::ffff:0:0/96 100' >> /etc/gai.conf"
```

**Workaround Windows:**

On windows the only way is to disable ipv6 entirely I am afraid (either in windows or in your home router).

**Workaround Mac OS X:**

Get name of your network device:

```bash
networksetup -listallnetworkservices
```

Disable IPv6 on that device (in this case "Wi-Fi"):

```bash
networksetup -setv6off Wi-Fi
```

Run composer ...

You can enable IPv6 again with:

```bash
networksetup -setv6automatic Wi-Fi
```

That said, if this fixes your problem, please talk to your ISP about it to
try and resolve the routing errors. That's the best way to get things resolved
for everyone.

## Composer hangs with SSH ControlMaster

When you try to install packages from a Git repository and you use the `ControlMaster`
setting for your SSH connection, Composer might just hang endlessly and you see a `sh`
process in the `defunct` state in your process list.

The reason for this is a SSH Bug: https://bugzilla.mindrot.org/show_bug.cgi?id=1988

As a workaround, open a SSH connection to your Git host before running Composer:

```bash
ssh -t git@mygitserver.tld
composer update
```

See also https://github.com/composer/composer/issues/4180 for more information.

## Zip archives are not unpacked correctly.

Composer can unpack zipballs using either a system-provided `unzip` utility or PHP's
native `ZipArchive` class. The `ZipArchive` class is preferred on Windows. On other
OSes where ZIP files can contain permissions and symlinks, the `unzip` utility is
preferred. You're advised to install it if you need these features.
