## Configuration scripts
Files in the `php-config` root collection are optional and will be executed by the autoloader if present when a corresponding class is loaded, before flow returns to the original code.
These can be used to dynamically configure a class with site-, sessino-, or request- specific customizations via any public static properties or methods exposed by the class.