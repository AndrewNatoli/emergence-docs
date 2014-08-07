Open <kbd>/sysadmin/xgettext</kbd> on your site to generate a `.pot` file. It may take a few moments
for the download to start as the script scans all the PHP and Dwoo source files on your site. `.pot` stands for
"Portable Object Template" and the file provides a starting point for any translation.

## Sample contents of `.pot` file
Line starting with # in the `.pot` file are comments and will indicate the file(s) and line number where
a given string was found. `msgid` shows the original string and `msgstr` is where you could fill in the
translated version.
```
#: php-classes/Laddr/ProjectsRequestHandler.php:56 php-classes/Laddr/ProjectsRequestHandler.php:92
msgid "Parameter \"username\" required"
msgstr ""

#: php-classes/Laddr/ProjectsRequestHandler.php:60 php-classes/Laddr/ProjectsRequestHandler.php:96
msgid "User not found"
msgstr ""

#: php-classes/Laddr/ProjectsRequestHandler.php:69
msgid "This member is already in this project"
msgstr ""

#: php-classes/Laddr/ProjectsRequestHandler.php:102
msgid "Are you sure you want to remove %s from %s?"
msgstr ""
```

## Next steps
- [Creating and uploading a .po file](po)