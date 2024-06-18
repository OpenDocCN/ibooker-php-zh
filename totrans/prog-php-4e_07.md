# 第六章。对象

在本章中，您将学习如何在 PHP 中定义、创建和使用对象。面向对象编程（OOP）为更清晰的设计、更容易的维护和更大的代码重用打开了大门。OOP 已被证明非常有价值，以至于今天很少有人敢引入非面向对象的语言。PHP 支持 OOP 的许多有用功能，本章将向您展示如何使用它们，涵盖基本的 OOP 概念以及诸如内省和序列化等高级主题。

# 对象

面向对象编程承认数据与处理数据的代码之间的基本联系，并允许您围绕该联系设计和实现程序。例如，公告板系统通常跟踪许多用户。在过程式编程语言中，每个用户由一个数据结构表示，并且可能有一组函数用于处理这些数据结构（创建新用户、获取他们的信息等）。在面向对象的语言中，每个用户由一个*对象*表示—一个附带代码的数据结构。数据和代码仍然存在，但它们被视为一个不可分割的单元。对象作为代码和数据的结合体，是应用程序开发和代码重用的模块化单元。

在这个假设的公告板设计中，对象不仅可以代表用户，还可以代表消息和主题。用户对象为该用户具有用户名和密码，并具有用于识别该作者的所有消息的代码。消息对象知道它属于哪个主题，并具有发布新消息、回复现有消息和显示消息的代码。主题对象是消息对象的集合，并具有显示主题索引的代码。然而，将必要功能划分为对象的方式只是其中一种。例如，在另一种设计中，发布新消息的代码存在于用户对象中，而不是消息对象中。

设计面向对象系统是一个复杂的主题，已经有许多书籍写就。好消息是，无论您如何设计系统，都可以在 PHP 中实现它。让我们首先介绍一些在深入研究这种编程方法之前需要了解的关键术语和概念。

# 术语

每种面向对象语言似乎对相同的概念有不同的术语集。本节描述了 PHP 使用的术语，但请注意，在其他语言中，这些术语可能具有其他含义。

让我们回到公告板用户的例子。您需要为每个用户跟踪相同的信息，并且可以在每个用户的数据结构上调用相同的函数。当您设计程序时，您决定每个用户的字段并提出函数。在面向对象编程术语中，您正在设计用户*类*。类是构建对象的模板。

*对象*是类的一个实例（或发生）。在这种情况下，它是一个具有附加代码的实际用户数据结构。对象和类有点像值和数据类型。只有一个整数数据类型，但可能有许多可能的整数。类似地，您的程序仅定义一个用户类，但可以从中创建许多不同（或相同）的用户。

与对象关联的数据称为其*属性*。与对象关联的函数称为其*方法*。当您定义一个类时，您定义其属性的名称并给出其方法的代码。

如果您使用*封装*，程序的调试和维护将变得更容易。这是一个类提供一定方法（*接口*）给使用其对象的代码的想法，因此外部代码不直接访问这些对象的数据结构。因此，调试更容易，因为您知道在哪里查找错误——只有更改对象数据结构的代码位于类内部——维护更容易，因为您可以在不更改使用类的代码的情况下交换类的实现，只要保持相同的接口。

任何非平凡的面向对象设计可能都涉及*继承*。这是通过指定一个新类说它像现有类一样，但具有某些新的或更改的属性和方法的一种方式。原始类称为*超类*（或父类或基类），新类称为*子类*（或派生类）。继承是代码重用的一种形式——超类代码被重用，而不是复制粘贴到子类中。对超类的任何改进或修改都会自动传递到子类。

# 创建对象

创建对象并使用它们比定义对象类要容易得多，因此在我们讨论如何定义类之前，让我们看看如何创建对象。要创建给定类的对象，请使用`new`关键字：

```php
$*`object`* = new *`Class`*;
```

假设已定义了一个`Person`类，以下是如何创建一个`Person`对象的方法：

```php
$moana = new Person;
```

不要引用类名，否则会导致编译错误：

```php
$moana = new "Person"; // does not work
```

有些类允许您向`new`调用传递参数。类的文档应该说明它是否接受参数。如果接受，您将像这样创建对象：

```php
$object = new Person("Sina", 35);
```

类名不必硬编码到您的程序中。您可以通过变量提供类名：

```php
$class = "Person";
$object = new $class;
// is equivalent to
$object = new Person;
```

指定一个不存在的类会导致运行时错误。

包含对象引用的变量只是普通变量——它们可以像其他变量一样使用。注意，变量变量可以与对象一起使用，如下所示：

```php
$account = new Account;
$object = "account";
${$object}->init(50000, 1.10); // same as $account->init
```

# 访问属性和方法

一旦您拥有对象，您可以使用`->`符号访问对象的方法和属性：

```php
*`$object`*->*`propertyname` `$object`*->*`methodname`*([*`arg`*, ... ])
```

例如：

```php
echo "Moana is {$moana->age} years old.\n"; // property access
$moana->birthday(); // method call
$moana->setAge(21); // method call with arguments
```

方法与函数的行为相同（只针对所讨论的对象），因此它们可以接受参数并返回一个值：

```php
$clan = $moana->family("extended");
```

在类的定义中，你可以使用`public`和`private`访问修饰符来指定哪些方法和属性是公共可访问的，哪些是只能在类内部访问的。你可以使用这些修饰符来提供封装性。

你可以使用带有属性名称的变量变量：

```php
$prop = 'age';
echo $moana->$prop;
```

静态方法是在类上调用而不是在对象上调用的方法。这样的方法无法访问属性。静态方法的名称是类名后跟两个冒号和函数名。例如，这在 HTML 类中调用`p()`静态方法：

```php
HTML::p("Hello, world");
```

在声明类时，使用静态访问属性定义哪些属性和方法是静态的。

一旦创建，对象通过引用传递——也就是说，不是复制整个对象本身（这是一项耗费时间和内存的工作），而是传递对象的引用。例如：

```php
$f = new Person("Pua", 75);

$b = $f; // $b and $f point at same object $b->setName("Hei Hei");

printf("%s and %s are best friends.\n", $b->getName(), $f->getName());
`Hei` `Hei` `and` `Hei` `Hei` `are` `best` `friends``.`
```

如果你想创建一个对象的真实副本，可以使用克隆运算符：

```php
$f = new Person("Pua", 35);

$b = clone $f; // make a copy $b->setName("Hei Hei");// change the copy 
printf("%s and %s are best friends.\n", $b->getName(), $f->getName());
`Pua` `and` `Hei` `Hei` `are` `best` `friends``.`
```

当你使用克隆运算符创建对象的副本，并且该类声明了`__clone()`方法时，该方法会在新对象被克隆后立即调用。在对象持有外部资源（如文件句柄）的情况下，你可以使用此方法创建新资源，而不是复制现有资源。

# 类声明

要以面向对象的方式设计你的程序或代码库，你需要使用`class`关键字定义自己的类。类定义包括类名以及类的属性和方法。类名不区分大小写，必须符合 PHP 标识符的规则。其中，类名`stdClass`是保留的。以下是类定义的语法：

```php
class classname [ extends baseclass ] [ implements interfacename ,
 [interfacename, ... ] ] {
 [ use traitname, [ traitname, ... ]; ]

 [ visibility $property [ = value ]; ... ]

 [ function functionname (args) [: type ] {
 // code
 }
 ...
 ]
}
```

## 方法声明

方法是定义在类内部的函数。虽然 PHP 没有施加特殊的限制，大多数方法只在方法所在的对象内部的数据上起作用。方法名称以两个下划线（`__`）开头的方法可能在未来由 PHP 使用（当前用于对象序列化方法`__sleep()`和`__wakeup()`，稍后在本章描述的其他方法），因此建议不要以这个序列开始方法名称。

在方法内部，`$this`变量包含对调用该方法的对象的引用。例如，如果你调用`$moana->birthday()`，在`birthday()`方法内部，`$this`持有与`$moana`相同的值。方法使用`$this`变量来访问当前对象的属性并调用该对象上的其他方法。

这里是`Person`类的简单类定义，展示了`$this`变量的使用方式：

```php
class Person {
 public $name = '';

 function getName() {
 return $this->name;
 }

 function setName($newName) {
 $this->name = $newName;
 }
}
```

如你所见，`getName()`和`setName()`方法使用`$this`访问和设置当前对象的`$name`属性。

要将方法声明为静态方法，请使用`static`关键字。在静态方法内部，变量`$this`未定义。例如：

```php
class HTMLStuff {
 static function startTable() {
 echo "<table border=\"1\">\n";
 }

 static function endTable() {
 echo "</table>\n";
 }
}

HTMLStuff::startTable();
 // print HTML table rows and columns
HTMLStuff::endTable();
```

如果你使用`final`关键字声明一个方法，子类将无法覆盖该方法。例如：

```php
class Person {
 public $name;

 final function getName() {
 return $this->name;
 }
}

class Child extends Person {
 // syntax error
 function getName() {
 // do something
 }
}
```

使用访问修饰符，你可以改变方法的可见性。在对象方法之外可访问的方法应该声明为`public`；只能被同一类内部方法调用的实例方法应该声明为`private`。最后，声明为`protected`的方法只能从对象的类方法和继承自该类的类方法调用。定义类方法的可见性是可选的；如果没有指定可见性，则方法是公共的。例如，你可以定义：

```php
class Person {
 public $age;

 public function __construct() {
 $this->age = 0;
 }

 public function incrementAge() {
 $this->age += 1;
 $this->ageChanged();
 }

 protected function decrementAge() {
 $this->age -= 1;
 $this->ageChanged();
 }

 private function ageChanged() {
 echo "Age changed to {$this->age}";
 }
}

class SupernaturalPerson extends Person {
 public function incrementAge() {
 // ages in reverse
 $this->decrementAge();
 }
}

$person = new Person;
$person->incrementAge();
$person->decrementAge(); // not allowed
$person->ageChanged(); // also not allowed

$person = new SupernaturalPerson;
$person->incrementAge(); // calls decrementAge under the hood
```

在声明对象方法时，你可以使用类型提示（在第三章中描述）：

```php
class Person {
 function takeJob(Job $job) {
 echo "Now employed as a {$job->title}\n";
 }
}
```

当方法返回一个值时，你可以使用类型提示声明方法的返回值类型：

```php
class Person {
 function bestJob(): Job {
 $job = Job("PHP developer");

 return $job;
 }
}
```

## 声明属性

在上述`Person`类的定义中，我们明确声明了`$name`属性。属性声明是可选的，只是对维护你的程序的人的一种礼貌。声明属性是良好的 PHP 风格，但你可以随时添加新的属性。

这是一个`Person`类的版本，其中有一个未声明的`$name`属性：

```php
class Person {
 function getName() {
 return $this->name;
 }

 function setName($newName) {
 $this->name = $newName;
 }
}
```

你可以为属性分配默认值，但这些默认值必须是简单的常量：

```php
public $name = "J Doe"; // works
public $age = 0; // works
public $day = 60 * 60 * hoursInDay(); // doesn't work
```

使用访问修饰符，你可以改变属性的可见性。在对象作用域之外可访问的属性应该声明为`public`；在同一类内部方法才能访问的实例属性应该声明为`private`。最后，声明为`protected`的属性只能被对象的类方法和继承自该类的类方法访问。例如，你可以声明一个用户类：

```php
class Person {
 protected $rowId = 0;

 public $username = 'Anyone can see me';

 private $hidden = true;
}
```

除了对象实例的属性外，PHP 还允许你定义静态属性，这些属性是对象类上的变量，可以通过类名引用该属性。例如：

```php
class Person {
 static $global = 23;
}

$localCopy = Person::$global;
```

在对象类的实例内部，你也可以使用`self`关键字引用静态属性，例如`echo self::$global;`。

如果在访问一个不存在的对象属性时，对象的类定义了`__get()`或`__set()`方法，那么该方法将有机会获取值或者为该属性设置值。

例如，你可以声明一个表示从数据库中拉取数据的类，但你可能不希望拉取大数据值（如二进制大对象（BLOBs）），除非专门请求。当然，实现这一点的一种方法是为该属性创建读取和写入数据的访问方法。另一种方法可能是使用这些重载方法：

```php
class Person {
 public function __get($property) {
 if ($property === 'biography') {
 $biography = "long text here..."; // would retrieve from database

 return $biography;
 }
 }

 public function __set($property, $value) {
 if ($property === 'biography') {
 // set the value in the database
 }
 }
}
```

## 声明常量

与通过`define()`函数分配的全局常量一样，PHP 提供了在类内部分配常量的方法。类似于静态属性，常量可以直接通过类或在对象方法内部使用`self`表示法访问。一旦定义了常量，其值就不能更改：

```php
class PaymentMethod {
 public const TYPE_CREDITCARD = 0;
 public const TYPE_CASH = 1;
}

echo PaymentMethod::TYPE_CREDITCARD;
`0`
```

与全局常量一样，通常使用大写标识符定义类常量。

使用访问修饰符，可以更改类常量的可见性。可以从对象方法外部访问的类常量应声明为`public`；只能通过同一类内部方法访问的实例上的类常量应声明为`private`。最后，声明为`protected`的常量只能从对象的类方法和继承类的类方法中访问。定义类常量的可见性是可选的；如果没有指定可见性，则方法为`public`。例如，您可以定义：

```php
class Person {
 protected const PROTECTED_CONST = false;
 public const DEFAULT_USERNAME = "<unknown>";
 private INTERNAL_KEY = "ABC1234";
}
```

## 继承

要继承另一个类的属性和方法，使用类定义中的`extends`关键字，后跟基类的名称：

```php
class Person {
 public $name, $address, $age;
}

class Employee extends Person {
 public $position, $salary;
}
```

`Employee`类包含`$position`和`$salary`属性，以及从`Person`类继承的`$name`，`$address`和`$age`属性。

如果派生类具有与其父类中同名的属性或方法，则派生类中的属性或方法优先于父类中的属性或方法。引用属性返回子类属性的值，引用方法调用子类的方法。

使用`parent::`*`method`*`()`表示法来访问对象父类中被重写的方法：

```php
parent::birthday(); // call parent class's birthday() method
```

一个常见的错误是在调用重写方法时将父类名称硬编码到中：

```php
Creature::birthday(); // when Creature is the parent class
```

这是一个错误，因为它将父类名称的知识分布到派生类中。使用`parent::`将父类的知识集中在`extends`子句中。

如果一个方法可能被子类继承，并且你希望确保在当前类上调用它，可以使用`self::`*`method`*`()`的表示法：

```php
self::birthday(); // call this class's birthday() method
```

要检查一个对象是否是特定类的实例或是否实现了特定接口（参见“接口”部分），可以使用`instanceof`运算符：

```php
if ($object instanceof Animal) {
 // do something
}
```

## 接口

接口提供了一种定义类遵循的契约的方式；接口提供方法原型和常量，任何实现接口的类必须为接口中的所有方法提供实现。以下是接口定义的语法：

```php
interface *`interfacename`* {
 [ function *`functionname`*();
 ...
 ]
}
```

要声明一个类实现一个接口，需要使用`implements`关键字，并且可以跟任意数量的接口，用逗号分隔：

```php
interface Printable {
 function printOutput();
}

class ImageComponent implements Printable {
 function printOutput() {
 echo "Printing an image...";
 }
}
```

接口可以继承其他接口（包括多个接口），只要它从父接口继承的方法没有与子接口中声明的方法同名。

## 特征

特征提供了在类层次结构之外重用代码的机制。特征允许你在不共享公共祖先的不同类之间共享功能。以下是特征定义的语法：

```php
trait *`traitname`* [ extends *`baseclass`* ] {
 [ use *`traitname`*, [ *`traitname`*, ... ]; ]

 [ visibility $property [ = value ]; ... ]

 [ function *`functionname`* (*`args`*) {
 // *`code`*
 }
 ...
 ]
}
```

要声明一个类应该包含特征的方法，请使用`use`关键字和任意数量的特征，用逗号分隔：

```php
trait Logger {
 public function log($logString) {
 $className = __CLASS__;
 echo date("Y-m-d h:i:s", time()) . ": [{$className}] {$logString}";
 }
}

class User {
 use Logger;

 public $name;

 function __construct($name = '') {
 $this->name = $name;
 $this->log("Created user '{$this->name}'");
 }

 function __toString() {
 return $this->name;
 }
}

class UserGroup {
 use Logger;

 public $users = array();

 public function addUser(User $user) {
 if (!in_array($this->users, $user)) {
 $this->users[] = $user;
 $this->log("Added user '{$user}' to group");
 }
 }
}

$group = new UserGroup;
$group->addUser(new User("Franklin"));
`2012``-``03``-``09` `07``:``12``:``58``:` `[``User``]` `Created` `user` `'Franklin'``2012``-``03``-``09` `07``:``12``:``58``:`
`[``UserGroup``]` `Added` `user` `'Franklin'` `to` `group`
```

由`Logger`特征定义的方法对`UserGroup`类的实例可用，就像这些方法在该类中定义一样。

要声明一个特征应该由其他特征组成，需在特征声明中使用`use`语句，后面跟上一个或多个用逗号分隔的特征名称，如下所示：

```php
trait First {
 public function doFirst( {
 echo "first\n";
 }
}

trait Second {
 public function doSecond() {
 echo "second\n";
 }
}

trait Third {
 use First, Second;

 public function doAll() {
 $this->doFirst();
 $this->doSecond();
 }
}

class Combined {
 use Third;
}

$object = new Combined;
$object->doAll();
`firstsecond`
```

特征可以声明抽象方法。

如果一个类使用多个定义了相同方法的特征，PHP 会给出致命错误。然而，你可以通过告诉编译器具体想要使用的给定方法的实现来覆盖这种行为。在定义一个类包含哪些特征时，对于每个冲突使用`insteadof`关键字：

```php
trait Command {
 function run() {
 echo "Executing a command\n";
 }
}

trait Marathon {
 function run() {
 echo "Running a marathon\n";
 }
}

class Person {
 use Command, Marathon {
 Marathon::run insteadof Command;
 }
}

$person = new Person;
$person->run();
`Running` `a` `marathon`
```

你可以使用`as`关键字给一个特征的方法在包含它的类中起别名。你仍然必须明确解决包含特征中的任何冲突。例如：

```php
trait Command {
 function run() {
 echo "Executing a command";
 }
}

trait Marathon {
 function run() {
 echo "Running a marathon";
 }
}

class Person {
 use Command, Marathon {
 Command::run as runCommand;
 Marathon::run insteadof Command;
 }
}

$person = new Person;
$person->run();
$person->runCommand();
`Running` `a` `marathonExecuting` `a` `command`
```

## 抽象方法

PHP 还提供了一种机制来声明类上的某些方法必须由子类实现——这些方法的实现在父类中未定义。在这些情况下，你提供一个抽象方法；此外，如果一个类包含任何抽象方法定义，你还必须将该类声明为抽象类：

```php
abstract class Component {
 abstract function printOutput();
}

class ImageComponent extends Component {
 function printOutput() {
 echo "Pretty picture";
 }
}
```

抽象类不能被实例化。此外，请注意，与一些语言不同，PHP 不允许为抽象方法提供默认实现。

特征也可以声明抽象方法。包含定义了抽象方法的特征的类必须实现该方法：

```php
trait Sortable {
 abstract function uniqueId();

 function compareById($object) {
 return ($object->uniqueId() < $this->uniqueId()) ? −1 : 1;
 }
}

class Bird {
 use Sortable;

 function uniqueId() {
 return __CLASS__ . ":{$this->id}";
 }
}

// this will not compile
class Car {
 use Sortable;
}

$bird = new Bird;
$car = new Car;
$comparison = $bird->compareById($car);
```

当你在子类中实现一个抽象方法时，方法签名必须匹配——即，它们必须接收相同数量的必需参数，并且如果任何参数具有类型提示，则这些类型提示必须匹配。此外，方法的可见性必须相同或更少受限。

## 构造函数

当实例化对象时，你可以在类名后提供一个参数列表：

```php
$person = new Person("Fred", 35);
```

这些参数传递给类的*构造函数*，一个特殊的函数，用于初始化类的属性。

构造函数是类中称为`__construct()`的函数。这是`Person`类的构造函数：

```php
class Person {
 function __construct($name, $age) {
 $this->name = $name;
 $this->age = $age;
 }
}
```

PHP 不提供自动构造函数链；也就是说，如果你实例化一个派生类的对象，只有派生类中的构造函数会自动调用。要调用父类的构造函数，派生类的构造函数必须显式调用父类的构造函数。在这个例子中，`Employee`类的构造函数调用了`Person`的构造函数：

```php
class Person {
 public $name, $address, $age;

 function __construct($name, $address, $age) {
 $this->name = $name;
 $this->address = $address;
 $this->age = $age;
 }
}

class Employee extends Person {
 public $position, $salary;

 function __construct($name, $address, $age, $position, $salary) {
 parent::__construct($name, $address, $age);

 $this->position = $position;
 $this->salary = $salary;
 }
}
```

## 析构函数

当对象被销毁时，比如当最后一个引用被移除或脚本结束时，它的*析构函数*会被调用。因为 PHP 在变量超出范围或脚本执行结束时自动清理所有资源，它们的应用是有限的。析构函数是一个名为`__destruct()`的方法：

```php
class Building {
 function __destruct() {
 echo "A Building is being destroyed!";
 }
}
```

# 匿名类

在创建用于测试的模拟对象时，创建匿名类非常有用。匿名类的行为与任何其他类相同，只是你不提供名称（这意味着不能直接实例化）：

```php
class Person {
 public $name = ‘';

 function getName() {
 return $this->name;
 }
}

// return an anonymous implementation of Person
$anonymous = new class() extends Person {
 public function getName() {
 // return static value for testing purposes
 return "Moana";
 }
}; // note: requires closing semicolon, unlike nonanonymous class definitions
```

与具名类的实例不同，匿名类的实例无法被序列化。尝试序列化匿名类的实例会导致错误。

# 自省

*自省*是程序检查对象特征（如其名称、父类（如果有）、属性和方法）的能力。使用自省，你可以编写操作任何类或对象的代码。在编写代码时，你无需知道定义了哪些方法或属性；相反，你可以在运行时发现这些信息，这使得编写通用调试器、序列化程序、分析器等成为可能。在本节中，我们将介绍 PHP 提供的自省函数。

## 检查类

要确定一个类是否存在，可以使用`class_exists()`函数，它接受一个字符串并返回一个布尔值。或者，你可以使用`get_declared_classes()`函数，它返回一个已定义类的数组，并检查类名是否在返回的数组中：

```php
$doesClassExist = class_exists(*`classname`*);

$classes = get_declared_classes();
$doesClassExist = in_array(*`classname`*, $classes);
```

使用`get_class_methods()`和`get_class_vars()`函数可以获取类中存在的方法和属性（包括从超类继承的方法和属性）。这些函数接受一个类名并返回一个数组：

```php
$methods = get_class_methods(*`classname`*);
$properties = get_class_vars(*`classname`*);
```

类名可以是包含类名的变量、裸单词或带引号的字符串：

```php
$class = "Person";
$methods = get_class_methods($class);
$methods = get_class_methods(Person); // same
$methods = get_class_methods("Person"); // same
```

`get_class_methods()`返回的数组是一个简单的方法名称列表。`get_class_vars()`返回的关联数组将属性名称映射到值，并且还包括继承的属性。

`get_class_vars()`的一个怪异之处在于它仅返回具有默认值并在当前作用域可见的属性；没有办法发现未初始化的属性。

使用`get_parent_class()`来查找一个类的父类：

```php
$superclass = get_parent_class(*`classname`*);
```

示例 6-1 列出了`displayClasses()`函数，该函数显示了当前声明的所有类及其每个类的方法和属性。

##### 示例 6-1\. 显示所有已声明的类

```php
function displayClasses() {
 $classes = get_declared_classes();

 foreach ($classes as $class) {
 echo "Showing information about {$class}<br />";
 $reflection = new ReflectionClass($class);

 $isAnonymous = $reflection->isAnonymous() ? "yes" : "no";
 echo "Is Anonymous: {$isAnonymous}<br />";

 echo "Class methods:<br />";
 $methods = $reflection->getMethods(ReflectionMethod::IS_STATIC);

 if (!count($methods)) {
 echo "<i>None</i><br />";
 }
 else {
 foreach ($methods as $method) {
 echo "<b>{$method}</b>()<br />";
 }
 }

 echo "Class properties:<br />";

 $properties = $reflection->getProperties();

 if (!count($properties)) {
 echo "<i>None</i><br />";
 }
 else {
 foreach(array_keys($properties) as $property) {
 echo "<b>\${$property}</b><br />";
 }
 }

 echo "<hr />";
 }
}
```

## 检查一个对象

要获取对象所属的类，首先确保它是一个对象，使用`is_object()`函数，然后用`get_class()`函数获取类：

```php
$isObject = is_object(*`var`*);
$classname = get_class(*`object`*);
```

在调用对象的方法之前，您可以使用`method_exists()`函数确保方法存在：

```php
$methodExists = method_exists(*`object`*, *`method`*);
```

调用未定义的方法会触发运行时异常。

就像`get_class_vars()`返回一个类的属性数组一样，`get_object_vars()`返回一个对象中设置的属性数组：

```php
$array = get_object_vars(*`object`*);
```

正如`get_class_vars()`只返回具有默认值的属性一样，`get_object_vars()`只返回已设置的属性：

```php
class Person {
 public $name;
 public $age;
}

$fred = new Person;
$fred->name = "Fred";
$props = get_object_vars($fred); // array('name' => "Fred", 'age' => NULL);
```

`get_parent_class()`函数接受一个对象或一个类名。它返回父类的名称，如果没有父类则返回`FALSE`：

```php
class A {}
class B extends A {}

$obj = new B;
echo get_parent_class($obj);
echo get_parent_class(B);
`AA`
```

## 示例内省程序

示例 6-2 展示了一组函数，显示了有关对象属性、方法和继承树的参考页面信息。

##### 示例 6-2\. 对象内省函数

```php
// return an array of callable methods (include inherited methods)
function getCallableMethods($object): Array {
 $reflection = new ReflectionClass($object);
 $methods = $reflection->getMethods();

 return $methods;
}

// return an array of superclasses
function getLineage($object): Array {
 $reflection = new ReflectionClass($object);

 if ($reflection->getParentClass()) {
 $parent = $reflection->getParentClass();

 $lineage = getLineage($parent);
 $lineage[] = $reflection->getName();
 }
 else {
 $lineage = array($reflection->getName());
 }

 return $lineage;
}

// return an array of subclasses
function getChildClasses($object): Array {
 $reflection = new ReflectionClass($object);

 $classes = get_declared_classes();

 $children = array();

 foreach ($classes as $class) {
 $checkedReflection = new ReflectionClass($class);

 if ($checkedReflection->isSubclassOf($reflection->getName())) {
 $children[] = $checkedReflection->getName();
 }
 }

 return $children;
}

// return an array of properties
function getProperties($object): Array {
 $reflection = new ReflectionClass($object);

 return $reflection->getProperties();
}

// display information on an object
function printObjectInfo($object) {
 $reflection = new ReflectionClass($object);
 echo "<h2>Class</h2>";
 echo "<p>{$reflection->getName()}</p>";

 echo "<h2>Inheritance</h2>";

 echo "<h3>Parents</h3>";
 $lineage = getLineage($object);
 array_pop($lineage);

 if (count($lineage) > 0) {
 echo "<p>" . join(" -&gt; ", $lineage) . "</p>";
 }
 else {
 echo "<i>None</i>";
 }

 echo "<h3>Children</h3>";
 $children = getChildClasses($object);
 echo "<p>";

 if (count($children) > 0) {
 echo join(', ', $children);
 }
 else {
 echo "<i>None</i>";
 }

 echo "</p>";

 echo "<h2>Methods</h2>";
 $methods = getCallableMethods($object);

 if (!count($methods)) {
 echo "<i>None</i><br />";
 }
 else {
 foreach($methods as $method) {
 echo "<b>{$method}</b>();<br />";
 }
 }

 echo "<h2>Properties</h2>";
 $properties = getProperties($object);

 if (!count($properties)) {
 echo "<i>None</i><br />";
 }
 else {
 foreach(array_keys($properties) as $property) {
 echo "<b>\${$property}</b> = " . $object->$property . "<br />";
 }
 }

 echo "<hr />";
}
```

这里是一些示例类和对象，演示了来自示例 6-2 的内省函数：

```php
class A {
 public $foo = "foo";
 public $bar = "bar";
 public $baz = 17.0;

 function firstFunction() { }

 function secondFunction() { }
}

class B extends A {
 public $quux = false;

 function thirdFunction() { }
}

class C extends B { }

$a = new A();
$a->foo = "sylvie";
$a->bar = 23;

$b = new B();
$b->foo = "bruno";
$b->quux = true;

$c = new C();

printObjectInfo($a);
printObjectInfo($b);
printObjectInfo($c);
```

# 序列化

*序列化*一个对象意味着将其转换为可以存储在文件中的字节流表示。这对于持久数据非常有用；例如，PHP 会话会自动保存和恢复对象。PHP 中的序列化大部分是自动的——除了调用`serialize()`和`unserialize()`函数外，你几乎不需要额外工作：

```php
$encoded = serialize(*`something`*);
$something = unserialize(*`encoded`*);
```

序列化最常用于 PHP 的会话中，会话会处理对象的序列化。你只需告诉 PHP 要跟踪哪些变量，它们就会在您网站上的页面访问之间自动保存。然而，会话并不是序列化的唯一用途——如果您想实现自己的持久对象形式，`serialize()`和`unserialize()`是一个自然的选择。

对象的类必须在反序列化之前定义。尝试反序列化一个尚未定义类的对象会将对象放入`stdClass`，这几乎使其无用。这的一个实际后果是，如果您使用 PHP 会话自动序列化和反序列化对象，则必须在您网站的每个页面中包含包含对象类定义的文件。例如，您的页面可能如下所示：

```php
include "object_definitions.php"; // load object definitions
session_start(); // load persistent variables
?>
<html>...
```

PHP 在序列化和反序列化过程中为对象提供了两个钩子：`__sleep()`和`__wakeup()`。这些方法用于通知对象它们正在被序列化或反序列化。如果对象没有这些方法，它们可以被序列化，但是它们不会被通知该过程。

`__sleep()`方法在对象序列化之前调用；它可以执行任何必要的清理操作以保留对象的状态，如关闭数据库连接、写入未保存的持久数据等。它应返回一个包含需要写入字节流的数据成员名称的数组。如果返回一个空数组，则不会写入任何数据。

相反，`__wakeup()`方法在从字节流创建对象后立即调用。该方法可以执行任何所需的操作，如重新打开数据库连接和其他初始化任务。

示例 6-3 是一个对象类，`Log`，提供两个有用的方法：`write()`用于将消息追加到日志文件中，`read()`用于获取日志文件的当前内容。它使用`__wakeup()`重新打开日志文件和`__sleep()`关闭日志文件。

##### 示例 6-3\. Log.php 文件

```php
class Log {
 private $filename;
 private $fh;

 function __construct($filename) {
 $this->filename = $filename;
 $this->open();
 }

 function open() {
 $this->fh = fopen($this->filename, 'a') or die("Can't open {$this->filename}");
 }

 function write($note) {
 fwrite($this->fh, "{$note}\n");
 }

 function read() {
 return join('', file($this->filename));
 }

 function __wakeup(array $data): void {
 $this->filename = $data["filename"];
 $this->open();
 }

 function __sleep() {
 // write information to the account file
 fclose($this->fh);

 return ["filename" => $this->filename];
 }
}
```

将`Log`类的定义存储在名为*Log.php*的文件中。示例 6-4 的 HTML 首页使用`Log`类和 PHP 会话来创建一个持久的日志变量`$logger`。

##### 示例 6-4\. front.php

```php
<?php
include_once "Log.php";
session_start();
?>

<html><head><title>Front Page</title></head>
<body>

<?php
$now = strftime("%c");

if (!isset($_SESSION['logger'])) {
 $logger = new Log("/tmp/persistent_log");
 $_SESSION['logger'] = $logger;
 $logger->write("Created $now");

 echo("<p>Created session and persistent log object.</p>");
}
else {
 $logger = $_SESSION['logger'];
}

$logger->write("Viewed first page {$now}");

echo "<p>The log contains:</p>";
echo nl2br($logger->read());
?>

<a href="next.php">Move to the next page</a>

</body></html>
```

示例 6-5 展示了文件*next.php*，一个 HTML 页面。从首页到该页面的链接触发了持久对象`$logger`的加载。`__wakeup()`调用重新打开日志文件，以便对象准备好使用。

##### 示例 6-5\. next.php

```php
<?php
include_once "Log.php";
session_start();
?>

<html><head><title>Next Page</title></head>
<body>

<?php
$now = strftime("%c");
$logger = $_SESSION['logger'];
$logger->write("Viewed page 2 at {$now}");

echo "<p>The log contains:";
echo nl2br($logger->read());
echo "</p>";
?>

</body></html>
```

# 下一步

学习如何在您自己的脚本中使用对象是一项巨大的任务。在下一章中，我们将从语言语义转向实践，并向您展示 PHP 中最常用的一组面向对象类之一——日期和时间类。
