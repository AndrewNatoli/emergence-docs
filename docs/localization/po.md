A `.po` file contains all the translations for a given language.

## Creating
`.po` files can be created by opening the generated `.pot` file with
a graphical tool like [Poedit](http://poedit.net/), or by renaming the
generated `.pot` file to `.po` and opening it in any text editor.

Given the example segment from the [Generating a .pot file](pot) guide,
the translated version saved in the `.po` file might look like this:

```
#: php-classes/Laddr/ProjectsRequestHandler.php:56
#: php-classes/Laddr/ProjectsRequestHandler.php:92
msgid "Parameter \"username\" required"
msgstr "Se requiere el parámetro \"nombre de usuario\" "

#: php-classes/Laddr/ProjectsRequestHandler.php:60
#: php-classes/Laddr/ProjectsRequestHandler.php:96
msgid "User not found"
msgstr "No se encuentra el usuario."

#: php-classes/Laddr/ProjectsRequestHandler.php:69
msgid "This member is already in this project"
msgstr "Este miembro ya participa en este proyecto"

#: php-classes/Laddr/ProjectsRequestHandler.php:102
msgid "Are you sure you want to remove %s from %s?"
msgstr "¿Seguro(a) de que quieres quitar %s de %s?"
```

## Uploading
The fully or partially translated `.po` file should be uploaded to
the site under a path+filename like `locales/es_US.utf8/site.po`, where
`es_US` matches a locale that has been installed on the server operating
system. Emergence will transparently compile the `.po` file to a binary `.mo`
as needed using the `gettext` command line tools installed to the server, so
you should not upload any `.mo` files.

## Next steps
- [Switching locale](switching)