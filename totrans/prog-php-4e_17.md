# 第十六章\. Web 服务

历史上，每当有两个系统需要通信时，通常会创建一个新的协议（例如，用于发送邮件的 SMTP，用于接收邮件的 POP3，以及数据库客户端和服务器使用的众多协议）。Web 服务的理念是通过基于 XML 和 HTTP 的标准化远程过程调用机制，消除创建新协议的需要。

Web 服务使得集成异构系统变得简单。比如说，你正在为一个已经存在的图书馆系统编写 Web 界面。它有一个复杂的数据库表系统，并且在程序代码中嵌入了大量的业务逻辑来操作这些表。而且它是用 C++ 编写的。你可以重新在 PHP 中实现业务逻辑，编写大量代码以正确操作表，或者你可以在 C++ 中写少量代码来将图书馆操作（例如，为用户借书、查看归还日期、查看该用户的逾期罚款）作为 Web 服务公开。现在，你的 PHP 代码只需处理 Web 前端；它可以使用图书馆服务来完成所有繁重的工作。

# REST 客户端

*RESTful Web 服务* 是使用 HTTP 和表现层状态转移（REST）原则实现的 Web API 的宽泛描述。它指的是一组资源以及客户端可以通过 API 执行的基本操作。

例如，一个 API 可能描述了一组作者及其所贡献的书籍。每个对象类型内的数据是任意的。在这种情况下，*资源*是每个单独的作者、每本单独的书籍，以及所有作者、所有书籍的集合，以及每个作者所贡献的书籍。每个资源必须有一个唯一的标识符，以便 API 调用知道正在检索或操作哪个资源。

你可能会用一个简单的类集合来表示书籍和作者资源，就像示例 16-1 中那样。

##### 示例 16-1\. 书籍和作者类

```php
class Book {
 public $id;
 public $name;
 public $edition;

 public function __construct($id) {
 $this->id = $id;
 }
}

class Author {
 public $id;
 public $name;
 public $books = array();

 public function __construct($id) {
 $this->id = $id;
 }
}
```

因为 HTTP 是为 REST 架构而建的，它提供了一组*动词*用于与 API 进行交互。我们已经看到了 `GET` 和 `POST` 动词，通常用于代表“检索数据”和“执行操作”。RESTful Web 服务引入了另外两个动词，`PUT` 和 `DELETE`：

`GET`

检索关于资源或资源集合的信息。

`POST`

创建一个新资源。

`PUT`

更新资源使用新数据，或者用新资源替换一组资源。

`DELETE`

删除一个资源或一组资源。

例如，`Books` 和 `Authors` API 可能包括以下基于对象类数据的 REST 端点：

`GET /api/authors`

返回集合中每个作者的标识符列表。

`POST /api/authors`

提供关于新作者的信息，创建新作者到集合中。

`GET /api/authors/`*`id`*

检索集合中具有标识符*`id`*的作者并将其返回。

`PUT /api/authors/`*`id`*

针对具有标识符*`id`*的作者的更新信息，更新集合中该作者的信息。

`DELETE /api/authors/`*`id`*

从集合中删除具有标识符*`id`*的作者。

`GET /api/authors/`*`id`*`/books`

检索具有标识符*`id`*的作者为每本书的标识符列表。

`POST /api/authors/`*`id`*`/books`

给定有关新书的信息，创建集合中具有标识符*`id`*的作者下的新书。

`GET /api/books/`*`id`*

检索集合中具有标识符*`id`*的书并将其返回。

RESTful web services 提供的`GET`、`POST`、`PUT`和`DELETE`动词可以被视为数据库中典型的*create*、*retrieve*、*update*和*delete*（CRUD）操作的粗略等效，尽管它们可以与集合相关联，而不仅仅是实体，这在 CRUD 实现中是典型的。

## 响应

在每个前述的 API 端点中，HTTP 状态码用于提供请求的结果。HTTP 提供了一长串标准状态码：例如，当您创建资源时将返回`201 Created`，当您向不存在的端点发送请求时将返回`501 Not Implemented`。

虽然完整的 HTTP 代码列表超出了本章的范围，但一些常见的代码包括：

`200 OK`

请求已成功完成。

`201 Created`

已成功完成创建新资源的请求。

`400 Bad Request`

请求命中了有效的端点，但格式错误，无法完成。

`401 Unauthorized`

与`403 Forbidden`一起，表示一个有效请求，但由于缺乏权限而无法完成。通常，此响应指示需要授权但尚未提供。

`403 Forbidden`

与`401 Unauthorized`类似，此响应指示一个有效请求，但由于缺乏权限而无法完成。通常，此响应指示授权可用但用户缺乏执行请求操作的权限。

`404 Not Found`

未找到资源（例如，尝试删除不存在 ID 的作者）。

`500 Internal Server Error`

服务器端发生错误。

这些代码仅作为指南和典型响应；RESTful API 提供的确切响应由 API 本身详细说明。

## 检索资源

检索资源信息涉及简单的`GET`请求。示例 16-2 使用*curl*扩展来格式化 HTTP 请求，在其上设置参数，发送请求并获取返回的信息。

##### 示例 16-2\. 检索作者数据

```php
$authorID = "ktatroe";
$url = "http://example.com/api/authors/{$authorID}";

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);

$response = curl_exec($ch);
$resultInfo = curl_getinfo($ch);

curl_close($ch);

// decode the JSON and use a Factory to instantiate an Author object
$authorJSON = json_decode($response);
$author = ResourceFactory::authorFromJSON($authorJSON);
```

要获取有关作者的信息，此脚本首先构造代表资源端点的 URL。然后，初始化 curl 资源并向其提供构造的 URL。最后，执行 curl 对象，发送 HTTP 请求，等待响应并返回它。

在这种情况下，响应是 JSON 数据，解码后传递给`Author`的`Factory`方法以构造`Author`类的实例。

## 更新资源

更新现有资源比检索有关资源信息稍微复杂一些。在这种情况下，您需要使用`PUT`动词。由于`PUT`最初是用于处理文件上传的，因此`PUT`请求要求您从文件中将数据流式传输到远程服务。

该脚本不是在磁盘上创建文件并从中进行流式传输，而是在示例 16-3 中使用 PHP 提供的`'memory'`流，首先用要发送的数据填充它，然后将其倒带到刚刚写入的数据的开头，最后将 curl 对象指向该文件。

##### 示例 16-3\. 更新书籍数据

```php
$bookID = "ProgrammingPHP";
$url = "http://example.com/api/books/{$bookID}";

$data = json_encode(array(
 'edition' => 4,
));

$requestData = http_build_query($data, '', '&');

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);

$fh = fopen("php://memory", 'rw');
fwrite($fh, $requestData);
rewind($fh);

curl_setopt($ch, CURLOPT_INFILE, $fh);
curl_setopt($ch, CURLOPT_INFILESIZE, mb_strlen($requestData));
curl_setopt($ch, CURLOPT_PUT, true);

$response = curl_exec($ch);
$resultInfo = curl_getinfo($ch);

curl_close($ch);
fclose($fh);
```

## 创建资源

要创建新资源，请使用`POST`动词调用适当的端点。请求的数据以`POST`请求的典型键值形式放入其中。

在示例 16-4 中，用于创建新作者的`Author` API 端点将创建新作者的信息作为 JSON 格式对象，存储在`'data'`键下。

##### 示例 16-4\. 创建作者

```php
<?php $newAuthor = new Author('pbmacintyre');
$newAuthor->name = "Peter Macintyre";

$url = "http://example.com/api/authors";

$data = array(
 'data' => json_encode($newAuthor)
);

$requestData = http_build_query($data, '', '&');

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);

curl_setopt($ch, CURLOPT_POSTFIELDS, $requestData);
curl_setopt($ch, CURLOPT_POST, true);

$response = curl_exec($ch);
$resultInfo = curl_getinfo($ch);

curl_close($ch);
```

该脚本首先构造一个新的`Author`实例，并将其值编码为 JSON 格式字符串。然后，它以适当的格式构造键值数据，将该数据提供给 curl 对象，并发送请求。

## 删除资源

删除资源同样直截了当。示例 16-5 创建一个请求，并通过`curl_setopt()`函数将动词设置为`'DELETE'`，然后发送请求。

##### 示例 16-5\. 删除书籍

```php
<?php $authorID = "ktatroe";
$bookID = "ProgrammingPHP";
$url = "http://example.com/api/authors/{$authorID}/books/{$bookID}";

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);

curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');

$result = curl_exec($ch);
$resultInfo = curl_getinfo($ch);

curl_close($ch);
```

# XML-RPC

虽然如今不如 REST 流行，但 XML-RPC 和 SOAP 是两种用于创建 Web 服务的较旧的标准协议。XML-RPC 是两者中更旧且更简单的，而 SOAP 则更新且更复杂。

PHP 通过*xmlrpc*扩展提供对 SOAP 和 XML-RPC 的访问，该扩展基于[xmlrpc-epi 项目](http://xmlrpc-epi.sourceforge.net)。*xmlrpc*扩展不会默认编译进 PHP 中，因此在编译 PHP 时需要添加`--with-xmlrpc`到您的`configure`行。

## 服务器

示例 16-6 展示了一个非常基础的 XML-RPC 服务器，仅暴露一个功能（XML-RPC 称之为“方法”）。该函数`multiply()`用于两个数字相乘并返回结果。这并不是一个非常激动人心的示例，但它展示了 XML-RPC 服务器的基本结构。

##### 示例 16-6\. Multiplier XML-RPC 服务器

```php
<?php
// expose this function via RPC as "multiply()"
function times ($method, $args) {
 return $args[0] * $args[1];
}

$request = $HTTP_RAW_POST_DATA;

if (!$request) {
 $requestXml = $_POST['xml'];
}

$server = xmlrpc_server_create() or die("Couldn't create server");
xmlrpc_server_register_method($server, "multiply", "times");

$options = array(
 'output_type' => 'xml',
 'version' => 'auto',
);

echo xmlrpc_server_call_method($server, $request, null, $options);

xmlrpc_server_destroy($server);
```

*xmlrpc* 扩展会为您处理分派。也就是说，它会确定客户端试图调用哪个方法，解码参数，并调用相应的 PHP 函数。然后，它返回一个 XML 响应，该响应编码了函数返回的任何可以被 XML-RPC 客户端解码的值。

使用 `xmlrpc_server_create()` 创建服务器：

```php
$server = xmlrpc_server_create();
```

创建服务器后，通过 XML-RPC 分派机制使用 `xmlrpc_server_register_method()` 公开函数：

```php
xmlrpc_server_register_method(*`server`*, *`method`*, *`function`*);
```

*`method`* 参数是 XML-RPC 客户端知道的名称。*`function`* 参数是实现该 XML-RPC 方法的 PHP 函数。在 示例 16-6 中，`multiply()` XML-RPC 客户端方法由 PHP 中的 `times()` 函数实现。通常，服务器会多次调用 `xmlrpc_server_register_method()` 来公开多个函数。

注册了所有方法后，调用 `xmlrpc_server_call_method()` 将传入的请求分派给适当的函数：

```php
$response = xmlrpc_server_call_method(*`server`*, *`request`*, *`user_data`* [, *`options`*]);
```

*`request`* 是 XML-RPC 请求，通常作为 HTTP `POST` 数据发送。我们通过 `$HTTP_RAW_POST_DATA` 变量获取它。它包含要调用的方法名称及其参数。这些参数被解码为 PHP 数据类型，并调用函数（本例中为 `times()`）。

作为 XML-RPC 方法公开的函数需要接受两到三个参数：

```php
$retval = exposedFunction(*`method`*, *`args`* [, *`user_data`*]);
```

*`method`* 参数包含 XML-RPC 方法的名称（因此您可以在多个名称下公开一个 PHP 函数）。方法的参数传递给数组 *`args`*，可选的 *`user_data`* 参数是 `xmlrpc_server_call_method()` 函数的 *`user_data`* 参数。

*`options`* 参数用于 `xmlrpc_server_call_method()`，它是一个将选项名称映射到它们的值的数组。选项包括：

`output_type`

控制所使用的数据编码。允许的值为 `"php"` 或 `"xml"`（默认）。

`verbosity`

控制添加到输出 XML 的空白量，以便使其对人类可读。允许的值为 `"no_white_space"`、`"newlines_only"` 和 `"pretty"`（默认）。

`escaping`

控制哪些字符需要转义以及如何转义它们。可以将多个值作为子数组给出。允许的值为 `"cdata"`、`"non-ascii"`（默认）、`"non-print"`（默认）和 `"markup"`（默认）。

`versioning`

控制要使用的 Web 服务系统。允许的值为 `"simple"`、`"soap 1.1"`、`"xmlrpc"`（客户端的默认值）和 `"auto"`（服务器的默认值，意味着“任何格式的请求”）。

`encoding`

控制数据的字符编码。允许的值包括任何有效的编码标识符，但您很少需要将其从 `"iso-8859-1"`（默认）更改。

## 客户端

XML-RPC 客户端发出 HTTP 请求并解析响应。随 PHP 一起提供的*xmlrpc*扩展可以处理编码 XML-RPC 请求的 XML，但不知道如何发出 HTTP 请求。为此，您必须下载[xmlrpc-epi 分发版](http://xmlrpc-epi.sourceforge.net)，并安装*sample/utils/utils.php*文件。该文件包含一个执行 HTTP 请求的函数。

示例 16-7 展示了`multiply` XML-RPC 服务的客户端。

##### 示例 16-7\. XML-RPC 客户端乘法

```php
<?php
require_once("utils.php");

$options = array('output_type' => "xml", 'version' => "xmlrpc");

$result = xu_rpc_http_concise(
 array(
 'method' => "multiply",
 'args' => array(5, 6),
 'host' => "192.168.0.1",
 'uri' => "/~gnat/test/ch11/xmlrpc-server.php",
 'options' => $options,
 )
);

echo "5 * 6 is {$result}";
```

我们首先加载 XML-RPC 便捷工具库。这使我们可以使用`xu_rpc_http_concise()`函数，该函数构建了一个`POST`请求：

```php
$response = xu_rpc_http_concise(*`hash`*);
```

*`hash`*数组包含 XML-RPC 调用的各种属性，作为一个关联数组：

`method`

要调用的方法名称。

`args`

方法的参数数组。

`host`

提供方法的 Web 服务的主机名。

`url`

Web 服务的 URL 路径。

`options`

选项的关联数组，如服务器端。

`debug`

如果非零，则打印调试信息（默认为`0`）。

`xu_rpc_http_concise()`返回的值是调用方法后解码的返回值。

XML-RPC 还有几个特性我们尚未涵盖。例如，XML-RPC 的数据类型并不总是精确映射到 PHP 的数据类型，并且有一些方法可以将值编码为特定的数据类型，而不是*xmlrpc*扩展的最佳猜测。此外，我们还未涵盖*xmlrpc*扩展的一些特性，如 SOAP 故障。请参阅[*xmlrpc*扩展的文档](http://www.php.net)获取完整详情。

欲了解更多关于 XML-RPC 的信息，请参见[*Programming Web Services in XML-RPC*](http://oreil.ly/Web_Services_XML-RPC)（O’Reilly），作者是 Simon St. Laurent 等人。欲了解有关 SOAP 的更多信息，请参见[*Programming Web Services with SOAP*](http://oreil.ly/Web_Services_SOAP)（O’Reilly），作者是 James Snell 等人。

# 接下来是什么

现在我们已经涵盖了 PHP 的大部分语法、特性和应用，下一章将探讨当事情出错时该怎么办：如何调试 PHP 应用程序和脚本中出现的问题。
