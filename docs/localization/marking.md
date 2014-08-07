Every string that you want to be able to translate must be wrapped in a call to [`gettext()` or
its short alias `_()`](http://php.net/manual/en/function.gettext.php). Any strings without translations
available will be passed through unmodified, so it is safe to start wrapping your strings in these
functions ahead of getting started on translations.

## Templates
Within templates, you can use dwoo's short function syntax for simple strings:

```language-markup
<h1>{_ Localization}</h1>
<p>{_ "Be sure to use single or double quotation marks for strings containing spaces"}</p>
```

If you want to apply any additional functions or modifiers to translating strings you'll need to use parenthesis
to ensure that they are recognized when [you generate your `.pot` file](pot):

```language-markup
<body>
    {_("**Markdown** offers an easy way to use _simple_ formatting within a translatable string")|markdown}
</body>
```

Use the `capture` function and `sprintf` together to embed complex or dynamic markup in translatable strings:

```language-markup
{capture assign=person}{personLink $Member} {if $data->Role}({$data->Role|escape}){/if}{/capture}
{capture assign=project}{projectLink $Project}{/capture}

<p>{sprintf(_("%s has been added to %s"), $person, $project)}</p>
```

## PHP Scripts
Strings inside PHP route handlers, PHP classes, and PHP config scripts can be translated with the same functions:

```language-php
return static::throwError(_('User not found'));
```

For more complex strigs, use `sprintf`:
```language-php
return static::respond('confirm', [
    'question' => sprintf(
        _('Are you sure you want to remove %s from %s?')
        ,htmlspecialchars($Member->FullName)
        ,htmlspecialchars($Project->Title)
    )
]);
```

Model validation error messages get run through `gettext` automatically and emergence's string scanner will recognize
lines matching the format `'errorMessage' => '*'`
```language-php
public static $validators = [
    'ProjectID' => [
        'validator' => 'number'
        ,'min' => 1
        ,'errorMessage' => 'Please select a project'
    ]
    ,'Headline' => [
        'errorMessage' => 'Please enter a headline for the buzz'
    ]
];
```

## Next steps
- [Generating a .pot file](pot)