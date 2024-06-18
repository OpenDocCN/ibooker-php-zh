# 第八章：类和对象

PHP 的早期版本不支持类定义或面向对象。PHP 4 是第一个真正尝试实现对象接口的版本。¹ 直到 PHP 5，开发者才拥有今天所知道和使用的复杂对象接口。

类使用 `class` 关键字定义，后跟类中固有的常量、属性和方法的完整描述。示例 8-1 在 PHP 中引入了一个基本的类结构，包括作用域常量值、属性和可调用方法。

##### 示例 8-1\. 带有属性和方法的基本 PHP 类

```php
class Foo
{
    const SOME_CONSTANT = 42;

    public string $hello = 'hello';

    public function __construct(public string $world = 'world') {}

    public function greet(): void
    {
        echo sprintf('%s %s', $this->hello, $this->world);
    }
}
```

可以使用 `new` 关键字和类的名称实例化对象；实例化看起来有点像函数调用。任何传递到此实例化的参数都会透明地传递到类构造函数（`__construct()` 方法），以定义对象的初始状态。示例 8-2 演示了如何从 示例 8-1 中的类定义实例化类，无论是使用还是不使用默认属性值。

##### 示例 8-2\. 实例化一个基本的 PHP 类

```php
$first = new Foo; ![1](img/1.png)
$second = new Foo('universe'); ![2](img/2.png)

$first->greet(); ![3](img/3.png)
$second->greet(); ![4](img/4.png)

echo Foo::SOME_CONSTANT; ![5](img/5.png)
```

![1](img/#co_classes_and_objects_CO1-1)

在不传递参数实例化对象时，仍会调用构造函数，但会利用其默认参数。如果函数签名中没有提供默认值，将导致错误。

![2](img/#co_classes_and_objects_CO1-2)

在实例化期间传递参数将向构造函数提供该参数。

![3](img/#co_classes_and_objects_CO1-3)

这会通过使用构造函数的默认值打印 `hello world`。

![4](img/#co_classes_and_objects_CO1-4)

这会在控制台打印 `hello universe`。

![5](img/#co_classes_and_objects_CO1-5)

常量直接从类名引用。这会在控制台打印字面值 `42`。

构造函数和属性在第 8.1 和 8.2 节中有详细说明。

## 过程式编程

大多数开发者对 PHP 的第一次接触是通过其更多的过程式接口。示例例程、简单脚本、教程——所有这些通常都利用在全局范围内定义的函数和变量。这并不是坏事，但它限制了您可以生成的程序的灵活性。

过程式编程经常导致无状态应用程序。在函数调用之间，几乎没有能力跟踪之前发生的事情，因此需要通过代码中的某些参考来传递应用程序的状态。再次强调，这并不一定是坏事。唯一的缺点是*复杂*应用程序变得难以分析或理解。

## 面向对象编程

另一种范式是利用对象作为状态的容器。一个常见的实际例子是将对象视为定义*事物*的方式。汽车是一个对象。公共汽车也是。自行车也是。它们是具有特征（如颜色、轮子数量和驱动类型）和功能（如前进、停止和转弯）的离散*事物*。

在编程世界中，这是描述对象最简单的方式之一。在 PHP 中，你首先通过定义`class`来创建对象，以描述该类型的对象将具有的属性（特征）和方法（能力）。

就像现实世界中的事物一样，编程空间中的对象可以继承自更原始的类型描述。汽车、公共汽车和自行车都是车辆的类型，因此它们都可以从特定类型继承。示例 8-3 展示了 PHP 可能如何构建这种对象继承。

##### 示例 8-3\. PHP 中的类抽象

```php
abstract class Vehicle
{
    abstract public function go(): void;

    abstract public function stop(): void;

    abstract public function turn(Direction $direction): void;
}

class Car extends Vehicle
{
    public int $wheels = 4;
    public string $driveType = 'gas';

    public function __construct(public Color $color) {}

    public function go(): void
    {
        // ...
    }

    public function stop(): void
    {
        // ...
    }

    public function turn(Direction $direction): void
    {
        // ...
    }
}

class Bus extends Vehicle
{
    public int $wheels = 4;
    public string $driveType = 'diesel';

    public function __construct(public Color $color) {}

    public function go(): void
    {
        // ...
    }

    public function stop(): void
    {
        // ...
    }

    public function turn(Direction $direction): void
    {
        // ...
    }
}

class Bicycle extends Vehicle
{
    public int $wheels = 2;
    public string $driveType = 'direct';

    public function __construct(public Color $color) {}

    public function go(): void
    {
        // ...
    }

    public function stop(): void
    {
        // ...
    }

    public function turn(Direction $direction): void
    {
        // ...
    }
}
```

实例化对象将创建一个类型化变量，表示初始状态和用于操作该状态的方法。对象*继承*提供了在其他代码中使用一个或多个类型作为替代品的可能性。示例 8-4 说明了由于继承关系，介绍的三种车辆类型可以互换使用。

##### 示例 8-4\. 具有类似继承的类可以互换使用

```php
function commute(Vehicle $vehicle) ![1](img/1.png)
{
    // ... }

function exercise(Bicycle $vehicle) ![2](img/2.png)
{
    // ... }
```

![1](img/#co_classes_and_objects_CO2-1)

三种车辆子类型都可以作为函数调用中`Vehicle`的有效替换。这意味着你可以选择乘坐公共汽车、轿车或自行车，任何选择都同样有效。

![2](img/#co_classes_and_objects_CO2-2)

有时候，您可能需要更精确，并直接使用子类型。除了`Bicycle`，`Bus`、`Car`或任何`Vehicle`类的其他子类都不会被视为有效练习。

类继承在“Recipes”中更深入地介绍，包括 8.6、8.7 和 8.8。

## 多范式语言

PHP 被视为*多范式*语言，因为你可以按照前述的任一范式编写代码。一个有效的 PHP 程序可以是纯过程化的。或者它可以严格依赖于对象定义和自定义类。该程序最终可能会混合使用这两种范式。

开源的 WordPress 内容管理系统（CMS）是互联网上最流行的 PHP 项目之一。² 它被编码为大量利用对象来实现常见抽象，如数据库对象或远程请求。然而，WordPress 也源自长期的过程化编程历史 —— 其代码库的很大一部分仍受到这种风格的深刻影响。WordPress 不仅是 PHP 本身成功的关键例子，也展示了语言对多种范式支持的灵活性。

没有一种固定的方法来组装应用程序。大多数都是多范式支持的混合体，受益于 PHP 对多种编程范式的强力支持。即使在绝大多数程序化应用程序中，您也可能会看到一些对象，因为这是语言标准库[实现其大部分功能的方式](https://oreil.ly/krXrW)。

第六章展示了 PHP 日期系统的功能和面向对象接口的使用。错误处理在第十三章中有更详细的讨论，重点利用内部的`Exception`和`Error`类。在一个否定程序化实现中，`yield`关键字会自动创建`Generator`类的实例。

即使您在程序中从未直接定义类，您很可能会使用 PHP 本身定义的类或程序所需的第三方库定义的类。³

## 可见性

PHP 中类还引入了*可见性*的概念。属性、方法甚至常量都可以使用可选的可见性修饰符进行定义，以改变它们在应用程序其他部分中的访问级别。声明为`public`的内容可以被应用程序中的任何其他类或函数访问。方法和属性可以声明为`protected`，使它们仅对类的实例或从它继承的类的实例可见。最后，`private`声明意味着类的成员只能被类本身的实例访问。

###### 注意

默认情况下，未显式声明为私有或受保护的内容默认为公共，因此您可能会看到一些开发人员跳过声明成员可见性的步骤。

虽然成员可见性可以通过反射直接覆盖，⁴ 但通常通过明确声明类的接口的哪些部分可供其他代码元素使用，是一个明智的方式。 示例 8-5 演示了如何利用每种可见性修饰符来构建复杂应用程序。

##### 示例 8-5\. 类成员可见性概述

```php
class A
{
    public    string $name  = 'Bob';
    public    string $city  = 'Portland';
    protected int    $year  = 2023;
    private   float  $value = 42.9;

    function hello(): string
    {
        return 'hello';
    }

    public function world(): string
    {
        return 'world';
    }

    protected function universe(): string
    {
        return 'universe';
    }

    private function abyss(): string
    {
        return 'the void';
    }
}

class B extends A
{
    public function getName(): string
    {
        return $this->name;
    }

    public function getCity(): string
    {
        return $this->city;
    }

    public function getYear(): int
    {
        return $this->year;
    }

    public function getValue(): float
    {
        return $this->value;
    }
}

$first = new B;
echo $first->getName() . PHP_EOL; ![1](img/1.png)
echo $first->getCity() . PHP_EOL; ![2](img/2.png)
echo $first->getYear() . PHP_EOL; ![3](img/3.png)
echo $first->getValue() . PHP_EOL; ![4](img/4.png)

$second = new A;
echo $second->hello() . PHP_EOL; ![5](img/5.png)
echo $second->world() . PHP_EOL; ![6](img/6.png)
echo $second->universe() . PHP_EOL; ![7](img/7.png)
echo $second->abyss() . PHP_EOL; ![8](img/8.png)
```

![1](img/#co_classes_and_objects_CO3-1)

打印`Bob`。

![2](img/#co_classes_and_objects_CO3-2)

打印`Portland`。

![3](img/#co_classes_and_objects_CO3-3)

打印`2023`。

![4](img/#co_classes_and_objects_CO3-4)

返回一个`Warning`，因为`::$value`属性是私有且不可访问的。

![5](img/#co_classes_and_objects_CO3-5)

打印`hello`。

![6](img/#co_classes_and_objects_CO3-6)

打印`world`。

![7](img/#co_classes_and_objects_CO3-7)

抛出一个`Error`，因为`::universe()`方法是受保护的且在类实例外部不可访问。

![8](img/#co_classes_and_objects_CO3-8)

由于前一行抛出了错误，这一行甚至不会执行。如果前一行没有抛出错误，则这一行会抛出错误，因为 `::abyss()` 方法是私有的，无法在类实例外部访问。

下面的示例进一步说明了前述概念，并涵盖了 PHP 中对象的一些最常见用例和实现。

# 8.1 从自定义类实例化对象

## 问题

你希望定义一个自定义类并从中创建一个新的对象实例。

## 解决方案

使用 `class` 关键字定义类及其属性和方法，然后使用 `new` 创建实例，如下所示：

```php
class Pet
{
    public string $name;
    public string $species;
    public int $happiness = 0;

    public function __construct(string $name, string $species)
    {
        $this->name = $name;
        $this->species = $species;
    }

    public function pet()
    {
        $this->happiness += 1;
    }
}

$dog = new Pet('Fido', 'golden retriever');
$dog->pet();
```

## 讨论

解决方案示例展示了对象的几个关键特征：

+   对象可以具有定义对象内部状态的属性。

+   这些对象可以具有特定的可见性。在解决方案示例中，对象是 `public` 的，这意味着可以被应用程序中的任何代码访问。

+   魔术 `::__construct()` 方法只能在首次实例化对象时接受参数。这些参数可用于定义对象的初始状态。

+   方法可以具有与对象属性类似的可见性。

自 PHP 5 开始，这种特定版本的类定义是许多开发者使用的默认方式，当时首次引入了真正的面向对象基元。然而，示例 8-6 展示了一种更新且更简单的定义简单对象的方式。与独立声明并直接赋值对象状态的属性不同，PHP 8（及更高版本）允许在构造函数中定义所有内容。

##### 示例 8-6\. PHP 8 中的构造函数提升

```php
class Pet
{
    public int $happiness = 0;

    public function __construct(
        public string $name,
        public string $species
    ) {}

    public function pet()
    {
        $this->happiness += 1;
    }
}

$dog = new Pet('Fido', 'golden retriever');
$dog->pet();
```

解决方案示例和 示例 8-6 在功能上是等效的，将导致在运行时创建具有相同内部结构的对象。然而，PHP 能够将构造函数参数提升为对象属性，大大减少了在定义类时需要输入的重复代码量。

每个构造函数参数也允许与对象属性相同的可见性（`public`/`protected`/`private`）。简写语法意味着您不需要在实例化对象时先声明属性，然后定义参数，然后将参数映射到这些属性。

## 参见

[类和对象的文档](https://oreil.ly/TfrNb) 和 [构造函数提升的原始 RFC](https://oreil.ly/nzD0s) 的文档。

# 8.2 构造对象以定义默认值

## 问题

你希望为对象的属性定义默认值。

## 解决方案

为构造函数的参数定义默认值如下：

```php
class Example
{
    public function __construct(
        public string $someString = 'default',
        public int    $someNumber = 5
    ) {}
}

$first = new Example;
$second = new Example('overridden');
$third = new Example('hitchhiker', 42);
$fourth = new Example(someNumber: 10);
```

## 讨论

类定义中的构造函数行为与 PHP 中的任何其他函数基本相同，除了它不返回值。你可以定义默认参数，类似于标准函数的方式。你甚至可以引用构造函数参数的名称，在函数签名中接受一些参数的默认值，同时在后续定义其他参数时。

解决方案示例通过使用构造函数提升明确定义了类属性，以简洁为目标，但旧式冗长的构造函数定义同样有效，如下所示：

```php
class Example
{
    public string $someString;
    public int $someNumber;

    public function __construct(
        string $someString = 'default',
        int    $someNumber = 5
    )
    {
        $this->someString = $someString;
        $this->someNumber = $someNumber;
    }
}
```

同样地，如果*不*使用构造函数提升，可以在定义时直接给对象属性赋默认值。这样做时，通常会在构造函数中省略这些参数，并在程序的其他地方进行操作，如下例所示：

```php
class Example
{
    public string $someString = 'default';
    public int $someNumber = 5;
}

$test = new Example;
$test->someString = 'overridden';
$test->someNumber = 42;
```

###### 警告

正如将在 Recipe 8.3 中讨论的那样，你不能直接使用默认值初始化`readonly`类属性。这与类常量等效，因此语法不允许。

## 参见

Recipe 3.2 详细说明了默认函数参数，Recipe 3.3 则介绍了命名函数参数，以及关于[构造函数和析构函数](https://oreil.ly/WJvYY)的文档。

# 8.3 在类中定义只读属性

## 问题

你希望以某种方式定义类，以便在对象存在后无法更改已定义的属性。

## 解决方案

在已定义类型的属性上使用`readonly`关键字：

```php
class Book
{
    public readonly string $title;

    public function __construct(string $title)
    {
        $this->title = $title;
    }
}

$book = new Book('PHP Cookbook');
```

如果使用构造函数提升，请将关键字与属性类型一起放在构造函数中：

```php
class Book
{
    public function __construct(public readonly string $title) {}
}

$book = new Book('PHP Cookbook');
```

## 讨论

`readonly`关键字在 PHP 8.1 中引入，旨在减少原本需要更多冗长变通方法来实现相同功能的需求。使用此关键字，属性只能在对象实例化时初始化*一次*。

###### 注意

只读属性不能有默认值。这将使它们在功能上等同于类常量，这已经存在，因此该功能不可用，语法不支持。然而，提升的构造函数属性可以利用参数定义中的默认值，因为这些值在运行时进行评估。

该关键字仅对已定义类型的属性有效。在 PHP 中，类型通常是可选的（除非使用严格类型检查⁷），以增强灵活性，因此可能*不能*将类的属性设置为一个或另一个类型。在这些情况下，请使用`mixed`类型，以便可以设置一个只读属性而不受其他类型约束。

###### 注意

此刻，只读声明*不*支持静态属性。

由于只读属性只能实例化一次，因此不能由后续代码取消或修改。在示例 8-7 中的所有代码将导致抛出`Error`异常。

##### 示例 8-7\. 修改只读属性的错误尝试

```php
class Example
{
    public readonly string $prop;
}

class Second
{
    public function __construct(public readonly int $count = 0) {}
}

$first = new Example; ![1](img/1.png)
$first->prop = 'test'; ![2](img/2.png)

$test = new Second;
$test->count += 1; ![3](img/3.png)
$test->count++; ![4](img/4.png)
++$test->count; ![5](img/5.png)
unset($test->count); ![6](img/6.png)
```

![1](img/#co_classes_and_objects_CO4-1)

`Example` 对象将具有未初始化的 `::$prop` 属性，无法访问（在初始化之前访问属性会抛出 `Error` 异常）。

![2](img/#co_classes_and_objects_CO4-2)

由于对象已经实例化，尝试写入只读属性会抛出 `Error`。

![3](img/#co_classes_and_objects_CO4-3)

`::$count` 属性是只读的，因此您无法为其分配新值，会导致 `Error`。

![4](img/#co_classes_and_objects_CO4-4)

由于 `::$count` 属性是只读的，因此无法直接对其进行增量操作。

![5](img/#co_classes_and_objects_CO4-5)

不能通过只读属性进行增量递增或递减。

![6](img/#co_classes_and_objects_CO4-6)

不能取消只读属性。

类中的属性可能是其他类自身。在这些情况下，属性上的只读声明意味着该属性无法被重写或取消，但不影响子类的属性。例如：

```php
class First
{
    public function __construct(public readonly Second $inner) {}
}

class Second
{
    public function __construct(public int $counter = 0) {}
}

$test = new First(new Second);
$test->inner->counter += 1; ![1](img/1.png)

$test->inner = new Second; ![2](img/2.png)
```

![1](img/#co_classes_and_objects_CO5-1)

由于 `::$counter` 属性本身未声明为只读，因此内部计数器的增量将成功。

![2](img/#co_classes_and_objects_CO5-2)

`::$inner` 属性是只读的，不能被覆盖。尝试这样做将导致 `Error` 异常。

## 参见

[只读属性文档](https://oreil.ly/P-AwN)。

# 8.4 拆解对象以在不再需要对象时进行清理

## 问题

类定义封装了一个昂贵的资源，在对象超出范围时必须小心清理。

## 解决方案

定义一个类析构函数，在对象从内存中移除后进行清理：

```php
class DatabaseHandler
{
    // ...

    public function __destruct()
    {
        dbo_close($this->dbh);
    }
}
```

## 讨论

当对象超出范围时，PHP 将自动回收该对象用于表示的任何内存或其他资源。然而，在对象超出范围时，可能希望强制执行特定操作。例如释放数据库句柄，就像解决方案示例中那样。或者显式记录事件到文件中。或者从系统中*删除*临时文件，正如示例 8-8 所示。

##### 示例 8-8\. 在析构函数中删除临时文件

```php
class TempLogger
{
    private string $filename;
    private mixed  $handle;

    public function __construct(string $name)
    {
        $this->filename = sprintf('tmp_%s_%s.tmp', $name, time());
        $this->handle = fopen($this->filename, 'w');
    }

    public function writeLog(string $line): void
    {
        fwrite($this->handle, $line . PHP_EOL);
    }

    public function getLogs(): Generator
    {
        $handle = fopen($this->filename, 'r');
        while(($buffer = fgets($handle, 4096)) !== false) {
            yield $buffer;
        }
        fclose($handle);
    }

    public function __destruct()
    {
        fclose($this->handle);
        unlink($this->filename);
    }
}

$logger = new TempLogger('test'); ![1](img/1.png)
$logger->writeLog('This is a test'); ![2](img/2.png)
$logger->writeLog('And another');

foreach($logger->getLogs() as $log) { ![3](img/3.png)
    echo $log;
}

unset($logger); ![4](img/4.png)
```

![1](img/#co_classes_and_objects_CO6-1)

对象将自动在当前目录中创建一个文件，其名称类似于 *tmp_test_1650837172.tmp*。

![2](img/#co_classes_and_objects_CO6-2)

每个新的日志条目都作为临时日志文件中的新行写入。

![3](img/#co_classes_and_objects_CO6-3)

访问日志将创建文件的第二个句柄，但用于读取。对象通过生成器公开此句柄，该生成器枚举文件中的每一行。

![4](img/#co_classes_and_objects_CO6-4)

当记录器从作用域中删除（或显式取消引用）时，析构函数将关闭打开的文件句柄，并自动删除文件。

这个更复杂的示例展示了析构函数的编写方式以及在何时调用它。当 PHP 在对象超出范围时会查找 `::__destruct()` 方法，并在那时调用它。此析构函数通过调用 `unset()` 明确取消引用对象来删除对象。您也可以通过将引用对象的变量设置为 `null` 来达到同样的结果。

与对象构造函数不同，析构函数不接受任何参数。如果您的对象在清理自身之后需要处理任何外部状态，请确保该状态通过对象本身的属性引用。否则，您将无法访问该信息。

## 另请参阅

[构造函数和析构函数](https://oreil.ly/fJMGM)的文档。

# 8.5 使用魔术方法提供动态属性

## 问题

您希望定义一个自定义类，而不预先定义其支持的属性。

## 解决方案

使用魔术获取器和设置器来处理动态定义的属性如下：

```php
class Magical
{
    private array $_data = [];

    public function __get(string $name): mixed
    {
        if (isset($this->_data[$name])) {
            return $this->_data[$name];
        }

        throw new Error(sprintf('Property `%s` is not defined', $name));
    }

    public function __set(string $name, mixed $value)
    {
        $this->_data[$name] = $value;
    }
}

$first = new Magical;
$first->custom = 'hello';
$first->another = 'world';

echo $first->custom . ' ' . $first->another . PHP_EOL;

echo $first->unknown; // Error
```

## 讨论

当您引用不存在的对象属性时，PHP 将回退到一组*魔术方法*来填补实现中的空白。当尝试引用属性时，自动使用 getter，而在向属性分配值时使用相应的 setter。

###### 注意

通过魔术方法进行属性重载仅适用于已实例化的对象。它不适用于静态类定义。

在内部，您可以控制获取和设置数据的行为。解决方案示例将其数据存储在私有关联数组中。您可以通过完全实现处理`isset()`和`unset()`的魔术方法来进一步完善此示例。示例 8-9 演示了如何使用魔术方法完全复制标准类定义，而无需预先声明所有属性。

##### Example 8-9\. 完整对象定义与魔术方法

```php
class Basic
{
    public function __construct(
        public string $word,
        public int $number
    ) {}
}

class Magic
{
    private array $_data = [];

    public function __get(string $name): mixed
    {
        if (isset($this->_data[$name])) {
            return $this->_data[$name];
        }

        throw new Error(sprintf('Property `%s` is not defined', $name));
    }

    public function __set(string $name, mixed $value)
    {
        $this->_data[$name] = $value;
    }

    public function __isset(string $name): bool
    {
        return array_key_exists($name, $this->_data);
    }

    public function __unset(string $name): void
    {
        unset($this->_data[$name]);
    }
}

$basic = new Basic('test', 22);

$magic = new Magic;
$magic->word = 'test';
$magic->number = 22;
```

在 示例 8-9 中，如果仅当 `Magic` 实例上使用的动态属性是 `Basic` 已定义的属性时，这两个对象功能上是等价的。这种动态特性使得这种方法非常有价值，即使类定义非常冗长。您可能选择在实现魔术方法的类中封装远程 API，以便以面向对象的方式向应用程序公开该 API 的数据。

## 另请参阅

[魔术方法](https://oreil.ly/1ZtlE)的文档。

# 8.6 扩展类以定义额外功能

## 问题

您希望定义一个类来为现有类定义添加功能。

## 解决方案

使用 `extends` 关键字定义额外方法或覆盖现有功能如下：

```php
class A
{
    public function hello(): string
    {
        return 'hello';
    }
}

class B extends A
{
    public function world(): string
    {
        return 'world';
    }
}

$instance = new B();
echo "{$instance->hello()} {$instance->world()}";
```

## 讨论

对象继承是任何高级语言的常见概念；它是在其他、通常更简单的对象定义之上构建新对象的一种方式。解决方案示例展示了一个类如何从父类*继承*方法定义，这是 PHP 继承模型的核心功能。

###### 警告

PHP 不支持从多个父类继承。为了从多个来源引入代码实现，PHP 使用*特性*，这在 配方 8.13 中有所涉及。

实际上，子类会继承其父类（扩展的类）的每个公共和受保护方法、属性和常量。私有方法、属性和常量*永远*不会被子类继承。⁸

子类也可以覆盖其父类对特定方法的实现。在实践中，您可能会这样做来改变特定方法的内部逻辑，但子类暴露的方法签名必须与父类定义的相匹配。示例 8-10 展示了一个子类如何覆盖其父类方法的实现。

##### 示例 8-10\. 覆盖父方法实现

```php
class A
{
    public function greet(string $name): string
    {
        return 'Good morning, ' . $name;
    }
}

class B extends A
{
    public function greet(string $name): string
    {
        return 'Howdy, ' . $name;
    }
}

$first = new A();
echo $first->greet('Alice'); ![1](img/1.png)

$second = new B();
echo $second->greet('Bob'); ![2](img/2.png)
```

![1](img/#co_classes_and_objects_CO7-1)

打印 `Good morning, Alice`

![2](img/#co_classes_and_objects_CO7-2)

打印 `Howdy, Bob`

覆盖的子方法并不完全失去父类实现的意义。在类内部，您可以使用 `$this` 变量引用对象的特定实例。同样，您可以使用 `parent` 关键字引用函数的父类实现。例如：

```php
class A
{
    public function hello(): string
    {
        return 'hello';
    }
}

class B extends A
{
    public function hello(): string
    {
        return parent::hello() . ' world';
    }
}

$instance = new B();
echo $instance->hello();
```

## 参见

PHP 的 [对象继承模型](https://oreil.ly/nsAM3) 的文档和讨论。

# 8.7 强制类表现特定行为

## 问题

您希望在一个类上定义方法，而将实际的方法实现留给其他人完成。

## 解决方案

定义对象接口并在应用程序中利用该接口如下：

```php
interface ArtifactRepository
{
    public function create(Artifact $artifact): bool;
    public function get(int $artifactId): ?Artifact;
    public function getAll(): array;
    public function update(Artifact $artifact): bool;
    public function delete(int $artifactId): bool;
}

class Museum
{
    public function __construct(
        protected ArtifactRepository $repository
    ) {}

    public function enumerateArtifacts(): Generator
    {
        foreach($this->repository->getAll() as $artifact) {
            yield $artifact;
        }
    }
}
```

## 讨论

接口看起来类似于类定义，但它只定义了特定方法的*签名*，而不是它们的实现。然而，接口确实定义了可以在应用程序中其他地方使用的类型——只要一个类直接实现给定接口，该类的实例就可以像接口本身的类型一样使用。

###### 警告

有几种情况下，您可能会有两个类实现了相同的方法并向应用程序公开了相同的签名。但是，除非这些类显式地实现了相同的接口（通过`implements`关键字作为证据），否则它们不能在严格类型的应用程序中互换使用。

实现必须使用`implements`关键字告诉 PHP 编译器发生了什么。解决方案示例说明了如何定义一个接口以及代码的另一部分如何利用该接口。Example 8-11 演示了如何使用内存数组实现`ArtifactRepository`接口。

##### Example 8-11\. 显式接口实现

```php
class MemoryRepository implements ArtifactRepository
{
    private array $_collection = [];

    private function nextKey(): int
    {
        $keys = array_keys($this->_collection);
        $max = array_reduce($keys, function($c, $i) {
            return max($c, $i);
        }, 0);

        return $max + 1;
    }

    public function create(Artifact $artifact): bool
    {
        if ($artifact->id === null) {
            $artifact->id = $this->nextKey();
        }

        if (array_key_exists($artifact->id, $this->_collection)) {
            return false;
        }

        $this->_collection[$artifact->id] = $artifact;
        return true;
    }
    public function get(int $artifactId): ?Artifact
    {
        return $this->_collection[$artifactId] ?? null;
    }
    public function getAll(): array
    {
        return array_values($this->_collection);
    }
    public function update(Artifact $artifact): bool
    {
        if (array_key_exists($artifact->id, $this->_collection)) {
            $this->_collection[$artifact->id] = $artifact;
            return true;
        }

        return false;
    }
    public function delete(int $artifactId): bool
    {
        if (array_key_exists($artifactId, $this->_collection)) {
            unset($this->_collection[$artifactId]);
            return true;
        }

        return false;
    }
}
```

在你的应用程序中，任何方法都可以通过使用接口本身在参数上声明类型。解决方案示例的`Museum`类将`ArtifactRepository`的具体实现作为唯一参数。然后，这个类可以操作，知道仓库暴露的 API 将是什么样子。代码并不关心 *每个* 方法是如何实现的，只关心它是否与接口定义的签名完全匹配。

类定义可以同时实现多个不同的接口。这允许一个复杂的对象在不同的代码片段中被用于不同的情境。请注意，如果两个或更多接口定义了相同的方法名，则它们定义的签名必须完全相同，正如 Example 8-12 所示。

##### Example 8-12\. 一次实现多个接口

```php
interface A
{
    public function foo(): int;
}

interface B
{
    public function foo(): int;
}

interface C
{
    public function foo(): string;
}

class First implements A, B
{
    public function foo(): int ![1](img/1.png)
    {
        return 1;
    }
}

class Second implements A, C
{
    public function foo(): int|string ![2](img/2.png)
    {
        return 'nope';
    }
}
```

![1](img/#co_classes_and_objects_CO8-1)

由于`A`和`B`定义了相同的方法签名，因此此实现是有效的。

![2](img/#co_classes_and_objects_CO8-2)

由于`A`和`C`定义了不同的返回类型，即使使用联合类型也无法定义一个同时实现这两个接口的类。试图这样做会导致致命错误。

还要记住，接口看起来有点像类，因此像类一样，它们也可以被扩展。⁹ 这通过相同的`extends`关键字完成，结果是一个由两个或更多接口组成的接口，如 Example 8-13 所示。

##### Example 8-13\. 复合接口

```php
interface A ![1](img/1.png)
{
    public function foo(): void;
}

interface B extends A ![2](img/2.png)
{
    public function bar(): void;
}

class C implements B
{
    public function foo(): void
    {
        // ... actual implementation
    }

    public function bar(): void
    {
        // ... actual implementation
    }
}
```

![1](img/#co_classes_and_objects_CO9-1)

任何实现`A`的类必须定义`foo()`方法。

![2](img/#co_classes_and_objects_CO9-2)

任何实现`B`的类必须从`A`中同时实现`bar()`和`foo()`方法。

## 参见

有关[对象接口](https://oreil.ly/A8hkg)的文档。

# 8.8 创建抽象基类

## 问题

您想让一个类实现特定的接口，但也想定义一些其他特定的功能。

## 解决方案

而不是实现一个接口，定义一个抽象基类可以扩展如下：

```php
abstract class Base
{
    abstract public function getData(): string;

    public function printData(): void
    {
        echo $this->getData();
    }
}

class Concrete extends Base
{
    public function getData(): string
    {
        return bin2hex(random_bytes(16));
    }
}

$instance = new Concrete;
$instance->printData(); ![1](img/1.png)
```

![1](img/#co_classes_and_objects_CO10-1)

打印类似于`6ec2aff42d5904e0ccef15536d8548dc`的内容。

## 讨论

抽象类看起来有点像接口和常规类定义的结合体。它具有一些未实现的方法和与之相对应的具体实现。与接口一样，您不能直接实例化抽象类——您必须首先扩展它并实现其定义的任何抽象方法。但是，与类一样，在子类实现中，您将自动访问基类的任何公共或受保护成员。¹⁰

接口和抽象类之间的一个关键区别在于后者可以将属性和方法定义捆绑在一起。抽象类实际上是仅部分实现的类。接口不能有属性——它仅定义了任何实现对象必须遵循的功能接口。

另一个区别是您可以同时实现多个接口，但一次只能扩展一个类。这一限制本身有助于确定何时使用抽象基类而不是接口——但您也可以混合使用两者！

抽象类也可以定义*私有*成员（这些成员不会被任何子类继承），否则这些成员将通过可访问的方法进行操作，如下例所示：

```php
abstract class A
{
    private string $data = 'this is a secret'; ![1](img/1.png)

    abstract public function viewData(): void;

    public function getData(): string
    {
        return $this->data; ![2](img/2.png)
    }
}

class B extends A
{
    public function viewData(): void
    {
        echo $this->getData() . PHP_EOL; ![3](img/3.png)
    }
}

$instance = new B();
$instance->viewData(); ![4](img/4.png)
```

![1](img/#co_classes_and_objects_CO11-1)

通过使您的数据私有，它只能在`A`的上下文中访问。

![2](img/#co_classes_and_objects_CO11-2)

因为`::getData()`是由`A`定义的，所以`$data`属性仍然是可访问的。

![3](img/#co_classes_and_objects_CO11-3)

虽然`::viewData()`在`B`的作用域中定义，但它正在访问`A`的公共方法。`B`中的任何代码都无法直接访问`A`的私有成员。

![4](img/#co_classes_and_objects_CO11-4)

这将在控制台打印`this is a secret`。

## 参见

[类抽象](https://oreil.ly/FMkcT)的文档和讨论。

# 8.9 防止类和方法的更改

## 问题

你希望阻止任何人修改你的类的实现或者用子类扩展它。

## 解决方案

使用`final`关键字来指示一个类不可扩展，如下所示：

```php
final class Immutable
{
    // Class definition
}
```

或者使用`final`关键字标记特定方法为不可更改，如下所示：

```php
class Mutable
{
    final public function fixed(): void
    {
        // Method definition
    }
}
```

## 讨论

`final`关键字是显式*阻止*对象扩展的一种方式，类似于前两个示例中讨论的机制。在想要确保方法的特定实现或整个类在整个代码库中被使用时非常有用。

将方法标记为`final`意味着任何类扩展都无法覆盖该方法的实现。以下示例将由于`Child`类尝试覆盖`Base`类中的`final`方法而抛出致命错误：

```php
class Base
{
    public function safe()
    {
        echo 'safe() inside Base class' . PHP_EOL;
    }

    final public function unsafe()
    {
        echo 'unsafe() inside Base class' . PHP_EOL;
    }
}

class Child extends Base
{
    public function safe()
    {
        echo 'safe() inside Child class' . PHP_EOL;
    }

    public function unsafe()
    {
        echo 'unsafe() inside Child class' . PHP_EOL;
    }
}
```

在前面的例子中，如果仅仅从子类中省略了`unsafe()`方法的定义，代码将按预期执行。但是，如果你想要阻止任何类扩展基类，你可以在类定义本身添加`final`关键字，如下所示：

```php
final class Base
{
    public function safe()
    {
        echo 'safe() inside Base class' . PHP_EOL;
    }

    public function unsafe()
    {
        echo 'unsafe() inside Base class' . PHP_EOL;
    }
}
```

在你的代码中，唯一需要利用`final`的时机是当重写特定方法或类实现会破坏你的应用程序时。这在实践中有些罕见，但在创建灵活接口时非常有用。一个具体的例子是，当你的应用程序引入一个接口及其接口的具体实现时。¹¹ 你的 API 将被构建为接受任何有效的接口实现，但你可能希望阻止子类化自己的具体实现（这样做可能会破坏你的应用程序）。示例 8-14 展示了这些依赖项如何在真实应用程序中构建。

##### 示例 8-14\. 接口和具体类

```php
interface DataAbstraction ![1](img/1.png)
{
    public function save();
}

final class DBImplementation implements DataAbstraction ![2](img/2.png)
{
    public function __construct(string $databaseConnection)
    {
        // Connect to a database
    }

    public function save()
    {
        // Save some data
    }
}

final class FileImplementation implements DataAbstraction ![3](img/3.png)
{
    public function __construct(string $filename)
    {
        // Open a file for writing
    }

    public function save()
    {
        // Write to the file
    }
}

class Application
{
    public function __construct(
        protected DataAbstraction $datalayer ![4](img/4.png)
    ) {}
}
```

![1](img/#co_classes_and_objects_CO12-1)

应用程序描述了任何数据抽象层必须实现的接口。

![2](img/#co_classes_and_objects_CO12-2)

具体的一个实现将数据显式存储在数据库中。

![3](img/#co_classes_and_objects_CO12-3)

另一种实现方式是使用平面文件进行数据存储。

![4](img/#co_classes_and_objects_CO12-4)

应用程序不关心你使用哪种实现，只要它实现了基础接口。你可以使用提供的（`final`）类，也可以定义自己的实现方式。

在某些情况下，你可能会遇到需要扩展的`final`类。在这种情况下，你唯一可以使用的方式是利用装饰器。*装饰器*是一个以另一个类作为构造函数属性并“装饰”其方法的类。

###### 注意

在某些情况下，装饰器不允许你绕过类的`final`属性。如果类型提示和严格类型要求将该类的实例传递给应用程序中的函数或另一个对象，则会发生这种情况。

假设，例如，你的应用程序中的一个库定义了一个 `Note` 类，该类实现了一个 `::publish()` 方法，用于将给定的数据发布到社交媒体（比如 Twitter）。你希望这个方法*同时*生成给定数据的静态 PDF 文档，通常会扩展该类本身，就像在 示例 8-15 中所示。

##### 示例 8-15\. 典型的类扩展，没有使用`final`关键字

```php
class Note
{
    public function publish()
    {
        // Publish the note's data to Twitter ...
    }
}

class StaticNote extends Note
{
    public function publish()
    {
        parent::publish();

        // Also produce a static PDF of the note's data ...
    }
}

$note = new StaticNote(); ![1](img/1.png)
$note->publish(); ![2](img/2.png)
```

![1](img/#co_classes_and_objects_CO13-1)

而不是实例化`Note`对象，你可以直接实例化`StaticNote`。

![2](img/#co_classes_and_objects_CO13-2)

当你调用对象的 `::publish()` 方法时，*同时*使用了这两个类定义。

如果`Note`类被标记为`final`，你将无法直接扩展该类。示例 8-16 演示了如何创建一个新的类，*装饰*`Note`类并间接扩展其功能。

##### 示例 8-16\. 使用装饰器自定义`final`类的行为

```php
final class Note
{
    public function publish()
    {
        // Publish the note's data to Twitter ...
    }
}

final class StaticNote
{
    public function __construct(private Note $note) {}

    public function publish()
    {
        $this->note->publish();

        // Also produce a static PDF of the note's data ...
    }
}

$note = new StaticNote(new Note()); ![1](img/1.png)
$note->publish(); ![2](img/2.png)
```

![1](img/#co_classes_and_objects_CO14-1)

而不是直接实例化`StaticNote`，你可以使用这个类来*包装*（或*装饰*）一个常规的`Note`实例。

![2](img/#co_classes_and_objects_CO14-2)

当调用对象的`::publish()`方法时，*两个*类定义都被使用。

## 参见

[`final` 关键字的文档](https://oreil.ly/k2ZGz)。

# 8.10 对象克隆

## 问题

想要创建一个对象的独立副本。

## 解决方案

使用`clone`关键字创建对象的第二个副本，例如：

```php
$dolly = clone $roslin;
```

## 讨论

默认情况下，当对象分配给新变量时，PHP 会*通过引用*复制对象。这个引用意味着新变量实际上指向内存中的同一对象。示例 8-17 说明了，尽管看起来你创建了对象的副本，但实际上只是处理了两个对同一数据的引用。

##### 示例 8-17\. 赋值操作符通过引用复制对象

```php
$obj1 = (object)  ![1
    'propertyOne' => 'some',
    'propertyTwo' => 'data',
];
$obj2 = $obj1; ![2](img/2.png)

$obj2->propertyTwo = 'changed'; ![3](img/3.png)

var_dump($obj1); ![4](img/4.png)
var_dump($obj2);
```

![1](img/#co_classes_and_objects_CO15-1)

此特定语法是 PHP 5.4 以来的简写，可以动态将新的关联数组转换为内置的`stdClass`类的实例。

![2](img/#co_classes_and_objects_CO15-2)

尝试通过赋值操作符将第一个对象复制到一个新实例。

![3](img/#co_classes_and_objects_CO15-3)

修改“复制”的对象的内部状态。

![4](img/#co_classes_and_objects_CO15-4)

检查原始对象显示其内部状态已更改。`$obj1`和`$obj2`都指向内存中的同一空间；你只是复制了对象的引用，而不是对象本身！

而不是复制对象引用，`clone` 关键字通过值将对象复制到新变量中。这意味着所有属性都复制到同一类的新实例中，并具有原始对象的所有方法。示例 8-18 说明了这两个对象现在完全解耦。

##### 示例 8-18\. `clone` 关键字通过值复制对象

```php
$obj1 = (object) [
    'propertyOne' => 'some',
    'propertyTwo' => 'data',
];
$obj2 = clone $obj1; ![1](img/1.png)

$obj2->propertyTwo = 'changed'; ![2](img/2.png)

var_dump($obj1); ![3](img/3.png)
var_dump($obj2); ![4](img/4.png)
```

![1](img/#co_classes_and_objects_CO16-1)

而不是使用严格的赋值，利用`clone`关键字创建对象的按值副本。

![2](img/#co_classes_and_objects_CO16-2)

再次更改副本的内部状态。

![3](img/#co_classes_and_objects_CO16-3)

检查原始对象的状态显示没有变化。

![4](img/#co_classes_and_objects_CO16-4)

然而，克隆并更改的对象展示了之前进行的属性修改。

这里的一个重要注意事项是要理解，在前面的示例中使用的`clone`是数据的*浅克隆*。此操作不会深入到像嵌套对象这样的更复杂属性中。即使使用适当的`clone`，可能仍然会留下两个不同的变量引用同一个内存中的对象。示例 8-19 说明了如果要复制的对象本身包含一个更复杂的对象会发生什么。

##### 示例 8-19\. 复杂数据结构的浅克隆

```php
$child = (object) [
    'name' => 'child',
];
$parent = (object) [
    'name'  => 'parent',
    'child' => $child
];

$clone = clone $parent;

if ($parent === $clone) { ![1](img/1.png)
    echo 'The parent and clone are the same object!' . PHP_EOL;
}

if ($parent == $clone) { ![2](img/2.png)
    echo 'The parent and clone have the same data!' . PHP_EOL;
}

if ($parent->child === $clone->child) { ![3](img/3.png)
    echo 'The parent and the clone have the same child!' . PHP_EOL;
}
```

![1](img/#co_classes_and_objects_CO17-1)

在比较对象时，严格比较只有在比较符号两侧引用同一个对象时才评估为`true`。在这种情况下，您已经正确克隆了对象并创建了一个全新的实例，因此这个比较是`false`。

![2](img/#co_classes_and_objects_CO17-2)

在对象之间进行宽松类型比较时，当操作符两侧的*值*相同时，即使是不同的实例，也会评估为`true`。这个语句评估为`true`。

![3](img/#co_classes_and_objects_CO17-3)

因为`clone`是一种浅操作，所以在两个对象上的`::$child`属性指向内存中相同的子对象。这个语句评估为`true`！

要支持更深层次的克隆，被克隆的类必须实现一个`__clone()`魔术方法，告诉 PHP 在使用`clone`时该做什么。如果这个方法存在，PHP 会在关闭类的实例时自动调用它。示例 8-20 明确展示了这个过程，同时还在处理动态类。

###### 注意

不能动态地在`stdClass`的实例上定义方法。如果您希望在应用程序中支持对象的深度克隆，必须直接定义一个类或利用匿名类，就像示例 8-20 所演示的那样。

##### 示例 8-20\. 对象的深度克隆

```php
$parent = new class {
    public string $name = 'parent';
    public stdClass $child;

    public function __clone()
    {
        $this->child = clone $this->child;
    }
};
$parent->child = (object) [
    'name' => 'child'
];

$clone = clone $parent;

if ($parent === $clone) { ![1](img/1.png)
    echo 'The parent and clone are the same object!' . PHP_EOL;
}

if ($parent == $clone) { ![2](img/2.png)
    echo 'The parent and clone have the same data!' . PHP_EOL;
}

if ($parent->child === $clone->child) { ![3](img/3.png)
    echo 'The parent and the clone have the same child!' . PHP_EOL;
}

if ($parent->child == $clone->child) { ![4](img/4.png)
    echo 'The parent and the clone have the same child data!' . PHP_EOL;
}
```

![1](img/#co_chapter_title_CO1-1)

这些对象是不同的引用，因此评估为`false`。

![2](img/#co_chapter_title_CO1-2)

父对象和克隆对象具有相同的数据，因此这评估为`true`。

![3](img/#co_chapter_title_CO1-3)

`::$child`属性也被内部克隆了，因此属性引用了不同的对象实例。这评估为`false`。

![4](img/#co_chapter_title_CO1-4)

两个`::$child`属性包含相同的数据，因此这评估为`true`。

在大多数应用程序中，您通常会使用自定义类定义，而不是匿名类。在这种情况下，您仍然可以实现魔术`__clone()`方法，在必要时告诉 PHP 如何克隆对象的更复杂属性。

## 参见

关于[`clone`关键字](https://oreil.ly/LqOE2)的文档。

# 8.11 定义静态属性和方法

## 问题

您希望为一个类定义一个方法或属性，这些方法或属性对该类的所有实例都可用。

## 解决方案

使用`static`关键字来定义可以在对象实例外部访问的属性或方法，例如：

```php
class Foo
{
    public static int $counter = 0;

    public static function increment(): void
    {
        self::$counter += 1;
    }
}
```

## 讨论

类的静态成员可以在代码的任何部分（假设可见性正确）直接从类定义中访问，而不管该类是否作为对象存在。静态属性非常有用，因为它们的行为几乎类似于全局变量，但作用域仅限于特定的类定义。Example 8-21 说明了在另一个函数中调用全局变量与静态类属性的区别。

##### 示例 8-21\. 静态属性与全局变量

```php
class Foo
{
    public static string $name = 'Foo';
}

$bar = 'Bar';

function demonstration()
{
    global $bar; ![1](img/1.png)

    echo Foo::$name . $bar; ![2](img/2.png)
}
```

![1](img/#co_classes_and_objects_CO18-1)

要在另一个作用域内访问全局变量，必须显式引用全局作用域。鉴于您可以在较窄的作用域内拥有与全局变量名称匹配的单独变量，这在实践中可能会变得令人困惑。

![2](img/#co_classes_and_objects_CO18-2)

然而，类范围的属性可以根据类本身的名称直接访问。

更有用的是，静态方法提供了在直接实例化该类对象之前调用与类绑定的实用功能的方法。一个常见的例子是构造应该表示序列化数据的值对象，在这种情况下，直接从头开始构造对象可能会很困难。

Example 8-22 演示了一个不允许直接实例化的类。相反，您必须通过反序列化一些固定数据来创建一个实例。构造函数在类的内部作用域之外是不可访问的，因此静态方法是创建对象的唯一手段。

##### 示例 8-22\. 静态方法中的对象实例化

```php
class BinaryString
{
    private function __construct(private string $bits) {} ![1](img/1.png)

    public static function fromHex(string $hex): self
    {
        return new self(hex2bin($hex)); ![2](img/2.png)
    }

    public static function fromBase64(string $b64): self
    {
        return new self(base64_decode($b64));
    }

    public function __toString(): string ![3](img/3.png)
    {
        return bin2hex($this->bits);
    }
}

$rawData = '48656c6c6f20776f726c6421';
$binary = BinaryString::fromHex($rawData); ![4](img/4.png)
```

![1](img/#co_classes_and_objects_CO19-1)

私有构造函数只能从类本身内部访问。

![2](img/#co_classes_and_objects_CO19-2)

在静态方法中，您仍然可以通过利用特殊的`self`关键字来引用类，从而创建一个新的对象实例。这允许您访问私有构造函数。

![3](img/#co_classes_and_objects_CO19-3)

PHP 的神奇`__toString()`方法在 PHP 尝试直接将对象强制转换为字符串时调用（例如，当您尝试将其`echo`到控制台时）。

![4](img/#co_classes_and_objects_CO19-4)

与其使用`new`关键字创建对象，不如利用一个专门的静态反序列化方法。

静态方法和属性与它们的非静态同行一样受到可见性约束。请注意，将它们标记为`private`意味着它们只能被彼此或类本身内部的非静态方法引用。

由于静态方法和属性并不直接绑定到对象实例，因此无法使用常规的对象绑定访问器来访问它们。而是直接使用类名和作用域解析操作符（双冒号，或`::`）——例如，对于属性可以使用`Foo::$bar`，对于方法可以使用`Foo::bar()`。在类定义内部，您可以使用`self`作为类名的简写，或者使用`parent`作为父类名的简写（如果使用继承）。

###### 注意

如果您可以访问类的对象实例，您可以使用该对象的名称而不是类名来访问其静态成员。例如，您可以使用`$foo::bar()`来访问命名为`$foo`的对象的类定义中的静态方法`bar()`。虽然这样可以工作，但可能会让其他开发人员更难理解您正在使用哪个类定义，因此如果可能的话，应该尽量少使用这种语法。

## 参见

[`static` 关键字](https://oreil.ly/tlxjn)的文档。

# 8.12 检查对象内部的私有属性或方法

## 问题

您希望枚举对象的属性或方法，并利用其私有成员。

## 解决方案

使用 PHP 的反射 API 枚举属性和方法。例如：

```php
$reflected = new ReflectionClass('SuperSecretClass');

$methods = $reflected->getMethods(); ![1](img/1.png)
$properties = $reflected->getProperties(); ![2](img/2.png)
```

## 讨论

PHP 的反射 API 赋予开发人员检查应用程序所有元素的强大能力。您可以枚举方法、属性、常量、函数参数等等。您还可以随意忽略每个元素的隐私性，并直接调用对象上的私有方法。示例 8-23 演示了如何使用反射 API 直接调用显式私有方法。

##### 示例 8-23\. 使用反射违反类的私有性

```php
class Foo
{
    private int $counter = 0; ![1](img/1.png)

    public function increment(): void
    {
        $this->counter += 1;
    }

    public function getCount(): int
    {
        return $this->counter;
    }
}

$instance = new Foo;
$instance->increment(); ![2](img/2.png)
$instance->increment(); ![3](img/3.png)

echo $instance->getCount() . PHP_EOL; ![4](img/4.png)

$instance->counter = 0; ![5](img/5.png)

$reflectionClass = new ReflectionClass('Foo');
$reflectionClass->getProperty('counter')->setValue($instance, 0); ![6](img/6.png)

echo $instance->getCount() . PHP_EOL; ![7](img/7.png)
```

![1](img/#co_classes_and_objects_CO20-1)

示例类具有单个私有属性，用于维护内部计数器。

![2](img/#co_classes_and_objects_CO20-2)

您希望将计数器增加到稍超过其默认值。现在它是`1`。

![3](img/#co_classes_and_objects_CO20-5)

另一个增量将计数器设置为`2`。

![4](img/#co_classes_and_objects_CO20-6)

此时，打印出计数器的状态将确认它是`2`。

![5](img/#co_classes_and_objects_CO20-7)

尝试直接与计数器进行交互将导致抛出`Error`，因为该属性是私有的。

![6](img/#co_classes_and_objects_CO20-8)

通过反射，您可以与对象成员进行交互，而不受其隐私设置的限制。

![7](img/#co_classes_and_objects_CO20-9)

现在您展示计数器确实已重置为`0`。

Reflection 是绕过类公开 API 中可见性修饰符的强大方式。然而，在生产应用程序中使用它很可能表明接口或系统设计不当。如果您的代码需要访问类的私有成员，要么该成员应该从一开始就是公开的，要么您需要创建一个适当的访问器方法。

Reflection 的唯一合法用途是检查和修改对象的内部状态。在应用程序中，此行为应限制于类的公开 API。然而，在*测试*中，可能需要以 API 不支持的方式在测试运行之间修改对象的状态。¹² 这些罕见情况可能需要重置内部计数器或调用类中私有清理方法。Reflection 在这时显得非常有用。

在常规应用程序开发中，Reflection 结合像 [`var_dump()`](https://oreil.ly/HXVwr) 这样的功能调用有助于澄清导入供应商代码中定义的类的内部操作。它可能对检查序列化对象或第三方集成有用，但要注意不要将这种内省应用到生产环境中。

## 另请参阅

PHP 的 [Reflection API 概述](https://oreil.ly/C49RP)。

# 8.13 在类之间重用任意代码

## 问题

您希望在多个类之间共享特定功能而不是利用类扩展。

## 解决方案

使用 `use` 语句导入 Trait，例如：

```php
trait Logger
{
    public function log(string $message): void
    {
        error_log($message);
    }
}

class Account
{
    use Logger; ![1](img/1.png)

    public function __construct(public int $accountNumber)
    {
        $this->log("Created account {$accountNumber}.");
    }
}

class User extends Person
{
    use Logger; ![2](img/2.png)

    public function authenticate(): bool
    {
        // ...
        $this->log("User {$userId} logged in.");
        // ...
    }
}
```

![1](img/#co_classes_and_objects_CO21-1)

`Account` 类从您的 `Logger` 特性导入日志记录功能，并可以像其本身定义的原生方法一样使用其方法。

![2](img/#co_classes_and_objects_CO21-2)

同样地，`User` 类可以本地级别访问 `Logger` 的方法，即使它扩展了一个带有附加功能的基础 `Person` 类。

## 讨论

如 8.6 节 中所讨论的，PHP 中的类最多可以从一个其他类继承。这被称为*单继承*，也是除 PHP 外其他语言的特征。幸运的是，PHP 还提供了一种称为*Traits* 的代码重用机制。Traits 允许在单独的类似定义中封装某些功能，可以轻松导入而不会破坏单继承。

Trait 看起来有点像类，但不能直接实例化。相反，Trait 定义的方法通过 `use` 语句导入到另一个类定义中。这允许在不共享继承树的类之间进行代码重用。

特性使您能够定义共享的常见方法（具有不同的方法可见性）和属性。您还可以在导入特性的类中覆盖特性中的默认可见性。示例 8-24 展示了如何将特性中原本为公共方法的方法导入为受保护甚至私有方法的方法。

##### 示例 8-24\. 覆盖特性中方法的可见性

```php
trait Foo
{
    public function bar()
    {
        echo 'Hello World!';
    }
}

class A
{
    use Foo { bar as protected; } ![1](img/1.png)
}

class B
{
    use Foo { bar as private; } ![2](img/2.png)
}
```

![1](img/#co_classes_and_objects_CO22-1)

此语法将导入`Foo`中定义的每个方法，但会在类`A`的范围内显式地将其`::bar()`方法设为受保护。这意味着只有类`A`（或其子类）的实例才能调用该方法。

![2](img/#co_classes_and_objects_CO22-2)

同样，类`B`将其导入的`::foo()`方法的可见性更改为私有，因此只有`B`的实例可以直接访问该方法。

特性可以深度组合在一起，这意味着特性可以像类一样轻松地`use`另一个特性。同样，导入特性的数量没有限制，可以由其他特性或类定义导入。

如果导入特性（或多个特性）的类也定义了与特性中同名的方法，则类的版本将优先使用，并成为默认的方法。示例 8-25 说明了这种优先级是如何默认工作的。

##### 示例 8-25\. 特性中方法的优先级

```php
trait Foo
{
    public function bar(): string
    {
        return 'FooBar';
    }
}

class Bar
{
    use Foo;
    public function bar(): string
    {
        return 'BarFoo';
    }
}

$instance = new Bar;
echo $instance->bar(); // BarFoo
```

在某些情况下，您可能会导入多个特性，它们都定义了相同的方法。在这些情况下，您可以在定义`use`语句时明确指定要在最终类中利用的方法版本，如下所示：

```php
trait A
{
    public function hello(): string
    {
        return 'Hello';
    }

    public function world(): string
    {
        return 'Universe';
    }
}

trait B
{
    public function world(): string
    {
        return 'World';
    }
}

class Demonstration
{
    use A, B {
        B::world insteadof A;
    }
}

$instance = new Demonstration;
echo "{$instance->hello()} {$instance->world()}!"; // Hello World!
```

与类定义类似，特性（Traits）也可以定义属性甚至静态成员。它们为您提供了一种有效的方式，将操作逻辑定义为可重用的代码块，并在应用程序中的类之间共享该逻辑。

## 另请参阅

有关[特性](https://oreil.ly/syk0E)的文档

¹ PHP 3 包含了一些原始的对象功能，但直到 4.0 的发布之前，大多数开发人员并没有真正将该语言视为面向对象的语言。

² 在撰写本文时，WordPress 用于驱动[所有网站的 43%](https://oreil.ly/tEaN8)。

³ 长篇章节中详细讨论了库和扩展功能，请参见第十五章。

⁴ 详见配方 8.12，了解有关反射 API 的更多信息。

⁵ 回顾“可见性”，以获取有关类内可见性的更多背景信息。

⁶ 详见配方 8.3，了解如何进一步将这些属性设置为只读。

⁷ 想要了解更多关于严格类型强制的内容，请参阅 Recipe 3.4。

⁸ 关于属性和方法可见性的更多信息，请查看“可见性”。

⁹ 想要了解更多关于类继承和扩展的内容，请参阅 Recipe 8.6。

¹⁰ 想要了解更多关于类继承的内容，请参阅 Recipe 8.6。

¹¹ 想要了解更多关于接口的内容，请查看 Recipe 8.7。

¹² 测试和调试内容详细讨论见第十三章。
