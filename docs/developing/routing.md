Routing is the process by which an Emergence site finds the code that will handle a particular request. It does this by working through the requested URL from
left-to-right to find a PHP script within `site-root` to handle the request. If a static file is found at the path instead of a .php script, it will be transferred to the client with
appropriate content and caching headers.

For example when you request the page `http://codeforphilly.org/projects/Code_for_Philadelphia/edit`, codeforphilly.org will search in this order to find a handler:

1. `site-root/projects.php`
2. `site-root/projects/Code_for_Philadelphia.php`
3. `site-root/projects/Code_for_Philadelphia/edit.php`

In the case for codeforphilly.org, #1 (projects.php) will match first and the second two will never be checked. projects.php gets invoked and asked to handle the path "Code_for_Philadelphia/edit"

#Custom Routing
If a handler is not found by the method described above Emergence will under normal circumstances pass a 404 header to the client with an appropriate 404 page. You may choose to instead handle this event yourself.
To do so set the value of Site::$onNotFound as a [callable](http://us2.php.net/manual/en/language.types.callable.php). The most conveniant location to set this value is inside `php-config/Site.config.php'.
The callable will run immediatly prior the normal 404 response allowing you to handle the response as you wish.

### For Example ###
    Site::$onNotFound = function($message) {
        switch($action = Site::$requestPath[0])
        {
            default:
                if($Page = Page::getByHandle($action))
                {
                    return Page::renderPage();
                }
                else
                {
                    header('HTTP/1.0 404 Not Found');
                    die($message);
                }
        }
    };