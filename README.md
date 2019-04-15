# SG - PHP Superglobals Management
[![Build Status](https://travis-ci.org/yulonghu/sg.svg?branch=master)](https://travis-ci.org/yulonghu/sg)

A Simple PHP Superglobals Management

[中文文档](https://github.com/yulonghu/sg/blob/master/README_ZH.md)

### Introduction

[SG](https://github.com/yulonghu/sg) Full name [Superglobals](http://php.net/manual/en/language.variables.superglobals.php), References all variables available in global scope, Extends the humanized management ability of PHP global variables.

These superglobal variables are: $GLOBALS, $_SERVER, $_GET, $_POST, $_FILES, $_COOKIE, $_SESSION, $_REQUEST, $_ENV.

Use SG can make complex code to simple, Turn on Auto-trim can save a lot of PHP code, So improve development efficiency.

### Features
- Simple, Fast, Lightweight
- Access global variable zero-copy
- Strip whitespace with [PHP trim](http://php.net/manual/en/function.trim.php)
- Solve problems with PHP undefined series (Undefined variable, Undefined offset)

## Install
### Supported Version
- PHP 5.4 +
- PHP 7.0 +

### DownLoad
```
git clone https://github.com/yulonghu/sg.git
```

### Compile SG in Linux
```
$ /path/to/php/bin/phpize
$ ./configure --with-php-config=/path/to/php/bin/php-config
$ make && make install
```

#### Add the follow information to your php.ini
```ini
extension=sg.so

[sg]
sg.enable = On
```

Restart the php-fpm.

## Help Manual

### Methods
```php
array sg::all(void)
mixed sg::get(string $key [, mixed $default_value = null])
bool sg::set(string $key, mixed $value)
bool sg::has(string $key)
bool sg::del(string $key)
```

### Inis
```ini
sg.enable = On/Off ; Default Off
sg.auto_trim = On/Off ; Strip whitespace with PHP trim
```

### Superglobals <=> SG HashMap
- Identifier __CONTAIN contains methods get, set, has, del
- When management sessions (HashKey = n), First call the function session_start()

|PHP Predefined Superglobals|SG HashKey| Method Example|
| ------ | ------ | ------ |
|$GLOBALS|NIL|sg::all()|
|$_SERVER|s|sg::__CONTAIN('s')|
|$_GET|g|sg::__CONTAIN('g')|
|$_POST|p|sg::__CONTAIN('p')|
|$_FILES|f|sg::__CONTAIN('f')|
|$_COOKIE|c|sg::__CONTAIN('c')|
|$_SESSION|n|sg::__CONTAIN('n')|
|$_REQUEST|r|sg::__CONTAIN('r')|
|$_ENV|e|sg::__CONTAIN('e')|

### Usage

#### bool sg::set(string $key, mixed $value)
```php
<?php
var_dump(sg::set('test', 'test apple'));
var_dump(sg::set('user.0.0', 'user 0 apple'));
var_dump(sg::set('user.0.1', 'user 1 apple'));
var_dump(sg::set('user.a.a', 'user a apple'));
var_dump(sg::set('user.a.b', 'user b apple'));
```
The above example will output:
```txt
bool(true)
bool(true)
bool(true)
bool(true)
bool(true)
```

#### mixed sg::get(string $key [, mixed $default_value = null])
```php
<?php
var_dump(sg::get('test', 'test apple'));
var_dump(sg::get('user');
var_dump(sg::get('not_found', 'def');
var_dump(sg::get('user.1.2.3.4'));
```
The above example will output:
```txt
string(10) "test apple"
array(2) {
  [0]=>
  array(2) {
    [0]=>
    string(12) "user 0 apple"
    [1]=>
    string(12) "user 1 apple"
  }
  ["a"]=>
  array(2) {
    ["a"]=>
    string(12) "user a apple"
    ["b"]=>
    string(12) "user b apple"
  }
}
string(3) "def"
NULL
```

#### bool sg::has(string $key)
```php
<?php
var_dump(sg::has('test'));
var_dump(sg::has('not_found'));
```
The above example will output:
```
bool(true)
bool(false)
```

#### bool sg::del(string $key)
```php
<?php
var_dump(sg::del('test'));
var_dump(sg::del('user.0.1'));
var_dump(sg::get('user');
```
The above example will output:
```
bool(true)
bool(true)
array(2) {
  [0]=>
  array(1) {
    [0]=>
    string(12) "user 0 apple"
  }
  ["a"]=>
  array(2) {
    ["a"]=>
    string(12) "user a apple"
    ["b"]=>
    string(12) "user b apple"
  }
}
```

### array sg::all(void)
The same result as [$GLOBALS](https://www.php.net/manual/zh/reserved.variables.globals.php)

## Example

### Example #1

|OLD GET METHOD (Short)|NEW GET METHOD|
| ------ | ------ |
|$_SERVER['key']|sg::get('s.key')|
|$_GET['key']|sg::get('g.key')|
|$_POST['key']|sg::get('p.key')|
|$_FILES['key']|sg::get('f.key')|
|$_COOKIE['key']|sg::get('c.key')|
|$_SESSION['key']|sg::get('n.key')|
|$_REQUEST['key']|sg::get('r.key')|
|$_ENV['key']|sg::get('e.key')|

|OLD GET METHOD (Long)|NEW GET METHOD|
| ------ | ------ |
|$_SERVER['key']['key1']['key2']|sg::get('s.key.key1.key2')|
|$_GET['key']['key1']['key2']|sg::get('g.key.key1.key2')|
|$_POST['key']['key1']['key2']|sg::get('p.key.key1.key2')|
|$_FILES['key']['key1']['key2']|sg::get('f.key.key1.key2')|
|$_COOKIE['key']['key1']['key2']|sg::get('c.key.key1.key2')|
|$_SESSION['key']['key1']['key2']|sg::get('n.key.key1.key2')|
|$_REQUEST['key']['key1']['key2']|sg::get('r.key.key1.key2')|
|$_ENV['key']['key1']['key2']|sg::get('e.key.key1.key2')|

|OLD GET METHOD (isset + trim)|NEW GET METHOD|
| ------ | ------ |
|$key = isset($_SERVER['key']) ? trim($_SERVER['key']) : null;|$key = sg::get('s.key');|
|$key = isset($_GET['key']) ? trim($_GET['key']) : null;|$key = sg::get('g.key');|
|$key = isset($_POST['key']) ? trim($_POST['key']) : null;|$key = sg::get('p.key');|
|$key = isset($_FILES['key']) ? trim($_FILES['key']) : null;|$key = sg::get('f.key');|
|$key = isset($_COOKIE['key']) ? trim($_COOKIE['key']) : null;|$key = sg::get('c.key');|
|$key = isset($_SESSION['key']) ? trim($_SESSION['key']) : null;|$key = sg::get('n.key');|
|$key = isset($_REQUEST['key']) ? trim($_REQUEST['key']) : null;|$key = sg::get('r.key');|
|$key = isset($_ENV['key']) ? trim($_ENV['key']) : null;|$key = sg::get('e.key');|

|(PHP7) OLD GET METHOD (??)|NEW GET METHOD|
| ------ | ------ |
|$key = $_SERVER['key'] ?? null; $key = trim($key);|$key = sg::get('s.key');|
|$key = $_GET['key'] ?? null; $key = trim($key);|$key = sg::get('g.key');|
|$key = $_POST['key'] ?? null; $key = trim($key);|$key = sg::get('p.key');|
|$key = $_FILES['key'] ?? null; $key = trim($key);|$key = sg::get('f.key');|
|$key = $_COOKIE['key'] ?? null; $key = trim($key);|$key = sg::get('c.key');|
|$key = $_SESSION['key'] ?? null; $key = trim($key);|$key = sg::get('n.key');|
|$key = $_REQUEST['key'] ?? null; $key = trim($key);|$key = sg::get('r.key');|
|$key = $_ENV['key'] ?? null; $key = trim($key);|$key = sg::get('e.key');|

### sg.auto_trim

For best performance, sg will change the original value if the trim condition is met. Avoid repeat the trim operation each time you take a value.

```php
<?php
ini_set('sg.auto_trim', 1);
function One() {
    var_dump(sg::set('test', ' test apple ')); // Auto-call PHP trim
}
function Two() {
    var_dump(sg::get('test'));
}
```
The above example will output:
```txt
bool(true)
string(10) "test apple"
```

## License
SG is open source software under the [PHP License v3.01](http://www.php.net/license/3_01.txt)

