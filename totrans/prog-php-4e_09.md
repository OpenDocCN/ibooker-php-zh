# 第八章。Web 技术

PHP 设计为一种 Web 脚本语言，尽管可以在纯命令行和 GUI 脚本中使用它，但 Web 占 PHP 使用的绝大多数。动态网站可能具有表单、会话，有时还会进行重定向，本章解释了如何在 PHP 中实现这些元素。您将学习 PHP 如何提供对表单参数和上传文件的访问，如何发送 Cookie 并重定向浏览器，如何使用 PHP 会话等等。

# HTTP 基础知识

Web 运行在 HTTP 或超文本传输协议上。该协议规定了 Web 浏览器如何从 Web 服务器请求文件以及服务器如何发送文件。为了理解本章中将向您展示的各种技术，您需要对 HTTP 有基本的了解。有关 HTTP 的更详细讨论，请参阅 [*HTTP Pocket Reference*](http://oreil.ly/HTTP_PocketRef)（O'Reilly）由 Clinton Wong 编写。

当 Web 浏览器请求 Web 页面时，它向 Web 服务器发送 HTTP 请求消息。请求消息始终包含一些头部信息，有时还包含正文。Web 服务器以回复消息响应，该消息始终包含头部信息，并且通常包含正文。HTTP 请求的第一行如下所示：

```php
GET /index.html HTTP/1.1
```

此行指定了一个称为 *方法* 的 HTTP 命令，后跟文档的地址和正在使用的 HTTP 协议的版本。在这种情况下，请求正在使用 `GET` 方法使用 HTTP 1.1 请求 *index.html* 文档。在此初始行之后，请求可以包含可选的头部信息，为服务器提供有关请求的其他数据。

例如：

```php
User-Agent: Mozilla/5.0 (Windows 2000; U) Opera 6.0 [en]
Accept: image/gif, image/jpeg, text/*, */*
```

`User-Agent` 头部提供有关 Web 浏览器的信息，而 `Accept` 头部指定了浏览器接受的 MIME 类型。在任何头部之后，请求包含一个空行以指示头部部分的结束。如果请求适用于所使用的方法（例如 `POST` 方法，我们将很快讨论），请求还可以包含其他数据。如果请求不包含任何数据，则以空行结束。

Web 服务器接收请求，处理它，并发送响应。HTTP 响应的第一行如下所示：

```php
HTTP/1.1 200 OK
```

此行指定协议版本、状态码及该代码的描述。在本例中，状态码为 `200`，表示请求成功（因此描述为 `OK`）。状态行之后，响应包含头部，为客户端提供有关响应的附加信息。例如：

```php
Date: Sat, 29 June 2019 14:07:50 GMT
Server: Apache/2.2.14 (Ubuntu)
Content-Type: text/html
Content-Length: 1845
```

`Server` 头部提供有关 Web 服务器软件的信息，而 `Content-Type` 头部指定响应中包含的数据的 MIME 类型。在头部之后，如果请求成功，则响应包含一个空行，然后是请求的数据。

最常见的两种 HTTP 方法是 `GET` 和 `POST`。`GET` 方法用于从服务器检索信息，例如文档、图像或数据库查询的结果。`POST` 方法用于提交信息，例如信用卡号或要存储在数据库中的信息。当用户在浏览器中键入 URL 或点击链接时，使用 `GET` 方法。当用户提交表单时，可以使用 `GET` 或 `POST` 方法，由 `form` 标签的 `method` 属性指定。我们将在“处理表单”章节详细讨论 `GET` 和 `POST` 方法。

# 变量

从 PHP 脚本中可以以三种不同的方式访问服务器配置和请求信息，这些信息总称为 *EGPCS*（环境、`GET`、`POST`、cookies 和 server）。

PHP 创建了六个包含 EGPCS 信息的全局数组：

`$_ENV`

包含任何环境变量的值，其中数组的键是环境变量的名称。

`$_GET`

包含作为 `GET` 请求一部分的任何参数，其中数组的键是表单参数的名称。

`$_COOKIE`

包含作为请求的一部分传递的任何 cookie 值，其中数组的键是 cookie 的名称。

`$_POST`

包含作为 `POST` 请求一部分的任何参数，其中数组的键是表单参数的名称。

`$_SERVER`

包含关于 Web 服务器的有用信息，如下一节所述。

`$_FILES`

包含有关上传文件的信息。

这些变量不仅是全局的，而且在函数定义内部也是可见的。`$_REQUEST` 数组由 PHP 自动创建，并包含 `$_GET`、`$_POST` 和 `$_COOKIE` 数组的所有元素。

# 服务器信息

`$_SERVER` 数组包含来自 Web 服务器的许多有用信息，其中大部分来自 [公共网关接口（CGI）规范](http://bit.ly/Vw912h) 所需的环境变量。以下是来自 CGI 的完整 `$_SERVER` 条目列表，包括一些示例值：

`PHP_SELF`

当前脚本的名称，相对于文档根目录（例如 `/store/cart.php`）。在前几章的示例代码中已经见过这个用法。当创建自引用脚本时，这个变量非常有用，稍后我们会看到。

`SERVER_SOFTWARE`

标识服务器的字符串（例如 `"Apache/1.3.33 (Unix) mod_perl/1.26 PHP/5.0.4"`）。

`SERVER_NAME`

用于自引用 URL 的主机名、DNS 别名或 IP 地址（例如 `www.example.com`）。

`GATEWAY_INTERFACE`

遵循的 CGI 标准版本（例如 `CGI/1.1`）。

`SERVER_PROTOCOL`

请求协议的名称和版本（例如 `HTTP/1.1`）。

`SERVER_PORT`

请求发送到的服务器端口号（例如 `80`）。

`REQUEST_METHOD`

客户端用来获取文档的方法（例如，`GET`）。

`PATH_INFO`

客户端提供的额外路径元素（例如，`/list/users`）。

`PATH_TRANSLATED`

`PATH_INFO` 的值，由服务器转换为文件名（例如，`/home/httpd/htdocs/list/users`）。

`SCRIPT_NAME`

当前页面的 URL 路径，对于自引用脚本很有用（例如，`/~me/menu.php`）。

`QUERY_STRING`

在 URL 中 `?` 后面的所有内容（例如，`name=Fred+age=35`）。

`REMOTE_HOST`

请求此页面的机器的主机名（例如，`http://dialup-192-168-0-1.example.com`）。如果该机器没有 DNS，这将为空，`REMOTE_ADDR` 是唯一提供的信息。

`REMOTE_ADDR`

包含请求此页面的机器的 IP 地址的字符串（例如，`"192.168.0.250"`）。

`AUTH_TYPE`

用于保护页面的认证方法，如果页面受密码保护的话（例如，`basic`）。

`REMOTE_USER`

客户端经过身份验证时使用的用户名，如果页面受密码保护的话（例如，`fred`）。请注意，无法找出使用的密码。

Apache 服务器还为请求中的每个 HTTP 头创建 `$_SERVER` 数组的条目。对于每个键，头名称转换为大写，连字符（`-`）转换为下划线（`_`），并在前面添加字符串 `"HTTP_"`。例如，`User-Agent` 头的条目键为 `"HTTP_USER_AGENT"`。两个最常见和有用的头部是：

`HTTP_USER_AGENT`

浏览器用来识别自身的字符串（例如，`"Mozilla/5.0 (Windows 2000; U) Opera 6.0 [en]"`）。

`HTTP_REFERER`

浏览器声称它来自以获取当前页面的页面的 URL（例如，`http://www.example.com/last_page.html`）。

# 处理表单

使用 PHP 处理表单很容易，因为表单参数在 `$_GET` 和 `$_POST` 数组中可用。本节描述了一些技巧和技术，使处理更加容易。

## 方法

正如我们已经讨论过的，客户端可以使用两种 HTTP 方法将表单数据传递给服务器：`GET` 和 `POST`。特定表单使用的方法通过 `form` 标签的 `method` 属性指定。理论上，HTML 中方法不区分大小写，但实际上一些损坏的浏览器要求方法名必须全部大写。

`GET` 请求在 URL 中以 *查询字符串* 的形式编码表单参数，这由紧随 `?` 的文本指示：

```php
/path/to/chunkify.php?word=despicable&length=3
```

`POST` 请求通过 HTTP 请求的正文传递表单参数，保持 URL 不变。

`GET` 和 `POST` 最显著的区别是 URL 行。因为所有表单参数都通过 `GET` 请求编码在 URL 中，用户可以为 `GET` 查询创建书签。然而，他们不能使用 `POST` 请求做到这一点。

然而，`GET` 和 `POST` 请求之间最大的区别要微妙得多。HTTP 规范指出，`GET` 请求是*幂等*的——也就是说，对于特定 URL 的一个 `GET` 请求，包括表单参数，与对该 URL 进行两次或更多次请求是相同的。因此，Web 浏览器可以缓存 `GET` 请求的响应页面，因为响应页面不会因页面加载的次数而改变。由于幂等性，`GET` 请求应该仅用于查询，例如将单词分割成较小的块或将数字相乘，响应页面永远不会改变。

`POST` 请求不是幂等的。这意味着它们不能被缓存，服务器在每次显示页面时都会被联系。您可能在显示或重新加载某些页面之前看到您的 Web 浏览器提示您“重新提交表单数据？”。这使得 `POST` 请求成为适合查询的选择，其响应页面可能随时间变化，例如显示购物车的内容或公告板中的当前消息。

也就是说，幂等性在现实世界中经常被忽略。浏览器缓存通常实现得很差，而重新加载按钮很容易点击，程序员往往只是根据他们是否希望在 URL 中显示查询参数来使用 `GET` 和 `POST`。您需要记住的是，`GET` 请求不应该用于导致服务器更改的任何操作，例如下订单或更新数据库。

用于请求 PHP 页面的方法类型可以通过 `$_SERVER['REQUEST_METHOD']` 获得。例如：

```php
if ($_SERVER['REQUEST_METHOD'] == 'GET') {
 // handle a GET request
}
else {
 die("You may only GET this page.");
}
```

## 参数

使用 `$_POST`、`$_GET` 和 `$_FILES` 数组从 PHP 代码中访问表单参数。键是参数名称，值是这些参数的值。因为 HTML 字段名称中允许出现句点，但 PHP 变量名称中不允许出现句点，所以字段名称中的句点在数组中被转换为下划线（`_`）。

示例 8-1 显示了一个 HTML 表单，用于将用户提供的字符串分块。表单包含两个字段：一个用于字符串（参数名 `word`），一个用于生成块的大小（参数名 `number`）。

##### 示例 8-1\. chunkify 表单（chunkify.html）

```php
<html>
 <head><title>Chunkify Form</title></head>

 <body>
 <form action="chunkify.php" method="POST">
 Enter a word: <input type="text" name="word" /><br />

 How long should the chunks be?
 <input type="text" name="number" /><br />
 <input type="submit" value="Chunkify!">
 </form>
</body>

</html>
```

示例 8-2 列出了 PHP 脚本 *chunkify.php*，该脚本接收 示例 8-1 中的表单提交的参数值。该脚本将参数值复制到变量中并使用它们。

##### 示例 8-2\. chunkify 脚本（chunkify.php）

```php
<?php
$word = $_POST['word'];
$number = $_POST['number'];

$chunks = ceil(strlen($word) / $number);

echo "The {$number}-letter chunks of '{$word}' are:<br />\n";

for ($i = 0; $i < $chunks; $i++) {
 $chunk = substr($word, $i * $number, $number);
 printf("%d: %s<br />\n", $i + 1, $chunk);
}
?>
```

图 8-1 显示了 chunkify 表单和生成的输出。

![chunkify 表单及其输出](img/php4_0801.png)

###### 图 8-1\. chunkify 表单及其输出

## 自处理页面

一个 PHP 页面可以用来生成表单，然后处理它。如果用`GET`方法请求示例 8-3 中显示的页面，它将打印一个接受华氏温度的表单。然而，如果用`POST`方法调用该页面，页面将计算并显示相应的摄氏温度。

##### 示例 8-3\. 自处理的温度转换页面（temp.php）

```php
<html>
<head><title>Temperature Conversion</title></head>
<body>

<?php if ($_SERVER['REQUEST_METHOD'] == 'GET') { ?>
 <form action="<?php echo $_SERVER['PHP_SELF'] ?>" method="POST">
 Fahrenheit temperature:
 <input type="text" name="fahrenheit" /><br />
 <input type="submit" value="Convert to Celsius!" />
 </form>

<?php }
else if ($_SERVER['REQUEST_METHOD'] == 'POST') {
 $fahrenheit = $_POST['fahrenheit'];
 $celsius = ($fahrenheit - 32) * 5 / 9;

 printf("%.2fF is %.2fC", $fahrenheit, $celsius);
}
else {
 die("This script only works with GET and POST requests.");
} ?>

</body>
</html>
```

图 8-2 展示了温度转换页面及其输出。

![温度转换页面及其输出](img/php4_0802.png)

###### 图 8-2\. 温度转换页面及其输出

脚本另一种判断是显示表单还是处理表单的方法是查看是否已提供其中一个参数。这样可以编写一个使用`GET`方法提交值的自处理页面。示例 8-4 展示了使用`GET`请求提交参数的温度转换页面的新版本。页面使用参数的存在或不存在来确定要执行的操作。

##### 示例 8-4\. 使用 GET 方法的温度转换（temp2.php）

```php
<html>
<head>
<title>Temperature Conversion</title>
</head>
<body>
<?php
if (isset ( $_GET ['fahrenheit'] )) {
 $fahrenheit = $_GET ['fahrenheit'];
} else {
 $fahrenheit = null;
}
if (is_null ( $fahrenheit )) {
 ?>
<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="GET">
 Fahrenheit temperature: <input type="text" name="fahrenheit" /><br />
 <input type="submit" value="Convert to Celsius!" />
 </form>
<?php
} else {
 $celsius = ($fahrenheit - 32) * 5 / 9;
 printf ( "%.2fF is %.2fC", $fahrenheit, $celsius );
}
?>
</body>
</html>
```

在示例 8-4 中，我们将表单参数值复制到`$fahrenheit`中。如果没有给出该参数，`$fahrenheit`将包含`NULL`，因此我们可以使用`is_null()`来测试我们是应该显示表单还是处理表单数据。

## 粘性表单

许多网站使用一种称为*粘性表单*的技术，查询结果显示一个搜索表单，其默认值是上一次查询的值。例如，如果在 Google 上搜索“Programming PHP”，结果页面顶部将包含另一个搜索框，其中已经包含“Programming PHP”。要将搜索精确到“Programming PHP from O’Reilly”，您只需添加额外的关键词。

这种粘性行为易于实现。示例 8-5 展示了我们从示例 8-4 中得到的温度转换脚本，其中表单被设置为粘性。基本技术是使用提交的表单值作为创建 HTML 字段时的默认值。

##### 示例 8-5\. 带有粘性表单的温度转换（sticky_form.php）

```php
<html>
<head><title>Temperature Conversion</title></head>
<body>
<?php $fahrenheit = $_GET['fahrenheit']; ?>

<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="GET">
 Fahrenheit temperature:
 <input type="text" name="fahrenheit" value="<?php echo $fahrenheit; ?>" /><br />
 <input type="submit" value="Convert to Celsius!" />
</form>

<?php if (!is_null($fahrenheit)) {
 $celsius = ($fahrenheit - 32) * 5 / 9;
 printf("%.2fF is %.2fC", $fahrenheit, $celsius);
} ?>

</body>
</html>
```

## 多值参数

使用`select`标签创建的 HTML 选择列表可以允许多项选择。为了确保 PHP 可以识别浏览器传递给表单处理脚本的多个值，您需要在 HTML 表单字段名称后使用方括号`[]`。例如：

```php
<select name="languages[]">

 <option name="c">C</option>
 <option name="c++">C++</option>
 <option name="php">PHP</option>
 <option name="perl">Perl</option>
</select>
```

当用户提交表单时，`$_GET['languages']` 包含一个数组，而不是一个简单的字符串。这个数组包含用户选择的数值。

示例 8-6 说明了 HTML 选择列表中的多个值选择。该表单为用户提供了一组人物属性。用户提交表单后，返回一个（不是很有趣的）用户个性描述。

##### 示例 8-6\. 使用选择框的多选值（select_array.php）

```php
<html>
<head><title>Personality</title></head>
<body>

<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="GET">
 Select your personality attributes:<br />
 <select name="attributes[]" multiple>
 <option value="perky">Perky</option>
 <option value="morose">Morose</option>
 <option value="thinking">Thinking</option>
 <option value="feeling">Feeling</option>
 <option value="thrifty">Spend-thrift</option>
 <option value="shopper">Shopper</option>
 </select><br />
 <input type="submit" name="s" value="Record my personality!" />
</form>
<?php if (array_key_exists('s', $_GET)) {
 $description = join(' ', $_GET['attributes']);
 echo "You have a {$description} personality.";
} ?>

</body>
</html>
```

在示例 8-6 中，提交按钮的名称为`"s"`。我们检查是否存在此参数值以确定是否需要生成人物描述。图 8-3 展示了多选页面及其结果输出。

![多选页面及其输出](img/php4_0803.png)

###### 图 8-3\. 多选页面及其输出

同样的技术适用于任何表单字段，其中可以返回多个值。示例 8-7 展示了我们的人物表单的修订版本，改用复选框而不是选择框。请注意，只有 HTML 发生了变化——处理表单的代码不需要知道多个值是来自复选框还是选择框。

##### 示例 8-7\. 复选框中的多选值（checkbox_array.php）

```php
<html>
<head><title>Personality</title></head>
<body>

<form action="<?php $_SERVER['PHP_SELF']; ?>" method="GET">
 Select your personality attributes:<br />
 <input type="checkbox" name="attributes[]" value="perky" /> Perky<br />
 <input type="checkbox" name="attributes[]" value="morose" /> Morose<br />
 <input type="checkbox" name="attributes[]" value="thinking" /> Thinking<br />
 <input type="checkbox" name="attributes[]" value="feeling" /> Feeling<br />
 <input type="checkbox" name="attributes[]" value="thrifty" />Spend-thrift<br />
 <input type="checkbox" name="attributes[]" value="shopper" /> Shopper<br />
 <br />
 <input type="submit" name="s" value="Record my personality!" />
</form>
<?php if (array_key_exists('s', $_GET)) {
 $description = join (' ', $_GET['attributes']);
 echo "You have a {$description} personality.";
} ?>

</body>
</html>
```

## 粘性多值参数

现在你可能会想，*我能把多选表单元素设为粘性吗？* 可以，但这并不容易。你需要检查表单中的每个可能值是否是提交的值之一。例如：

```php
Perky: <input type="checkbox" name="attributes[]" value="perky"
<?php
if (is_array($_GET['attributes']) && in_array('perky', $_GET['attributes'])) {
 echo "checked";
} ?> /><br />
```

你可以为每个复选框使用这种技术，但这样做很重复且容易出错。此时，编写一个函数以生成可能值的 HTML 并从提交的参数副本中工作会更容易。示例 8-8 展示了一个新版本的多选复选框，表单被设为粘性。尽管这个表单看起来与示例 8-7 中的一样，但在幕后，表单生成的方式有了实质性的变化。

##### 示例 8-8\. 粘性多值复选框（checkbox_array2.php）

```php
<html>
<head><title>Personality</title></head>
<body>
<?php // fetch form values, if any
$attrs = $_GET['attributes'];

if (!is_array($attrs)) {
 $attrs = array();
}

// create HTML for identically named checkboxes

function makeCheckboxes($name, $query, $options)
{
 foreach ($options as $value => $label) {
 $checked = in_array($value, $query) ? "checked" : '';

 echo "<input type=\"checkbox\" name=\"{$name}\"
 value=\"{$value}\" {$checked} />";
 echo "{$label}<br />\n";
 }
}

// the list of values and labels for the checkboxes
$personalityAttributes = array(
 'perky' => "Perky",
 'morose' => "Morose",
 'thinking' => "Thinking",
 'feeling' => "Feeling",
 'thrifty' => "Spend-thrift",
 'prodigal' => "Shopper"
); ?>

<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="GET">
 Select your personality attributes:<br />
 <?php makeCheckboxes('attributes[]', $attrs, $personalityAttributes); ?><br />

 <input type="submit" name="s" value="Record my personality!" />
</form>

<?php if (array_key_exists('s', $_GET)) {
 $description = join (' ', $_GET['attributes']);
 echo "You have a {$description} personality.";
} ?>

</body>
</html>
```

这段代码的核心是`makeCheckboxes()`函数。它接受三个参数：复选框组的名称，默认启用的值数组，以及将值映射到描述的数组。复选框的选项列表在`$personalityAttributes`数组中。

## 文件上传

要处理文件上传（大多数现代浏览器都支持），使用`$_FILES`数组。使用各种身份验证和文件上传函数，您可以控制谁可以上传文件以及在文件进入系统后要执行的操作。有关需要注意的安全问题，请参阅第 14 章。

下面的代码显示了一个允许在同一页面上传文件的表单：

```php
<form enctype="multipart/form-data"
 action="<?php echo $_SERVER['PHP_SELF']; ?>" method="POST">
 <input type="hidden" name="MAX_FILE_SIZE" value="10240">
 File name: <input name="toProcess" type="file" />
 <input type="submit" value="Upload" />
</form>
```

文件上传的最大问题是风险，即收到太大以至于无法处理的文件。PHP 有两种方式来防止这种情况发生：硬限制和软限制。*php.ini* 中的 `upload_max_filesize` 选项为上传文件的硬上限设定了默认值为 2 MB。如果你的表单在任何文件字段参数之前提交一个名为 `MAX_FILE_SIZE` 的参数，PHP 将使用该值作为软上限。例如，在上一个示例中，上限设置为 10 KB。PHP 会忽略尝试将 `MAX_FILE_SIZE` 设置为大于 `upload_max_filesize` 的值的尝试。

此外，请注意，表单标签需要一个 `enctype` 属性，其值为 `"multipart/form-data"`。

`$_FILES` 中的每个元素本身都是一个数组，提供有关上传文件的信息。键包括：

`name`

浏览器提供的上传文件的名称。这很难进行有意义的使用，因为客户端机器可能具有不同的文件名约定，与运行 Unix 的 Web 服务器（例如，从运行 Windows 的客户端机器获取的 *D:\PHOTOS\ME.JPG* 文件路径对于 Web 服务器来说毫无意义）。

`type`

客户端猜测的上传文件的 MIME 类型。

`size`

上传文件的大小（以字节为单位）。如果用户尝试上传一个太大的文件，则大小将报告为 0。

`tmp_name`

服务器上保存上传文件的临时文件的名称。如果用户尝试上传一个太大的文件，则名称将显示为 `"none"`。

测试文件是否成功上传的正确方式是使用 `is_uploaded_file()` 函数，如下所示：

```php
if (is_uploaded_file($_FILES['toProcess']['tmp_name'])) {
 // successfully uploaded
}
```

文件存储在服务器的默认临时文件目录中，该目录在 *php.ini* 中使用 `upload_tmp_dir` 选项指定。要移动文件，请使用 `move_uploaded_file()` 函数：

```php
move_uploaded_file($_FILES['toProcess']['tmp_name'], "path/to/put/file/{$file}");
```

调用 `move_uploaded_file()` 自动检查是否为上传文件。当脚本完成时，从临时目录中删除上传到该脚本的任何文件。

## 表单验证

当允许用户输入数据时，通常需要在使用或存储数据之前对其进行验证。有几种可用的验证数据的策略。首先是客户端的 JavaScript。然而，由于用户可以选择关闭 JavaScript，或者甚至可能使用不支持 JavaScript 的浏览器，因此这不应是你唯一的验证手段。

更安全的选择是使用 PHP 进行验证。示例 8-9 展示了一个带有表单的自处理页面。页面允许用户输入媒体项目；表单的三个元素——名称、媒体类型和文件名——是必填项。如果用户忽略了其中任何一个值，页面会重新显示，并显示详细的错误信息。用户已经填写的表单字段将保留最初输入的值。最后，作为对用户的额外提示，当用户正在纠正表单时，提交按钮的文本从“Create”更改为“Continue”。

##### 示例 8-9\. 表单验证（data_validation.php）

```php
<?php
$name = $_POST['name'];
$mediaType = $_POST['media_type'];
$filename = $_POST['filename'];
$caption = $_POST['caption'];
$status = $_POST['status'];

$tried = ($_POST['tried'] == 'yes');

if ($tried) {
 $validated = (!empty($name) && !empty($mediaType) && !empty($filename));

 if (!$validated) { ?>
 <p>The name, media type, and filename are required fields. Please fill
 them out to continue.</p>
 <?php }
}

if ($tried && $validated) {
 echo "<p>The item has been created.</p>";
}

// was this type of media selected? print "selected" if so
function mediaSelected($type)
{
 global $mediaType;

 if ($mediaType == $type) {
 echo "selected"; }
} ?>

<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="POST">
 Name: <input type="text" name="name" value="<?php echo $name; ?>" /><br />

 Status: <input type="checkbox" name="status" value="active"
 <?php if ($status == "active") { echo "checked"; } ?> /> Active<br />

 Media: <select name="media_type">
 <option value="">Choose one</option>
 <option value="picture" <?php mediaSelected("picture"); ?> />Picture</option>
 <option value="audio" <?php mediaSelected("audio"); ?> />Audio</option>
 <option value="movie" <?php mediaSelected("movie"); ?> />Movie</option>
 </select><br />

 File: <input type="text" name="filename" value="<?php echo $filename; ?>" /><br />

 Caption: <textarea name="caption"><?php echo $caption; ?></textarea><br />

 <input type="hidden" name="tried" value="yes" />
 <input type="submit" value="<?php echo $tried ? "Continue" : "Create"; ?>" />
</form>
```

在这种情况下，验证仅仅是检查是否提供了一个值。只有当`$name`、`$type`和`$filename`都不为空时，我们才将`$validated`设置为`true`。其他可能的验证包括检查电子邮件地址是否有效或检查提供的文件名是否是本地存在的。

例如，要验证年龄字段以确保它包含非负整数，请使用以下代码：

```php
$age = $_POST['age'];
$validAge = strspn($age, "1234567890") == strlen($age);
```

调用`strspn()`函数可以找到字符串开头的数字数量。在非负整数中，整个字符串应该由数字组成，因此如果整个字符串由数字组成，则它是一个有效的年龄。我们也可以使用正则表达式进行这种检查：

```php
$validAge = preg_match('/^\d+$/', $age);
```

验证电子邮件地址是一项几乎不可能的任务。没有办法拿到一个字符串然后判断它是否对应一个有效的电子邮件地址。但是，你可以通过要求用户在两个不同的字段中输入电子邮件地址两次来捕捉输入错误。你还可以通过要求在@符号后的某个位置输入一个句点（.），以及为了额外加分，你可以检查你不希望发送邮件到的域（例如[whitehouse.gov](http://whitehouse.gov)或竞争对手的网站）。例如：

```php
$email1 = strtolower($_POST['email1']);
$email2 = strtolower($_POST['email2']);

if ($email1 !== $email2) {
 die("The email addresses didn't match");
}

if (!preg_match('/@.+\..+$/', $email1)) {
 die("The email address is malformed");
}

if (strpos($email1, "whitehouse.gov")) {
 die("I will not send mail to the White House");
}
```

字段验证基本上是字符串处理。在本例中，我们使用了正则表达式和字符串函数来确保用户提供的字符串是我们期望的类型。

# 设置响应头

正如我们已经讨论过的那样，服务器发送给客户端的 HTTP 响应包含标识响应主体内容类型、发送响应的服务器、响应主体的字节数、发送响应的时间等头部信息。PHP 和 Apache 通常会为您处理头部信息（例如标识文档为 HTML、计算 HTML 页面的长度等）。大多数 Web 应用程序通常不需要自己设置头部信息。但是，如果您想发送非 HTML 内容、设置页面的过期时间、重定向客户端的浏览器或生成特定的 HTTP 错误，则需要使用`header()`函数。

设置头文件的唯一注意事项是必须在生成任何正文内容之前执行。这意味着所有对 `header()`（或者如果您设置 cookies 则为 `setcookie()`）的调用都必须发生在文件的非常顶部，甚至在 `<html>` 标签之前。例如：

```php
<?php header("Content-Type: text/plain"); ?>
Date: today
From: fred
To: barney
Subject: hands off!

My lunchbox is mine and mine alone. Get your own,
you filthy scrounger!
```

在文档开始后尝试设置头部会导致以下警告：

```php
Warning: Cannot add header information - headers already sent
```

您还可以使用输出缓冲区；有关更多信息，请参阅 `ob_start()`、`ob_end_flush()` 和相关函数。

## 不同的内容类型

`Content-Type` 头部标识返回文档的类型。通常这是 `"text/html"`，表示一个 HTML 文档，但还有其他有用的文档类型。例如，`"text/plain"` 强制浏览器将页面视为纯文本。这种类型就像自动的“查看源代码”，在调试时非常有用。

在 第十章 和 第十一章 中，我们将大量使用 `Content-Type` 头部生成实际上是图形图像和 Adobe PDF 文件的文档。

## 重定向

要将浏览器重定向到一个新的 URL，即*重定向*，您需要设置 `Location` 头。通常，随后会立即退出，以便脚本不会继续生成和输出代码清单的其余部分：

```php
header("Location: http://www.example.com/elsewhere.html");
exit();
```

当提供部分 URL（例如 `/elsewhere.html`）时，Web 服务器会在内部处理重定向。这通常很少有用，因为浏览器通常不会意识到它没有获取到请求的页面。如果新文档中有相对 URL，则浏览器会将这些 URL 解释为相对于请求的文档，而不是最终发送的文档。一般情况下，您会希望重定向到绝对 URL。

## 过期

服务器可以明确告知浏览器及可能存在于服务器与浏览器之间的任何代理缓存文档的具体过期日期和时间。代理和浏览器缓存可以在那个时间之前持有文档或者提前过期。重复加载缓存文档不会联系服务器。但尝试获取已过期文档会联系服务器。

要设置文档的过期时间，请使用 `Expires` 头部：

```php
header("Expires: Tue, 02 Jul 2019 05:30:00 GMT");
```

要使文档在生成页面后的三小时内过期，使用 `time()` 和 `gmstrftime()` 生成过期日期字符串：

```php
$now = time();
$then = gmstrftime("%a, %d %b %Y %H:%M:%S GMT", $now + 60 * 60 * 3);

header("Expires: {$then}");
```

要指示文档“永不”过期，使用一年后的时间：

```php
$now = time();
$then = gmstrftime("%a, %d %b %Y %H:%M:%S GMT", $now + 365 * 86440);

header("Expires: {$then}");
```

要标记文档已过期，使用当前时间或过去的时间：

```php
$then = gmstrftime("%a, %d %b %Y %H:%M:%S GMT");

header("Expires: {$then}");
```

这是防止浏览器或代理缓存存储您的文档的最佳方法：

```php
header("Expires: Mon, 26 Jul 1997 05:00:00 GMT");
header("Last-Modified: " . gmdate("D, d M Y H:i:s") . " GMT");
header("Cache-Control: no-store, no-cache, must-revalidate");
header("Cache-Control: post-check=0, pre-check=0", false);
header("Pragma: no-cache");
```

有关控制浏览器和 Web 缓存行为的更多信息，请参阅 [*Web Caching*](http://bit.ly/Web_Caching)（O’Reilly 出版）由 Duane Wessels 的第六章。

## 认证

HTTP 身份验证通过请求头和响应状态工作。浏览器可以在请求头中发送用户名和密码（*凭证*）。如果凭证未发送或不符合要求，服务器将发送“401 未授权”响应，并通过`WWW-Authenticate`头标识身份验证的*领域*（例如`"Mary's` `Pictures"`或`"Your Shopping Cart"`）。这通常会在浏览器上弹出一个“输入用户名和密码以 . . .”的对话框，并且页面会使用更新后的凭证重新请求。

要在 PHP 中处理身份验证，请检查用户名和密码（`$_SERVER`的`PHP_AUTH_USER`和`PHP_AUTH_PW`项），并调用`header()`设置领域并发送“401 未授权”响应：

```php
header('WWW-Authenticate: Basic realm="Top Secret Files"');
header("HTTP/1.0 401 Unauthorized");
```

您可以使用任何方法来验证用户名和密码；例如，您可以查询数据库，读取有效用户的文件，或查询 Microsoft 域服务器。

本示例检查确保密码是用户名的反向（确实不是最安全的身份验证方法！）：

```php
$authOK = false;

$user = $_SERVER['PHP_AUTH_USER'];
$password = $_SERVER['PHP_AUTH_PW'];

if (isset($user) && isset($password) && $user === strrev($password)) {
 $authOK = true;
}

if (!$authOK) {
 header('WWW-Authenticate: Basic realm="Top Secret Files"');
 header('HTTP/1.0 401 Unauthorized');

 // anything else printed here is only seen if the client hits "Cancel"
 exit;
}

<!-- your password-protected document goes here -->
```

如果您要保护多个页面，请将上述代码放入单独的文件中，并将其包含在每个受保护页面的顶部。

如果您的主机使用的是 PHP 的 CGI 版本而不是 Apache 模块，则无法设置这些变量，您需要使用其他形式的身份验证，例如通过 HTML 表单收集用户名和密码。

# 维护状态

HTTP 是一种*无状态*协议，这意味着一旦 Web 服务器完成客户端的 Web 页面请求，两者之间的连接就会中断。换句话说，服务器无法识别一系列请求是否来自同一个客户端。

尽管如此，状态是有用的。例如，如果无法跟踪单个用户的一系列请求，就无法构建购物车应用程序。您需要知道用户何时添加或删除项目，以及用户在决定结账时购物车中有什么物品。

为了解决网络缺乏状态的问题，程序员们想出了许多技巧来在请求之间跟踪状态信息（也称为*会话跟踪*）。其中一种技术是使用隐藏表单字段来传递信息。PHP 将隐藏表单字段视为普通表单字段，因此这些值可以在`$_GET`和`$_POST`数组中使用。使用隐藏表单字段，您可以传递整个购物车的内容。但是，更常见的做法是为每个用户分配一个唯一标识符，并使用单个隐藏表单字段传递该标识符。虽然隐藏表单字段在所有浏览器中都有效，但它们仅适用于一系列动态生成的表单，因此它们不如其他一些技术那样普遍有用。

另一种技术是 URL 重写，其中用户可能单击的每个本地 URL 都会动态修改以包含额外信息。这些额外信息通常作为 URL 的参数指定。例如，如果为每个用户分配一个唯一的 ID，可以在所有 URL 中包含该 ID，如下所示：

```php
http://www.example.com/catalog.php?userid=123
```

如果确保动态修改所有本地链接以包含用户 ID，现在可以在应用程序中跟踪个别用户。URL 重写适用于所有动态生成的文档，不仅仅是表单，但实际执行重写可能很繁琐。

维护状态的第三种最普遍的技术是使用**cookies**。*Cookie* 是服务器可以给客户端的一小段信息。在随后的每个请求中，客户端将该信息返回给服务器，从而标识自己。Cookies 对于通过浏览器重复访问时保留信息很有用，但它们也不是没有问题的。主要问题在于，大多数浏览器允许用户禁用 cookies。因此，任何使用 cookies 进行状态维护的应用程序都需要使用另一种技术作为后备机制。我们稍后将更详细地讨论 cookies。

使用 PHP 维护状态的最佳方式是使用内置的会话跟踪系统。此系统允许您创建可从应用程序的不同页面访问的持久变量，以及同一用户在访问站点的不同次数时也可以访问这些变量。在幕后，PHP 的会话跟踪机制使用 cookies（或 URLs）优雅地解决大多数需要状态的问题，并为您处理所有细节。我们稍后将在本章中详细介绍 PHP 的会话跟踪系统。

## Cookies

一个 cookie 基本上是一个包含多个字段的字符串。服务器可以在响应的头部中向浏览器发送一个或多个 cookies。某些 cookie 的字段指示浏览器应将 cookie 作为请求的一部分发送到哪些页面。cookie 的 `value` 字段是有效载荷——服务器可以在其中存储任何喜欢的数据（在限制内），如标识用户的唯一代码、偏好等。

使用 `setcookie()` 函数将 cookie 发送到浏览器：

```php
setcookie(*`name`* [, *`value`* [, *`expires`* [, *`path`* [, *`domain`* [, *`secure`* [, 
*`httponly`* ]]]]]]);
```

此函数从给定参数创建 cookie 字符串，并创建一个 `Cookie` 头部，其值为该字符串。因为 cookies 作为响应的头部发送，所以必须在发送文档的任何主体之前调用 `setcookie()`。`setcookie()` 的参数包括：

*名称*

特定 cookie 的唯一名称。您可以拥有多个具有不同名称和属性的 cookies。名称不能包含空格或分号。

*值*

此 cookie 附加的任意字符串值。最初的 Netscape 规范将 cookie 的总大小（包括名称、过期日期和其他信息）限制为 4 KB，因此虽然 cookie 值的大小没有具体限制，但它可能不会大于 3.5 KB。

*过期*

此 cookie 的过期日期。如果未指定过期日期，则浏览器将 cookie 保存在内存中而不是在磁盘上。当浏览器退出时，cookie 消失。过期日期是自 1970 年 1 月 1 日午夜（GMT）以来的秒数。例如，传递`time() + 60 * 60 * 2`将使 cookie 在两小时后过期。

*path*

浏览器只会为此路径下的 URL 返回 cookie。默认情况下是当前页面所在的目录。例如，如果*/store/front/cart.php*设置了一个 cookie，并且没有指定路径，那么该 cookie 将在所有 URL 路径以*/store/front/*开头的页面上发送回服务器。

*domain*

浏览器只会为此域名下的 URL 返回 cookie。默认是服务器主机名。

*secure*

浏览器只会在*https*连接下传输 cookie。默认情况下是`false`，意味着可以在不安全的连接下发送 cookie。

*httponly*

如果将此参数设置为`TRUE`，则 cookie 将仅通过 HTTP 协议可用，因此无法通过其他方式如 JavaScript 访问。关于这是否能提供更安全的 cookie，仍有争论，因此请谨慎使用此参数并进行充分测试。

`setcookie()`函数还有一种替代语法：

```php
 `setcookie` ($name [, $value = "" [, $options = [] ]] )
```

其中`$options`是一个数组，保存了跟在`$value`内容后面的其他参数。这样可以稍微节省`setcookie()`函数的代码行长度，但是在使用前必须先构建好`$options`数组，因此存在一定的权衡考量。

当浏览器将 cookie 发送回服务器时，您可以通过`$_COOKIE`数组访问该 cookie。键是 cookie 名称，值是 cookie 的`value`字段。例如，页面顶部的以下代码跟踪了客户端访问该页面的次数：

```php
$pageAccesses = $_COOKIE['accesses'];
setcookie('accesses', ++$pageAccesses);
```

解码 cookie 时，cookie 名称中的任何句点(`.`)都会变成下划线。例如，名为`tip.top`的 cookie 可以通过`$_COOKIE['tip_top']`访问。

让我们看看 cookie 的实际效果。首先，示例 8-10 显示了一个 HTML 页面，提供了各种背景和前景颜色的选项。

##### 示例 8-10\. 偏好选择 (colors.php)

```php
<html>
<head><title>Set Your Preferences</title></head>
<body>
<form action="prefs.php" method="post">
 <p>Background:
 <select name="background">
 <option value="black">Black</option>
 <option value="white">White</option>
 <option value="red">Red</option>
 <option value="blue">Blue</option>
 </select><br />

 Foreground:
 <select name="foreground">
 <option value="black">Black</option>
 <option value="white">White</option>
 <option value="red">Red</option>
 <option value="blue">Blue</option>
 </select></p>

 <input type="submit" value="Change Preferences">
</form>

</body>
</html>
```

示例 8-10 中的表单提交到 PHP 脚本*prefs.php*，如示例 8-11 所示，该脚本会为表单中指定的颜色偏好设置 cookie。注意，在 HTML 页面启动后才调用`setcookie()`函数。

##### 示例 8-11\. 使用 cookie 设置偏好 (prefs.php)

```php
<html>
<head><title>Preferences Set</title></head>
<body>

<?php
$colors = array(
 'black' => "#000000",
 'white' => "#ffffff",
 'red' => "#ff0000",
 'blue' => "#0000ff"
);

$backgroundName = $_POST['background'];
$foregroundName = $_POST['foreground'];

setcookie('bg', $colors[$backgroundName]);
setcookie('fg', $colors[$foregroundName]);
?>

<p>Thank you. Your preferences have been changed to:<br />
Background: <?php echo $backgroundName; ?><br />
Foreground: <?php echo $foregroundName; ?></p>

<p>Click <a href="prefs_demo.php">here</a> to see the preferences
in action.</p>

</body>
</html>
```

示例 8-11 生成的页面包含指向另一页的链接，如示例 8-12 所示，该页面通过访问`$_COOKIE`数组使用颜色偏好。

##### 示例 8-12\. 使用带有 cookie 的颜色偏好（prefs_demo.php）

```php
<html>
<head><title>Front Door</title></head>
<?php
$backgroundName = $_COOKIE['bg'];
$foregroundName = $_COOKIE['fg'];
?>
<body bgcolor="<?php echo $backgroundName; ?>" text="<?php echo $foregroundName; ?>">

<h1>Welcome to the Store</h1>

<p>We have many fine products for you to view. Please feel free to browse
the aisles and stop an assistant at any time. But remember, you break it
you bought it!</p>

<p>Would you like to <a href="colors.php">change your preferences?</a></p>

</body>
</html>
```

关于 cookie 使用有许多注意事项。并非所有客户端（浏览器）都支持或接受 cookie，即使客户端支持 cookie，用户也可以关闭它们。此外，cookie 规范规定单个 cookie 大小不得超过 4 KB，每个域名最多允许 20 个 cookie，并且客户端上最多可以存储 300 个 cookie。某些浏览器可能有更高的限制，但不能依赖此点。最后，你无法控制浏览器何时实际过期 cookie —— 如果浏览器达到容量上限并需要添加新 cookie，则可能丢弃尚未过期的 cookie。你还应注意设置快速过期的 cookie。到期时间依赖于客户端时钟与你的时钟一样准确。许多人没有准确设置系统时钟，因此不能依赖于快速过期。

尽管存在这些限制，cookie 对于通过浏览器的重复访问保留信息非常有用。

## 会话

PHP 内置支持会话，处理所有的 cookie 操作，以提供可以从不同页面访问并跨多次访问站点的持久变量。会话使你能够轻松创建多页面表单（如购物车）、保存用户认证信息以及在站点上存储持久用户首选项。

每个首次访问者都会被分配一个唯一的会话 ID。默认情况下，会话 ID 存储在名为 `PHPSESSID` 的 cookie 中。如果用户的浏览器不支持 cookie 或已关闭 cookie，会话 ID 将通过网站内的 URL 传播。

每个会话都有一个关联的数据存储。你可以*注册*变量，以便在每个页面启动时从数据存储加载，并在页面结束时保存回数据存储。注册的变量在页面间持久存在，并且在一个页面上对变量的更改可以在其他页面上看到。例如，“将此商品加入购物车”的链接可以将用户带到一个页面，向购物车中注册的数组添加项目。然后可以在另一个页面上使用此注册的数组来显示购物车的内容。

### 会话基础

脚本开始运行时会自动启动会话。如有必要，会生成新的会话 ID，可能创建一个要发送到浏览器的 cookie，并从存储中加载任何持久变量。

你可以通过将变量名称传递给 `$_SESSION[]` 数组来将变量注册到会话中。例如，这里是一个基本的点击计数器：

```php
session_start();
$_SESSION['hits'] = $_SESSION['hits'] + 1;

echo "This page has been viewed {$_SESSION['hits']} times.";
```

`session_start()` 函数将注册的变量加载到关联数组 `$_SESSION` 中。键是变量的名称（例如 `$_SESSION['hits']`）。如果你感兴趣，`session_id()` 函数返回当前会话 ID。

要结束会话，请调用 `session_destroy()`。这将删除当前会话的数据存储，但不会从浏览器缓存中删除 cookie。这意味着，在后续访问启用会话的页面时，用户将具有与调用 `session_destroy()` 之前相同的会话 ID，但没有数据。

示例 8-13 展示了从 示例 8-11 改写的代码，使用会话而不是手动设置 cookie。

##### 示例 8-13\. 使用会话设置首选项（prefs_session.php）

```php
<?php session_start(); ?>

<html>
<head><title>Preferences Set</title></head>
<body>

<?php
$colors = `array`(
 'black' => "#000000",
 'white' => "#ffffff",
 'red' => "#ff0000",
 'blue' => "#0000ff"
);

$bg = $colors[`$_POST`['background']];
$fg = $colors[`$_POST`['foreground']];

`$_SESSION`['bg'] = $bg;
`$_SESSION`['fg'] = $fg;
?>

<p>Thank you. Your preferences have been changed to:<br /> Background: <?php `echo` `$_POST`['background']; ?><br /> Foreground: <?php `echo` `$_POST`['foreground']; ?></p>

<p>Click <a href="prefs_session_demo.php">here</a> to see the preferences
in action.</p>

</body>
</html>
```

示例 8-14 展示了从 示例 8-12 改写为使用会话。会话启动后，创建了 `$bg` 和 `$fg` 变量，脚本只需使用它们即可。

##### 示例 8-14\. 使用会话中的首选项（prefs_session_demo.php）

```php
<?php
session_start() ;
$backgroundName = `$_SESSION`['bg'] ;
$foregroundName = `$_SESSION`['fg'] ;
?>
<html>
<head><title>Front Door</title></head>
<body bgcolor="<?php `echo` $backgroundName; ?>" text="<?php `echo` $foregroundName; ?>">

<h1>Welcome to the Store</h1>

<p>We have many fine products for you to view. Please feel free to browse
the aisles and stop an assistant at any time. But remember, you break it
you bought it!</p>

<p>Would you like to <a href="colors.php">change your preferences?</a></p>

</body></html>
```

要查看此更改，只需更新 *colors.php* 文件中的操作目标。默认情况下，PHP 会话 ID cookie 在浏览器关闭时过期。也就是说，会话在浏览器停止存在后不会持久保存。要更改此设置，您需要在 *php.ini* 中设置 `session.cookie_lifetime` 选项为 cookie 的生命周期（以秒为单位）。

### 替代方案为 cookie

默认情况下，会话 ID 通过 `PHPSESSID` cookie 从页面传递到页面。但是，PHP 的会话系统支持两种替代方案：表单字段和 URL。通过隐藏表单字段传递会话 ID 非常笨拙，因为它强制您将每个页面之间的每个链接都变成表单的提交按钮。我们将不在此处进一步讨论此方法。

传递会话 ID 的 URL 系统相对更加优雅。PHP 可以重写您的 HTML 文件，将会话 ID 添加到每个相对链接中。但是，要使此功能正常工作，PHP 在编译时必须配置 `-enable-trans-id` 选项。这会导致性能损失，因为 PHP 必须解析和重写每个页面。繁忙的站点可能希望使用 cookie，因为它们不会因页面重写而减慢速度。此外，这会暴露您的会话 ID，可能导致中间人攻击。

### 自定义存储

默认情况下，PHP 将会话信息存储在服务器临时目录中的文件中。每个会话变量存储在单独的文件中。每个变量以专有格式序列化到文件中。您可以在 *php.ini* 文件中更改所有这些值。

您可以通过在 *php.ini* 中设置 `session.save_path` 值来更改会话文件的位置。如果您在具有自己安装的 PHP 的共享服务器上，请将目录设置为您自己目录树中的某个位置，这样同一台机器上的其他用户就无法访问您的会话文件。

PHP 可以将会话信息存储在当前会话存储中的两种格式之一——PHP 的内置格式或 Web 分布数据交换（WDDX）格式。您可以通过在您的 *php.ini* 文件中设置 `session.serialize_handler` 值为 `php`（默认行为）或 `wddx`（WDDX 格式）来更改格式。

## 结合 Cookies 和 Sessions

使用 Cookies 和自定义的会话处理程序的组合，您可以跨访问保留状态。任何应该在用户离开站点时被遗忘的状态，例如用户正在访问的页面，可以交给 PHP 的内置会话处理。任何应该在用户访问之间保持的状态，例如唯一的用户 ID，可以存储在一个 Cookie 中。使用用户 ID，您可以从永久存储（如数据库）中检索用户更长期的状态（显示偏好、邮寄地址等）。

示例 8-15 允许用户选择文本和背景颜色，并将这些值存储在一个 Cookie 中。在接下来的一周内访问页面时，会将颜色值在 Cookie 中发送。

##### 示例 8-15\. 跨访问保存状态（save_state.php）

```php
<?php
if($_POST['bgcolor']) {
 setcookie('bgcolor', $_POST['bgcolor'], time() + (60 * 60 * 24 * 7));
}

if (isset($_COOKIE['bgcolor'])) {
 $backgroundName = $_COOKIE['bgcolor'];
}
else if (isset($_POST['bgcolor'])) {
 $backgroundName = $_POST['bgcolor'];
}
else {
 $backgroundName = "gray";
} ?>
<html>
<head><title>Save It</title></head>
<body bgcolor="<?php echo $backgroundName; ?>">

<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="POST">
 <p>Background color:
 <select name="bgcolor">
 <option value="gray">Gray</option>
 <option value="white">White</option>
 <option value="black">Black</option>
 <option value="blue">Blue</option>
 <option value="green">Green</option>
 <option value="red">Red</option>
 </select></p>

 <input type="submit" />
</form>

</body>
</html>
```

# SSL

安全套接字层（SSL）提供了一个安全的通道，使得常规的 HTTP 请求和响应可以流动。PHP 并不专门关注 SSL，因此您无法通过 PHP 控制加密方式。一个 *https://* 的 URL 表示该文档的连接是安全的，而不像 *http://* 的 URL。

如果 PHP 页面是在 SSL 连接的请求下生成的，`$_SERVER` 数组中的 `HTTPS` 条目将被设置为 `'on'`。要防止页面在非加密连接下生成，只需简单地使用：

```php
if ($_SERVER['HTTPS'] !== 'on') {
 die("Must be a secure connection.");
}
```

一个常见的错误是在安全连接下发送表单（例如，*https://www.example.com/form.html*），但是表单的 `action` 设置为一个 *http://* 的 URL。用户输入的任何表单参数都会通过不安全的连接发送，而简单的数据包嗅探器可以将其暴露出来。

# 接下来是什么

在现代 Web 开发中有许多技巧和陷阱，我们希望本章所指出的内容能帮助您构建出色的网站。下一章将讨论如何将数据保存到 PHP 中的数据存储中，我们将涵盖大多数常用的方法，如数据库、SQL 和 NoSQL 风格、SQLite，以及直接文件信息存储。
