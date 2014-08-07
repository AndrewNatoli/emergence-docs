## Prerequisites
If `compass` isn't already installed as a command on the server, install it:

```language-bash
user@hostname ~ $ sudo apt-get install ruby-compass
```

## Compiling Sass to CSS
The Emergence skeleton site has a script called `compile.php` within `site-root/sass` that will compile all
`.sass` and `.scss` files in the `site-root/sass` directory and import the resulting CSS files to `site-root/css`.

Visit `/sass/compile` and wait for the request to complete to trigger a recompile.