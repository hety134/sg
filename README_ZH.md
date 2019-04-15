# SG - PHP Superglobals Management
[![Build Status](https://travis-ci.org/yulonghu/sg.svg?branch=master)](https://travis-ci.org/yulonghu/sg)

一个超级简单的PHP超全局变量管理扩展

[English Document](https://github.com/yulonghu/sg/blob/master/README.md)

### 介绍

[SG](https://github.com/yulonghu/sg) 全称 [Superglobals](http://php.net/manual/en/language.variables.superglobals.php)，引用全局作用域中可用的全部变量，扩展了PHP全局变量人性化的管理能力。

这些全局变量是：$GLOBALS，$_SERVER，$_GET，$_POST，$_FILES，$_COOKIE，$_SESSION，$_REQUEST，$_ENV。

使用SG类，复杂代码变得简单，变得优雅，开启自动过滤还能减少代码量，进而提高我们的开发效率。

### 亮点功能
- 简单，快速，轻量
- 零拷贝访问全局变量
- 支持自动过滤前后空格[PHP trim](http://php.net/manual/en/function.trim.php)
- 解决操作PHP全局变量时出现未定义系列的问题 (Undefined variable, Undefined offset)

## 安装
### PHP版本支持包括
- PHP 5.4 +
- PHP 7.0 +

### 下载源码
```
git clone https://github.com/yulonghu/sg.git
```

### Linux系统，编译SG扩展
```
$ /path/to/php/bin/phpize
$ ./configure --with-php-config=/path/to/php/bin/php-config
$ make && make install
```

#### php.ini，添加、开启扩展
```ini
extension=sg.so

[sg]
sg.enable = On
```

重启php进程，就安装成功啦。

## 帮助手册

### 支持的方法
```php
array sg::all(void)
mixed sg::get(string $key [, mixed $default_value = null])
bool sg::set(string $key, mixed $value)
bool sg::has(string $key)
bool sg::del(string $key)
```

### 支持的INI配置项
```ini
sg.enable = On/Off ; Default Off
sg.auto_trim = On/Off ; Strip whitespace with PHP trim
```

### 超全局变量 <=> SG MAP表

备注: 标识符__CONTAIN包含方法有 get，set，has，del

|PHP默认超全局变量|对应SG参数的缩写| Method Example|
| ------ | ------ | ------ |
|$GLOBALS|无|sg::all()|
|$_SERVER|s|sg::__CONTAIN('s')|
|$_GET|g|sg::__CONTAIN('g')|
|$_POST|p|sg::__CONTAIN('p')|
|$_FILES|f|sg::__CONTAIN('f')|
|$_COOKIE|c|sg::__CONTAIN('c')|
|$_SESSION|n|sg::__CONTAIN('n')|
|$_REQUEST|r|sg::__CONTAIN('r')|
|$_ENV|e|sg::__CONTAIN('e')|

### 方法的使用

#### bool sg::set(string $key, mixed $value)

设置全局作用域变量，找不到$key则新增，否则更新值。

返回值: TRUE 成功，FALSE 失败

```php
<?php
var_dump(sg::set('test', 'test apple'));
var_dump(sg::set('user.0.0', 'user 0 apple'));
var_dump(sg::set('user.0.1', 'user 1 apple'));
var_dump(sg::set('user.a.a', 'user a apple'));
var_dump(sg::set('user.a.b', 'user b apple'));
```
以上例子输出的结果:
```txt
bool(true)
bool(true)
bool(true)
bool(true)
bool(true)
```

#### mixed sg::get(string $key [, mixed $default_value = null])

读取全局作用域变量, 找不到$key时，$default_value值生效。

```php
<?php
var_dump(sg::get('test', 'test apple'));
var_dump(sg::get('user');
var_dump(sg::get('not_found', 'def');
var_dump(sg::get('user.1.2.3.4'));
```
以上例子输出的结果:
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

检查全局作用域中$key是否存在

返回值: TRUE 存在，FALSE 不存在

```php
<?php
var_dump(sg::has('test'));
var_dump(sg::has('not_found'));
```
以上例子输出的结果:
```
bool(true)
bool(false)
```

#### bool sg::del(string $key)

删除全局作用域中变量$key

返回值: TRUE 成功，FALSE 失败

```php
<?php
var_dump(sg::del('test'));
var_dump(sg::del('user.0.1'));
var_dump(sg::get('user');
```
以上例子输出的结果:
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
结果与 [$GLOBALS](https://www.php.net/manual/zh/reserved.variables.globals.php) 相同

## 补充PHP预定义的超全局变量例子

### 获取PHP预定义的超全局变量

|传统的获取方式 (短)|新获取方式|
| ------ | ------ |
|$_SERVER['key']|sg::get('s.key')|
|$_GET['key']|sg::get('g.key')|
|$_POST['key']|sg::get('p.key')|
|$_FILES['key']|sg::get('f.key')|
|$_COOKIE['key']|sg::get('c.key')|
|$_SESSION['key']|sg::get('n.key')|
|$_REQUEST['key']|sg::get('r.key')|
|$_ENV['key']|sg::get('e.key')|

|传统的获取方式 (长)|新获取方式|
| ------ | ------ |
|$_SERVER['key']['key1']['key2']|sg::get('s.key.key1.key2')|
|$_GET['key']['key1']['key2']|sg::get('g.key.key1.key2')|
|$_POST['key']['key1']['key2']|sg::get('p.key.key1.key2')|
|$_FILES['key']['key1']['key2']|sg::get('f.key.key1.key2')|
|$_COOKIE['key']['key1']['key2']|sg::get('c.key.key1.key2')|
|$_SESSION['key']['key1']['key2']|sg::get('n.key.key1.key2')|
|$_REQUEST['key']['key1']['key2']|sg::get('r.key.key1.key2')|
|$_ENV['key']['key1']['key2']|sg::get('e.key.key1.key2')|

|传统的获取方式 (isset + trim)|新获取方式|
| ------ | ------ |
|$key = isset($_SERVER['key']) ? trim($_SERVER['key']) : null;|$key = sg::get('s.key');|
|$key = isset($_GET['key']) ? trim($_GET['key']) : null;|$key = sg::get('g.key');|
|$key = isset($_POST['key']) ? trim($_POST['key']) : null;|$key = sg::get('p.key');|
|$key = isset($_FILES['key']) ? trim($_FILES['key']) : null;|$key = sg::get('f.key');|
|$key = isset($_COOKIE['key']) ? trim($_COOKIE['key']) : null;|$key = sg::get('c.key');|
|$key = isset($_SESSION['key']) ? trim($_SESSION['key']) : null;|$key = sg::get('n.key');|
|$key = isset($_REQUEST['key']) ? trim($_REQUEST['key']) : null;|$key = sg::get('r.key');|
|$key = isset($_ENV['key']) ? trim($_ENV['key']) : null;|$key = sg::get('e.key');|

|(PHP7) 传统的获取方式 (??)|新获取方式|
| ------ | ------ |
|$key = $_SERVER['key'] ?? null; $key = trim($key);|$key = sg::get('s.key');|
|$key = $_GET['key'] ?? null; $key = trim($key);|$key = sg::get('g.key');|
|$key = $_POST['key'] ?? null; $key = trim($key);|$key = sg::get('p.key');|
|$key = $_FILES['key'] ?? null; $key = trim($key);|$key = sg::get('f.key');|
|$key = $_COOKIE['key'] ?? null; $key = trim($key);|$key = sg::get('c.key');|
|$key = $_SESSION['key'] ?? null; $key = trim($key);|$key = sg::get('n.key');|
|$key = $_REQUEST['key'] ?? null; $key = trim($key);|$key = sg::get('r.key');|
|$key = $_ENV['key'] ?? null; $key = trim($key);|$key = sg::get('e.key');|

通过以上的整理，可以得出一个结论，传统取值方式容易出错，如果数组维度越深，代码复杂度会直线上升。

使用SG，这些情况都变得很简单。更新、删除方式类同。

### sg.auto_trim

演示自动过滤前后空格的例子, 支持两种方式开启自动过滤。

为了获得最佳性能，如果满足trim条件, sg将改变原始值。避免每次取值时重复做trim操作。

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
以上例子输出的结果:
```txt
bool(true)
string(10) "test apple"
```

## License
SG is open source software under the [PHP License v3.01](http://www.php.net/license/3_01.txt)

