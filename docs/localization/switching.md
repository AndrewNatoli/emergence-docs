There are several ways to switch what language a site is rendered with.

## Specify via query string parameter with each request
By appending `?locale=es_US` to a request URI you can select the locale
es_US.utf8 for a single response.

## Set via client-side cookie
By setting a cookie called `locale` to `es_US` you can switch the locale for
all responses during the life of the cookie. This is the recommended technique
for implementing a user-selectable locale. You can set the cookie either client-side
with JavaScript or server side with a response header.

## Send Accept-Language header with requests
If the browser or client application provides an Accept-Language header, Emergence
will attempt to pick the closest available locale.

## Set default language site-wide
To set the locale that will be used for all users in the absense of any of the above
personal settings, create a config file at `php-config/Emergence/Locale.config.php`
and set the default locale:

```language-php
<?php

Emergence\Locale::$default = 'es_US.utf8`;
```
