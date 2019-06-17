# Composer 中文文档

## 规则

- 本仓库同步自 [英文官方仓库](https://github.com/composer/composer.git) 的 `master` 分支。
- 所有需要翻译的文档都位于 [doc](doc) 目录内。
- 保持文档的原始结构，便于与英文文档同步。 （ **不要修改文档的内容结构** ，例如，不要为文档手动添加页内导航等）

## 当前进度

- [x] [00-intro.md](doc/00-intro.md)
- [x] [01-basic-usage.md](doc/01-basic-usage.md)
- [ ] [02-libraries.md](doc/02-libraries.md)
- [ ] [03-cli.md](doc/03-cli.md)
- [ ] [04-schema.md](doc/04-schema.md)
- [ ] [05-repositories.md](doc/05-repositories.md)
- [x] [06-config.md](doc/06-config.md)
- [x] [07-community.md](doc/07-community.md)

- [ ] [aliases.md](doc/articles/aliases.md)
- [ ] [autoloader-optimization.md](doc/articles/autoloader-optimization.md)
- [ ] [custom-installers.md](doc/articles/custom-installers.md)
- [ ] [handling-private-packages-with-satis.md](doc/articles/handling-private-packages-with-satis.md)
- [ ] [http-basic-authentication.md](doc/articles/http-basic-authentication.md)
- [ ] [plugins.md](doc/articles/plugins.md)
- [ ] [scripts.md](doc/articles/scripts.md)
- [ ] [troubleshooting.md](doc/articles/troubleshooting.md)
- [ ] [vendor-binaries.md](doc/articles/vendor-binaries.md)
- [ ] [versions.md](doc/articles/versions.md)

- [ ] [how-do-i-install-a-package-to-a-custom-path-for-my-framework.md](doc/faqs/how-do-i-install-a-package-to-a-custom-path-for-my-framework.md)
- [ ] [how-to-install-composer-programmatically.md](doc/faqs/how-to-install-composer-programmatically.md)
- [x] [how-to-install-untrusted-packages-safely.md](doc/faqs/how-to-install-untrusted-packages-safely.md)
- [x] [should-i-commit-the-dependencies-in-my-vendor-directory.md](doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md)
- [x] [which-version-numbering-system-does-composer-itself-use.md](doc/faqs/which-version-numbering-system-does-composer-itself-use.md)
- [x] [why-are-unbound-version-constraints-a-bad-idea.md](doc/faqs/why-are-unbound-version-constraints-a-bad-idea.md)
- [x] [why-are-version-constraints-combining-comparisons-and-wildcards-a-bad-idea.md](doc/faqs/why-are-version-constraints-combining-comparisons-and-wildcards-a-bad-idea.md)
- [ ] [why-can't-composer-load-repositories-recursively.md](doc/faqs/why-can't-composer-load-repositories-recursively.md)

## 中文翻译贡献者名单

- [5-say](https://github.com/5-say)
- [wangsai](https://github.com/wangsai)

## 版权说明

Composer 中文文档采用 MIT 开源许可证发布。


---


Composer - Dependency Management for PHP
========================================

Composer helps you declare, manage, and install dependencies of PHP projects.

See [https://getcomposer.org/](https://getcomposer.org/) for more information and documentation.

[![Build Status](https://travis-ci.org/composer/composer.svg?branch=master)](https://travis-ci.org/composer/composer)

Installation / Usage
--------------------

Download and install Composer by following the [official instructions](https://getcomposer.org/download/).

For usage, see [the documentation](https://getcomposer.org/doc/).

Packages
--------

Find packages on [Packagist](https://packagist.org).

Community
---------

IRC channels are on irc.freenode.org: [#composer](irc://irc.freenode.org/composer)
for users and [#composer-dev](irc://irc.freenode.org/composer-dev) for development.

For support, Stack Overflow also offers a good collection of
[Composer related questions](https://stackoverflow.com/questions/tagged/composer-php).

Please note that this project is released with a
[Contributor Code of Conduct](http://contributor-covenant.org/version/1/4/).
By participating in this project and its community you agree to abide by those terms.

Requirements
------------

PHP 5.3.2 or above (at least 5.3.4 recommended to avoid potential bugs)

Authors
-------

- Nils Adermann  | [GitHub](https://github.com/naderman)  | [Twitter](https://twitter.com/naderman) | <naderman@naderman.de> | [naderman.de](http://naderman.de)
- Jordi Boggiano | [GitHub](https://github.com/Seldaek) | [Twitter](https://twitter.com/seldaek) | <j.boggiano@seld.be> | [seld.be](http://seld.be)

See also the list of [contributors](https://github.com/composer/composer/contributors) who participated in this project.

Security Reports
----------------

Please send any sensitive issue to [security@packagist.org](mailto:security@packagist.org). Thanks!

License
-------

Composer is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

Acknowledgments
---------------

- This project's Solver started out as a PHP port of openSUSE's
  [Libzypp satsolver](https://en.opensuse.org/openSUSE:Libzypp_satsolver).
