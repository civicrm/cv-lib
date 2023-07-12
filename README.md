# cv-lib

`cv-lib` is a subpackage provided by `cv`. It defines the essential core of `cv` -- locating and booting CiviCRM.

The canonical home for developing this code is in `cv.git:lib/`. It will be periodically published to the read-only
mirror `cv-lib.git` to facilitate usage by other projects.

## Installation

```bash
composer require civicrm/cv-lib
```

## Primary API

The library provides a handful of supported classes:

* `Civi\Cv\CmsBootstrap` supports the standard boot protocol. In this protocol, we search for a recognized UF/CMS, start
  that, and then start CiviCRM. The advantage of this protocol is that it is more representative of a typical
  HTTP-request. (Events and add-ons supported by UF/CMS and CRM will tend to work more normally.)

    Basic usage:

    ```php
    Civi\Cv\CmsBootstrap::singleton()->bootCms()->bootCivi();
    ```

    End-users may fine-tune the behavior by setting `CIVICRM_BOOT` (as documented in `cv`).

    As a developer, you may give additional options and hints -- for example, directing the boot log to a custom logger.
    For all options, see the docblocks.

* `Civi\Cv\Bootstrap` supports the legacy boot protocol. In this protocol, we search for `civicrm.settings.php` and
  start CiviCRM. Finally, we use `civicrm-core` API's to start the associated UF/CMS.

    Basic usage:

    ```php
    \Civi\Cv\Bootstrap::singleton()->boot();
    \CRM_Core_Config::singleton();
    \CRM_Utils_System::loadBootStrap([], FALSE);
    ```

    End-users may fine-tune the behavior by setting `CIVICRM_SETTING` (as documented in `cv`).

    As a developer, you may give additional options and hints -- for example, directing the boot log to a custom logger.
    For all options, see the docblocks.

## Experimental API

Other classes are included, but their contracts are subject to change.

A particularly interesting one is `BootTrait`.  This requires `symfony/console`, and it is used by most `cv` subcommands
to achieve common behaviors:

1. `BootTrait` defines certain CLI options (`--level`, `--user`, `--hostname`, etc).
2. `BootTrait` automatically decides between `Bootstrap.php` and `CmsBootstrap.php`.
3. `BootTrait` passes CLI options through to `Bootstrap.php` or `CmsBootstrap.php`.