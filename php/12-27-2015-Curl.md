CURL php
=========================

What i learned today is how to use cURL in php. 

```php 
$url = "https://graph.facebook.com/< account >";
$ch = curl_init();
$timeout = 5;
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, $timeout);
$data = curl_exec($ch);
curl_close($ch);

var_dump($data);
```
