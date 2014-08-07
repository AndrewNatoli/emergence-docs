## Prerequisites
Emergence classes can be unit tested with the [PHPUnit](http://phpunit.de/) framework. If `phpunit` isn't already installed as a command on your server, download the PHAR and place it in your path:

```language-bash
user@hostname ~ $ wget https://phar.phpunit.de/phpunit.phar
user@hostname ~ $ sudo mv phpunit.phar /usr/local/bin/phpunit
user@hostname ~ $ sudo chmod +x /usr/local/bin/phpunit
```


## Running tests
Browse to the path `/phpunit/browse` on your server and click the **Run** button next to any test suite.


## Creating a new test case
Create a new file within `phpunit-tests/_suite-name_` with a filename ending in `Test.php`, e.g. `phpunit-tests/emergence.read-only/ActiveRecordTest.php`.

Follow PHPUnit's ["Writing Tests for PHPUnit"](http://phpunit.de/manual/current/en/writing-tests-for-phpunit.html) manual to write the test case's class.


## Creating a new test suite
Simply create a new folder under `phpunit-tests`.