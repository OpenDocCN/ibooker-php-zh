# 第十三章：JSON

类似于 XML，JavaScript 对象表示法（JSON）被设计为一种标准化的数据交换格式。但是，与 XML 不同，JSON 非常轻量级且易于人阅读。虽然它从 JavaScript 中借鉴了许多语法提示，但 JSON 设计为与语言无关。

JSON 基于两种结构构建：名/值对的集合称为*对象*（相当于 PHP 的关联数组）和值的有序列表称为*数组*（相当于 PHP 的索引数组）。每个值可以是多种类型之一：对象、数组、字符串、数字、布尔值`TRUE`或`FALSE`，或者`NULL`（表示缺少值）。

# 使用 JSON

*json*扩展在 PHP 安装中默认包含，本地支持将数据从 PHP 变量转换为 JSON 格式，反之亦然。

要获取 PHP 变量的 JSON 表示形式，请使用`json_encode()`：

```php
$data = array(1, 2, "three");
$jsonData = json_encode($data);
echo $jsonData;
`[``1``,` `2``,` `"``three``"``]`
```

类似地，如果有包含 JSON 数据的字符串，可以使用`json_decode()`将其转换为 PHP 变量：

```php
$jsonData = "[1, 2, [3, 4], \"five\"]";
$data = json_decode($jsonData);
print_r($data);
`Array``(` `[``0``]` `=>` `1` `[``1``]` `=>` `2` `[``2``]` `=>` `Array``(` `[``0``]` `=>` `3` `[``1``]` `=>` `4` `)` `[``3``]` `=>` `five``)`
```

如果字符串是无效的 JSON，或者字符串不是以 UTF-8 格式编码，那么将返回一个`NULL`值。

JSON 中的值类型转换为 PHP 等效值如下：

`object`

包含对象键值对的关联数组。每个值也被转换为其 PHP 等效值。

`array`

包含包含的值的索引数组，每个值也被转换为其 PHP 等效值。

`string`

直接转换为 PHP 字符串。

`number`

返回一个数字。如果值过大而无法由 PHP 的数字值表示，则返回`NULL`，除非使用`json_decode()`调用`JSON_BIGINT​_AS_STRING`（此时将返回一个字符串）。

`boolean`

布尔值`true`转换为`TRUE`；布尔值`false`转换为`FALSE`。

`null`

`null`值以及无法解码的任何值都被转换为`NULL`。

# 序列化 PHP 对象

尽管名称相似，PHP 对象与 JSON 对象之间没有直接转换——JSON 称之为“对象”的东西实际上是一个关联数组。要将 JSON 数据转换为 PHP 对象类的实例，必须编写基于 API 返回格式的代码来执行此操作。

然而，`JsonSerializable`接口允许您根据您的喜好将对象转换为 JSON 数据。如果对象类未实现该接口，`json_encode()`简单地创建一个 JSON 对象，其中包含与对象数据成员对应的键和值。

否则，`json_encode()`调用类的`jsonSerialize()`方法并使用其序列化对象数据。

示例 13-1 将`JsonSerializable`接口添加到`Book`和`Author`类中。

##### 示例 13-1\. 书籍和作者的 JSON 序列化

```php
class Book implements JsonSerializable {
 public $id;
 public $name;
 public $edition;

 public function __construct($id) {
 $this->id = $id;
 }

 public function jsonSerialize() {
 $data = array(
 'id' => $this->id,
 'name' => $this->name,
 'edition' => $this->edition,
 );

 return $data;
 }
}

class Author implements JsonSerializable {
 public $id;
 public $name;
 public $books = array();

 public function __construct($id) {
 $this->id = $id;
 }

 public function jsonSerialize() {
 $data = array(
 'id' => $this->id,
 'name' => $this->name,
 'books' => $this->books,
 );

 return $data;
 }
}
```

从 JSON 数据创建 PHP 对象需要编写代码执行转换。

示例 13-2 展示了一个类，实现了将 JSON 数据转换为`Book`和`Author`实例成为 PHP 对象的工厂式转换。

##### 示例 13-2\. 通过工厂对 Book 和 Author 进行 JSON 序列化

```php
class ResourceFactory {
 static public function authorFromJSON($jsonData) {
 $author = new Author($jsonData['id']);
 $author->name = $jsonData['name'];

 foreach ($jsonData['books'] as $bookIdentifier) {
 $this->books[] = new Book($bookIdentifier);
 }

 return $author;
 }

 static public function bookFromJSON($jsonData) {
 $book = new Book($jsonData['id']);
 $book->name = $jsonData['name'];
 $book->edition = (int) $jsonData['edition'];

 return $book;
 }
}
```

## 选项

JSON 解析器函数有多个选项可设置以控制转换过程。

对于`json_decode()`，最常见的选项包括：

`JSON_BIGINT_AS_STRING`

当解码一个过大无法表示为 PHP 数字类型的数字时，返回该值作为字符串。

`JSON_OBJECT_AS_ARRAY`

将 JSON 对象解码为 PHP 数组。

对于`json_encode()`，最常见的选项包括：

`JSON_FORCE_OBJECT`

将 PHP 值的索引数组编码为 JSON 对象，而不是 JSON 数组。

`JSON_NUMERIC_CHECK`

将表示数字值的字符串编码为 JSON 数字，而不是 JSON 字符串。在实践中，最好手动转换，这样你可以清楚知道类型是什么。

`JSON_PRETTY_PRINT`

使用空白符将返回的数据格式化为更易读的形式。虽然不是必需的，但可以简化调试。

最后，以下选项可用于`json_encode()`和`json_decode()`：

`JSON_INVALID_UTF8_IGNORE`

忽略无效的 UTF-8 字符。如果还设置了`JSON_INVALID_UTF8_SUBSTITUTE`，则替换它们；否则，在结果字符串中删除它们。

`JSON_INVALID_UTF8_SUBSTITUTE`

用`\0xfffd`（Unicode 字符`'REPLACEMENT CHARACTER'`）替换无效的 UTF-8 字符。

`JSON_THROW_ON_ERROR`

当发生错误时，抛出错误而不是填充全局最后错误状态。

# 接下来是什么

在编写 PHP 时，考虑的最重要的事情之一是代码的安全性，从代码吸收和防御攻击的能力到如何保护你自己和用户的数据。下一章提供了指导和最佳实践，帮助你避免与安全相关的灾难。
