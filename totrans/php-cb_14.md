# 第十四章 性能调优

PHP 等动态解释语言以其灵活性和易用性而闻名，但不一定因为它们的速度。这部分是因为它们的类型系统工作方式的原因。当类型在运行时推断时，解析器无法准确知道如何执行某个操作，直到提供了数据。

考虑以下松散类型的 PHP 函数来将两个项目相加：

```php
function add($a, $b)
{
    return $a + $b;
}
```

由于此函数未声明传入变量的类型或返回类型，它可能表现出多个签名。在 Example 14-1 中的所有方法签名都是调用前述函数的同样有效方式。

##### Example 14-1\. 同一个函数定义的各种签名

```php
add(int $a,    int $b):   int ![1](img/1.png)
add(float $a,  float $b): float ![2](img/2.png)
add(int $a,    float $b): float ![3](img/3.png)
add(float $a,  int $b):   float ![4](img/4.png)
add(string $a, int $b):   int ![5](img/5.png)
add(string $a, int $b):   float ![6](img/6.png)
```

![1](img/#co_performance_tuning_CO1-1)

`add(1, 2)` 返回 `int(3)`

![2](img/#co_performance_tuning_CO1-2)

`add(1., 2.)` 返回 `float(3)`

![3](img/#co_performance_tuning_CO1-3)

`add(1, 2.)` 返回 `float(3)`

![4](img/#co_performance_tuning_CO1-4)

`add(1., 2)` 返回 `float(3)`

![5](img/#co_performance_tuning_CO1-5)

`add("1", 2)` 返回 `int(3)`

![6](img/#co_performance_tuning_CO1-6)

`add("1.", 2)` 返回 `float(3)`

上述示例说明了您可以编写一个单一函数，而 PHP 内部需要以多种方式处理它。直到它看到您提供的数据，语言才知道您实际需要函数的哪个版本，并且会在必要时内部将某些值转换为其他类型。在运行时，实际函数被编译为操作码（opcode），通过专用虚拟机在处理器上运行——PHP 将需要生成同一函数的多个版本的操作码来处理不同的输入和返回类型。

###### 注释

PHP 利用的松散类型系统是使其易于学习但也容易出现致命编程错误的因素之一。本书已经在可能的地方利用*严格类型*来避免这些确切的陷阱。查阅 Recipe 3.4 了解更多关于在您自己的代码中使用严格类型的信息。

对于编译语言来说，这里表达的关于松散类型的问题将是微不足道的——只需将程序编译成多个 opcode 的分支即可。不幸的是，PHP 更像是一种解释语言；它根据应用程序的加载情况在需求时重新加载和重新编译您的脚本。幸运的是，多个代码路径的性能损耗可以通过两个现代特性来解决，这些特性内置于语言本身中：即时编译（JIT）和 opcode 的缓存。

## 即时编译

截至版本 8.0，PHP [配备了即时编译器（JIT compiler）](https://oreil.ly/XS9gg)，能够立即提高程序执行速度，使应用性能更佳。它通过利用传递给处理脚本执行的虚拟机（VM）的实际指令的轨迹来实现这一点。当特定轨迹频繁调用时，PHP 将自动识别操作的重要性，并评估代码是否从编译中获益。

对同一段代码的后续调用将使用编译后的字节码而不是动态脚本，从而显著提升性能。基于[Zend 在发布 PHP 8.0 时发布的度量数据](https://oreil.ly/LpZ3w)，JIT 编译器的引入使得 PHP 基准测试套件的性能提高了最多三倍！

需要记住的一点是，JIT 编译主要有利于低级算法。这包括数字计算和原始数据操作。除了 CPU 密集型操作（例如图形处理或大量数据库集成）外，其他任何操作对这些更改的受益都不会太大。但是，知道 JIT 编译器的存在，您可以利用它以及以新的方式使用 PHP。

## 操作码缓存

提高性能的最简单方法之一，事实上 JIT 编译器所做的方法之一，是缓存昂贵的操作并引用结果，而不是一遍又一遍地执行这些操作。自版本 5.5 起，PHP 附带了一个可选的内存中缓存预编译字节码的扩展，称为[OPcache](https://oreil.ly/wH2ue)。¹

请记住，PHP 主要是动态脚本解释器，并在程序启动时读取您的脚本。如果您频繁停止和启动应用程序，PHP 将需要重新编译您的脚本以将代码转换为计算机可读的字节码，以便正确执行。频繁的停止/启动可能导致频繁重新编译脚本，从而降低性能。然而，OPcache 允许您选择性地编译脚本，以在应用程序其余部分运行之前向 PHP 提供字节码。这样就无需每次加载和解析脚本了！

###### 注意

自 PHP 8 及以上版本起，JIT 编译器只能在服务器上启用了 OPcache 时才能启用，因为它使用缓存作为其共享内存后端。然而，您不需要使用 JIT 编译器来使用 OPcache 本身。

JIT 编译和操作码缓存都是语言的低级性能改进，您可以在运行时轻松利用它们，但这并不是全部。还要了解如何计时执行用户定义的函数是至关重要的。这使得相对容易地识别业务逻辑中的瓶颈。全面对应用程序进行基准测试也有助于评估在新环境部署、语言新版本发布或后续依赖更新时的性能变化。

以下示例描述了如何对用户应用程序代码进行计时/基准测试，并如何利用语言级别的操作码缓存来优化应用程序和环境的性能。

# 14.1 计时函数执行

## 问题

您希望了解特定函数执行所需的时间，以识别优化的潜在机会。

## 解决方案

利用 PHP 内置的 `hrtime()` 函数在函数执行前后来确定函数运行的时间。例如：

```php
$start = hrtime(true);

doSomethingComputationallyExpensive();

$totalTime = (hrtime(true) - $start) / 1e+9;

echo "Function took {$totalTime} seconds." . PHP_EOL;
```

## 讨论

`hrtime()` 函数将返回系统内置的高分辨率时间，从系统定义的任意时间点开始计数。默认情况下，它返回一个包含两个整数的数组——分别是秒和纳秒。将 `true` 传递给函数将返回总纳秒数，需要通过 `1e+9` 进行除法运算，将原始输出转换回人类可读的秒数。

一个稍微高级的方法是将计时机制抽象为装饰器对象。如第八章所述，装饰器是一种编程设计模式，允许您通过包装它在另一个类实现中来扩展单个函数调用（或整个类）的功能。在这种情况下，您希望触发使用 `hrtime()` 来计时函数的执行时间，而不改变函数本身。示例 14-2 中的装饰器将完全做到这一点。

##### 示例 14-2\. 用于测量函数调用性能的定时装饰器对象

```php
class TimerDecorator
{
    private int $calls = 0;
    private float $totalRuntime = 0.;

    public function __construct(public $callback, private bool $verbose = false) {}

    public function __invoke(...$args): mixed ![1](img/1.png)
    {
        if (! is_callable($this->callback)) {
            throw new ValueError('Class does not wrap a callable function!');
        }

        $this->calls += 1;
        $start = hrtime(true); ![2](img/2.png)

        $value = call_user_func($this->callback, ...$args); ![3](img/3.png)

        $totalTime = (hrtime(true) - $start) / 1e+9;
        $this->totalRuntime += $totalTime;

        if ($this->verbose) {
            echo "Function took {$totalTime} seconds." . PHP_EOL; ![4](img/4.png)
        }

        return $value; ![5](img/5.png)
    }

    public function getMetrics(): array ![6](img/6.png)
    {
        return [
            'calls'   => $this->calls,
            'runtime' => $this->totalRuntime,
            'avg'     => $this->totalRuntime / $this->calls
        ];
    }
}
```

![1](img/#co_performance_tuning_CO2-1)

`__invoke()` 魔术方法使类实例可调用，就像它们是函数一样。使用 `...` 扩展运算符将捕获运行时传递的任何参数，以便稍后传递给包装方法。

![2](img/#co_performance_tuning_CO2-2)

装饰器使用的实际计时机制与解决方案示例中使用的相同。

![3](img/#co_performance_tuning_CO2-3)

假设包装函数可调用，PHP 将调用该函数并由于 `...` 扩展运算符传递所有必要的参数。

![4](img/#co_performance_tuning_CO2-4)

此装饰器的实现可以实例化为具有打印运行时间到控制台的详细标志。

![5](img/#co_performance_tuning_CO2-5)

由于包装函数可能返回数据，您需要确保装饰器也返回该输出。

![6](img/#co_performance_tuning_CO2-6)

由于装饰的函数本身是一个对象，您可以直接公开额外的属性和方法。在这种情况下，装饰器跟踪可以直接检索的聚合指标。

假设与解决方案示例中相同的`doSomethingComputationallyExpensive()`函数是您要测试的函数，则上述装饰器可以包装该函数并像示例 14-3 中显示的那样生成指标。

##### 示例 14-3\. 利用装饰器计时函数执行时间

```php
$decorated = new TimerDecorator('doSomethingComputationallyExpensive');

$decorated(); ![1](img/1.png)

var_dump($decorated->getMetrics()); ![2](img/2.png)
```

![1](img/#co_performance_tuning_CO3-1)

由于装饰器类实现了`__invoke()`魔术方法，您可以像使用函数本身一样使用类的实例。

![2](img/#co_performance_tuning_CO3-2)

结果指标数组将包括调用次数计数，所有调用的总运行时间（秒），以及所有调用的平均运行时间（秒）。

同样地，您可以多次测试相同的包装函数，并从所有调用中获取汇总的运行时指标，如下所示：

```php
$decorated = new TimerDecorator('doSomethingComputationallyExpensive');

for ($i = 0; $i < 10; $i++) {
    $decorated();
}

var_dump($decorated->getMetrics());
```

由于`TimerDecorator`类可以包装任何可调用函数，您可以像使用本地函数一样轻松地用它来装饰类方法。示例 14-4 中的类定义了一个静态方法和一个实例方法，两者都可以被装饰器包装。

##### 示例 14-4\. 用于测试装饰器的简单类定义

```php
class DecoratorFriendly
{
    public static function doSomething()
    {
        // ...
    }

    public function doSomethingElse()
    {
        // ...
    }
}
```

示例 14-5 展示了在 PHP 中如何在运行时将类方法（静态和实例绑定的）作为可调用对象引用。任何可以表示为可调用接口的内容都可以被装饰器包装。

##### 示例 14-5\. 任何可调用接口都可以被装饰器包装

```php
$decoratedStatic = new TimerDecorator(['DecoratorFriendly', 'doSomething']); ![1](img/1.png)
$decoratedStatic(); ![2](img/2.png)

var_dump($decoratedStatic->getMetrics());

$instance = new DecoratorFriendly();

$decoratedMember = new TimerDecorator([$instance, 'doSomethingElse']); ![3](img/3.png)
$decoratedMember(); ![4](img/4.png)

var_dump($decoratedMember->getMetrics());
```

![1](img/#co_performance_tuning_CO4-1)

通过传递类名和其静态方法的名称数组，可以将静态类方法用作可调用对象。

![2](img/#co_performance_tuning_CO4-2)

创建后，装饰的静态方法可以像调用任何其他函数一样调用，并以相同方式生成指标。

![3](img/#co_performance_tuning_CO4-3)

通过传递实例化对象和方法字符串名称的数组，可以将类实例的方法用作可调用对象。

![4](img/#co_performance_tuning_CO4-4)

类似于装饰的静态方法，装饰的实例方法可以像调用任何其他函数一样调用，以在装饰器内部填充指标。

一旦你知道一个函数运行多长时间，你可以专注于优化其执行。这可能涉及重构逻辑或使用替代方法来定义算法。

使用`hrtime()`最初需要 PHP 的 [HRTime 扩展](https://oreil.ly/P_4Fq)，但现在已默认作为核心函数捆绑。如果您使用的是早于 7.3 版本的 PHP 或者显式省略了扩展的预构建分发版本，则该函数本身可能会丢失。在这种情况下，您可以通过 PECL 自行安装扩展，或者改用类似的`microtime()`函数。²

`microtime()` 函数不是从任意时间点开始计算秒数，而是返回自 Unix 纪元以来的微秒数。此函数可用于代替 `hrtime()` 来评估函数执行时间，如下所示：

```php
$start = microtime(true);

doSomethingComputationallyExpensive();

$totalTime = microtime(true) - $start;

echo "Function took {$totalTime} seconds." . PHP_EOL;
```

无论您是像解决方案示例中使用 `hrtime()`，还是像上面的代码片段中使用 `microtime()`，都要确保在读取结果数据时保持一致。这两种机制返回不同精度级别的时间概念，如果在任何输出格式化中混用，可能会导致混淆。

## 参见

PHP 文档关于 [`hrtime()`](https://oreil.ly/AjZ4H) 和 [`microtime()`](https://oreil.ly/r_U84)。

# 14.2 应用程序性能基准测试

## 问题

您希望对整个应用程序的性能进行基准测试，以便评估代码库、依赖项和底层语言版本的变化（例如性能退化）。

## 解决方案

利用像 PHPBench 这样的自动化工具来为您的代码进行仪表化，并定期进行性能基准测试。例如，下面的类被构建用来测试所有可用的哈希算法在不同字符串大小下的性能。³

```php
/**
 * @BeforeMethods("setUp")
 */
class HashingBench
{
    private $string = '';

    public function setUp(array $params): void
    {
        $this->string = str_repeat('X', $params['size']);
    }

    /**
 * @ParamProviders({
 *     "provideAlgos",
 *     "provideStringSize"
 * })
 */
    public function benchAlgos($params): void
    {
        hash($params['algo'], $this->string);
    }

    public function provideAlgos()
    {
        foreach (array_slice(hash_algos(), 0, 20) as $algo) {
            yield ['algo' => $algo];
        }

    }

    public function provideStringSize() {
        yield ['size' => 10];
        yield ['size' => 100];
        yield ['size' => 1000];
    }

}
```

要运行默认的前述示例基准测试，首先克隆 PHPBench，然后安装 Composer 依赖项，最后运行以下命令：

```php
$ ./bin/phpbench run --profile=examples --report=examples --filter=HashingBench
```

一旦基准测试完成，生成的输出将类似于 图 14-1 中的图表。

![PHPBench 示例哈希基准测试的输出指标](img/phpc_1401.png)

###### 图 14-1\. PHPBench 示例哈希基准测试的输出指标

## 讨论

PHPBench 是一种有效的方法，用于在各种情况下对用户定义的代码进行性能基准测试。它可以在开发环境中使用，以评估新代码的性能水平，并可以直接集成到持续集成环境中。

PHPBench 自己的 [GitHub Actions 配置](https://oreil.ly/D6PMf) 在每次拉取请求和更改时都运行应用程序的完整基准测试套件。这使项目维护者可以确保项目在支持的多个 PHP 版本的广泛矩阵中，每次引入更改时都能如预期地继续执行。

任何旨在包含自动化基准测试的项目必须首先使用 Composer。⁴ 您需要利用 Composer 的自动加载功能，使 PHPBench 知道从哪里获取类，但一旦设置完成，您可以按照您希望的方式构建您的项目。

假设您正在构建一个项目，利用值对象和哈希技术来保护它们存储的敏感数据。您的初始 *composer.json* 文件可能看起来像以下内容：

```php
{
    "name": "phpcookbook/valueobjects",
    "require-dev": {
        "phpbench/phpbench": "¹.0"
    },
    "autoload": {
        "psr-4": {
            "Cookbook\\": "src/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "Cookbook\\Tests\\": "tests/"
        }
    },
    "minimum-stability": "dev",
    "prefer-stable": true
}
```

当然，您的项目代码将位于 *src/* 目录中，任何测试、基准测试或其他内容将位于单独的 *tests/* 目录中。仅供基准测试，您还需要创建一个专用的 *tests/Benchmark/* 目录来跟踪名称空间和可筛选的代码。

您的第一个类，即您想要进行基准测试的类，是一个值对象，接受电子邮件地址并且可以像处理字符串一样轻松操作。但是，当它将其内容转储到像 `var_dump()` 或 `print_r()` 这样的调试上下文时，它会自动对值进行哈希处理。

###### 警告

电子邮件是足够常见的格式，即使对数据进行哈希处理也不足以保护其免受真正专注的攻击者。本文中的插图旨在演示如何使用哈希对数据进行*混淆*。这不应被视为全面的安全教程。

在您的新 *src/* 目录中创建如示例 14-6 所定义的类，命名为 *ProtectedString.php*。此类包含很多内容——主要是几个实现的魔术方法，以确保没有办法*意外*序列化对象并访问其内部值。相反，一旦实例化 `ProtectedString` 对象，唯一访问其内容的方法是使用 `::getValue()` 方法。其他任何方法将返回内容的 SHA-256 哈希。

##### 示例 14-6\. 受保护的字符串包装类定义

```php
namespace Cookbook;

class ProtectedString implements \JsonSerializable
{
    protected bool $valid = true;

    public function __construct(protected ?string $value) {}

    public function getValue(): ?string
    {
        return $this->value;
    }

    public function equals(ProtectedString $other): bool
    {
        return $this->value === $other->getValue();
    }

    protected function redacted(): string
    {
        return hash('sha256', $this->value, false);
    }

    public function isValid(): bool
    {
        return $this->valid;
    }

    public function __serialize(): array
    {
        return [
            'value' => $this->redacted()
        ];
    }

    public function __unserialize(array $serialized): void
    {
        $this->value = null;
        $this->valid = false;
    }

    public function jsonSerialize(): mixed
    {
        return $this->redacted();
    }

    public function __toString()
    {
        return $this->redacted();
    }

    public function __debugInfo()
    {
        return [
            'valid' => $this->valid,
            'value' => $this->redacted()
        ];
    }
}
```

您希望验证所选哈希算法的性能。SHA-256 是绝对合理的选择，但是您希望通过性能对所有可能的序列化方式进行基准测试，以便在需要更改到不同哈希算法时，可以确保系统中没有性能退化。

要实际开始对此类进行基准测试，请在项目根目录创建以下 *phpbench.json* 文件：

```php
{
    "$schema": "./vendor/phpbench/phpbench/phpbench.schema.json",
    "runner.bootstrap": "vendor/autoload.php"
}
```

最后，创建一个实际的基准测试来计时用户可以序列化字符串的各种方式。在 *tests/Benchmark/ProtectedStringBench.php* 中定义的示例 14-7 应该存在。

##### 示例 14-7\. 对 `ProtectedString` 类进行基准测试

```php
namespace Cookbook\Tests\Benchmark;

use Cookbook\ProtectedString;

class ProtectedStringBench
{
    public function benchSerialize()
    {
        $data = new ProtectedString('testValue');
        $serialized = serialize($data);
    }

    public function benchJsonSerialize()
    {
        $data = new ProtectedString('testValue');
        $serialized = json_encode($data);
    }

    public function benchStringTypecast()
    {
        $data = new ProtectedString('testValue');
        $serialized = '' . $data;
    }

    public function benchVarExport()
    {
        $data = new ProtectedString('testValue');
        ob_start();
        var_dump($data);
        $serialized = ob_end_clean();
    }
}
```

最后，您可以使用以下 shell 命令运行您的基准测试：

```php
$ ./vendor/bin/phpbench run tests/Benchmark --report=default
```

此命令将生成与 图 14-2 中类似的输出，详细说明每个序列化操作的内存使用和运行时间。

![PHPBench 对带哈希值对象序列化的输出。](img/phpc_1402.png)

###### 图 14-2\. PHPBench 输出，用于带哈希值对象序列化

您的应用程序的每个元素都可以且应该内置基准测试。这将大大简化在新环境（如新的服务器硬件或新发布的 PHP 版本下）中测试应用程序性能的过程。尽可能地将这些基准测试集成到持续集成运行中，并确保测试能够经常运行和记录。

## 另请参阅

[PHPBench 项目](https://oreil.ly/4HCMc)的官方文档。

# 14.3 加速应用程序的 Opcode 缓存

## 问题

想要在你的环境中利用 opcode 缓存来提升应用程序的整体性能。

## 解决方案

在你的环境中安装共享的 OPcache 扩展并在*php.ini*中配置它。⁵ 由于它是一个默认的扩展程序，你只需更新你的配置来启用缓存。以下设置通常建议用于良好的性能，但应根据你特定的应用程序和基础设施进行测试：

```php
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=4000
opcache.revalidate_freq=60
opcache.fast_shutdown=1
opcache.enable=1
opcache.enable_cli=1
```

## 讨论

当 PHP 运行时，解释器会读取你的脚本，并将你友好的 PHP 代码编译成机器易于理解的形式。不幸的是，由于 PHP 不是正式编译的语言，每次加载脚本时都必须进行这种编译。对于简单的应用程序，这并不是什么问题。但对于复杂的应用程序，这可能导致加载时间缓慢和重复请求的高延迟。

环绕这个特定问题优化应用程序的最简单方法是缓存编译后的字节码，以便在后续请求中重复使用。

为了在本地测试和验证 opcode 缓存的功能，你可以在启动脚本时使用命令行的`-d`标志。 `-d`标志在使用时会明确地覆盖配置值，否则会被*php.ini*设置（或保持其默认）。具体来说，示例 14-8 中的命令行标志将利用本地 PHP 开发服务器来完全*禁用*OPcache 运行应用程序。

##### 示例 14-8\. 启动不带 OPcache 支持的本地 PHP Web 服务器

```php
$ php -S localhost:8080 -t public/ -dopcache.enable_cli=0 -dopcache.enable=0
```

类似地，你可以几乎使用完全相同的命令来显式*启用*opcode 缓存，以直接比较你的应用程序的行为和性能，如示例 14-9 所示。

##### 示例 14-9\. 启动带 OPcache 支持的本地 PHP Web 服务器

```php
$ php -S localhost:8080 -t public/ -dopcache.enable_cli=1 -dopcache.enable=1
```

为了全面展示其工作原理，花些时间安装一个使用开源 Symfony 框架的演示应用程序。以下两条命令将在本地将演示应用程序克隆到*/demosite/*目录，并使用 Composer 安装所需的依赖项：

```php
$ composer create-project symfony/symfony-demo demosite
$ cd demosite && composer install
```

接下来，使用内置的 PHP Web 服务器启动应用程序本身。使用示例 14-8 中的命令启动时没有 opcache 支持。该应用程序将在 8080 端口上可用，并且看起来像图 14-3。

![Symfony 演示应用程序的加载页面。](img/phpc_1403.png)

###### 图 14-3\. Symfony 演示应用程序的加载页面

默认应用程序在本地运行，使用轻量级的 SQLite 数据库，因此应该加载得相当快。正如示例 14-10 中所示，你可以在终端中使用 cURL 命令有效地测试加载时间。

##### 示例 14-10\. 用于测量 Web 应用响应时间的简单 cURL 命令

```php
curl -s -w "\nLookup time:\t%{time_namelookup}\
    \nConnect time:\t%{time_connect}\
    \nPreXfer time:\t%{time_pretransfer}\
    \nStartXfer time:\t%{time_starttransfer}\
    \n\nTotal time:\t%{time_total}\n" -o /dev/null \
    http://localhost:8080
```

如果未启用 opcode 缓存，Symfony 演示应用加载总时间约为 ~0.3677 秒。这非常快速，但是请注意，该应用完全在本地环境中运行。在生产环境中，使用远程数据库可能会慢一些，但这是一个坚实的基准。

现在，停止应用并启用 opcode 缓存，使用 示例 14-9 中定义的命令重新启动。然后从 示例 14-10 重新运行 cURL 性能测试。启用 opcode 缓存后，应用现在加载总时间约为 ~0.0371 秒。

这是一个相对简单的默认应用程序，但性能提升了 10 倍对系统性能来说是一个巨大的提升。应用加载得越快，您的系统在同一时间段内就能服务更多客户！

## 另请参阅

PHP 扩展 [OPcache](https://oreil.ly/Tb8rC) 的文档。

¹ PHP 8.0 中发布的新 JIT 编译器在底层使用了 OPcache，但即使 JIT 编译不可用，您仍然可以手动利用缓存来控制系统。

² 更多关于 PECL 和扩展管理的信息，请参考 15.4 配方。

³ 此特定示例来自默认随 PHPBench 发布的 [示例基准](https://oreil.ly/zZE4X)。

⁴ 更多关于使用 Composer 初始化项目的信息，请参阅 15.1 配方。

⁵ OPcache 是一个共享扩展，如果您的 PHP 是使用 `--disable-all` 标志编译以禁用默认扩展，则此扩展不存在。在这种情况下，您只能重新编译 PHP 并设置 `--enable-opcache` 标志，或者安装一个新编译并设置了此标志的 PHP 引擎版本。
