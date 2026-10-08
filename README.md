# Propel ORM 1.x — PHP 8.5 Compatibility Fork

This repository is a compatibility fork of Propel ORM 1.x, intended primarily for legacy Symfony 1.x applications running on newer PHP versions, including PHP 8.5.

The goal is to preserve existing Propel functionality while addressing PHP compatibility issues without requiring a complete application migration.

**This is not an official Propel release or a fully modernized ORM.** The changes are primarily intended to keep existing applications operational.

## Repository Information

- Repository: https://github.com/jestep/propel-orm
- Compatibility branch: `php85`
- Composer package name: `rock-symphony/propel-orm`
- Intended environment: Legacy Symfony 1.x / Propel ORM applications
- PHP target: PHP 8.5

The original Composer package name is retained for compatibility with existing applications.

## Installation Using Composer

The recommended approach is to install the fork through Composer using a VCS repository definition.

### 1. Configure composer.json

Add the fork to the application's `repositories` section:

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/jestep/propel-orm.git"
        }
    ],
    "require": {
        "rock-symphony/propel-orm": "dev-php85",
        "propel/sf-propel-o-r-m-plugin": "dev-master"
    }
}
```

Merge these entries with your existing Composer configuration rather than replacing the entire file.

The Symfony plugin dependency is needed only for applications using `sfPropelORMPlugin`.

For a legacy Symfony application using `lib/vendor` as its Composer vendor directory, the configuration may also include:

```json
{
    "config": {
        "vendor-dir": "lib/vendor"
    }
}
```

The paths in the following examples assume this directory layout.

### 2. Install dependencies

From the application's root directory:

```bash
composer update rock-symphony/propel-orm propel/sf-propel-o-r-m-plugin -W
```

For a new installation with an existing lock file:

```bash
composer install
```

Composer should retrieve the Propel fork from the `php85` branch.

**Note:** The `dev-php85` constraint references a development branch. Review dependency changes before deploying updates to production.

## Symfony 1.x Plugin Symlink

Legacy Symfony 1.x applications commonly expect plugins to be located in the application's `plugins/` directory.

When `sfPropelORMPlugin` is installed by Composer under `lib/vendor`, create a symbolic link so Symfony can locate it.

From the application root:

```bash
ln -s ../lib/vendor/propel/sf-propel-o-r-m-plugin plugins/sfPropelORMPlugin
```

Expected structure:

```text
application/
├── apps/
├── config/
├── lib/
│   └── vendor/
│       ├── autoload.php
│       ├── rock-symphony/
│       └── propel/
│           └── sf-propel-o-r-m-plugin/
├── plugins/
│   └── sfPropelORMPlugin -> ../lib/vendor/propel/sf-propel-o-r-m-plugin
└── web/
```

If an existing `plugins/sfPropelORMPlugin` directory is present, back it up or remove it only after verifying that it contains no custom modifications.

For example, to replace an existing plugin directory or symlink:

```bash
rm -rf plugins/sfPropelORMPlugin
ln -s ../lib/vendor/propel/sf-propel-o-r-m-plugin plugins/sfPropelORMPlugin
```

Verify the symlink:

```bash
ls -l plugins/sfPropelORMPlugin
readlink -f plugins/sfPropelORMPlugin
```

The resolved path should point to:

```text
<application-root>/lib/vendor/propel/sf-propel-o-r-m-plugin
```

Ensure that the plugin is enabled in the application's Symfony configuration, as required by the existing installation.

## Updating the Application

To retrieve the latest changes from the compatibility branch through Composer:

```bash
composer update rock-symphony/propel-orm -W
```

If both Propel ORM and its Symfony plugin need updating:

```bash
composer update rock-symphony/propel-orm propel/sf-propel-o-r-m-plugin -W
```

The symbolic link normally does not need to be recreated after a Composer update.

After updating, clear the Symfony application cache using the appropriate command for your installation, typically:

```bash
php symfony cc
```

Test the application and any Propel model-generation workflows before deploying to production.

## Compatibility Notes

This fork includes compatibility changes intended to address issues encountered when running older Propel ORM code on newer PHP releases.

Some legacy applications may require additional changes outside this repository, including changes to:

- Symfony framework classes and plugins
- Application-specific Propel models and behaviors
- Deprecated PHP functionality
- Error reporting and type compatibility
- Older third-party dependencies

Compatibility with PHP 8.5 does not imply that every legacy Symfony application will run without modification.

The fork has primarily been developed and tested against existing applications rather than a comprehensive matrix of PHP versions, databases, and frameworks.

## Troubleshooting

**Composer installs the original Propel package**

Verify that the VCS repository is defined in `composer.json` and that the requirement specifies:

```text
rock-symphony/propel-orm: dev-php85
```

Inspect the installed package:

```bash
composer show rock-symphony/propel-orm
```

**Symfony cannot locate sfPropelORMPlugin**

Verify the symbolic link and ensure that Composer installed the plugin at the expected location.

**Classes cannot be loaded**

Verify the application's existing autoloading configuration and confirm that the required Composer dependencies are installed.

**Errors after updating**

Clear the Symfony cache, review the PHP error log, and check for compatibility issues in the application or other legacy plugins. In some circumstances, deleting the entire cache directory is necessary to clear cache files if there are blocking errors in symfony's execution, it is recommended to backup and/or verify that the cache directory is not used by other applications prior to removal.

## Support and Maintenance

This repository is provided primarily for compatibility with existing legacy applications.

It is made publicly available in case the changes are useful to others maintaining similar systems.

There is no guarantee of ongoing maintenance, compatibility with all environments, or individual installation support.

Issues and pull requests may be reviewed as time permits, but this project should not be considered an actively supported replacement for modern ORM solutions.

For new applications, a currently maintained ORM or framework is generally preferable.

## License

This fork retains the licensing terms of the upstream Propel ORM project. Refer to the repository's license file for details.