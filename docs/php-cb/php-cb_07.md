# 第七章：数组

*数组* 是有序映射——将特定值关联到易于识别的键的构造。这些映射是构建简单列表和更复杂的对象集合的有效方式。它们也很容易操作——向数组中添加或删除项是直接且通过多个功能接口支持的。

## 数组类型

PHP 中有两种形式的数组——数字和关联。当您在不明确设置键的情况下定义数组时，PHP 将为数组的每个成员内部分配一个整数索引。数组从 0 开始索引，自动增加 1 步骤。

关联数组的键可以是字符串或整数，但通常使用字符串。字符串键是“查找”存储在数组中特定值的有效方式。

数组在内部实现为哈希表，允许键和值之间进行有效的直接关联。例如：

```php
$colors = [];
$colors['apple']  = 'red';
$colors['pear']   = 'green';
$colors['banana'] = 'yellow';

$numbers = [22, 15, 42, 105];

echo $colors['pear']; // green
echo $numbers[2]; // 42
```

与更简单的哈希表不同，PHP 数组还实现了一个可迭代接口，允许您逐个遍历它们的所有元素。当键是数字时，迭代是相当明显的，但即使是关联数组，元素也有固定的顺序，因为它们存储在内存中。 Recipe 7.3 详细介绍了在这两种类型的数组中对每个元素执行操作的不同方法。

在许多情况下，您可能还会遇到看起来和感觉像数组但实际上不是数组的对象或类。事实上，任何实现了 [`ArrayAccess` 接口](https://oreil.ly/kdN4_) 的对象都可以被用作数组。¹ 这些更高级的实现将数组的可能性推向了超出简单列表和哈希表的极限。

## 语   语法

PHP 支持两种不同的语法来定义数组。那些在 PHP 中工作了一段时间的人会认识到 [`array()`](https://oreil.ly/v75i9) 构造，它允许在运行时字面上定义数组，如下所示：

```php
$movies = array('Fahrenheit 451', 'Without Remorse', 'Black Panther');
```

另一种更简洁的语法是使用方括号来定义数组。前述示例可以重写为以下具有相同行为的形式：

```php
$movies = ['Fahrenheit 451', 'Without Remorse', 'Black Panther'];
```

两种格式都可以用于创建嵌套数组（其中一个数组包含另一个数组），并且可以如下交替使用：

```php
$array = array(1, 2, array(3, 4), [5, 6]);
```

虽然像前面的示例中混合和匹配语法是可能的，但强烈建议在应用程序括号）。

PHP 中的所有数组都将键映射到值。在上述示例中，数组仅指定了值，让 PHP 自动分配键。这些被视为*数值*数组，因为键将是整数，从 0 开始。更复杂的数组，如示例 7-1 中展示的嵌套结构，同时指定了值和键。这是通过使用双字符箭头操作符 (`=>`) 从键映射到值完成的。

##### 示例 7-1\. 带有嵌套值的关联数组

```php
$array = array(
    'a' => 'A',
    'b' => ['b', 'B'],
    'c' => array('c', ['c', 'K'])
);
```

虽然不是语法要求，但许多编码环境和集成开发环境（IDE）会自动对齐多行数组文字中的箭头操作符。这使得代码更易读，也是本书采用的标准。

接下来的示例展示了开发者可以使用数组（包括数值和关联数组）完成 PHP 中常见任务的各种方法。

# 7.1 在数组中关联多个元素与键

## 问题

你想要将多个项目与单个数组键关联。

## 解决方案

让每个数组值都成为独立的数组，例如：

```php
$cars = [
    'fast'     => ['ferrari', 'lamborghini'],
    'slow'     => ['honda', 'toyota'],
    'electric' => ['rivian', 'tesla'],
    'big'      => ['hummer']
];
```

## 讨论

PHP 对数组中值的数据类型没有要求。但是，键必须是字符串或整数。此外，数组中的每个键都必须是唯一的硬性要求。如果尝试为同一键设置多个值，将会覆盖现有数据，如示例 7-2 所示。

##### 示例 7-2\. 通过赋值覆盖数组数据

```php
$basket = [];

$basket['color']    = 'brown';
$basket['size']     = 'large';
$basket['contents'] = 'apple';
$basket['contents'] = 'orange';
$basket['contents'] = 'pineapple';

print_r($basket);

// Array
// (
//    [color] => brown
//    [size] => large
//    [contents] => pineapple
// )
```

由于 PHP 在数组中只允许一个唯一键对应一个值，写入更多数据到该键会覆盖其值，就像重新分配应用程序中变量的值一样。如果需要在一个键中存储多个值，可以使用嵌套数组。

解决方案示例说明了*每个*键都可以指向自己的数组。然而，PHP 不要求每个键都这样做——除了一个键指向多个项目的情况外，其他键可以指向标量。在示例 7-3 中，你将使用嵌套数组存储多个项目，而不是意外地覆盖特定键中存储的单个值。

##### 示例 7-3\. 将数组写入键

```php
$basket = [];

$basket['color']    = 'brown';
$basket['size']     = 'large';
$basket['contents'] = [];
$basket['contents'][] = 'apple';
$basket['contents'][] = 'orange';
$basket['contents'][] = 'pineapple';

print_r($basket);

// Array
// (
//    [color] => brown
//    [size] => large
//    [contents] => Array
//        (
//            [0] => apple
//            [1] => orange
//            [2] => pineapple
//        )
// )

echo $basket['contents'][2]; // pineapple
```

要利用嵌套数组的元素，你需要像处理父数组一样对其进行循环。例如，如果你想打印存储在`$basket`数组中的所有数据，可以使用两个循环，如示例 7-4 所示。

##### 示例 7-4\. 在循环中访问数组数据

```php
foreach ($basket as $key => $value) { ![1](img/1.png)
    if (is_array($value)) { ![2](img/2.png)
        echo "{$key} => " . PHP_EOL;

        foreach ($value as $item) { ![3
            echo "\t{$item}" . PHP_EOL;
        }

        echo ']' . PHP_EOL;
    } else {
        echo "{$key}: $value" . PHP_EOL;
    }
}

// color: brown // size: large // contents => [ //     apple //     orange //     pineapple // ]
```

![1](img/#co_arrays_CO1-1)

父数组是关联的，你需要它的键和值。

![2](img/#co_arrays_CO1-2)

你使用一种逻辑分支处理嵌套数组，另一种处理标量。

![3](img/#co_arrays_CO1-3)

由于你知道嵌套数组是数值型的，可以忽略键，只迭代值。

## 参见

Recipe 7.3 中有更多关于数组迭代的例子。

# 7.2 用数字范围初始化数组

## 问题

您希望构建一个连续整数数组。

## 解决方案

使用`range()`函数如下所示：

```php
$array = range(1, 10);
print_r($array);

// Array
// (
//     [0] => 1
//     [1] => 2
//     [2] => 3
//     [3] => 4
//     [4] => 5
//     [5] => 6
//     [6] => 7
//     [7] => 8
//     [8] => 9
//     [9] => 10
// )
```

## 讨论

PHP 的`range()`函数会自动迭代给定的序列，并根据该序列的定义为键分配一个值。默认情况下，如解决方案示例所示，该函数一次逐步通过序列。但这并不是该函数行为的极限——向函数传递第三个参数将改变其步长。

您可以按如下方式迭代从 2 到 100 的所有偶数整数：

```php
$array = range(2, 100, 2);
```

同样，您可以通过将序列的起始点更改为 1 来迭代从 1 到 100 的所有*奇数*整数。例如：

```php
$array = range(1, 100, 2);
```

`range()`的起始和结束参数（分别是前两个参数）可以是整数、浮点数，甚至字符串。这种灵活性允许您在代码中做一些非常惊人的事情。例如，您可以生成一个浮点数数组，而不是计算自然数（整数）：

```php
$array = range(1, 5, 0.25);
```

当将字符串字符传递给`range()`时，PHP 将开始枚举 ASCII 字符。您可以利用这个功能快速构建代表英语字母表的数组，如示例 7-5 所示。

###### 注意

PHP 将根据它们的十进制表示内部使用任何和所有可打印的 ASCII 字符来完成对`range()`的请求。这是枚举可打印字符的高效方式，但您需要牢记特殊字符如`=`, `?`和`)`在 ASCII 表中的位置，特别是如果您的程序期望数组中的字母数字值。

##### 示例 7-5\. 创建一个英文字母数组

```php
$uppers = range('A', 'Z'); ![1](img/1.png)

$lowers = range('a', 'z'); ![2](img/2.png)

$special = range('!', ')'); ![3](img/3.png)
```

![1](img/#co_arrays_CO2-1)

返回从`A`到`Z`的所有大写字符

![2](img/#co_arrays_CO2-2)

返回从`a`到`z`的所有小写字符

![3](img/#co_arrays_CO2-3)

返回一个特殊字符数组：`[!, ", #, $, %, &, ', (, )]`

## 参见

PHP 文档中关于[`range()`](https://oreil.ly/qH_iW)的介绍。

# 7.3 遍历数组中的项目

## 问题

您希望对数组中的每个元素执行操作。

## 解决方案

对于数值数组，请按以下方式使用`foreach`：

```php
foreach ($array as $value) {
    // Act on each $value
}
```

对于关联数组，请按以下方式使用`foreach()`及其可选键：

```php
foreach ($array as $key => $value) {
    // Act on each $value and/or $key
}
```

## 讨论

PHP 具有*可迭代对象*的概念，并且在内部，数组正是如此。其他数据结构也可以实现可迭代行为，²但*任何*可迭代表达式都可以提供给`foreach`，并将在循环中逐个返回它包含的项。

###### 警告

当退出循环时，PHP 不会隐式地取消设置`foreach`循环中使用的变量。您仍然可以在循环外的程序中显式地引用`$value`中存储的*最后*值！

但要记住的最重要的事情是，`foreach`是一个*语言结构*，而不是一个函数。作为结构，它对给定的表达式进行操作，并在该表达式中的每个项上应用定义的循环。默认情况下，该循环不修改数组的内容。如果要使数组的值可变，则必须通过在变量名前加上`&`字符将它们传递到循环中的引用方式，如下所示：

```php
$array = [1, 2, 3];

foreach ($array as &$value) {
    $value += 1;
}

print_r($array); // 2, 3, 4
```

###### 警告

PHP 8.0 之前的版本支持`each()`函数，该函数会维护数组光标并在前进光标之前返回数组的当前键/值对。此函数在 PHP 7.2 中已被弃用，并在 8.0 版本中完全移除，但您可能会在书籍和在线资源中找到其使用的遗留示例。请将所有`each()`的出现升级为`foreach`的实现，以确保代码的向前兼容性。

使用`for`循环显式地迭代数组的键是替代`foreach`循环的另一种方法。数值数组最简单，因为它们的键已经是从 0 开始的增量整数。迭代数值数组相对简单，如下所示：

```php
$array = ['red', 'green', 'blue'];

$arrayLength = count($array);
for ($i = 0; $i < $array_length; $i++) {
    echo $array[$i] . PHP_EOL;
}
```

###### 提示

虽然可以调用`count()`来直接标识`for`循环的上界，但最好将数组的长度存储在表达式外部。否则，`count()`将在每次迭代循环时重新调用，以检查是否仍在范围内。对于小数组，这没有关系；但随着您开始处理更大的集合，重复调用`count()`会导致性能下降成为问题。

使用`for`循环迭代关联数组略有不同。与直接迭代数组元素不同，您将直接迭代数组的键。然后，使用每个键从数组中提取相应的值，如下所示：

```php
$array = [
    'os'   => 'linux',
    'mfr'  => 'system76',
    'name' => 'thelio',
];

$keys = array_keys($array);
$arrayLength = count($keys);
for ($i = 0; $i < $arrayLength; $i++) {
    $key = $keys[$i];
    $value = $array[$key];

    echo "{$key} => {$value}" . PHP_EOL;
}
```

## 另请参阅

PHP 关于[`foreach`](https://oreil.ly/lmeAe)和[`for`](https://oreil.ly/chSRT)语言结构的文档。

# 7.4 从关联和数字数组中删除元素

## 问题

您想从数组中删除一个或多个元素。

## 解决方案

通过直接目标化其键或数值索引，使用`unset()`删除一个元素：

```php
unset($array['key']);

unset($array[3]);
```

通过将多个键或索引传递到`unset()`中一次删除多个元素，如下所示：

```php
unset($array['first'], $array['second']);

unset($array[3], $array[4], $array[5]);
```

## 讨论

在 PHP 中，`unset()`实际上会销毁包含指定变量的内存引用。在此解决方案的上下文中，该变量是数组的一个元素，因此取消设置它会从数组本身中删除该元素。在关联数组中，这将导致删除指定的键及其表示的值。

在数值数组中，`unset()` 的作用远不止如此。它不仅会移除指定的元素，还会将数值数组有效地转换为具有整数键的关联数组。一方面，这很可能是你一开始期望的行为，正如在示例 7-6 中展示的那样。

##### 示例 7-6\. 在数值数组中取消元素

```php
$array = range('a', 'z');

echo count($array) . PHP_EOL; ![1](img/1.png)
echo $array[12] . PHP_EOL; ![2](img/2.png)
echo $array[25] . PHP_EOL; ![3](img/3.png)

unset($array[22]);
echo count($array) . PHP_EOL; ![4](img/4.png)
echo $array[12] . PHP_EOL; ![5](img/5.png)
echo $array[25] . PHP_EOL; ![6](img/6.png)
```

![1](img/#co_arrays_CO3-1)

默认情况下，数组表示从*a*到*z*的所有英文字母，因此此行打印`26`。

![2](img/#co_arrays_CO3-2)

字母表中第 13 个字母是*m*。（记住，数组从索引`0`开始。）

![3](img/#co_arrays_CO3-3)

字母表中第 26 个字母是*z*。

![4](img/#co_arrays_CO3-4)

元素被移除后，数组的大小减小到了`25`！

![5](img/#co_arrays_CO3-5)

字母表中第 13 个字母仍然是*m*。

![6](img/#co_arrays_CO3-6)

字母表中第 26 个字母仍然是*z*。此外，这个索引仍然有效，因为移除一个元素并不会重新索引数组。

通常可以忽略数值数组的索引，因为它们由 PHP 自动设置。这使得`unset()`将这些索引隐式地转换为数值键的行为有些令人惊讶。对于数值数组，尝试访问大于数组长度的索引会导致错误。然而，一旦你使用了`unset()`并减小了数组的大小，你往往会得到一个具有大于数组大小的数值键的数组，正如在示例 7-6 中所示。

如果您希望在移除元素后返回数值数组的世界，可以重新索引整个数组。PHP 的`array_values()`函数将返回一个新的、数值索引的数组，其中仅包含指定数组的值。例如：

```php
$array = ['first', 'second', 'third', 'fourth']; ![1](img/1.png)

unset($array[2]); ![2](img/2.png)

$array = array_values($array); ![3](img/3.png)
```

![1](img/#co_arrays_CO4-1)

默认数组具有数值索引：`[0 => first, 1 => second, 2 => third, 3 => fourth]`。

![2](img/#co_arrays_CO4-2)

取消元素将其从数组中移除，但索引（键）保持不变：`[0 => first, 1 => second, 3 => fourth]`。

![3](img/#co_arrays_CO4-3)

调用`array_values()`将为您返回一个带有全新、适当增加数值索引的*新*数组：`[0 => first, 1 => second, 2 => fourth]`。

从数组中移除元素的另一个选项是使用`array_splice()`函数。³ 此函数将从数组中移除一部分并用其他内容替换。⁴ 参见示例 7-7，其中`array_splice()`用于用*nothing*替换数组元素，从而将它们移除。

##### 示例 7-7\. 使用`array_splice()`移除数组元素

```php
$celestials = [
    'sun',
    'mercury',
    'venus',
    'earth',
    'mars',
    'asteroid belt',
    'jupiter',
    'saturn',
    'uranus',
    'neptune',
    'pluto',
    'voyagers 1 & 2',
];

array_splice($celestials, 0, 1); ![1](img/1.png)
array_splice($celestials, 4, 1); ![2](img/2.png)
array_splice($celestials, 8); ![3](img/3.png)

print_r($celestials);

// Array // ( //     [0] => mercury //     [1] => venus //     [2] => earth //     [3] => mars //     [4] => jupiter //     [5] => saturn //     [6] => uranus //     [7] => neptune // )
```

![1](img/#co_arrays_CO5-1)

首先，移除太阳，以清理太阳系行星列表。

![2](img/#co_arrays_CO5-2)

一旦太阳被移除，所有物体的索引都会发生变化。你仍然想要从列表中移除小行星带，因此使用它新的移位后的索引。

![3](img/#co_arrays_CO5-3)

最后，通过从冥王星到数组末尾的移除操作来截断数组。

与 `unset()` 不同，由 `array_splice()` 创建的修改后的数组在数字数组中*不会*保留数值索引/键！这可能是在从数组中删除项后避免额外调用 `array_values()` 的一个好方法。这也是在无需显式指定每个元素的情况下，从数字索引数组中删除*连续*元素的有效方法。

## 另请参阅

[`unset()`](https://oreil.ly/-ebRG)、[`array_splice()`](https://oreil.ly/g-M9G) 和 [`array_values()`](https://oreil.ly/9FvTV) 的文档。

# 7.5 改变数组的大小

## 问题

你想要增加或减少数组的大小。

## 解决方案

使用 `array_push()` 在数组末尾添加元素：

```php
$array = ['apple', 'banana', 'coconut'];
array_push($array, 'grape');

print_r($array);

// Array
// (
//     [0] => apple
//     [1] => banana
//     [2] => coconut
//     [3] => grape
// )
```

使用 `array_splice()` 从数组中移除元素：

```php
$array = ['apple', 'banana', 'coconut', 'grape'];
array_splice($array, 1, 2);

print_r($array);

// Array
// (
//     [0] => apple
//     [1] => grape
// )
```

## 讨论

与许多其他语言不同，PHP 不要求你声明数组的大小。数组是动态的，你可以随时添加或删除其中的数据而没有实际的负面影响。

第一个解决方案示例仅在数组末尾添加一个单独的元素。虽然这种方法很简单直接，但并不是最高效的。相反，你可以直接将单个项推入数组中，如下所示：

```php
$array = ['apple', 'banana', 'coconut'];
$array[] = 'grape';

print_r($array);

// Array
// (
//     [0] => apple
//     [1] => banana
//     [2] => coconut
//     [3] => grape
// )
```

前面的示例与解决方案文档中记录的示例之间的关键区别在于函数调用。在 PHP 中，函数调用比语言构造（如赋值操作符）的开销更大。前面的示例略微更高效，但仅在应用程序中多次使用时才是如此。

如果你要在数组末尾添加*多个*项，`array_push()` 函数将更有效。它一次接受并追加多个项，从而避免多次赋值。示例 7-8 展示了这两种方法之间的区别。

##### 示例 7-8\. 使用 `array_push()` 和赋值运算符追加多个元素的比较

```php
$first = ['apple'];
array_push($first, 'banana', 'coconut', 'grape');

$second = ['apple'];
$second[] = 'banana';
$second[] = 'coconut';
$second[] = 'grape';

echo 'The arrays are ' . ($first === $second ? 'equal' : 'different');

// The arrays are equal
```

如果你想要在数组*前面*而不是后面添加元素，可以使用 `array_unshift()` 将指定的项放置在数组的开头，如下所示：

```php
$array = ['grape'];
array_unshift($array, 'apple', 'banana', 'coconut');

print_r($array);

// Array
// (
//     [0] => apple
//     [1] => banana
//     [2] => coconut
//     [3] => grape
// )
```

###### 注意

当使用 `array_unshift()` 在目标数组的开头插入元素时，PHP 会保留元素的传递顺序。第一个参数将成为第一个元素，第二个将成为第二个元素，依此类推，直到达到数组的*原始*第一个元素。

请记住，在 PHP 中，数组没有固定的大小，并且可以以不同的方式轻松操作。所有前面的功能示例（`array_push()`、`array_splice()` 和 `array_unshift()`）在数字数组上表现良好，并且*不会改变*它们的数值索引的顺序或结构。你可以通过直接引用新索引轻松地将元素添加到数字数组的末尾。例如：

```php
$array = ['apple', 'banana', 'coconut'];
$array[3] = 'grape';
```

只要你的代码引用的索引与数组的其余部分连续，前面的例子就能无缝工作。然而，如果你的计数出现偏差，并且在索引中引入了间隙，则你实际上将你的数值数组转换为关联数组，只是键值是数字。

虽然本篇食谱中使用的所有函数都适用于关联数组，但它们主要针对数值键，并且在用于非数值键时会导致奇怪的行为。建议仅在数值数组中使用这些函数，并根据其键直接操作关联数组的大小。

## 参见

[`array_push()`](https://oreil.ly/DhVgq)、[`array_splice()`](https://oreil.ly/eLoTZ)和[`array_unshift()`](https://oreil.ly/BYisR)的文档。

# 7.6 追加一个数组到另一个数组

## 问题

您想将两个数组合并成一个新的数组。

## 解决方案

使用`array_merge()`如下所示：

```php
$first = ['a', 'b', 'c'];
$second = ['x', 'y', 'z'];

$merged = array_merge($first, $second);
```

此外，您还可以利用扩展操作符（`…​`）直接合并数组。而不是调用`array_merge()`，前面的示例可以这样变为：

```php
$merged = [...$first, ...$second];
```

扩展操作符适用于数值数组和关联数组。

## 讨论

PHP 的`array_merge()`函数是将两个数组合并成一个的明显方法。但是，对于数值数组和关联数组，它的行为稍有不同。

###### 警告

任何关于合并数组的讨论都将不可避免地使用“合并”这个术语。请注意，[`array_combine()`](https://oreil.ly/wcM69)本身是 PHP 中的一个函数。然而，它不像本篇食谱中展示的那样将两个数组合并。相反，它通过使用两个指定的数组——第一个用于新数组的*键*，第二个用于新数组的*值*——来创建一个新数组。这是一个有用的函数，但不是您可以用来合并两个数组的东西。

对于数值数组（如解决方案示例中的那些），第二个数组的所有元素都将追加到第一个数组的元素后面。该函数忽略两个数组的索引，并且新产生的数组具有连续的索引（从`0`开始），就像直接构建它一样。

对于关联数组，第二个数组的键（和值）将添加到第一个数组的键（和值）中。如果两个数组具有相同的键，则第二个数组的值将覆盖第一个数组的值。示例 7-9 说明了一个数组中的数据如何覆盖另一个数组的数据。

##### 示例 7-9\. 使用`array_merge()`覆盖关联数组数据

```php
$first = [
    'title'  => 'Practical Handbook',
    'author' => 'Bob Mills',
    'year'   => 2018
];
$second = [
    'year'   => 2023,
    'region' => 'United States'
];

$merged = array_merge($first, $second);
print_r($merged);

// Array
// (
//     [title] => Practical Handbook
//     [author] => Bob Mills
//     [year] => 2023
//     [region] => United States
// )
```

可能存在这样的情况，您希望在合并两个或多个数组时保留重复键中保存的数据。在这种情况下，请使用`array_merge_recursive()`。与前面的示例不同，此函数将创建一个包含重复键中定义的数据的数组，而不是将一个值覆盖为另一个值。示例 7-10 重新编写了前面的示例，以说明这是如何发生的。

##### 示例 7-10\. 带有重复键的数组合并

```php
$first = [
    'title'  => 'Practical Handbook',
    'author' => 'Bob Mills',
    'year'   => 2018
];
$second = [
    'year'   => 2023,
    'region' => 'United States'
];

$merged = array_merge_recursive($first, $second);
print_r($merged);

// Array
// (
//     [title] => Practical Handbook
//     [author] => Bob Mills
//     [year] => Array
//         (
//             [0] => 2018
//             [1] => 2023
//         )
//
//     [region] => United States
// )
```

虽然前面的示例仅合并了两个数组，但你可以使用 `array_merge()` 或 `array_merge_recursive()` 合并任意数量的数组。在开始合并超过两个数组时，请记住这两个函数如何处理重复键，以避免潜在数据丢失。

第三种将两个数组合并为一个的方法是使用字面添加运算符 `+` ：在纸上，这看起来是将两个数组相加。但实际上它是将第二个数组中的任何新键添加到第一个数组的键中。与 `array_merge()` 不同的是，此操作不会覆盖数据。如果第二个数组的键与第一个数组中的任何键重复，那么这些键将被忽略，并且使用第一个数组中的数据。

此运算符还可以*明确*与数组键一起使用，这意味着它不适合于数值数组。两个相同大小的数值数组，如果像关联数组一样处理，将具有完全相同的键，因为它们具有相同的索引。这意味着第二个数组的数据将被完全忽略！

## 参见

[`array_merge()`](https://oreil.ly/s38Xa) 和 [`array_merge_recursive()`](https://oreil.ly/aFQxS) 的文档。

# 7.7 从现有数组片段创建数组

## 问题

您希望选择现有数组的子集并将其独立使用。

## 解决方案

使用 `array_slice()` 来从现有数组中选择一系列元素，如下所示：

```php
$array = range('A', 'Z');
$slice = array_slice($array, 7, 4);

print_r($slice);

// Array
// (
//     [0] => H
//     [1] => I
//     [2] => J
//     [3] => K
// )
```

## 讨论

`array_slice()` 函数根据定义的偏移量（在数组中的位置）和要检索的元素长度，快速从给定的数组中提取连续的项序列。与 `array_splice()` 不同的是，它复制数组中的项序列，而不改变原始数组。

要理解完整的函数签名，以理解这个函数的强大之处非常重要：

```php
array_slice(
    array $array,
    int   $offset,
    ?$int $length = null,
    $bool $preserve_keys = false
): array
```

仅需要前两个参数——目标数组和初始偏移量。如果偏移量为正数（或 `0` ），新序列将从数组的开头该位置开始。如果偏移量为负数，则序列将从数组的末尾向前偏移该数目的位置开始。

###### 注意

数组偏移明确地参考了数组内的*位置*，而不是键或索引。`array_slice()` 函数易于在关联数组上使用，就像在数值数组上一样，因为它使用数组中元素的相对位置来定义一个新序列，并忽略数组的实际键。

当您定义可选的 `$length` 参数时，这定义了新序列中的最大项数。请注意，新序列受原始数组中项数的限制，因此如果长度超出了数组的末尾，您的序列将比预期的短。示例 7-11 展示了这种行为的快速示例。

##### 示例 7-11\. 使用 `array_slice()` 处理一个太短的数组

```php
$array = range('a', 'e');
$newArray = array_slice($array, 4, 100);

print_r($newArray);

// Array
// (
//     [0] => e
// )
```

如果指定的长度为*负数*，则序列将停在目标数组末尾的指定元素之前。如果未指定长度（或为`null`），则序列将包括从原始偏移到目标数组末尾的所有内容。

最后一个参数`$preserve_keys`告诉 PHP 是否重置数组切片的整数索引。默认情况下，PHP 将返回一个重新索引的数组，其整数键从`0`开始。Example 7-12 展示了该函数基于此参数的行为差异。

###### 注意

`array_slice()`函数将始终在关联数组中保留字符串键，而不管`$preserve_keys`的值如何。

##### Example 7-12\. 在`array_slice()`中的键保留行为

```php
$array = range('a', 'e');

$standard = array_slice($array, 1, 2);
print_r($standard);

// Array
// (
//     [0] => b
//     [1] => c
// )

$preserved = array_slice($array, 1, 2, true);
print_r($preserved);

// Array
// (
//     [1] => b
//     [2] => c
// )
```

请记住，PHP 中的数字数组可以被视为具有从`0`开始连续递增的整数键的关联数组。有了这个理解，很容易看出`array_slice()`在具有字符串和整数键的关联数组上的行为方式——它基于位置而不是键，正如 Example 7-13 所示。

##### Example 7-13\. 对具有混合键的数组使用`array_slice()`

```php
$array = ['a' => 'apple', 'b' => 'banana', 25 => 'cola', 'd' => 'donut'];
print_r(array_slice($array, 0, 3));

// Array
// (
//     [a] => apple
//     [b] => banana
//     [0] => cola
// )

print_r(array_slice($array, 0, 3, true));

// Array
// (
//     [a] => apple
//     [b] => banana
//     [25] => cola
// )
```

在 Recipe 7.4 中，您已经了解了用于从数组中删除一系列元素的`array_splice()`。方便地，此函数使用与`array_slice()`类似的方法签名：

```php
 array_splice(
    array &$array,
    int   $offset,
    ?int  $length = null,
    mixed $replacement = []
): array
```

这些函数之间的关键区别在于一个修改源数组，而另一个则不会。您可能会使用`array_slice()`在隔离的较大序列的子集上操作，或者完全将两个序列彻底分开。在任何情况下，这些函数表现出类似的行为和用例。

## 另请参阅

[`array_slice()`](https://oreil.ly/9iBvj)和[`array_splice()`](https://oreil.ly/k-h7n)的文档。

# 7.8 在数组和字符串之间转换

## 问题

您希望将字符串转换为数组或将数组元素组合成字符串。

## 解决方案

使用`str_split()`将字符串转换为数组：

```php
$string = 'To be or not to be';
$array = str_split($string);
```

使用`join()`将数组元素组合成字符串：

```php
$array = ['H', 'e', 'l', 'l', 'o', ' ', 'w', 'o', 'r', 'l', 'd'];
$string = join('', $array);
```

## 讨论

`str_split()`函数是将任何字符的字符串转换为大小相同的块数组的强大方法。默认情况下，它将字符串分解为单字符块，但您也可以轻松地将字符串分解为任意数量的字符。序列中的最后一个块仅保证*最多*达到指定的长度。例如，Example 7-14 试图将字符串分解为五字符块，但请注意最后一个块的长度少于五个字符。

##### Example 7-14\. 使用任意块大小的`str_split()`

```php
$string = 'To be or not to be';
$array = str_split($string, 5);
var_dump($array);

// array(4) {
//   [0]=>
//   string(5) "To be"
//   [1]=>
//   string(5) " or n"
//   [2]=>
//   string(5) "ot to"
//   [3]=>
//   string(3) " be"
// }
```

###### 警告

请记住，`str_split()`适用于字节。当处理多字节编码的字符串时，您需要使用[`mb_​str_​split()`](https://oreil.ly/ocQi1)。

在某些情况下，您可能希望将字符串分割成单独的单词而不是单个字符。PHP 的`explode()`函数允许您指定分隔符来分割内容。这对于将句子分割成其组成单词的数组非常方便，正如示例 7-15 所示。

##### 示例 7-15\. 将字符串拆分为单词数组

```php
$string = 'To be or not to be';
$words = explode(' ', $string);

print_r($words);

// Array
// (
//     [0] => To
//     [1] => be
//     [2] => or
//     [3] => not
//     [4] => to
//     [5] => be
// )
```

###### 注意

虽然`explode()`似乎与`str_split()`功能类似，但它不能使用空分隔符（函数的第一个参数）来分解字符串。如果尝试传递空字符串，则会遇到`ValueError`。如果想要处理字符数组，请坚持使用`str_split()`。

将字符串数组合并成单个字符串需要使用`join()`函数，它本身只是`implode()`的别名。但它比`str_split()`的倒转功能强大得多，因为你可以选择定义一个分隔符，用于在新连接的代码块之间放置。

分隔符是可选的，但 PHP 中`implode()`的长期遗留已导致了以下两个有些不直观的函数签名：

```php
implode(string $separator, array $array): string

implode(array $array): string
```

如果只需将字符数组合并成字符串，可以使用等效的方法，如示例 7-16 所示。

##### 示例 7-16\. 从字符数组创建字符串

```php
$array = ['H', 'e', 'l', 'l', 'o', ' ', 'w', 'o', 'r', 'l', 'd'];

$option1 = implode($array);

$option2 = implode('', $array);

echo 'The two are ' . ($option1 === $option2 ? 'identical' : 'different');

// The two are identical
```

因为您可以明确指定分隔符——用于连接每个文本块的粘合剂，所以`implode()`允许您做的事情几乎没有限制。假设您的数组是一个单词列表而不是字符列表。您可以使用`implode()`将它们连接在一起作为可打印的逗号分隔列表，如下例所示：

```php
$fruit = ['apple', 'orange', 'pear', 'peach'];

echo implode(', ', $fruit);

// apple, orange, pear, peach
```

## 参见

[`implode()`](https://oreil.ly/mpdcI)、[`explode()`](https://oreil.ly/PScj_)和[`str_split()`](https://oreil.ly/2dTMD)的文档。

# 7.9 反转数组

## 问题

您想要反转数组中元素的顺序。

## 解决方法

使用`array_reverse()`如下：

```php
$array = ['five', 'four', 'three', 'two', 'one', 'zero'];

$reversed = array_reverse($array);
```

## 讨论

`array_reverse()`函数创建一个新数组，其中每个元素是输入数组的倒序。默认情况下，此函数不保留源数组的数值键，而是重新为每个元素编制索引。非数值键（在关联数组中）通过此重新索引保持不变；然而，它们的顺序仍然按预期反转。示例 7-17 演示了如何通过`array_reverse()`重新排序关联数组。

##### 示例 7-17\. 反转关联数组

```php
$array = ['a' => 'A', 'b' => 'B', 'c' => 'C'];
$reversed = array_reverse($array);

print_r($reversed);

// Array
// (
//     [c] => C
//     [b] => B
//     [a] => A
// )
```

由于关联数组可能以数值键开始，重新索引行为可能会产生意外结果。幸运的是，可以通过在反转数组时传递一个可选的布尔参数作为第二个参数来禁用此行为。示例 7-18 展示了这种索引行为如何影响这样的数组（以及如何禁用它）。

##### 示例 7-18\. 反转具有数值键的关联数组

```php
$array = ['a' => 'A', 'b' => 'B', 42 => 'C', 'd' => 'D'];
print_r(array_reverse($array)); ![1](img/1.png)

// Array // ( //     [d] => D //     [0] => C //     [b] => B //     [a] => A // ) 
print_r(array_reverse($array, true)); ![2](img/2.png)

// Array // ( //     [d] => D //     [42] => C //     [b] => B //     [a] => A // )
```

![1](img/#co_arrays_CO6-1)

第二个参数的默认值为 `false`，这意味着在数组反转后将不保留数字键。

![2](img/#co_arrays_CO6-2)

将 `true` 作为第二个参数传递仍然允许数组反转，但会在新数组中保留数字键。

## 另请参阅

[`array_reverse()` 的文档](https://oreil.ly/mI5eG)。

# 7.10 对数组进行排序

## 问题

您希望对数组元素进行排序。

## 解决方案

要使用 PHP 中默认的比较规则对项目进行排序，请使用如下所示的 `sort()`：

```php
$states = ['Oregon', 'California', 'Alaska', 'Washington', 'Hawaii'];
sort($states);
```

## 讨论

PHP 的原生排序系统建立在快速排序算法 Quicksort 的基础上，这是一种常见且相对快速的排序算法。默认情况下，它使用 PHP 比较运算符定义的规则来确定数组中每个元素的顺序。⁵ 然而，你可以通过将一个标志作为 `sort()` 的可选第二参数来使用不同的规则进行排序。可用的排序标志在 表 7-1 中描述。

表 7-1\. 排序类型标志

| Flag | 描述 |
| --- | --- |
| `SORT_REGULAR` | 使用默认的比较操作正常比较项目 |
| `SORT_NUMERIC` | 数值比较项目 |
| `SORT_STRING` | 将项目作为字符串进行比较 |
| `SORT_LOCALE_STRING` | 使用当前系统区域设置按字符串比较项目 |
| `SORT_NATURAL` | 使用“自然顺序”比较项目 |
| `SORT_FLAG_CASE` | 结合 `SORT_STRING` 或 `SORT_NATURAL` 使用位或运算符比较字符串时不区分大小写 |

当默认排序比较产生不合理的排序数组时，排序类型标志非常有用。例如，将整数数组按字符串排序会导致排序错误。使用 `SORT_NUMERIC` 标志将确保整数按正确的顺序排序。Example 7-19 展示了这两种排序类型的差异。

##### 示例 7-19\. 使用常规和数值排序类型对整数进行排序

```php
$numbers = [1, 10, 100, 5, 50, 500];
sort($numbers, SORT_STRING);
print_r($numbers);

// Array
// (
//     [0] => 1
//     [1] => 10
//     [2] => 100
//     [3] => 5
//     [4] => 50
//     [5] => 500
// )

sort($numbers, SORT_NUMERIC);
print_r($numbers);

// Array
// (
//     [0] => 1
//     [1] => 5
//     [2] => 10
//     [3] => 50
//     [4] => 100
//     [5] => 500
// )
```

`sort()` 函数忽略数组键和索引，仅按其值对数组元素进行排序。因此，尝试使用 `sort()` 对关联数组进行排序会破坏该数组中的键。如果希望在保留数组键的同时按值排序，可以使用 `asort()`。

要做到这一点，调用 `asort()` 的方式与 `sort()` 完全相同；甚至可以使用与 表 7-1 中定义的相同标志。然而，生成的数组将保留与之前相同的键，即使元素的顺序不同。例如：

```php
$numbers = [1, 10, 100, 5, 50, 500];
asort($numbers, SORT_NUMERIC);
print_r($numbers);

// Array
// (
//     [0] => 1
//     [3] => 5
//     [1] => 10
//     [4] => 50
//     [2] => 100
//     [5] => 500
// )
```

`sort()` 和 `asort()` 都会生成按升序排序的数组。如果要按降序获取数组，有两种选择：

+   将数组按升序排序，然后像 Recipe 7.9 中演示的那样将其反转。

+   利用 `rsort()` 或 `arsort()` 对数字和关联数组进行排序。

为了减少总体代码复杂性，通常更倾向于后一种选项。这些函数的签名与 `sort()` 和 `asort()` 相同，但仅仅颠倒了元素在结果数组中的位置顺序。

## 参见

[`arsort()`](https://oreil.ly/G14ve)、[`asort()`](https://oreil.ly/jkl5w)、[`rsort()`](https://oreil.ly/Z6p49)和[`sort()`](https://oreil.ly/sHWtt)的文档。

# 7.11 基于函数对数组进行排序

## 问题

您想根据用户定义的函数或比较器对数组进行排序。

## 解决方案

使用 `usort()` 和自定义排序回调如下所示：

```php
$bonds = [
    ['first' => 'Sean',    'last' => 'Connery'],
    ['first' => 'Daniel',  'last' => 'Craig'],
    ['first' => 'Pierce',  'last' => 'Brosnan'],
    ['first' => 'Roger',   'last' => 'Moore'],
    ['first' => 'Timothy', 'last' => 'Dalton'],
    ['first' => 'George',  'last' => 'Lazenby'],
];

function sorter(array $a, array $b) {
    return [$a['last'], $a['first']] <=> [$b['last'], $b['first']];
}

usort($bonds, 'sorter');

foreach ($bonds as $bond) {
    echo "{$bond['last']}. {$bond['first']} {$bond['last']}" . PHP_EOL;
}
```

## 讨论

`usort()` 函数利用用户定义的函数作为其排序算法背后的比较操作。您可以将任何可调用对象作为第二个参数传递，并通过此函数检查数组的每个元素以确定其适当的顺序。解决方案示例引用了一个回调函数的名称，但您也可以轻松地传递匿名函数。

[解决方案示例](https://oreil.ly/TuK1L)进一步利用了 PHP 的新太空船操作符，对数组元素进行了复杂的比较。⁶ 在这种特定情况下，您希望首先按姓氏，然后按名字对詹姆斯·邦德的演员进行排序。同样的功能可以用于任何姓名集合。

PHP 中应用自定义排序到日期的一个更强大的例子。日期相对容易排序，因为它们是连续系列的一部分。但可以定义打破这些预期的自定义行为。示例 7-20 尝试根据星期几、年份和月份依次对日期数组进行排序。

##### 示例 7-20\. 应用于日期的用户定义排序

```php
$dates = [
    new DateTime('2022-12-25'),
    new DateTime('2022-04-17'),
    new DateTime('2022-11-24'),
    new DateTime('2023-01-01'),
    new DateTime('2022-07-04'),
    new DateTime('2023-02-14'),
];

function sorter(DateTime $a, DateTime $b) {
    return
        [$a->format('N'), $a->format('Y'), $a->format('j')]
        <=>
        [$b->format('N'), $b->format('Y'), $b->format('j')];
}

usort($dates, 'sorter');

foreach ($dates as $date) {
    echo $date->format('l, F jS, Y') . PHP_EOL;
}

// Monday, July 4th, 2022
// Tuesday, February 14th, 2023
// Thursday, November 24th, 2022
// Sunday, April 17th, 2022
// Sunday, December 25th, 2022
// Sunday, January 1st, 2023
```

像本章讨论的许多其他数组函数一样，`usort()` 忽略数组键/索引，并在其操作中重新索引数组。如果需要保留元素的索引或键关联，请改用 `uasort()`。此函数与 `usort()` 具有相同的签名，但在排序后保持数组键不变。

数组键通常包含有关数组内数据的重要信息，因此在排序操作期间保留它们有时可能至关重要。此外，您可能实际上想按数组的键而不是每个元素的值进行排序。在这种情况下，可以利用 `uksort()`。

`uksort()` 函数将使用您定义的函数按键对数组进行排序。像 `uasort()` 一样，它尊重键并在数组排序后保持它们的位置。

## 参见

[`usort()`](https://oreil.ly/TuK1L)、[`uasort()`](https://oreil.ly/igH5E)和[`uksort()`](https://oreil.ly/MEyff)的文档。

# 7.12 随机化数组元素

## 问题

您希望随机打乱数组元素的顺序，使其完全随机。

## 解决方案

使用 `shuffle()` 如下所示：

```php
$array = range('a', 'e');
shuffle($array);
```

## 讨论

函数`shuffle()`作用于传入的现有数组的引用。它完全忽略数组的键，并随机对元素值排序，以原地更新数组。洗牌后，数组键将从 0 重新索引。

###### 警告

虽然洗牌关联数组不会导致错误，但在操作期间将丢失所有关于键的信息。您应仅对数值数组执行洗牌操作。

内部使用[Mersenne Twister](https://oreil.ly/86yIo)伪随机数生成器来识别数组中每个元素的新顺序。当需要真正的随机性时（如加密或安全场景），此伪随机数生成器并不合适，但它是快速洗牌数组内容的有效方法。

## 参见

[`shuffle()`](https://oreil.ly/AkcpO)文档

# 7.13 对数组的每个元素应用函数

## 问题

您希望通过将函数应用于依次修改数组的每个元素来转换数组

## 解决方案

要在原地修改数组，请使用以下方式调用`array_walk()`：

```php
$values = range(2, 5);

array_walk($values, function(&$value, $key) {
    $value *= $value;
});

print_r($values);

// Array
// (
//     [0] => 4
//     [1] => 9
//     [2] => 16
//     [3] => 25
// )
```

## 讨论

遍历数据集合是 PHP 应用程序的常见需求。例如，您可能希望使用集合定义重复任务。或者，您可能希望对集合中的每个项目执行特定操作，如解决方案示例中所示的平方值。

函数`array_walk()`既能定义要应用的转换，也能将其应用到数组每个元素的值上。回调函数（第二个参数）接受三个参数：数组中元素的值和键，以及可选的`$arg`参数。在初始调用`array_walk()`时定义此最终参数，并在每次回调使用时传递。这是向回调传递常量值的有效方式，正如示例 7-21 所示。

##### 示例 7-21\. 带额外参数调用`array_walk()`

```php
function mutate(&$value, $key, $arg)
{
    $value *= $arg;
}

$values = range(2, 5);

array_walk($values, 'mutate', 10);

print_r($values);

// Array
// (
//     [0] => 20
//     [1] => 30
//     [2] => 40
//     [3] => 50
// )
```

使用`array_walk()`在原地修改数组需要将数组值通过引用传递给回调（请注意参数名前的额外`&`）。此函数也可用于仅遍历数组中的每个元素并执行某些其他函数，而不修改源数组。实际上，这是此函数最常见的用法。

除了遍历数组的每个元素外，还可以通过使用`array_walk_recursive()`遍历嵌套数组中的*叶子*节点。与前面的示例不同，`array_walk_recursive()`将在应用您指定的回调函数之前遍历嵌套数组直到找到非数组元素。示例 7-22 巧妙地展示了递归和非递归函数调用在处理嵌套数组时的区别。具体来说，如果处理嵌套数组，`array_walk()`将抛出错误并完全无法操作。

##### 示例 7-22 比较 `array_walk()` 和 `array_walk_recursive()`

```php
$array = [
    'even' => [2, 4, 6],
    'odd'  => 1,
];

function mutate(&$value, $key, $arg)
{
    $value *= $arg;
}

array_walk_recursive($array, 'mutate', 10);
print_r($array);

// Array
// (
//     [even] => Array
//         (
//             [0] => 20
//             [1] => 40
//             [2] => 60
//         )
//
//     [odd] => 10
// )

array_walk($array, 'mutate', 10);

// PHP Warning: Uncaught TypeError: Unsupported operand types: array * int
```

在许多情况下，您可能希望创建一个经过变异的数组的新副本，同时不会丢失其原始状态的跟踪。在这些情况下，`array_map()`可能比`array_walk()`更安全。与修改源数组不同，`array_map()`使您能够对源数组中的每个元素应用函数，并返回一个全新的数组。其优点在于，您可以进一步使用原始数组和修改后的数组。以下示例利用与解决方案示例相同的逻辑，在*不*更改源数组的情况下：

```php
$values = range(2, 5);

$mutated = array_map(function($value) {
    return $value * $value;
}, $values);

print_r($mutated);

// Array
// (
//     [0] => 4
//     [1] => 9
//     [2] => 16
//     [3] => 25
// )
```

以下是需要注意的这两个数组函数家族之间的一些关键区别：

+   `array_walk()` 期望数组在前，回调函数在后。

+   `array_map()` 期望回调函数在前，数组在后。

+   `array_walk()` 返回一个布尔标志，而`array_map()`返回一个新数组。

+   `array_map()` 不会将键传递给回调函数。

+   `array_map()` 不会将附加参数传递给回调函数。

+   `array_map()` 没有递归形式。

## 另请参见

[`array_map()`的文档](https://oreil.ly/fzU_0)，[`array_walk()`的文档](https://oreil.ly/OTpL4)，以及[`array_walk_recursive()`的文档](https://oreil.ly/qCt7G)。

# 7.14 将数组减少为单个值

## 问题

您想要将一系列值迭代地减少到单个值。

## 解决方案

使用以下回调函数`array_reduce()`：

```php
$values = range(0, 10);

$sum = array_reduce($values, function($carry, $item) {
    return $carry + $item;
}, 0);

// $sum = 55
```

## 讨论

`array_reduce()` 函数遍历数组的每个元素，并修改其内部状态，最终得出一个单一的答案。解决方案示例遍历数字列表的每个元素，并将它们全部添加到初始值`0`，返回所讨论的所有数字的最终总和。

回调函数接受两个参数。第一个是您从上次操作中传递过来的值。第二个是您正在迭代的数组中当前项目的值。无论回调函数返回什么，都将作为下一个数组元素的`$carry`参数传递到回调函数中。

当您开始时，您可以将一个可选的初始值（默认为`null`）作为`$carry`参数传递到回调函数中。如果您正在应用于数组的减少操作是直接的，您通常可以提供更好的初始值，就像解决方案示例中所做的那样。

`array_reduce()` 的最大缺点是它不处理数组键。为了利用数组中的任何键作为减少操作的一部分，您需要定义自己版本的函数。

示例 7-23 展示了你可以通过迭代 `array_keys()` 返回的数组来利用元素的键和值进行减少。你将数组和回调传递到由 `array_reduce()` 处理的闭包中，因此你可以引用该键定义的数组中的元素，并将你的自定义函数应用于它。在主程序中，你可以像减少数值数组一样减少关联数组——除了你的回调中有一个额外的参数包含每个元素的键。

##### 示例 7-23\. `array_reduce()` 的关联替代方法

```php
function array_reduce_assoc(
    array $array,
    callable $callback,
    mixed $initial = null
): mixed
{
    return array_reduce(
        array_keys($array),
        function($carry, $item) use ($array, $callback) {
            return $callback($carry, $array[$item], $item);
        },
        $initial
    );
}

$array = [1 => 10, 2 => 10, 3 => 5];

$sumMultiples = array_reduce_assoc(
    $array,
    function($carry, $item, $key) {
        return $carry + ($item * $key);
    },
    0
);

// $sumMultiples = 45
```

上述代码将返回 `$array` 键的总和乘以它们对应的值——具体来说，`1 * 10 + 2 * 10 + 3 * 5 = 45`。

## 另请参阅

[`array_reduce()` 的文档](https://oreil.ly/iu_XM)。

# 7.15 迭代无限或非常大/昂贵的数组

## 问题

如果你希望迭代一个列表项，该列表项过大以至于无法存储在内存中或者生成速度过慢。

## 解决方案

使用生成器一次生成一个数据块到你的程序中，如下：

```php
function weekday()
{
    static $day = 'Monday';

    while (true) {
        yield $day;

        switch($day) {
            case 'Monday':
                $day = 'Tuesday';
                break;
            case 'Tuesday':
                $day = 'Wednesday';
                break;
            case 'Wednesday':
                $day = 'Thursday';
                break;
            case 'Thursday':
                $day = 'Friday';
                break;
            case 'Friday':
                $day = 'Monday';
                break;
        }
    }
}

$weekdays = weekday();
foreach ($weekdays as $day) {
    echo $day . PHP_EOL;
}
```

## 讨论

生成器是在 PHP 中处理大量数据的内存高效方式。在解决方案示例中，生成器按顺序产生工作日（从周一到周五）作为一个无限序列。无限序列无法适应 PHP 可用的内存，但生成器结构允许你逐步构建它。

不同于实例化一个过大的数组，你生成数据的第一部分并通过 `yield` 关键字将其返回给调用生成器的程序。这样可以冻结生成器的状态，并将执行控制权返回给主应用程序。与一般仅返回数据一次的函数不同，生成器可以多次提供数据，只要它仍然有效。

在解决方案示例中，`yield` 出现在一个无限的 `while` 循环中，因此它将无限枚举工作日。如果你希望生成器退出，可以在结尾使用一个空的 `return` 语句（或者简单地中断循环并隐式返回）。

###### 提示

从生成器返回数据与通常的函数调用不同。你通常使用 `yield` 关键字返回数据，并使用空的 `return` 语句退出生成器。然而，如果生成器确实有最终返回，你必须在生成器对象上调用 `::getReturn()` 来访问该数据。这种额外的方法调用通常看起来有些奇怪，因此除非你的生成器有理由在其典型的 `yield` 操作之外返回数据，否则应尽量避免这样做。

由于生成器可以无限提供数据，你可以通过使用标准的 `foreach` 循环来迭代这些数据。同样，你可以利用有限的 `for` 循环来避免无限序列。以下代码利用了这样的有限循环和解决方案的原始生成器：

```php
$weekdays = weekday();
for ($i = 0; $i < 14; $i++) {
    echo $weekdays->current() . PHP_EOL;
    $weekdays->next();
}
```

虽然生成器被定义为一个函数，但在 PHP 内部被识别为生成器并转换为[`Generator`类](https://oreil.ly/R_geQ)的实例。该类提供了`::current()`和`::next()`方法，允许您逐个访问生成的数据。

应用程序内的控制流在主程序和生成器的`yield`语句之间来回传递。第一次访问生成器时，它会在内部运行到`yield`，然后将控制（及可能的数据）返回给主应用程序。对生成器的后续调用从`yield`关键字之后开始。循环需要强制生成器从头开始以便再次`yield`。

## 另请参见

概述关于[生成器](https://oreil.ly/cR4-V)的内容。

¹ 类继承在第八章中有详细讨论，对象接口则在 Recipe 8.7 中有明确介绍。

² 请查看 Recipe 7.15，了解大型可迭代数据结构的示例。

³ 注意不要混淆`array_splice()`和`array_slice()`。这两个函数有着完全不同的用途，后者在#slicing_arrays 中有详细介绍。

⁴ `array_splice()`函数还将从目标数组中*返回*提取的元素，以便在需要时用于其他操作。有关此行为的进一步讨论，请参阅 Recipe 7.7。

⁵ 详细了解“Comparison Operators”以及比较运算符的用法。

⁶ 长篇解释了太空船操作符在 Recipe 2.4 中，其中还介绍了`usort()`的一个使用示例。
