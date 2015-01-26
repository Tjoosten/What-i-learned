Installing The Slim framework
==============================

What i learned today is how i install the Slim framework trough Composer.

### 1) Setup the composer file

```json
{
  "require": {
    "slim/slim": "2.*"
  }
}
```

and install it with the `php composer.phar` command in your terminal.

### 2) setup the index file

```php
<?php
  require 'vendor/autoload.php';

  $app = new \Slim\Slim();

  $app->get('/hello/:name', function ($name) {
    echo "Hello, $name";
  });

  // Bootstrap the project.
  $app->run();
```
