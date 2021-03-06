#PHP编码规范#

<h2 id="PSR-0">PSR-0 自动加载</h2>

为了自动加载器互通性，以下强制要求的描述内容必须遵守。
强制性
-----
* 合格的命名空间和类必须遵守以下结构：

    `\<Vendor Name>\(<Namespace>\)*<Class Name>`

    `\<应用包名>\(<命名空间>\)*<类名>`

* 命名空间必须有一个顶级命名空间（“Vendor Name”）。
* 命名空间可以有多个子命名空间（“Namespace”）。
* 从文件系统中加载时，命名空间的分隔符将转成DIRECTORY_SEPARATOR。
* 类名中每个_字符将转成DIRECTORY_SEPARATOR。命名空间中的_字符没有特殊意义。
* 从文件系统中加载时，合格的命名空间和类将加以后缀.php。
* 应用包名、命名空间和类名中的字母可以是任意大小写。

实例
----

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

命名空间和类名中的下划线
---------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

这些是为了容易实现自动加载互通性的最低标准。以下是可以加载PHP 5.3的类的加载器样例。
你可以调用该样例检查是否遵守标准。


样例
----

下面是一个简单方法，用来描述以上建议的标准是如何进行自动加载类。

```php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
```

<h2 id="PSR-1">PSR-1 基本编码标准</h2>

为保证团队合作编码的高度互通性，这章包含了PHP元素标准。

文中所用关键字 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", 和 "OPTIONAL" 在[RFC 2119][]中描述.

MUST: `必须`

MUST NOT: `禁止`

REQUIRED: `必要`

SHALL: `将要`

SHALL NOT: `不得`

SHOULD: `应该`

SHOULD NOT: `不该`

RECOMMENDED: `推荐`

MAY: `可以`

OPTIONAL: `可选`

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: #PSR-0


1. 概述
-------

- 源文件内容 `必须` 只用 `<?php` 或 `<?=` 标签。

- PHP源文件内容 `必须` 只用不含 BOM 头的 UTF-8 字符编码。

- 源文件 `应该` *不是* 声明符号 （类、函数和常量等）
  *就是* 辅助效应 (例如生成输出内容和修改.ini设置等)。
  但是 `不该` 两都皆有。

- 命名空间和类 `必须` 遵守[PSR-0][]标准。

- 类名 `必须` 按 `StudlyCaps（首字母大写驼峰法）` 来命名。

- 类的常量 `必须` 用大写字母与下划线来声明。

- 方法名 `必须` 按 `camelCase（首字母小写驼峰法）` 进行声明。


2. 源文件
--------

### 2.1. PHP标签

PHP代码 `必须` 使用长标签 `<?php ?>` 或短标签 `<?= ?>` ; 
`禁止` 使用其他标签。

### 2.2. 字符编码

PHP代码 `必须` 只用不含 BOM 头的 UTF-8 字符编码。

### 2.3. 辅助效应

一个源文件 `应该` 声明新符号（类、函数和常量等）并不引起其他辅助效应，
或者 `应该` 作为辅助效应执行逻辑运算，但是 `不该` 两都皆有。

仅引入源文件，短语 "辅助效应" 表示逻辑运算执行，但不直接与声明类、函数和常量关联。

"辅助效应"包含但不局限于：生成输出内容，明确调用`require`或`include`，连接额外服务，
修改ini设置，抛出错误或异常，修改全局（global）或静态（static）变量，读取或修改文件等。

以下是一个既有声明又有辅助效应的源文件的例子，需要避免：

```php
<?php
// 辅助效应: 修改 ini 设置
ini_set('error_reporting', E_ALL);

// 辅助效应: 加载源文件
include "file.php";

// 辅助效应: 生成输出内容
echo "<html>\n";

// 声明
function foo()
{
    // 函数内容
}
```

以下是一个只有声明没有辅助效应的的源文件的例子，需要效仿：

```php
<?php
// 声明
function foo()
{
    // 函数内容
}

// 条件声明*不*是辅助效应
if (! function_exists('bar')) {
    function bar()
    {
        // 函数内容
    }
}
```


3. 命名空间和类的名称
-------------------

命名空间和类 `必须` 遵守[PSR-0][]标准，即一个类一个源文件，
并且至少在一级命名空间里——顶级应用包名。

类名 `必须` 按 `StudlyCaps（首字母大写驼峰法）` 来声明。

PHP 5.3及以后版本 `必须` 用正规的命名空间进行代码输写，例如：

```php
<?php
// PHP 5.3及以后版本:
namespace Vendor\Model;

class Foo
{
}
```

PHP 5.2.x及早期版本 `应该` 用伪命名空间：`应用包名_（Vendor_）` 作为类名前缀。

```php
<?php
// PHP 5.2.x及早期版本:
class Vendor_Model_Foo
{
}
```

4. 类的常量、属性和方法
--------------------

术语"类（class）"提及所有类（classes）、接口（interfaces）和特征（traits）。

### 4.1. 常量

类的常量 `必须` 用大写字母与下划线来声明，例如：

```php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. 属性

关于用`$StudlyCaps（首字母大写驼峰法）`、`$camelCase（首字母小写驼峰法）`
和`$under_score（下划线法）命名属性，这份指导故意回避任何建议。

无论命名习惯如何使用，`应该` 一贯应用一个合理的范围，可以是应用包级（vendor-level）、
包级（package-level）、类级（class-level）或方法级（method-level）。

### 4.3. 方法

方法名 `必须` 按 `camelCase()（首字母小写驼峰法）`命名。
