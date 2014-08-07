## Background
The filesystem hierarchy within an Emergence site is designed more to resemble something like the Unix [Filesystem Hierarchy Standard](http://www.pathname.com/fhs/) than most PHP framework or package management schemes, where function comes first in the hierarchy before package. For example, path to a script on a Unix-like system might be `/usr/local/bin/myapp-dosomething`, while in another PHP framework it could look something like `./vendor/MyCompany/MyPackage/src/bin/dosomething.php`. On a Unix terminal it is easy to discover what commands are available, your shell can scan all the system `bin` directories and autocomplete command names for you. A sufficient amount of code could of course still do that even if they were dispersed between a web of vendor and package hierarchies, but this makes it simpler for people AND programs to discover and create new commands. Now consider the directories on many Unix-like systems like `/etc/cron.d` or `/etc/apache2/vhosts.d` that confer not just organization on their contents, but active behaviors just by nature of being within the right tree. List those directories to see all the installed startup scripts or cron jobs, and just create a new file to extend the system's built-in workflows.

Putting function at the forefront of the file hierarchy and package/item names on the right side create an environment that is easier for people to explore and extend, and makes it simpler to design site components that are extensible and customizable.

## Standard root collections

- `_parent`: This is a virtual tree that provides read-only access to the lower, inherited layer of the site's filesystem in a mirrored hierarchy.
- `site-root`: Public-facing namespace for the site, like the traditional htdocs. Files and scripts in here are available to be downloaded or executed via the web.
    - `site-root/css`: CSS sources files for site and packages
    - `site-root/js`: JavaScript source files for site and packages
    - `site-root/img`: Image files for site and packages
    - `site-root/sass`: SASS source and build directory (_maybe this shoud be moved outside site-root eventually?_)
- `php-classes`: All PHP classes available for autoload in [PSR-4](https://github.com/php-fig/fig-standards/blob/master/proposed/psr-4-autoloader/psr-4-autoloader.md) layout. Directories are namespaces.
- `php-config`: Like `php-classes`, directories in here need to correspond to PHP class namespaces and files to classes, but files are suffixed with `.config.php`. 
- `html-templates`: Templates, typically ending in .tpl for dwoo 1.x, used to render responses to HTML based on deepest match with the request path
- `dwoo-plugins`: Plugins that can be autoloaded by using within dwoo
- `sencha-workspace`: A site-level [workspace for Sencha CMD](http://docs.sencha.com/cmd/4.0.0/#!/guide/command_workspace)
- `sencha-build`: Output from Sencha CMD processing sources in `sencha-workspace`

### Proposed new root collections
- `dwoo-subtemplates`: A place where dwoo `{tempalate myTemplate}...{/template}` can be stored by name (one block per file) for autoloading by dwoo
- `sass-workspace`: A non-public source and build directory for SASS