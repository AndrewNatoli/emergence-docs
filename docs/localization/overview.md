Localization allows you to make your site available in one or more different languages
while using the same code and templates. Emergence utilizes [PHP's built-in implementation](http://us2.php.net/gettext)
of the [GNU gettext](https://en.wikipedia.org/wiki/Gettext) library and utilities.

The general workflow is:

1. [Wrap translatable strings in PHP files and templates with calls to `gettext`](marking)
2. [Generate a `.pot` file providing a template for translation from all the `gettext` calls found in the source files](pot)
3. [Use a text editor or Poedit to create a `.po` file containing translations of strings from the `.pot` file for a particular language](po)
4. [Enable users to select locale by setting a cookie or request parameter](switching)

## Requirements
- The `gettext` command line tools must be installed to the server's operating system
- The language packs for any locale you plan to offer a translation for must be installed to the server's operating system
- The PHP `intl` extension must be installed (and is by default in the current Emergence setup guides)