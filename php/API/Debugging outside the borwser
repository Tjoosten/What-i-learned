## Stuff I did to debug during API dev on my local machine

Run the PHP dev server on your given port

```
php -S 0.0.0.0:8080 -t public public/index.php
```

In httpie, pass `?XDEBUG_SESSION_START=foobar` on the query string with the request

```
http -v POST 0.0.0.0:8080/ XDEBUG_SESSION_START==foobar
```

XDebug ini settings

```ini
xdebug.remote_enable=1
xdebug.remote_host=localhost
xdebug.remote_port=9000
```

Hit the "Start Listening for PHP Debug connections" icon in PHPStorm

![](http://img.spz.im/VmMQb.png)
