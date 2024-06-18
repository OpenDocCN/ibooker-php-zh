# 第十一章\. 容器

Laravel 的服务容器或依赖注入容器，位于几乎每个其他功能的核心。容器是一个简单的工具，你可以用它来绑定和解析类和接口的具体实例，同时它也是一个强大而微妙的管理器，管理着一个相互关联的依赖网络。在本章中，你将学到更多关于它是什么、如何工作以及如何使用它的知识。

# 命名与容器

在本书、文档以及其他教育资源中，你会注意到容器有很多称呼，比如：

+   应用程序容器

+   IoC（控制反转）容器

+   服务容器

+   DI（依赖注入）容器

所有这些都很有用且有效，但请知道它们指的是同一个东西。它们都是在指服务容器。

# 依赖注入的快速介绍

依赖注入意味着，与其在类内部实例化（“newed up”），每个类的依赖将会从外部*注入*进来。这通常发生在*构造函数注入*中，即在创建对象时注入其依赖。但也有*设置器注入*，类公开一个专门用于注入特定依赖的方法，以及*方法注入*，其中一个或多个方法在调用时期望它们的依赖被注入。

查看 示例 11-1 以获取构造函数注入的快速示例，这是依赖注入最常见的类型之一。

##### 示例 11-1\. 基本依赖注入

```php
<?php

class UserMailer
{
    protected $mailer;

    public function __construct(Mailer $mailer)
    {
        $this->mailer = $mailer;
    }

    public function welcome($user)
    {
        return $this->mailer->mail($user->email, 'Welcome!');
    }
}
```

正如你所见，这个 `UserMailer` 类期望在实例化时注入一个类型为 `Mailer` 的对象，然后其方法引用该实例。

依赖注入的主要好处在于它赋予了我们改变我们注入的自由，为测试而模拟依赖关系，并且仅为共享使用而实例化共享依赖项一次。

# 依赖注入与 Laravel

正如你在 示例 11-1 中看到的那样，依赖注入最常见的模式是构造函数注入，即在对象实例化（“构造”）时注入其依赖。

让我们从 示例 11-1 中获取我们的 `UserMailer` 类。示例 11-2 展示了如何创建和使用它的实例。

##### 示例 11-2\. 简单手动依赖注入

```php
$mailer = new MailgunMailer($mailgunKey, $mailgunSecret, $mailgunOptions);
$userMailer = new UserMailer($mailer);

$userMailer->welcome($user);
```

现在假设我们希望我们的 `UserMailer` 类能够记录消息，并且在发送消息时每次都通知 Slack 频道。示例 11-3 展示了这将是什么样子。正如你所见，如果我们每次想要创建一个新实例都要做所有这些工作，那将变得非常难以管理—尤其是当你考虑到我们必须从某处获取所有这些参数时。

##### 示例 11-3\. 更复杂的手动依赖注入

```php
$mailer = new MailgunMailer($mailgunKey, $mailgunSecret, $mailgunOptions);
$logger = new Logger($logPath, $minimumLogLevel);
$slack = new Slack($slackKey, $slackSecret, $channelName, $channelIcon);
$userMailer = new UserMailer($mailer, $logger, $slack);

$userMailer->welcome($user);
```

想象一下，每次想要`UserMailer`时都需要编写那些代码。依赖注入很棒，但这样做太乱了。

# `app()` 全局助手

在我们深入探讨容器的实际工作方式之前，让我们快速看一下从容器中获取对象的最简单方式：`app()`助手。

将任何字符串传递给该助手，无论是完全合格的类名（FQCN，如`*App\ThingDoer*`）还是 Laravel 的快捷方式（我们稍后会更多讨论），它都会返回该类的一个实例：

```php
$logger = app(Logger::class);
```

这是与容器交互的绝对最简单的方式。它为您创建一个类的实例并返回它，非常简单易行。就像`new Logger`，但是，正如您马上会看到的，要好得多。

正如您在这里所看到的，创建`Logger`实例似乎很简单，但您可能已经注意到我们在示例 11-3 中的`$logger`类有两个参数：`$logPath`和`$minimumLogLevel`。容器如何知道在这里传递什么？

简短的回答是：它不知道。您可以使用`app()`全局助手创建一个在构造函数中没有参数的类的实例，但此时您可以自己运行`new Logger`。当构造函数有一些复杂性时，容器才会发挥作用，这时我们需要看看容器如何确切地能够构造具有构造函数参数的类。

# 容器如何连接

在我们进一步挖掘`Logger`类之前，看看示例 11-4。

##### 示例 11-4\. Laravel 自动装配

```php
class Bar
{
    public function __construct() {}
}

class Baz
{
    public function __construct() {}
}

class Foo
{
    public function __construct(Bar $bar, Baz $baz) {}
}

$foo = app(Foo::class);
```

这看起来与我们邮件发送器示例中的情况类似，但不同之处在于，这些依赖项（`Bar`和`Baz`）都非常简单，容器可以在不需要进一步信息的情况下解决它们。容器读取`Foo`构造函数中的类型提示，解析`Bar`和`Baz`的实例，然后在创建`Foo`实例时将它们注入其中。这就是*自动装配*：根据类型提示解析实例，而开发者无需在容器中显式绑定这些类。

自动装配意味着，如果一个类没有显式绑定到容器（比如在这个上下文中的`Foo`、`Bar`或`Baz`），但容器可以找出如何解决它，那么容器就会解决它。这意味着任何没有构造函数依赖的类（比如`Bar`和`Baz`），以及容器可以解决其构造函数依赖的类（比如`Foo`），都可以从容器中解析出来。

这意味着我们只需要绑定那些具有无法解析的构造函数参数的类——例如，我们在示例 11-3 中的`$logger`类，它具有与我们的日志路径和日志级别相关的参数。

对于这些内容，我们需要学习如何显式地将某些内容绑定到容器中。

# 将类绑定到容器

将一个类绑定到 Laravel 的容器本质上是告诉容器：“如果开发者要求`Logger`的一个实例，这里是实例化它并返回正确的参数和依赖项的代码运行方式。”

我们教容器，当有人请求特定的字符串（通常是类的完全限定类名时），应该以这种方式解析它。

## 绑定到一个闭包

因此，让我们看看如何绑定到容器。请注意，绑定到容器的适当位置是服务提供者的`register()`方法中（参见示例 11-5）。

##### 示例 11-5\. 基本容器绑定

```php
// In any service provider (maybe LoggerServiceProvider)
public function register(): void
{
    $this->app->bind(Logger::class, function ($app) {
        return new Logger('\log\path\here', 'error');
    });
}
```

在这个示例中有几个重要的事情需要注意。首先，我们运行了`$this``->app->bind()`。`$this->app`是每个服务提供者上始终可用的容器实例。容器的`bind()`方法是我们用来绑定到容器的方法。

`bind()`的第一个参数是我们绑定的“键”。在这里，我们使用了类的完全限定类名（FQCN）。第二个参数取决于你正在做什么，但基本上它应该是*某些东西*，用于告诉容器如何解析绑定键的实例。

因此，在这个例子中，我们传递了一个闭包。现在，每当有人运行`app(Logger::class)`时，他们将得到这个闭包的结果。闭包被传递了容器本身的实例(`$app`)，所以如果你要解析的类有一个依赖项，你希望从容器中解析出来，你可以在你的定义中使用它，就像在示例 11-6 中看到的那样。

##### 示例 11-6\. 在容器绑定中使用传递的`$app`实例

```php
// Note that this binding is not doing anything technically useful, since this
// could all be provided by the container's autowiring already.
$this->app->bind(UserMailer::class, function ($app) {
    return new UserMailer(
        $app->make(Mailer::class),
        $app->make(Logger::class),
        $app->make(Slack::class)
    );
});
```

请注意，每次请求你的类的新实例时，这个闭包将被重新运行，并返回新的输出。

## 绑定到单例、别名和实例

如果你希望绑定闭包的输出被缓存，以便不必每次请求实例时都重新运行该闭包，那就是单例模式，你可以运行`$this->app->singleton()`来实现。示例 11-7 展示了这是什么样子。

##### 示例 11-7\. 将单例绑定到容器

```php
public function register(): void
{
    $this->app->singleton(Logger::class, function () {
        return new Logger('\log\path\here', 'error');
    });
}
```

如果你已经拥有你想让单例返回的对象实例，也可以获得类似的行为，如示例 11-8 所示。

##### 示例 11-8\. 将现有的类实例绑定到容器

```php
public function register(): void
{
    $logger = new Logger('\log\path\here', 'error');
    $this->app->instance(Logger::class, $logger);
}
```

最后，如果你想将一个类别名为另一个类，或者将一个快捷方式绑定到一个类，你可以简单地传递两个字符串，如示例 11-9 所示。

##### 示例 11-9\. 类和字符串的别名

```php
// Asked for Logger, give FirstLogger
$this->app->bind(Logger::class, FirstLogger::class);

// Asked for log, give FirstLogger
$this->app->bind('log', FirstLogger::class);

// Asked for log, give FirstLogger
$this->app->alias(FirstLogger::class, 'log');
```

请注意，这些快捷方式在 Laravel 的核心中很常见；它提供了一套快捷方式系统，用于提供核心功能的类，使用易于记忆的键，如`log`。

## 将一个具体实例绑定到接口

就像我们可以将一个类绑定到另一个类，或者将一个类绑定到一个快捷方式一样，我们也可以绑定到一个接口。这非常强大，因为我们现在可以用接口类型提示而不是类名，就像在示例 11-10 中那样。

##### 示例 11-10\. 接口类型提示和绑定

```php
...
use Interfaces\Mailer as MailerInterface;

class UserMailer
{
    protected $mailer;

    public function __construct(MailerInterface $mailer)
    {
        $this->mailer = $mailer;
    }
}
```

```php
// Service provider
public function register(): void
{
    $this->app->bind(\Interfaces\Mailer::class, function () {
        return new MailgunMailer(...);
    });
}
```

您现在可以在代码的各个地方使用`Mailer`或`Logger`接口类型提示，然后在服务提供者中选择一次要在整个应用程序中使用的特定邮件发送器或记录器。这就是控制反转。

使用此模式的一个关键好处是，以后，如果您决定使用不同于 Mailgun 的邮件提供程序，只要您有一个实现`Mailer`接口的新提供程序的邮件发送器类，您可以在服务提供者中进行一次更改，而其余代码将继续正常工作。

## 上下文绑定

有时候，您需要根据上下文更改接口的解析方式。您可能希望从一个地方将事件日志记录到本地系统日志，而从其他地方记录到外部服务。因此，让我们告诉容器区分—查看示例 11-11。

##### 示例 11-11\. 上下文绑定

```php
// In a service provider
public function register(): void
{
    $this->app->when(FileWrangler::class)
        ->needs(Interfaces\Logger::class)
        ->give(Loggers\Syslog::class);

    $this->app->when(Jobs\SendWelcomeEmail::class)
        ->needs(Interfaces\Logger::class)
        ->give(Loggers\PaperTrail::class);
}
```

# 在 Laravel 框架文件中的构造函数注入

我们已经讨论了构造函数注入的概念，并且已经看过容器如何轻松地从容器中解析类或接口的实例。您看到了使用`app()`助手来创建实例是多么简单，以及当创建类时，容器将解析其构造函数依赖项。

我们尚未涵盖的是容器还负责解析应用程序核心操作类中的许多类。例如，每个控制器都是由容器实例化的。这意味着如果您希望在控制器中获取日志记录器的实例，您可以简单地在控制器的构造函数中类型提示日志记录器类，当 Laravel 创建控制器时，它将从容器中解析它，并且该日志记录器实例将对您的控制器可用。看看示例 11-12。

##### 示例 11-12\. 向控制器注入依赖项

```php
...
class MyController extends Controller
{
    protected $logger;

    public function __construct(Logger $logger)
    {
        $this->logger = $logger;
    }

    public function index()
    {
        // Do something
        $this->logger->error('Something happened');
    }
}
```

容器负责解析控制器、中间件、队列作业、事件侦听器以及在应用程序生命周期过程中由 Laravel 自动生成的任何其他类—​因此，这些类中的任何一个都可以在其构造函数中类型提示依赖项，并期望它们自动注入。

# 方法注入

在应用程序中有一些地方，Laravel 不仅仅读取构造函数签名：它还会读取*方法*签名，并且还会为您注入依赖项。

在控制器方法中使用方法注入最常见。如果您只想在单个控制器方法中使用某个依赖项，您可以像在示例 11-13 中那样，仅将其注入到该方法中。

##### 示例 11-13\. 向控制器方法注入依赖项

```php
...
class MyController extends Controller
{
    // Method dependencies can come after or before route parameters.
    public function show(Logger $logger, $id)
    {
        // Do something
        $logger->error('Something happened');
    }
}
```

你可以在服务提供者的`boot()`方法中做同样的事情，你也可以使用容器任意调用任何类的方法，在那里可以进行方法注入（参见示例 11-14）。

##### 示例 11-14\. 使用容器的`call()`方法手动调用类方法

```php
class Foo
{
    public function bar($parameter1) {}
}

// Calls the 'bar' method on 'Foo' with a first parameter of 'value'
app()->call('Foo@bar', ['parameter1' => 'value']);
```

# 门面和容器

在本书中，我们已经相当详细地介绍了门面，但实际上我们并没有讨论它们的工作原理。

Laravel 的门面是提供对 Laravel 核心功能的简单访问的类。门面有两个显著特点：首先，它们都在全局命名空间中可用（`\Log`是`\Illuminate\Support\Facades\Log`的别名）；其次，它们使用静态方法访问非静态资源。

让我们看一下`Log`门面，因为在本章中我们已经在讨论日志记录。在你的控制器或视图中，你可以使用这个调用：

```php
Log::alert('Something has gone wrong!');
```

这是没有使用门面进行相同调用的情况：

```php
$logger = app('log');
$logger->alert('Something has gone wrong!');
```

正如你所看到的，门面将静态调用（在类本身上使用`::`进行的任何方法调用，而不是在实例上进行的）转换为实例上的普通方法调用。

# 导入门面命名空间

如果你在有命名空间的类中，你会想确保在顶部导入门面：

```php
...
use Illuminate\Support\Facades\Log;

class Controller extends Controller
{
    public function index()
    {
        // ...
        Log::error('Something went wrong!');
    }
```

## 门面是如何工作的

让我们看一下`Cache`门面，看看它实际是如何工作的。

首先，打开`Illuminate\Support\Facades\Cache`类。你会看到类似于示例 11-15 的东西。

##### 示例 11-15\. `Cache`门面类

```php
<?php

namespace Illuminate\Support\Facades;

class Cache extends Facade
{
    protected static function getFacadeAccessor()
    {
        return 'cache';
    }
}
```

每个门面都有一个单一的方法：`getFacadeAccessor()`。这定义了 Laravel 应该使用哪个键从容器中查找该门面的后备实例。

在这个例子中，我们可以看到每次调用`Cache`门面都会被代理为调用容器中`cache`快捷方式的实例。当然，这不是一个真实的类或接口名称，所以我们知道它是我之前提到的那些快捷方式之一。

所以，这里是实际发生的事情：

```php
Cache::get('key');

// Is the same as...

app('cache')->get('key');
```

有几种方法可以查找每个门面访问器实际指向的类，但检查文档是最简单的。在[门面文档页面](https://oreil.ly/IRsgc)上有一张表格显示了每个门面连接到哪个容器绑定（比如`cache`这样的快捷方式），以及返回的类。它看起来像这样：

| 门面 | 类 | 服务容器绑定 |
| --- | --- | --- |
| `App` | `Illuminate\Foundation\Application` | `app` |
| …​ | …​ | …​ |
| `Cache` | `Illuminate\Cache\CacheManager` | `cache` |
| …​ | …​ | …​ |

现在你有了这个参考，你可以做三件事情。

首先，你可以找出门面上有哪些方法。只需找到其后备类并查看该类的定义，你就会知道其所有公共方法在门面上都是可调用的。

第二，您可以找出如何使用依赖注入注入门面的后备类。如果您希望使用依赖注入而不是门面的功能，只需类型提示门面的后备类或使用`app()`获取其实例，并调用您本应在门面上调用的相同方法。

第三，您可以看到如何创建自己的门面。创建一个门面类，扩展`Illuminate\Support\Facades\Facade`，并为其添加一个`getFacadeAccessor()`方法，该方法返回一个字符串。使该字符串成为可以从容器中解析出您的后备类的东西——也许只是类的完全限定类名。最后，在*config/app.php*中的`aliases`数组中添加该门面以注册门面。完成！您刚刚创建了自己的门面。

## 实时门面

与创建新类使得您的类的实例方法作为静态方法可用不同，*实时外观*允许您简单地使用`Facades\`前缀您类的完全限定类名，并*像使用门面一样*使用它。示例 11-16 展示了这如何工作。

##### 示例 11-16\. 使用实时外观

```php
namespace App;

class Charts
{
    public function burndown()
    {
        // ...
    }
}
```

```php
<h2>Burndown Chart</h2>
{{ Facades\App\Charts::burndown() }}
```

正如您在这里所看到的，非静态方法`burndown()`作为实时外观的静态方法变得可访问，我们通过在类的完整名称前加上`Facades\`来创建。

# 服务提供者

我们在前一章节中介绍了服务提供者的基础知识（参见“服务提供者”）。关于容器最重要的是您要记住，在某个服务提供者的`register()`方法中注册您的绑定。

您可以将松散的绑定直接添加到`App\Providers\AppServiceProvider`中，这有点像万金油，但通常最好为您开发的每一组功能创建一个独特的服务提供者，并在其独特的`register()`方法中绑定其类。

# 测试

使用控制反转和依赖注入的能力使得在 Laravel 中进行测试变得非常灵活。例如，您可以根据应用程序是在线还是在测试中绑定不同的日志记录器。或者，您可以将事务性电子邮件服务从 Mailgun 更改为本地电子邮件记录器以便轻松检查。这些交换实际上非常常见，以至于使用 Laravel 的*.env*配置文件更加简单，但您也可以使用任何您想要的接口或类进行类似的交换。

最简单的方法是在测试中直接重新绑定类和接口，当您需要它们重新绑定时。示例 11-17 展示了如何操作。

##### 示例 11-17\. 在测试中重写绑定

```php
public function test_it_does_something()
{
    app()->bind(Interfaces\Logger, function () {
        return new DevNullLogger;
    });

    // Do stuff
}
```

如果您需要为您的测试全局重新绑定某些类或接口（这并不是特别常见的情况），您可以在测试类的`setUp()`方法中或在 Laravel 的`TestCase`基本测试的`setUp()`方法中进行操作，就像示例 11-18 所示。

##### 示例 11-18\. 重写所有测试的绑定

```php
class TestCase extends \Illuminate\Foundation\Testing\TestCase
{
    public function setUp()
    {
        parent::setUp();

        app()->bind('whatever', 'whatever else');
    }
}
```

当使用类似 Mockery 这样的工具时，通常会创建一个类的模拟对象或间谍对象或存根对象，然后将其重新绑定到容器中，以取代其原有的引用。

# 简而言之

Laravel 的服务容器有许多名称，但无论你如何称呼它，最终它的目标都是简化定义如何将某些字符串名称解析为具体实例。这些字符串名称可以是类或接口的完全限定类名，或者像 `log` 这样的快捷方式。

每个绑定都告诉应用程序，给定一个字符串键（例如，`app('log')`），如何解析出一个具体的实例。

容器足够智能，可以进行递归依赖项解析。因此，如果尝试解析具有构造函数依赖项的实例，容器将尝试基于它们的类型提示解析这些依赖项，然后将它们传递给您的类，并最终返回一个实例。

绑定到容器的方式有几种，但最终它们都定义了在给定特定字符串时返回什么。

门面是简单的快捷方式，使得可以在根命名空间别名类上使用静态调用，以调用容器中解析出的类的非静态方法。实时门面允许您将任何类视为门面，只需在其完全限定类名之前加上 `Facades\` 即可。
