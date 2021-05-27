# **PLINK Magento Plugin**

<img alt="PHP" src="https://img.shields.io/badge/php-%23777BB4.svg?&style=for-the-badge&logo=php&logoColor=white"/>
<img alt="JavaScript" src="https://img.shields.io/badge/javascript-%23323330.svg?&style=for-the-badge&logo=javascript&logoColor=%23F7DF1E"/>

**Requirements:**

* Magento2 requirements [](https://devdocs.magento.com/guides/v2.4/install-gde/system-requirements.htmlhttps://google.com)
* Base currency is IDR
* Elastic search running

**How to install:**

1. Create folder named `PrismaLink` (required) <as vendor name>
2. Clone or copy this project into `PrismaLink` folder
3. Copy `PrismaLink` folder to `<your_magento_directory>/app/code`
4. Then from from your terminal type `cd  <your_magento_directory>`
5. Input these command sequentially
   `php bin/magento setup:upgrade`
   `php bin/magento setup:di:compile`
   `php bin/magento cache:flush`
6. Login to admin page. Go to `Store (on the side panel) > Sales (on the side panel) > payment method > click tab  other payment methods` and then input all required credential data

**Set base url**

`php bin/magento setup:store-config:set --base-url="http://localhost/magento"`

**Logging**
Logging is available in `tail -f <your_magento_directory>/var/log/debug`

**Payment Workflow**

![](https://docs.magento.com/user-guide/images/images/order-workflow.png)

**Uninstall Module**
$`bin/magento module:uninstall [--backup-code] [--backup-media] [--backup-db] [-r|--remove-data] [-c|--clear-static-content] \{ModuleName} ... {ModuleName}`

where `{ModuleName}` specifies the module name in `<VendorName>_<ModuleName>` format. For example, the Magento Customer module name is `Magento_Customer`. To get a list of module names, enter `magento module:status`

The module uninstall command performs the following tasks:

1. Verifies that the specified modules exist in the code base and are packages installed by [Composer](https://glossary.magento.com/composer).
   This command works *only* with modules defined as Composer packages.
2. Checks for dependencies with other modules; if there are any, the command terminates..
   To work around this, you can either uninstall all modules at the same time or you can uninstall the depending modules first.
3. Requests confirmation to proceed.
4. Puts the store in maintenance mode.
5. Processes the following command options.


   | OPTION           | MEANING                                                                         | BACKUP FILE NAME AND LOCATION     |
   | ------------------ | --------------------------------------------------------------------------------- | ----------------------------------- |
   | `--backup-code`  | Backs up the Magento file system (excluding`var` and `pub/static` directories). | var/backups/_filesystem.tgz       |
   | `--backup-media` | Backs up the pub/media directory.                                               | var/backups/_filesystem_media.tgz |
   | `--backup-db`    | Backs up the Magento 2 database.                                                | var/backups/_db.gz                |
6. If `--remove-data` is specified, removes the database schema and data defined in the module’s `Uninstall` classes.
   For each specified module to uninstall, invokes the `uninstall` method in its `Uninstall` class. This class must inherit from [Magento\Framework\Setup\UninstallInterface](https://github.com/magento/magento2/blob/2.4/lib/internal/Magento/Framework/Setup/UninstallInterface.php).
7. Removes the specified modules from the `setup_module` database table.
8. Removes the specified modules from the module list in the [deployment configuration](https://devdocs.magento.com/guides/v2.4/config-guide/config/config-php.html).
9. Removes code from the codebase using `composer remove`.
   Uninstalling a module *always* runs `composer remove`. The `--remove-data` option removes database data and schema defined by the module’s `Uninstall` class.
10. Cleans the [cache](https://glossary.magento.com/cache).
11. Updates generated classes.
12. If `--clear-static-content` is specified, cleans [generated static view files](https://devdocs.magento.com/guides/v2.4/config-guide/cli/config-cli-subcommands-static-view.html#config-cli-static-overview).
13. Takes the store out of maintenance mode.

For example, if you attempt to uninstall a module that another module depends on, the following message displays:

```
magento module:uninstall Magento_SampleMinimal
    Cannot uninstall module 'Magento_SampleMinimal' because the following module(s) depend on it:
        Magento_SampleModifyContent
```

One alternative is to uninstall both modules after backing up the Magento module file system, `pub/media` files, and database tables but *not* removing the module’s [database schema](https://glossary.magento.com/database-schema) or data:

```
bin/magento module:uninstall Magento_SampleMinimal Magento_SampleModifyContent --backup-code --backup-media --backup-db
```

Messages similar to the following display:

```
You are about to remove code and/or database tables. Are you sure?[y/N]y
Enabling maintenance mode
Code backup is starting...
Code backup filename: 1435261098_filesystem_code.tgz (The archive can be uncompressed with 7-Zip on Windows systems)
Code backup path: /var/www/html/magento2/var/backups/1435261098_filesystem_code.tgz
[SUCCESS]: Code backup completed successfully.
Media backup is starting...
Media backup filename: 1435261098_filesystem_media.tgz (The archive can be uncompressed with 7-Zip on Windows systems)
Media backup path: /var/www/html/magento2/var/backups/1435261098_filesystem_media.tgz
[SUCCESS]: Media backup completed successfully.
DB backup is starting...
DB backup filename: 1435261098_db.gz (The archive can be uncompressed with 7-Zip on Windows systems)
DB backup path: /var/www/html/magento2/var/backups/1435261098_db.gz
[SUCCESS]: DB backup completed successfully.
You are about to remove a module(s) that might have database data. Remove the database data manually after uninstalling, if desired.
Removing Magento_SampleMinimal, Magento_SampleModifyContent from module registry in database
Removing Magento_SampleMinimal, Magento_SampleModifyContent from module list in deployment configuration
Removing code from Magento codebase:
Loading composer repositories with package information
Updating dependencies (including require-dev)
  - Removing magento/sample-module-modifycontent (1.0.0)
Removing Magento/SampleModifycontent
  - Removing magento/sample-module-minimal (1.0.0)
Removing Magento/SampleMinimal
Writing lock file
Generating autoload files
Cache cleared successfully.
Generated classes cleared successfully.
Alert: Generated static view files were not cleared. You can clear them using the --clear-static-content option. Failure to clear static view files might cause display issues in the Admin and storefront.
Disabling maintenance mode
```
