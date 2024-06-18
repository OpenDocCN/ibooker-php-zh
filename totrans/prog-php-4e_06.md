# 第五章。数组

正如我们在第二章中讨论的那样，PHP 支持标量和复合数据类型。在本章中，我们将讨论复合类型之一：数组。*数组*是一组按键值对组织的数据值的有序集合。可能有助于以宽泛的术语来思考数组，就像一个鸡蛋盒一样。鸡蛋盒的每个隔间可以容纳一个鸡蛋，但它整体上作为一个容器运行。而且，正如鸡蛋盒不仅仅可以装鸡蛋（您可以放入任何东西，比如石头、雪球、四叶草或螺丝和螺母），数组也不限于一种数据类型。它可以包含字符串、整数、布尔值等。另外，数组隔间也可以包含其他数组——但稍后再详细介绍。

本章讨论创建数组、向数组中添加和删除元素以及遍历数组内容。由于数组非常常见和有用，因此在 PHP 中有许多内置函数与数组一起使用。例如，如果您要向多个电子邮件地址发送电子邮件，则将电子邮件地址存储在数组中，然后通过数组循环，将消息发送到当前电子邮件地址。此外，如果您有一个允许多个选择的表单，用户选择的项目将以数组形式返回。

# 索引数组与关联数组

PHP 中有两种类型的数组：索引数组和关联数组。*索引*数组的键是整数，从 0 开始。当您通过它们的位置来标识事物时使用索引数组。*关联*数组的键是字符串，更像是双列表。第一列是键，用于访问值。

PHP 内部将所有数组存储为关联数组；关联数组与索引数组之间的唯一区别在于键。某些数组特性主要用于与索引数组一起使用，因为它们假设您具有或希望键是从 0 开始的连续整数。在两种情况下，键都是唯一的。换句话说，无论键是字符串还是整数，您都不能具有两个具有相同键的元素。

PHP 数组对其元素有内部顺序，这与键和值无关，并且有函数可用于根据此内部顺序遍历数组。顺序通常是插入到数组中的值的顺序，但是本章后面描述的排序函数允许您根据键、值或任何其他选择更改顺序。

# 识别数组元素

在我们讨论创建数组之前，让我们先看看现有数组的结构。您可以使用数组变量的名称访问现有数组中的特定值，后跟元素的键（或*索引*），放在方括号内：

```php
$age['fred']
$shows[2]
```

键可以是字符串或整数。与整数数字等效的字符串值（没有前导零）被视为整数。因此，`$array[3]`和`$array['3']`引用相同的元素，但`$array['03']`引用不同的元素。负数是有效的键，但它们不像在 Perl 中那样指定数组末尾的位置。

您不必引用单词字符串。例如，`$age['fred']`与`$age[fred]`相同。然而，始终使用引号被认为是良好的 PHP 风格，因为没有引号的键与常量无法区分。当您使用常量作为未引用的索引时，PHP 使用常量的值作为索引并发出警告。这在未来版本的 PHP 中将抛出错误：

```php
$person = `array`("name" => 'Peter');
*`print`* "Hello, {$person[name]}";
*`// output: Hello, Peter`*
*`// this 'works' but emits this warning as well:`* `Warning``:` Use of undefined constant name - assumed 'name' (this will throw an 
Error in a future version of PHP)

```

如果使用插值构建数组索引，则必须使用引号：

```php
$person = `array`("name" => 'Peter');
*`print`* "Hello, {$person["name"]}";*`// output: Hello, Peter (with no warning)`*
```

尽管在插入数组查找中插入数组时理论上是可选的，但应该用引号引起键，以确保获取您期望的值。考虑以下例子：

```php
define('NAME', 'bob');
$person = `array`("name" => 'Peter');
echo "Hello, {$person['name']}";
`echo` "<br/>" ;
`echo` "Hello, NAME";
`echo` "<br/>" ;
`echo` NAME ;
// output: Hello, Peter
Hello, NAME
bob
```

# 在数组中存储数据

将值存储在数组中将创建数组（如果尚不存在），但尝试从尚未定义的数组中检索值将不会创建数组。例如：

```php
// $addresses not defined before this point
echo $addresses[0]; // prints nothing
echo $addresses; // prints nothing

$addresses[0] = "spam@cyberpromo.net";
echo $addresses; // prints "Array"
```

使用简单的赋值在程序中初始化数组可能会导致这样的代码：

```php
$addresses[0] = "spam@cyberpromo.net";
$addresses[1] = "abuse@example.com";
$addresses[2] = "root@example.com";
```

这是一个索引数组，其整数索引从 0 开始。以下是一个关联数组：

```php
$price['gasket'] = 15.29;
$price['wheel'] = 75.25;
$price['tire'] = 50.00;
```

初始化数组的更简单的方法是使用`array()`结构，该结构从其参数构建数组。这将构建一个索引数组，并且索引值（从 0 开始）会自动创建：

```php
$addresses = array("spam@cyberpromo.net", "abuse@example.com", 
"root@example.com");
```

要使用`array()`创建一个关联数组，请使用`=>`符号将索引（键）与值分开：

```php
$price = array(
 'gasket' => 15.29,
 'wheel' => 75.25,
 'tire' => 50.00
);
```

注意使用空白和对齐。我们本可以将代码挤在一起，但那样不容易阅读（这相当于之前的代码示例），也不容易添加或移除值：

```php
$price = array('gasket' => 15.29, 'wheel' => 75.25, 'tire' => 50.00);
```

您还可以使用较短的备选语法指定数组：

```php
$price = ['gasket' => 15.29, 'wheel' => 75.25, 'tire' => 50.0];
```

要构建一个空数组，请将`array()`不传递参数：

```php
$addresses = array();
```

您可以使用`=>`指定初始键，然后列出值的列表。从该键开始将值插入数组，随后的值具有顺序键：

```php
$days = array(1 => "Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun");
// 2 is Tue, 3 is Wed, etc.
```

如果初始索引是非数字字符串，则随后的索引从 0 开始为整数。因此，以下代码可能是一个错误：

```php
$whoops = array('Fri' => "Black", "Brown", "Green");

// same as
$whoops = array('Fri' => "Black", 0 => "Brown", 1 => "Green");
```

## 向数组附加值

要在现有索引数组的末尾添加更多值，请使用`[]`语法：

```php
$family = array("Fred", "Wilma");
$family[] = "Pebbles"; // $family[2] is "Pebbles"
```

该结构假定数组的索引是数字，并从 0 开始为下一个可用的数值索引分配元素。试图追加到关联数组而没有适当的键几乎总是程序员的错误，但 PHP 会为新元素分配数值索引而不发出警告：

```php
$person = array('name' => "Fred");
$person[] = "Wilma"; // $person[0] is now "Wilma"
```

## 分配数值范围

`range()`函数创建一个包含在两个参数中传递的连续整数或字符值之间的数组。例如：

```php
$numbers = range(2, 5); // $numbers = array(2, 3, 4, 5);
$letters = range('a', 'z'); // $letters holds the alphabet
$reversedNumbers = range(5, 2); // $reversedNumbers = array(5, 4, 3, 2);
```

仅使用字符串参数的第一个字母来构建范围：

```php
range("aaa", "zzz"); // same as range('a','z')
```

## 获取数组的大小

`count()`和`sizeof()`函数在使用和效果上是相同的。它们返回数组中的元素数。对于使用哪个函数，没有风格上的偏好。这里有一个例子：

```php
$family = array("Fred", "Wilma", "Pebbles");
$size = count($family); // $size is 3
```

此函数仅计算实际设置的数组值：

```php
$confusion = array( 10 => "ten", 11 => "eleven", 12 => "twelve");
$size = count($confusion); // $size is 3
```

## 填充数组

要创建一个值初始化为相同内容的数组，请使用`array_pad()`。`array_pad()`的第一个参数是数组，第二个参数是您希望数组具有的最小元素数，第三个参数是要赋予创建的任何元素的值。`array_pad()`函数返回一个新的填充数组，保留其参数（源）数组不变。

这里是`array_pad()`的示例：

```php
$scores = array(5, 10);
$padded = array_pad($scores, 5, 0); // $padded is now array(5, 10, 0, 0, 0)
```

注意新值如何附加到数组中。如果要将新值添加到数组的开头，请使用负的第二个参数：

```php
$padded = array_pad($scores, −5, 0); // $padded is now array(0, 0, 0, 5, 10);
```

如果填充关联数组，则现有键将被保留。新元素将具有从 0 开始的数值键。

# 多维数组

数组中的值本身可以是数组。这使您可以轻松创建多维数组：

```php
$row0 = array(1, 2, 3);
$row1 = array(4, 5, 6);
$row2 = array(7, 8, 9);
$multi = array($row0, $row1, $row2);
```

您可以通过追加更多方括号`[]`来引用多维数组的元素：

```php
$value = $multi[2][0]; // row 2, column 0\. $value = 7
```

要插入多维数组的查找，必须将整个数组查找括在花括号中：

```php
echo("The value at row 2, column 0 is {$multi[2][0]}\n");
```

如果未使用花括号，则输出如下所示：

```php
`The` `value` `at` `row` `2``,` `column` `0` `is` `Array``[``0``]`
```

# 提取多个值

要将数组的所有值复制到变量中，请使用`list()`结构：

```php
list (*`$variable``,` `...`*) = *`$array`*;
```

数组的值按照数组的内部顺序复制到列出的变量中。默认情况下，这是它们插入的顺序，但后面描述的排序函数可以改变这个顺序。这里有一个例子：

```php
$person = array("Fred", 35, "Betty");
list($name, $age, $wife) = $person;
// $name is "Fred", $age is 35, $wife is "Betty"
```

###### 注意

使用`list()`函数是从数据库选择中获取值的常见做法，其中仅返回一行。这自动将来自简单查询的数据加载到一系列本地变量中。这里是从体育赛程数据库中选择两支对立队伍的示例：

```php
$sql = "SELECT HomeTeam, AwayTeam FROM schedule WHERE 
Ident = 7";
$result = mysql_query($sql);
list($hometeam, $awayteam) = mysql_fetch_assoc($result);
```

在第九章中有更多关于数据库的覆盖内容。

如果在数组中有比`list()`中更多的值，则额外的值将被忽略：

```php
$person = array("Fred", 35, "Betty");
list($name, $age) = $person; // $name is "Fred", $age is 35
```

如果在`list()`中有比数组中更多的值，额外的值将被设置为`NULL`：

```php
$values = array("hello", "world");
list($a, $b, $c) = $values; // $a is "hello", $b is "world", $c is NULL
```

`list()`中的两个或多个连续逗号会跳过数组中的值：

```php
$values = range('a', 'e'); // use range to populate the array
list($m, , $n, , $o) = $values; // $m is "a", $n is "c", $o is "e"
```

## 切片数组

要仅提取数组的子集，请使用`array_slice()`函数：

```php
$subset = array_slice*`(``array`*, *`offset`*, *`length`*);
```

`array_slice()`函数返回一个由原始数组中一系列连续值组成的新数组。*offset*参数标识要复制的初始元素（`0`表示数组中的第一个元素），*length*参数标识要复制的值的数量。新数组从 0 开始具有连续的数值键。例如：

```php
$people = array("Tom", "Dick", "Harriet", "Brenda", "Jo");
$middle = array_slice($people, 2, 2); // $middle is array("Harriet", "Brenda")
```

通常只有在索引数组上使用`array_slice()`才有意义（即具有从 0 开始的连续整数索引）：

```php
// this use of array_slice() makes no sense
$person = array('name' => "Fred", 'age' => 35, 'wife' => "Betty");
$subset = array_slice($person, 1, 2); // $subset is array(0 => 35, 1 => "Betty")
```

使用`array_slice()`和`list()`结合在变量中提取部分数值：

```php
$order = array("Tom", "Dick", "Harriet", "Brenda", "Jo");
list($second, $third) = array_slice($order, 1, 2);
// $second is "Dick", $third is "Harriet"
```

## 将数组拆分成块

要将数组分成较小的、均匀大小的数组，请使用`array_chunk()`函数：

```php
$chunks = array_chunk(*`array`*, *`size`* [, *`preserve_keys`*]);
```

该函数返回较小数组的数组。第三个参数*preserve_keys*是一个布尔值，用于确定新数组的元素是否与原始数组中的相同键（对关联数组有用）或从 0 开始的新数字键（对索引数组有用）。默认情况下分配新键，如下所示：

```php
$nums = range(1, 7);
$rows = array_chunk($nums, 3);
print_r($rows);

`Array` `(`
 `[``0``]` `=>` `Array` `(`
 `[``0``]` `=>` `1`
 `[``1``]` `=>` `2`
 `[``2``]` `=>` `3`
 `)`
 `[``1``]` `=>` `Array` `(`
 `[``0``]` `=>` `4`
 `[``1``]` `=>` `5`
 `[``2``]` `=>` `6`
 `)`
 `[``2``]` `=>` `Array` `(`
 `[``0``]` `=>` `7`
 `)`
`)`
```

## 键和值

`array_keys()`函数按照数组内部顺序返回仅包含键的数组：

```php
$arrayOfKeys = array_keys(*`array`*);
```

下面是一个示例：

```php
$person = array('name' => "Fred", 'age' => 35, 'wife' => "Wilma");
$keys = array_keys($person); // $keys is array("name", "age", "wife")
```

PHP 还提供了一个（通常不太有用的）函数来获取数组中仅包含值的数组，即`array_values()`：

```php
$arrayOfValues = array_values(*`array`*);
```

与`array_keys()`类似，值按照数组的内部顺序返回：

```php
$values = array_values($person); // $values is array("Fred", 35, "Wilma");
```

## 检查元素是否存在

要查看数组中是否存在元素，请使用`array_key_exists()`函数：

```php
if (array_key_exists(*`key`*, *`array`*)) { ... }
```

该函数返回一个布尔值，指示第一个参数是否是作为第二个参数给出的数组中的有效键。

简单地说：

```php
if ($person['name']) { ... } // this can be misleading
```

即使数组中有一个键为`name`的元素，其对应的值可能为 false（即`0`，`NULL`或空字符串）。请改用`array_key_exists()`，如下所示：

```php
$person['age'] = 0; // unborn? 
if ($person['age']) {
 echo "true!\n";
}

if (array_key_exists('age', $person)) {
 echo "exists!\n";
}

`exists``!`
```

许多人改用`isset()`函数，如果元素存在且不为`NULL`，则返回`true`：

```php
$a = array(0, NULL, '');

function tf($v)
{
 return $v ? 'T' : 'F';
}

for ($i=0; $i < 4; $i++) {
 printf("%d: %s %s\n", $i, tf(isset($a[$i])), tf(array_key_exists($i, $a)));
}
`0``:` `T` `T`
`1``:` `F` `T`
`2``:` `T` `T`
`3``:` `F` `F`
```

## 在数组中删除和插入元素

`array_splice()`函数可以从数组中删除或插入元素，并可选择从已删除元素创建另一个数组：

```php
$removed = array_splice(*`array`*, *`start`* [, *`length`* [, *`replacement`* ] ]);
```

我们将使用这个数组查看`array_splice()`：

```php
$subjects = array("physics", "chem", "math", "bio", "cs", "drama", "classics");
```

我们可以通过告诉`array_splice()`从位置 2 开始删除 3 个元素来删除`"math"`、`"bio"`和`"cs"`元素：

```php
$removed = array_splice($subjects, 2, 3);
// $removed is array("math", "bio", "cs")
// $subjects is array("physics", "chem", "drama", "classics")
```

如果省略长度，`array_splice()`将删除到数组的末尾：

```php
$removed = array_splice($subjects, 2);
// $removed is array("math", "bio", "cs", "drama", "classics")
// $subjects is array("physics", "chem")
```

如果您只想从源数组中删除元素，并且不关心保留它们的值，则不需要存储`array_splice()`的结果：

```php
array_splice($subjects, 2);
// $subjects is array("physics", "chem");
```

要在移除其他元素的位置插入元素，请使用第四个参数：

```php
$new = array("law", "business", "IS");
array_splice($subjects, 4, 3, $new);
// $subjects is array("physics", "chem", "math", "bio", "law", "business", "IS")
```

替换数组的大小不必与删除元素的数量相同。数组会根据需要增长或缩小：

```php
$new = array("law", "business", "IS");
array_splice($subjects, 3, 4, $new);
// $subjects is array("physics", "chem", "math", "law", "business", "IS")
```

要插入新元素到数组中，并将现有元素推向右侧，请删除零个元素：

```php
$subjects = array("physics", "chem", "math');
$new = array("law", "business");
array_splice($subjects, 2, 0, $new);
// $subjects is array("physics", "chem", "law", "business", "math")
```

虽然到目前为止的示例使用的是索引数组，`array_splice()`也适用于关联数组：

```php
$capitals = array(
 'USA' => "Washington",
 'Great Britain' => "London",
 'New Zealand' => "Wellington",
 'Australia' => "Canberra",
 'Italy' => "Rome",
 'Canada' => "Ottawa"
);

$downUnder = array_splice($capitals, 2, 2); // remove New Zealand and Australia
$france = array('France' => "Paris");

array_splice($capitals, 1, 0, $france); // insert France between USA and GB
```

# 数组和变量之间的转换

PHP 提供了两个函数`extract()`和`compact()`，用于在数组和变量之间进行转换。变量的名称对应于数组中的键，变量的值成为数组中的值。例如，这个数组

```php
$person = array('name' => "Fred", 'age' => 35, 'wife' => "Betty");
```

可以转换或从这些变量构建：

```php
$name = "Fred";
$age = 35;
$wife = "Betty";
```

## 从数组创建变量

`extract()` 函数会自动从数组中创建局部变量。数组元素的索引成为变量名：

```php
extract($person); // $name, $age, and $wife are now set
```

如果由提取创建的变量与现有变量同名，则现有变量的值将被数组中的值覆盖。

你可以通过传递第二个参数来修改 `extract()` 的行为。附录 描述了此第二个参数可能的取值。最有用的值是 `EXTR_PREFIX_ALL`，它指示 `extract()` 的第三个参数是在使用 `extract()` 时创建的变量名称的前缀。这有助于确保在使用 `extract()` 时创建唯一的变量名称。在 PHP 中，始终使用 `EXTR_PREFIX_ALL` 是良好的编程风格，如下所示：

```php
$shape = "round";
$array = array('cover' => "bird", 'shape' => "rectangular");

extract($array, EXTR_PREFIX_ALL, "book");
echo "Cover: {$book_cover}, Book Shape: {$book_shape}, Shape: {$shape}";

`Cover``:` `bird``,` `Book` `Shape``:` `rectangular``,` `Shape``:` `round`
```

## 从变量创建数组

`compact()` 函数是 `extract()` 函数的反向操作；你可以将变量名作为单独的参数或者作为数组传递给 `compact()`。`compact()` 函数创建一个关联数组，其键是变量名，值是变量的值。数组中未对应实际变量的名称将被跳过。以下是 `compact()` 的示例：

```php
$color = "indigo";
$shape = "curvy";
$floppy = "none";

$a = compact("color", "shape", "floppy");
// or
$names = array("color", "shape", "floppy");
$a = compact($names);
```

# 遍历数组

处理数组中每个元素的最常见任务是执行某些操作，例如向地址数组中的每个元素发送邮件，更新文件名数组中的每个文件，或者将价格数组中的每个元素相加。在 PHP 中，有几种遍历数组的方法，你选择的方法将取决于你的数据和正在执行的任务。

## `foreach` 结构

遍历数组元素的最常见方法是使用 `foreach` 结构：

```php
$addresses = array("spam@cyberpromo.net", "abuse@example.com");

foreach ($addresses as $value) {
 echo "Processing {$value}\n";
}
`Processing` `spam``@``cyberpromo``.``net`
`Processing` `abuse``@``example``.``com`
```

PHP 依次对`$addresses`数组中的每个元素执行循环体（`echo`语句），其中将`$value`设置为当前元素。元素按其内部顺序处理。

`foreach`的另一种形式允许你访问当前键：

```php
$person = array('name' => "Fred", 'age' => 35, 'wife' => "Wilma");

foreach ($person as $key => $value) {
 echo "Fred's {$key} is {$value}\n";
}
`Fred``'s name is Fred`
`Fred'``s` `age` `is` `35`
`Fred``'``s` `wife` `is` `Wilma`
```

在这种情况下，每个元素的键都放在`$key`中，相应的值放在`$value`中。

`foreach`结构不会在数组本身上操作，而是在其副本上操作。你可以在`foreach`循环的主体中插入或删除元素，安全地知道循环不会尝试处理已删除或已插入的元素。

## 迭代器函数

每个 PHP 数组都会跟踪你正在处理的当前元素；指向当前元素的指针称为*迭代器*。PHP 具有设置、移动和重置此迭代器的函数。迭代器函数包括：

`current()`

返回迭代器当前指向的元素。

`reset()`

将迭代器移动到数组中的第一个元素并返回它。

`next()`

将迭代器移动到数组中的下一个元素并返回它。

`prev()`

将迭代器移动到数组中的上一个元素并返回它。

`end()`

将迭代器移动到数组中的最后一个元素并返回它。

`each()`

返回当前元素的键和值作为数组，并将迭代器移动到数组的下一个元素。

`key()`

返回当前元素的键。

`each()`函数用于遍历数组元素。它根据它们的内部顺序处理元素：

```php
reset($addresses);

while (list($key, $value) = each($addresses)) {
 echo "{$key} is {$value}<br />\n";
}
`0` `is` `spam``@``cyberpromo``.``net`
`1` `is` `abuse``@``example``.``com`
```

这种方法不像`foreach`那样制作数组的副本。当处理非常大的数组并希望节省内存时，这非常有用。

当你需要将数组的一些部分与其他部分分开考虑时，迭代函数非常有用。示例 5-1 展示了构建表格的代码，将关联数组中的第一个索引和值视为表格列标题。

##### 示例 5-1\. 使用迭代函数构建表格

```php
$ages = array(
 'Person' => "Age",
 'Fred' => 35,
 'Barney' => 30,
 'Tigger' => 8,
 'Pooh' => 40
);

// start table and print heading
reset($ages);

list($c1, $c2) = each($ages);

echo("<table>\n<tr><th>{$c1}</th><th>{$c2}</th></tr>\n");

// print the rest of the values
while (list($c1, $c2) = each($ages)) {
 echo("<tr><td>{$c1}</td><td>{$c2}</td></tr>\n");
}

// end the table
echo("</table>");
```

## 使用`for`循环

如果你知道你在处理一个索引数组，其中键是从 0 开始的连续整数，你可以使用`for`循环通过索引计数。`for`循环作用于数组本身，而不是数组的副本，并且按键顺序处理元素，而不考虑它们的内部顺序。

这里是如何使用`for`打印一个数组：

```php
$addresses = array("spam@cyberpromo.net", "abuse@example.com");
$addressCount = count($addresses);

for ($i = 0; $i < $addressCount; $i++) {
 $value = $addresses[$i];
 echo "{$value}\n";
}
`spam``@``cyberpromo``.``net`
`abuse``@``example``.``com`
```

## 为每个数组元素调用函数

PHP 提供了一种机制，`array_walk()`，用于在数组中的每个元素上调用用户定义的函数一次：

```php
array_walk(*`array`*, *`callable`*);
```

您定义的函数接受两个或可选的三个参数：第一个是元素的值，第二个是元素的键，第三个是在调用`array_walk()`时提供给它的值。例如，这里是另一种从数组值打印表列的方法：

```php
$printRow = function ($value, $key)
{
 print("<tr><td>{$key}</td><td>{$value}</td></tr>\n");
};

$person = array('name' => "Fred", 'age' => 35, 'wife' => "Wilma");

echo "<table border=1>";

array_walk($person, $printRow);

echo "</table>";
```

此示例的变体使用`array_walk()`的可选第三个参数指定背景颜色。此参数为我们提供了打印许多带有不同背景颜色表格的灵活性：

```php
function printRow($value, $key, $color)
{
 echo "<tr>\n<td bgcolor=\"{$color}\">{$value}</td>";
 echo "<td bgcolor=\"{$color}\">{$key}</td>\n</tr>\n";
}

$person = array('name' => "Fred", 'age' => 35, 'wife' => "Wilma");

echo "<table border=\"1\">";

array_walk($person, "printRow", "lightblue");
echo "</table>";
```

如果您有多个选项要传递给调用函数，只需将数组作为第三个参数传递：

```php
$extraData = array('border' => 2, 'color' => "red");
$baseArray = array("Ford", "Chrysler", "Volkswagen", "Honda", "Toyota");

array_walk($baseArray, "walkFunction", $extraData);

function walkFunction($item, $index, $data)
{
 echo "{$item} <- item, then border: {$data['border']}";
 echo " color->{$data['color']}<br />" ;
}
`Ford` `<-` `item``,` `then` `border``:` `2` `color``->``red`
`Crysler` `<-` `item``,` `then` `border``:` `2` `color``->``red`
`VW` `<-` `item``,` `then` `border``:` `2` `color``->``red`
`Honda` `<-` `item``,` `then` `border``:` `2` `color``->``red`
`Toyota` `<-` `item``,` `then` `border``:` `2` `color``->``red`
```

`array_walk()`函数按其内部顺序处理元素。

## 减少数组

`array_reduce()`的衍生函数，`array_walk()`将函数依次应用于数组的每个元素，以构建单个值：

```php
$result = array_reduce(*`array`*, *`callable`* [, *`default`* ]);
```

函数接受两个参数：运行总数和当前正在处理的值。它应该返回新的运行总数。例如，要计算数组值的平方和，使用：

```php
$addItUp = function ($runningTotal, $currentValue)
{
 $runningTotal += $currentValue * $currentValue;

 return $runningTotal;
};

$numbers = array(2, 3, 5, 7);
$total = array_reduce($numbers, $addItUp);

echo $total;

`87`
```

`array_reduce()`行执行以下函数调用：

```php
addItUp(0, 2);
addItUp(4, 3);
addItUp(13, 5);
addItUp(38, 7);
```

如果提供了*default*参数，则它是一个种子值。例如，如果我们将前面示例中`array_reduce()`的调用更改为：

```php
$total = array_reduce($numbers, "addItUp", 11);
```

生成的函数调用如下：

```php
addItUp(11, 2);
addItUp(15, 3);
addItUp(24, 5);
addItUp(49, 7);
```

如果数组为空，`array_reduce()`返回*default*值。如果没有提供默认值并且数组为空，则`array_reduce()`返回`NULL`。

## 搜索值

`in_array()`函数根据第一个参数是否是作为第二个参数给出的数组中的元素返回`true`或`false`：

```php
if (in_array(*`to_find`*, *`array`* [, *`strict`*])) { ... }
```

如果可选的第三个参数为`true`，*to_find*的类型和数组中的值必须匹配。默认情况下不检查数据类型。

这是一个简单的例子：

```php
$addresses = array("spam@cyberpromo.net", "abuse@example.com", 
"root@example.com");
$gotSpam = in_array("spam@cyberpromo.net", $addresses); // $gotSpam is true
$gotMilk = in_array("milk@tucows.com", $addresses); // $gotMilk is false
```

PHP 自动索引数组中的值，因此`in_array()`通常比循环检查数组中每个值来查找您想要的值要快得多。

示例 5-2 检查用户是否在表单中填写了所有必填字段。

##### 示例 5-2\. 搜索一个数组

```php
<?php
function hasRequired($array, $requiredFields) {
 $array =

 $keys = array_keys ( $array );
 foreach ( $requiredFields as $fieldName ) {
 if (! in_array ( $fieldName, $keys )) {
 return false;
 }
 }
 return true;
}
if ($_POST ['submitted']) {
 $testArray = array_filter($_POST);
 echo "<p>You ";
 echo hasRequired ( $testArray, array (
 'name',
 'email_address'
 ) ) ? "did" : "did not";
 echo " have all the required fields.</p>";
}
?>
<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="POST">
 <p>
 Name: <input type="text" name="name" /><br /> Email address: <input
 type="text" name="email_address" /><br /> Age (optional): <input
 type="text" name="age" />
 </p>
 <p align="center">
 <input type="submit" value="submit" name="submitted" />
 </p>
</form>
```

`in_array()` 的变体是 `array_search()` 函数。`in_array()` 返回值找到时返回`true`，而 `array_search()` 返回元素的键，如果找到的话：

```php
$person = array('name' => "Fred", 'age' => 35, 'wife' => "Wilma");
$k = array_search("Wilma", $person);

echo("Fred's {$k} is Wilma\n");

`Fred``'``s` `wife` `is` `Wilma`
```

`array_search()` 函数还接受第三个 *strict* 可选参数，要求被搜索的值的类型和数组中的值匹配。

# 排序

排序改变数组元素的内部顺序，并可选择重写键以反映这种新顺序。例如，您可以使用排序将得分从大到小排列，将姓名按字母顺序排列，或者根据用户发布的消息数量对一组用户进行排序。

PHP 提供了三种排序数组的方式——按键排序、按值排序而不更改键、或按值排序然后更改键。每种排序可以按升序、降序或用户定义的函数确定的顺序进行。

## 逐个数组排序

PHP 提供的用于对数组排序的函数如 表格 5-1 所示。

表格 5-1\. PHP 数组排序函数

| 效果 | 升序 | 降序 | 用户定义的顺序 |
| --- | --- | --- | --- |
| 按值排序数组，然后重新分配从 0 开始的索引 | `sort()` | `rsort()` | `usort()` |
| 按值排序数组 | `asort()` | `arsort()` | `uasort()` |
| 按键名排序数组 | `ksort()` | `krsort()` | `uksort()` |

`sort()`、`rsort()` 和 `usort()` 函数设计用于处理索引数组，因为它们分配新的数字键来表示排序。当您需要回答“前十名的得分是多少？”和“按字母顺序第三个是谁？”等问题时，它们非常有用。其他排序函数可以用于索引数组，但只能通过遍历结构（如`foreach`和`next()`）访问排序顺序。

要按升序字母顺序对名称进行排序，请这样做：

```php
$names = array("Cath", "Angela", "Brad", "Mira");
sort($names); // $names is now "Angela", "Brad", "Cath", "Mira"
```

要按照逆字母顺序排列它们，只需调用`rsort()` 而不是 `sort()`。

如果你有一个将用户名映射到登录时间分钟数的关联数组，可以使用`arsort()`来显示前三名的表格，如下所示：

```php
$logins = array(
 'njt' => 415,
 'kt' => 492,
 'rl' => 652,
 'jht' => 441,
 'jj' => 441,
 'wt' => 402,
 'hut' => 309,
);

arsort($logins);

$numPrinted = 0;

echo "<table>\n";

foreach ($logins as $user => $time) {
 echo("<tr><td>{$user}</td><td>{$time}</td></tr>\n");

 if (++$numPrinted == 3) {
 break; // stop after three
 }
}

echo "</table>";
```

如果您希望按用户名升序显示该表格，请改用`ksort()`。

用户定义的排序要求你提供一个函数，该函数接受两个值并返回一个指定排序数组中两个值顺序的值。如果第一个值大于第二个值，函数应返回`1`；如果第一个值小于第二个值，应返回`−1`；如果这两个值对于自定义排序目的相同，则返回`0`。

[示例 5-3](https://example.org/example_five_threedot_sorting_arrays)中的程序将各种排序函数应用于相同的数据。

##### 示例 5-3\. 排序数组

```php
<?php
function userSort($a, $b)
{
 // smarts is all-important, so sort it first
 if ($b == "smarts") {
 return 1;
 }
 else if ($a == "smarts") {
 return −1;
 }

 return ($a == $b) ? 0 : (($a < $b) ? −1 : 1);
}

$values = array(
 'name' => "Buzz Lightyear",
 'email_address' => "buzz@starcommand.gal",
 'age' => 32,
 'smarts' => "some"
);

if ($_POST['submitted']) {
 $sortType = $_POST['sort_type'];

 if ($sortType == "usort" || $sortType == "uksort" || $sortType == "uasort") {
 $sortType($values, "userSort");
 }
 else {
 $sortType($values);
 }
} ?>
<form action="<?php echo $_SERVER['PHP_SELF']; ?> " method="post">
 <p>
 <input type="radio" name="sort_type"
 value="sort" checked="checked" /> Standard<br />
 <input type="radio" name="sort_type" value="rsort" /> Reverse<br />
 <input type="radio" name="sort_type" value="usort" /> User-defined<br />
 <input type="radio" name="sort_type" value="ksort" /> Key<br />
 <input type="radio" name="sort_type" value="krsort" /> Reverse key<br />
 <input type="radio" name="sort_type"
 value="uksort" /> User-defined key<br />
 <input type="radio" name="sort_type" value="asort" /> Value<br />
 <input type="radio" name="sort_type"
 value="arsort" /> Reverse value<br />
 <input type="radio" name="sort_type"
 value="uasort" /> User-defined value<br />
 </p>

 <p align="center"><input type="submit" value="Sort" name="submitted" /></p>

 <p>Values <?php echo $_POST['submitted'] ? "sorted by {$sortType}" : "unsorted"; 
 ?>:</p>

 <ul>
 <?php foreach ($values as $key => $value) {
 echo "<li><b>{$key}</b>: {$value}</li>";
 } ?>
 </ul>
</form>
```

## 自然顺序排序

PHP 的内置排序函数可以正确排序字符串和数字，但无法正确排序包含数字的字符串。例如，如果你有文件名*ex10.php*、*ex5.php*和*ex1.php*，正常排序函数会以这种顺序重新排列它们：*ex1.php*、*ex10.php*、*ex5.php*。要正确排序包含数字的字符串，请使用`natsort()`和`natcasesort()`函数：

```php
$output = natsort(*`input`*);
$output = natcasesort(*`input`*);
```

## 一次对多个数组进行排序

`array_multisort()`函数一次对多个索引数组进行排序：

```php
array_multisort(*`array1`* [, *`array2`*, ... ]);
```

传递一系列数组和排序顺序（由`SORT_ASC`或`SORT_DESC`常量标识），它重新排序所有数组的元素，分配新索引。这类似于关系数据库上的连接操作。

想象一下你有很多人，每个人有几个数据片段：

```php
$names = array("Tom", "Dick", "Harriet", "Brenda", "Joe");
$ages = array(25, 35, 29, 35, 35);
$zips = array(80522, '02140', 90210, 64141, 80522);
```

每个数组的第一个元素表示一个单独的记录——关于汤姆的所有已知信息。同样，第二个元素构成另一条记录——关于迪克的所有已知信息。`array_multisort()`函数重新排列数组的元素，保留记录。也就是说，如果在排序后`$names`数组中“迪克”排在第一位，其他数组中关于迪克的信息也将排在第一位。（注意，我们需要引用迪克的邮政编码，以防止它被解释为八进制常量。）

下面是如何首先按年龄升序，然后按邮政编码降序排序记录的方法：

```php
array_multisort($ages, SORT_ASC, $zips, SORT_DESC, $names, SORT_ASC);
```

我们需要在函数调用中包含`$names`，以确保迪克的姓名与他的年龄和邮政编码一起。打印出数据将显示排序结果：

```php
for ($i = 0; $i < count($names); $i++) {
 echo "{$names[$i]}, {$ages[$i]}, {$zips[$i]}\n";
}
`Tom``,` `25``,` `80522`
`Harriet``,` `29``,` `90210`
`Joe``,` `35``,` `80522`
`Brenda``,` `35``,` `64141`
`Dick``,` `35``,` `02140`
```

## 颠倒数组

`array_reverse()`函数颠倒数组中元素的内部顺序：

```php
$reversed = array_reverse(*`array`*);
```

数字键从 0 开始重新编号，而字符串索引不受影响。总的来说，最好使用反序排序函数而不是先排序，然后反转数组的顺序。

`array_flip()`函数返回一个数组，反转每个原始元素的键值对的顺序：

```php
$flipped = array_flip(*`array`*);
```

对于数组中的每个元素，其值是有效键的情况下，元素的值变成其键，元素的键变成其值。例如，如果你有一个将用户名映射到主目录的数组，你可以使用`array_flip()`创建一个将主目录映射到用户名的数组：

```php
$u2h = array(
 'gnat' => "/home/staff/nathan",
 'frank' => "/home/action/frank",
 'petermac' => "/home/staff/petermac",
 'ktatroe' => "/home/staff/kevin"
);
$h2u = array_flip($u2h);

$user = $h2u["/home/staff/kevin"]; // $user is now 'ktatroe'
```

其原始值既不是字符串也不是整数的元素会在结果数组中保持不变。新数组允许你根据其值在原始数组中发现键，但这种技术仅在原始数组具有唯一值时有效。

## 随机顺序

要以随机顺序遍历数组中的元素，请使用 `shuffle()` 函数。它将所有现有的键（字符串或数值）替换为从 0 开始的连续整数。

这里是如何随机排列一周中的天的方式：

```php
$weekdays = array("Monday", "Tuesday", "Wednesday", "Thursday", "Friday");
shuffle($weekdays);

print_r($weekdays);

`Array``(`
 `[``0``]` `=>` `Tuesday`
 `[``1``]` `=>` `Thursday`
 `[``2``]` `=>` `Monday`
 `[``3``]` `=>` `Friday`
 `[``4``]` `=>` `Wednesday`
`)`
```

显然，使用 `shuffle()` 后的顺序可能与此处示例输出不同，因为函数的随机性质。除非您有兴趣从数组中获取多个随机元素而不重复任何特定项，否则使用 `rand()` 函数来选择索引更有效。

# 操作整个数组

PHP 中有几个有用的内置函数，用于修改或对数组的所有元素应用操作。你可以计算数组的总和，合并多个数组，找到两个数组之间的差异，等等。

## 计算数组的总和

`array_sum()` 函数将索引或关联数组中的值相加：

```php
$sum = array_sum(*`array`*);
```

例如：

```php
$scores = array(98, 76, 56, 80);
$total = array_sum($scores); // $total = 310
```

## 合并两个数组

`array_merge()` 函数可以智能地合并两个或更多个数组：

```php
$merged = array_merge(*`array1`*, *`array2`* [, *`array` `...`* ])
```

如果一个早期数组中的数值键重复出现，则后来数组中的值将被分配一个新的数值键：

```php
$first = array("hello", "world"); // 0 => "hello", 1 => "world"
$second = array("exit", "here"); // 0 => "exit", 1 => "here"

$merged = array_merge($first, $second);
// $merged = array("hello", "world", "exit", "here")
```

如果一个早期数组中的字符串键重复出现，早期的值将被后来的值替换：

```php
$first = array('bill' => "clinton", 'tony' => "danza");
$second = array('bill' => "gates", 'adam' => "west");

$merged = array_merge($first, $second);
// $merged = array('bill' => "gates", 'tony' => "danza", 'adam' => "west")
```

## 计算两个数组之间的差异

`array_diff()` 函数计算两个或更多个数组之间的差异，返回一个数组，其中包含第一个数组中不在其他数组中的值：

```php
$diff = array_diff(*`array1`*, *`array2`* [, *`array`* ... ]);
```

例如：

```php
$a1 = array("bill", "claire", "ella", "simon", "judy");
$a2 = array("jack", "claire", "toni");
$a3 = array("ella", "simon", "garfunkel");

// find values of $a1 not in $a2 or $a3 $difference = array_diff($a1, $a2, $a3);
print_r($difference);

`Array``(`
 `[``0``]` `=>` `"``bill``"``,`
 `[``4``]` `=>` `"``judy``"`
`);`
```

值是使用严格比较运算符 `===` 进行比较，所以 `1` 和 `"1"` 被视为不同。第一个数组的键被保留，所以在 `$diff` 中 `"bill"` 的键是 `0`，而 `"judy"` 的键是 `4`。

在另一个示例中，以下代码返回两个数组的差异：

```php
$first = array(1, "two", 3);
$second = array("two", "three", "four");

$difference = array_diff($first, $second);
print_r($difference);

`Array``(`
 `[``0``]` `=>` `1`
 `[``2``]` `=>` `3`
`)`
```

## 从数组中筛选元素

要根据其值标识数组的子集，请使用 `array_filter()` 函数：

```php
$filtered = array_filter(*`array`*, *`callback`*);
```

每个 *array* 的值都传递给 *callback* 中命名的函数。返回的数组仅包含原始数组中函数返回 `true` 值的元素。例如：

```php
`function` *`isOdd`* ($element) {
 `return` $element % 2;
}

$numbers = `array`(9, 23, 24, 27);
$odds = *`array_filter`*($numbers, "isOdd");

// $odds is array(0 => 9, 1 => 23, 3 => 27)
```

正如你所看到的，键会被保留。这个函数在关联数组中非常有用。

# 使用数组实现数据类型

数组在几乎每个 PHP 程序中都会出现。除了明显用于存储值的目的外，它们还用于实现各种抽象数据类型。在本节中，我们展示如何使用数组来实现集合和堆栈。

## 集合

数组使您能够实现集合论的基本操作：并集、交集和差集。每个集合由一个数组表示，各种 PHP 函数实现了这些集合操作。集合中的值是数组中的值——键未被使用，但通常由操作保留。

两个集合的*并集*是两个集合中所有元素的集合，去除重复项。使用 `array_merge()` 和 `array_unique()` 函数可以计算并集。下面是如何找到两个数组的并集：

```php
function arrayUnion($a, $b)
{
 $union = array_merge($a, $b); // duplicates may still exist
 $union = array_unique($union);

 return $union;
}

$first = array(1, "two", 3);
$second = array("two", "three", "four");

$union = arrayUnion($first, $second);
print_r($union);

`Array``(`
 `[``0``]` `=>` `1`
 `[``1``]` `=>` `two`
 `[``2``]` `=>` `3`
 `[``4``]` `=>` `three`
 `[``5``]` `=>` `four`
`)`
```

两个集合的*交集*是它们共有的元素集合。PHP 内置的 `array_intersect()` 函数将任意数量的数组作为参数，并返回存在于每个数组中的那些值的数组。如果多个键具有相同的值，则保留第一个具有该值的键。

## 栈

虽然在 PHP 程序中不如其他程序常见，但一个相当常见的数据类型是后进先出（LIFO）栈。我们可以使用一对 PHP 函数 `array_push()` 和 `array_pop()` 来创建栈。`array_push()` 函数与对 `$array[]` 的赋值相同。我们使用 `array_push()` 是因为它强调了我们正在处理栈，并且与 `array_pop()` 的并行性使我们的代码更易于阅读。还有 `array_shift()` 和 `array_unshift()` 函数用于将数组视为队列。

栈对于保持状态特别有用。示例 5-4 提供了一个简单的状态调试器，允许您打印到目前为止调用过的函数列表（即 *堆栈跟踪*）。

##### 示例 5-4\. 状态调试器

```php
$callTrace = array();

function enterFunction($name)
{
 global $callTrace;
 $callTrace[] = $name;

 echo "Entering {$name} (stack is now: " . join(' -> ', $callTrace) . ")<br />";
}

function exitFunction()
{
 echo "Exiting<br />";

 global $callTrace;
 array_pop($callTrace);
}

function first()
{
 enterFunction("first");
 exitFunction();
}

function second()
{
 enterFunction("second");
 first();
 exitFunction();
}

function third()
{
 enterFunction("third");
 second();
 first();
 exitFunction();
}

first();
third();
```

下面是来自 示例 5-4 的输出：

```php
`Entering` `first` `(``stack` `is` `now``:` `first``)`
`Exiting`
`Entering` `third` `(``stack` `is` `now``:` `third``)`
`Entering` `second` `(``stack` `is` `now``:` `third` `->` `second``)`
`Entering` `first` `(``stack` `is` `now``:` `third` `->` `second` `->` `first``)`
`Exiting`
`Exiting`
`Entering` `first` `(``stack` `is` `now``:` `third` `->` `first``)`
`Exiting`
`Exiting`
```

# 实现迭代器接口

使用 `foreach` 结构，你不仅可以迭代数组，还可以迭代实现了 `Iterator` 接口的类的实例（有关对象和接口的更多信息，请参见第六章）。要实现 `Iterator` 接口，必须在类中实现五个方法：

`current()`

返回迭代器当前指向的元素。

`key()`

返回迭代器当前指向的元素的键。

`next()`

将迭代器移动到对象中的下一个元素并返回它。

`rewind()`

将迭代器移动到数组中的第一个元素。

`valid()`

如果迭代器当前指向有效元素，则返回 `true`，否则返回 `false`。

示例 5-5 重新实现了一个包含静态数据数组的简单迭代器类。

##### 示例 5-5\. 迭代器接口

```php
class BasicArray implements Iterator
{
 private $position = 0;
 private $array = ["first", "second", "third"];

 public function __construct()
 {
 $this->position = 0;
 }

 public function rewind()
 {
 $this->position = 0;
 }

 public function current()
 {
 return $this->array[$this->position];
 }

 public function key()
 {
 return $this->position;
 }

 public function next()
 {
 $this->position += 1;
 }

 public function valid()
 {
 return isset($this->array[$this->position]);
 }
}

$basicArray = new BasicArray;

foreach ($basicArray as $value) {
 echo "{$value}\n";
}

foreach ($basicArray as $key => $value) {
 echo "{$key} => {$value}\n";
}

`first`
`second`
`third`

`0` `=>` `first`
`1` `=>` `second`
`2` `=>` `third`
```

当你在一个类上实现`Iterator`接口时，它只允许你使用`foreach`结构遍历该类实例中的元素；它不允许你将这些实例视为数组或作为其他方法的参数。例如，使用内置的`rewind()`函数而不是在`$trie`上调用`rewind()`方法，可以重置指向`$trie`属性的`Iterator`：

```php
class Trie implements Iterator
{
 const POSITION_LEFT = "left";
 const POSITION_THIS = "this";
 const POSITION_RIGHT = "right";

 var $leftNode;
 var $rightNode;

 var $position;

 // implement Iterator methods here...
}

$trie = new Trie();

rewind($trie);
```

可选的 SPL 库提供了各种有用的迭代器，包括文件系统目录、树形和正则表达式匹配迭代器。

# 接下来做什么

最后三章——关于函数、字符串和数组——涵盖了许多基础内容。下一章将在此基础上构建，并将你带入对象和面向对象编程（OOP）的新世界。有人认为 OOP 是更好的编程方式，因为它比过程化编程更加封装和可重用。这场辩论仍在继续，但一旦你进入面向对象的编程方法并理解其好处，你就能对未来的编程方式做出明智的决定。话虽如此，编程世界的总体趋势是尽可能多地使用 OOP。

在继续之前，有一点需要注意：有很多情况下，初学者 OOP 程序员可能会迷失方向，所以确保你真正掌握了 OOP 之后再进行重要或关键任务。
