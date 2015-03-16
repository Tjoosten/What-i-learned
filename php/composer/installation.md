Install composer on systems.
==============================

## Windows *(Manual install)* 

```bash
C:\Users\username>cd C:\bin
C:\bin>php -r "readfile('https://getcomposer.org/installer');" | php
```

**Note:** if the above fails due to readfile, use the http url or enable `php_openssl.dll` in `php.ini`

Create a new composer.bat file alongside composer.phar:

```bash 
C:\bin>echo @php "%~dp0composer.phar" %*>composer.bat
```

Close terminal and test it in a new terminal.

```bash
C:\Users\username>composer -V
```
