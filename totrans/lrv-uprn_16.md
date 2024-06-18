# 第十六章. 队列、作业、事件、广播和调度器

到目前为止，我们已经涵盖了一些支持 Web 应用程序的最常见结构：数据库、邮件、文件系统等等。这些在大多数应用程序和框架中都很常见。

Laravel 还提供了一些不太常见的架构模式和应用程序结构的工具。在本章中，我们将介绍 Laravel 实现队列、排队作业、事件和 WebSocket 事件发布的工具。我们还将介绍 Laravel 的调度器，它使手动编辑的 cron 调度成为过去时。

# 队列

要理解什么是队列，只需想象一下在银行排队的概念。即使有多条线—​队列—​每次也只有一个人从每条队列中被服务，并且每个人最终都会到达前面并被服务。在某些银行中，这是严格的先进先出策略，但在其他银行中，并不能完全保证某个时刻不会有人插队。基本上，有人可以被加入队列，被过早移出队列，或者成功“处理”然后被移除。有时候，有人可能会到达队列的前面，但无法正确地得到服务，于是又返回队列一段时间，然后再次被处理。

编程中的队列非常相似。你的应用程序向队列添加一个“作业”，这是一段代码，告诉应用程序如何执行特定的行为。然后，另一个单独的应用程序结构，通常是“队列工作者”，负责逐个从队列中取出作业并执行适当的行为。队列工作者可以删除作业，延迟返回到队列，或标记为成功处理。

Laravel 提供了使用 Redis、*beanstalkd*、Amazon Simple Queue Service (SQS) 或数据库表格来轻松服务队列的工具。你也可以选择 `sync` 驱动程序，在你的应用程序中直接运行作业而不实际排队，或者选择 `null` 驱动程序让作业被丢弃；这两者通常用于本地开发或测试环境。

## 为什么要使用队列？

队列使得轻松地将昂贵或慢速的过程从任何同步调用中移除变得可能。最常见的例子是发送邮件—​这样做可能很慢，而你不希望用户在等待邮件发送响应他们的操作时被阻塞。相反，你可以触发一个“发送邮件”队列作业，让用户继续他们的日常工作。有时候，你可能不关心节省用户的时间，但你可能有像 cron 作业或 webhook 这样需要大量工作量的进程；与其让所有工作一次性运行（可能导致超时），你可以选择逐个将其作业加入队列，让队列工作者逐个处理。

另外，如果您有一些处理繁重的工作，超出了服务器的处理能力，您可以启动多个队列工作程序，以比您的正常应用服务器更快的速度处理您的队列。

## 基本队列配置

就像许多其他 Laravel 功能一样，它们抽象了多个提供程序，队列也有自己的专用配置文件（*config/queue.php*），允许您设置多个驱动程序，并定义默认驱动程序。这也是您将存储 SQS、Redis 或*beanstalkd*身份验证信息的地方。

# Laravel Forge 上的简单 Redis 队列

[Laravel Forge](http://forge.laravel.com) 是由 Laravel 创建者 Taylor Otwell 提供的托管管理服务，使得通过 Redis 服务队列变得轻松。您创建的每个服务器都会自动配置 Redis，因此，如果您访问任何站点的 Forge 控制台，只需转到队列选项卡并点击“启动工作者”，您就可以准备好使用 Redis 作为队列驱动程序；您可以保留所有默认设置，无需进行其他工作。

## 排队作业

记得我们的银行类比吗？银行队列中的每个人（行）在编程术语中称为*作业*。根据环境的不同，排队的作业可以采用多种形式，如数据数组或简单字符串。在 Laravel 中，每个作业都是一个包含作业名称、数据有效载荷、到目前为止已尝试处理此作业的次数以及一些其他简单元数据的信息集合。

但是在与 Laravel 的交互中，您无需担心任何这些。Laravel 提供了一个称为`Job`的结构，旨在封装单个任务—​您的应用程序可以被命令执行的行为—​并允许将其添加到队列中并从中提取。还有简单的助手函数，使排队 Artisan 命令和邮件变得容易。

让我们从一个示例开始，每当用户在您的 SaaS 应用程序中更改他们的计划时，您希望重新运行一些关于整体利润的计算。

### 创建一个作业

像往常一样，都有一个 Artisan 命令：

```php
php artisan make:job CrunchReports
```

查看 示例 16-1 ，看看您将得到什么。

##### 示例 16-1\. Laravel 中作业的默认模板

```php
<?php

namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldBeUnique;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class CrunchReports implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    /**
 * Create a new job instance.
 */
    public function __construct()
    {
        //
    }

    /**
 * Execute the job.
 */
    public function handle(): void
    {
        //
    }
}
```

正如您所看到的，这个模板导入了`Dispatchable`、`InteractsWithQueue`、`Queueable`和`SerializesModels`特性，并实现了`ShouldQueue`接口。

我们还从这个模板中获得了两种方法：构造函数，您将使用它来附加数据到作业中，以及`handle()`方法，这是作业逻辑应驻留的地方（也是您将用于注入依赖项的方法签名）。

特征和接口提供了向类添加到队列并与之交互的能力。`Dispatchable`为其提供了调度自身的方法；`InteractsWithQueue`允许每个作业在处理时控制其与队列的关系，包括删除或重新排队自身；`Queueable`允许您指定 Laravel 如何将此作业推送到队列；而`SerializesModels`使作业能够序列化和反序列化 Eloquent 模型。

# 序列化模型

`SerializesModels`特征使作业能够*序列化*（转换为可以存储在数据库或队列系统等数据存储中的更平坦格式）注入的模型，以便您的作业的`handle()`方法可以访问它们。然而，由于可靠地序列化整个 Eloquent 对象太困难，该特征确保在将作业推送到队列时，仅序列化附加的 Eloquent 对象的主键。当作业反序列化并处理时，该特征会通过它们的主键从数据库中重新获取这些 Eloquent 模型。这意味着当您的作业运行时，它将从数据库中获取这个模型的最新实例，而不是您排队作业时的状态。

让我们填写我们示例类的方法，就像示例 16-2 那样。

##### 示例 16-2\. 一个示例作业

```php
...
use App\ReportGenerator;

class CrunchReports implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    protected $user;

    public function __construct($user)
    {
        $this->user = $user;
    }

    public function handle(ReportGenerator $generator): void
    {
        $generator->generateReportsForUser($this->user);

        Log::info('Generated reports.');
    }
}
```

我们期望在创建作业时注入`User`实例，并且在处理时，我们使用了`ReportGenerator`类的类型提示（我们可能编写了该类）。Laravel 将读取类型提示并自动注入该依赖项。

### 将作业推送到队列

有多种方法可以调度作业，包括每个控制器都可以使用的某些方法和全局的`dispatch()`辅助程序。但更简单和首选的方法是在作业本身上调用`dispatch()`方法，这就是我们本章剩余部分要做的事情。

要调度您的作业，只需创建其实例，然后调用其`dispatch()`方法，并直接传入任何必要的数据。请查看示例 16-3 了解示例。

##### 示例 16-3\. 调度作业

```php
$user = auth()->user();
$daysToCrunch = 7;
\App\Jobs\CrunchReports::dispatch($user, $daysToCrunch);
```

有三个设置可以控制确切如何调度作业：连接、队列和延迟。

#### 自定义连接

如果您同时存在多个队列连接，可以在`dispatch()`方法后链式调用`onConnection()`来自定义连接：

```php
DoThingJob::dispatch()->onConnection('redis');
```

#### 自定义队列

在队列服务器中，您可以指定将作业推送到哪个命名队列。例如，您可以根据其重要性区分队列，将一个命名为`low`，另一个命名为`high`。

您可以使用`onQueue()`方法自定义要将作业推送到哪个队列：

```php
DoThingJob::dispatch()->onQueue('high');
```

#### 自定义延迟

您可以使用 `delay()` 方法自定义队列工作者在处理作业之前应等待的时间，该方法接受一个整数（表示延迟作业的秒数）或 `*DateTime*` / `*Carbon*` 实例：

```php
// Delays five minutes before releasing the job to queue workers
$delay = now()->addMinutes(5);
DoThingJob::dispatch()->delay($delay);
```

请注意，Amazon SQS 不允许超过 15 分钟的延迟。

### 作业链

如果您需要一系列作业按顺序运行，可以将它们“链”在一起。每个作业将等待上一个作业完成后运行，如果一个作业失败，其后的作业将不会运行。

```php
$user = auth()->user();
$daysToCrunch = 7;

Bus::chain([
    new CrunchReports($user, $daysToCrunch),
    new SendReport($user),
])->dispatch();
```

当链式作业之一失败时，您可以使用`catch()`方法执行：

```php
$user = auth()->user();
$daysToCrunch = 7;

Bus::chain([
    new CrunchReports($user, $daysToCrunch),
    new NotifyNewReportsDone($user)
])->catch(function (Throwable $e) {
    new ReportsNotCrunchedNotification($user)
})->dispatch($user);
```

### 作业批处理

作业批处理使得能够同时将一组作业推送到队列中，检查批处理的状态，并在批处理完成后采取行动成为可能。

此功能需要一个数据库表来跟踪作业；正如您可能期望的那样，有一个 Artisan 命令来创建它：

```php
php artisan queue:batches-table
php artisan migrate
```

要将作业标记为可批处理，包括 `Illuminate\Bus\Batchable` 特性。此特性将在您的作业中添加一个 `batch()` 方法，允许您检索有关当前作业批次的信息。

查看 示例 16-4 了解其工作原理。在这个示例中，您可以看到可批处理作业中最重要的一步是确保如果其批处理已取消，则不执行任何操作。

##### 示例 16-4\. Laravel 中的可批处理作业

```php
...
class SampleBatchableJob implements ShouldQueue
{
    use Batchable, Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function handle(): void
    {
        // Don't run if this batch is canceled
        if ($this->batch()->cancelled()) {
            return;
        }

        // Otherwise, run like normal
        // ...
    }
}
```

#### 调度可批处理作业

`Bus` 门面提供了一个 `batch()` 方法，允许您调度一批作业。您还可以使用 `then()`（成功时）、`catch()`（失败时）或 `finally()`（成功或失败时）方法定义批处理成功或失败后要执行的操作。

您可以查看 示例 16-5 中如何调用它们。

##### 示例 16-5\. 调度可批处理作业

```php
use App\Jobs\CrunchReports;
use Illuminate\Support\Facades\Bus;

$user = auth()->user();
$admin = User::admin()->first();
$supervisor = User::supervisor()->first();

$daysToCrunch = 7;

Bus::batch([
    new CrunchReports::dispatch($user, $daysToCrunch),
    new CrunchReports::dispatch($admin, $daysToCrunch),
    new CrunchReports::dispatch($supervisor, $daysToCrunch)
])->then(function (Batch $batch) {
    // Run when the batch is completed successfully
})->catch(function (Batch $batch, Throwable $e) {
    // Run when any job fails
})->finally(function (Batch $batch) {
    // Run when the batch is complete
})->dispatch();
```

#### 从作业中添加作业到批次

如果您的批处理作业负责向批处理中添加作业——例如，如果您最初调度了几个作业调度器类型的作业——它们可以在由`batch()`返回的`Batch`对象上使用`add()`方法：

```php
public function handle(): void
{
    if ($this->batch()->cancelled()) {
        return;
    }

    $this->batch()->add([
        new \App\Jobs\ImportContacts,
        new \App\Jobs\ImportContacts,
        new \App\Jobs\ImportContacts,
    ]);
}
```

#### 取消批处理

如果作业有理由取消其批处理，则可以：

```php
public function handle(): void
{
    if (/* This batch should be canceled for whatever reason */) {
        return $this->batch()->cancel();
    }

    // ...
}
```

#### 批处理失败

默认情况下，如果批处理中的单个作业失败，批处理将标记为“已取消”。如果您想定义不同的行为，可以在调度批处理时链式调用 `allowFailures()`：

```php
$batch = Bus::batch([
    // ...
])->allowFailures()->dispatch();
```

#### 清理批次表

批次表不会自我修剪，因此您需要计划您的应用程序定期“修剪”该表：

```php
$schedule->command('queue:prune-batches')->daily();
```

## 运行队列工作者

那么队列工作者是什么，它是如何工作的？在 Laravel 中，它是一个运行到手动停止之前（直到手动停止）的 Artisan 命令，负责从队列中拉取作业并运行它们：

```php
php artisan queue:work
```

此命令启动一个守护进程来“监听”您的队列；每当队列中有作业时，它将拉取第一个作业，处理它，然后删除它，并继续下一个。如果在任何时候队列中没有作业，它会“休眠”一段可配置的时间，然后再次检查是否有更多作业。

您可以定义作业在队列监听器停止之前允许运行的秒数（`--timeout`），当没有作业时监听器应“休眠”的秒数（`--sleep`），每个作业允许的重试次数（`--tries`），工作器应该监听的连接（`queue:work`后的第一个参数），以及它应该监听的队列（`--queue=`）：

```php
php artisan queue:work redis --timeout=60 --sleep=15 --tries=3
 --queue=high,medium
```

您还可以使用`php artisan queue:work`处理单个作业。

## 处理错误

那么，当处理中的作业出现问题时会发生什么？

### 处理异常情况

如果抛出异常，队列监听器将释放该作业回到队列中。作业将被重新释放以再次处理，直到成功完成或达到队列监听器允许的最大尝试次数为止。

### 限制重试次数

最大重试次数由传递给`queue:listen`或`queue:work` Artisan 命令的`--tries`开关定义。

# 无限重试的危险

如果您没有设置`--tries`，或者将其设置为`0`，队列监听器将允许无限重试。这意味着如果有任何情况下一个工作可能*永远*无法完成——例如，如果它依赖于一个已经被删除的推文——您的应用程序将因为无限重试而逐渐停滞。

[文档](https://oreil.ly/7BIW-)和 Laravel Forge 都将`3`作为建议的最大重试次数的起点。因此，在困惑时，从这里开始并进行调整：

```php
php artisan queue:work --tries=3
```

如果您想要随时检查作业已尝试的次数，请在作业本身使用`attempts()`方法，例如示例 16-6。

##### 示例 16-6\. 检查作业已尝试的次数

```php
public function handle(): void
{
    ...
    if ($this->attempts() > 3) {
        //
    }
}
```

您还可以通过在作业类本身定义`$tries`属性来指定给定作业可以重试的最大次数。当指定时，此值将优先于使用`--tries`开关设置的值：

```php
public $tries = 3;
```

您可以在作业类中设置`$maxExceptions`属性，以指定作业可以抛出异常（因此可以重试）多少次，然后应该被视为失败：

```php
// Can attempt this job 10 times.
public $tries = 10;

// If the job fails 3 times because an exception was thrown,
// stop attempting the job and fail it.
public $maxExceptions = 3;
```

您还可以指定作业何时超时，指示框架在指定时间范围内尝试任意次数的作业。您可以在作业上指定`retryUntil()`方法，并从中返回一个`DateTime/Carbon`实例：

```php
public function retryUntil()
{
    return now()->addSeconds(30);
}
```

### 基于作业的重试延迟

我们可以通过在作业上设置`$retryAfter`属性来指定在重新尝试失败的作业之前等待多长时间，等效于等待的分钟数。对于更复杂的计算，我们可以定义一个`retryAfter`方法，该方法也应返回等待的分钟数：

```php
public $retryAfter = 10;

public function retryAfter() {...}
```

### 作业中间件

我们可以通过中间件运行作业，就像我们通过中间件运行 HTTP 请求一样。这是提取保护或验证作业或它们运行条件的逻辑的好机会：

```php
<?php

namespace App\Jobs\Middleware;

use Illuminate\Http\Response;

class MyMiddleware
{
    public function handle($job, $next): Response
    {
        if ($something) {
            $next($job);
        } else {
            $job->release(5);
        }
    }
}
```

要为作业分配一个中间件，指定作业类中的`middleware()`方法：

```php
...
use App\Jobs\Middleware\MyMiddleware;

...
public function middleware()
{
    return [new MyMiddleware];
}
```

你还可以在调度作业时使用`through`方法指定一个中间件：

```php
DoThingJob::dispatch()->through([new MyMiddleware]);
```

#### 作业的速率限制中间件

Laravel 默认带有一个作业速率限制中间件。要使用它，在服务提供者的`boot()`方法中使用`RateLimiter::for()`定义速率限制器，如示例 16-7 所示。

##### 示例 16-7\. 一个示例作业的速率限制中间件

```php
// In a service provider
public function boot(): void
{
    RateLimiter::for('imageConversions', function (object $job) {
        return $job->user->paidForPriorityConversions()
            ? Limit::none()
            : Limit::perHour(1)->by($job->user->id);
    });
}
```

作业速率限制中间件的语法与路由速率限制中间件相同（“速率限制”）。

### 处理失败的作业

一旦一个作业超过了允许的重试次数，它被视为“失败”作业。在做任何其他事情之前，即使你只想限制作业的尝试次数，你也需要创建一个“失败的作业”数据库表。

有一个 Artisan 命令来创建迁移（然后你会想要迁移）：

```php
php artisan queue:failed-table
php artisan migrate
```

任何超过允许的最大尝试次数的作业都会被倾倒在那里。但是，你可以对失败的作业做很多事情。

首先，你可以在作业本身定义一个`failed()`方法，在作业失败时运行（参见示例 16-8）。

##### 示例 16-8\. 定义一个作业失败时运行的方法

```php
...
class CrunchReports implements ShouldQueue
{
    ...

    public function failed()
    {
        // Do whatever you want, like notify an admin
    }
}
```

接下来，你可以注册一个全局的失败作业处理程序。在应用程序的启动过程中的任何位置，如果你不知道该放在哪里，只需将代码放在`AppServiceProvider`的`boot()`方法中，像示例 16-9 一样定义一个监听器。

##### 示例 16-9\. 注册一个全局处理程序来处理失败的作业

```php
// Some service provider
use Illuminate\Support\Facades\Queue;
use Illuminate\Queue\Events\JobFailed;
// ...
    public function boot(): void
    {
        Queue::failing(function (JobFailed $event) {
            // $event->connectionName
            // $event->job
            // $event->exception
        });
    }
```

还有一套用于与失败作业表交互的 Artisan 工具。

`queue:failed`显示你的失败作业列表：

```php
php artisan queue:failed
```

列表看起来像这样：

```php
+----+------------+---------+----------------------+---------------------+
| ID | Connection | Queue   | Class                | Failed At           |
+----+------------+---------+----------------------+---------------------+
| 9  | database   | default | App\Jobs\AlwaysFails | 2018-08-26 03:42:55 |
+----+------------+---------+----------------------+---------------------+
```

然后，你可以获取任何单个失败作业的 ID，并使用`queue:retry`重试它：

```php
php artisan queue:retry 9
```

如果你宁愿重试所有作业，而不是传递 ID，请传递`all`：

```php
php artisan queue:retry all
```

你可以使用`queue:forget`删除一个单独的失败作业：

```php
php artisan queue:forget 5
```

你可以删除所有超过一定时间的失败作业（默认为 24 小时，但也可以使用`--hours=48`传递自定义小时数）：

```php
php artisan queue:prune-failed
```

你可以使用`queue:flush`删除所有失败的作业：

```php
php artisan queue:flush
```

## 控制队列

有时，在作业的处理过程中，你可能希望添加条件，可能会将作业释放以后重新启动，或永久删除作业。

要将作业释放回队列，使用`release()`方法，如示例 16-10 所示。

##### 示例 16-10\. 将作业释放回队列

```php
public function handle()
{
    ...
    if (condition) {
        $this->release($numberOfSecondsToDelayBeforeRetrying);
    }
}
```

如果你想在处理过程中删除一个作业，你可以随时使用`return`，如示例 16-11 所示；这是向队列发送的信号，表明作业已适当处理，不应返回到队列。

##### 示例 16-11\. 删除作业

```php
public function handle(): void
{
    // ...
    if ($jobShouldBeDeleted) {
        return;
    }
}
```

## 支持其他功能的队列

队列的主要用途是将作业推送到其中，但您也可以使用 `Mail::queue` 功能排队邮件。您可以在“队列”中了解更多信息。您还可以排队 Artisan 命令，我们在第八章中介绍过。

# Laravel Horizon

Laravel Horizon，就像我们涵盖过的其他一些工具（如 Scout、Passport 等），是 Laravel 提供的一个工具，不随核心捆绑。

Horizon 提供了有关您的 Redis 队列作业状态的见解。您可以看到哪些作业失败了，有多少作业在排队，以及它们的工作速度，甚至可以在任何队列超载或失败时收到通知。Horizon 仪表板显示在图 16-1 中。

安装和运行 Horizon 相对简单，文档也很详细，因此如果您有兴趣，请查看[Horizon 文档](https://oreil.ly/6tpkn)了解如何安装、配置和部署它。

请注意，您需要在您的 *.env* 或 *config/queue.php* 配置文件中将队列连接设置为 `redis`，以便运行 Horizon。

![Horizon 仪表板的截图](img/lur3_1601.png)

###### 图 16-1\. Horizon 仪表板

# 事件

对于作业，调用代码通知应用程序应该*做些什么*：`CrunchReports` 或 `NotifyAdminOfNewSignup`。

对于事件，调用代码通知应用程序*发生了什么*：`UserSubscribed`、`UserSignedUp` 或 `ContactWasAdded`。*事件*是通知发生了某事的方式。

框架本身可能会“触发”其中一些事件。例如，当保存、创建或删除 Eloquent 模型时，会触发事件。但是，应用程序的代码也可以手动触发某些事件。

触发事件本身不会做任何事情。但是，您可以绑定*事件监听器*，它们的唯一目的是监听特定事件的广播并响应。任何事件可以有从零到多个事件监听器。

Laravel 的事件结构类似于观察者或“发布/订阅”模式。许多事件被发送到应用程序中；有些可能从不被监听，而其他一些可能有十几个监听器。这些事件不知道也不关心。

## 触发事件

有三种方法可以触发一个事件。您可以使用 `Event` 门面，注入 `Dispatcher`，或者使用 `event()` 全局辅助函数，如示例 16-12 所示。

##### 示例 16-12\. 触发事件的三种方法

```php
Event::fire(new UserSubscribed($user, $plan));
// or
$dispatcher = app(Illuminate\Contracts\Events\Dispatcher::class);
$dispatcher->fire(new UserSubscribed($user, $plan));
// or
event(new UserSubscribed($user, $plan));
```

如果不确定，我建议使用全局辅助函数。

要创建要触发的事件，请使用 `make:event` Artisan 命令：

```php
php artisan make:event UserSubscribed
```

这将生成一个类似于示例 16-13 的文件。

##### 示例 16-13\. Laravel 事件的默认模板

```php
<?php

namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class UserSubscribed
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    /**
 * Create a new event instance.
 */
    public function __construct()
    {
        //
    }

    /**
 * Get the channels the event should broadcast on.
 *
 * @return array<int, \Illuminate\Broadcasting\Channel>
 */
    public function broadcastOn(): array
    {
        return [
            new PrivateChannel('channel-name'),
        ];
    }
}
```

让我们来看看这里得到了什么。`SerializesModels` 就像作业一样工作；它允许您接受 Eloquent 模型作为参数。`InteractsWithSockets`、`ShouldBroadcast` 和 `broadcastOn()` 方法为使用 WebSockets 广播事件提供了支持功能，稍后我们将详细介绍。

或许奇怪的是这里没有 `handle()` 或 `fire()` 方法。但请记住，此对象的存在并不是为了确定特定的动作，而只是为了封装一些数据。第一个数据片段是其名称；`UserSubscribed` 告诉我们发生了特定事件（用户订阅）。其余数据是我们传递到构造函数并与此实体关联的任何数据。

示例 16-14 显示了我们可能希望在 `UserSubscribed` 事件中执行的操作。

##### 示例 16-14\. 向事件中注入数据

```php
...
class UserSubscribed
{
    use InteractsWithSockets, SerializesModels;

    public $user;
    public $plan;

    public function __construct($user, $plan)
    {
        $this->user = $user;
        $this->plan = $plan;
    }
}
```

现在我们有了一个恰当表示发生事件的对象：`$event->user` 订阅了 `$event->plan` 计划。记住，触发此事件就像 `event(new UserSubscribed($user, $plan))` 这样简单。

## 监听事件

我们已经有一个事件及其触发能力。现在让我们看看如何监听它。

首先，我们将创建一个事件监听器。假设我们希望每当新用户订阅时都向应用程序所有者发送电子邮件：

```php
php artisan make:listener EmailOwnerAboutSubscription --event=UserSubscribed
```

这给我们提供了 示例 16-15 文件。

##### 示例 16-15\. Laravel 事件监听器的默认模板

```php
<?php

namespace App\Listeners;

use App\Events\UserSubscribed;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class EmailOwnerAboutSubscription
{
    /**
 * Create the event listener.
 */
    public function __construct()
    {
        //
    }

    /**
 * Handle the event.
 */
    public function handle(UserSubscribed $event): void
    {
        //
    }
}
```

这就是发生动作的地方——`handle()` 方法所在的地方。此方法期望接收一个 `UserSubscribed` 类型的事件，并对其做出响应。

所以，让我们让它发送一封电子邮件（示例 16-16）。

##### 示例 16-16\. 一个示例事件监听器

```php
...
use App\Mail\UserSubscribed as UserSubscribedMessage;

class EmailOwnerAboutSubscription
{
    public function handle(UserSubscribed $event): void
    {
        Log::info('Emailed owner about new user: ' . $event->user->email);

        Mail::to(config('app.owner-email'))
            ->send(new UserSubscribedMessage($event->user, $event->plan);
    }
}
```

现在，最后一项任务：我们需要设置此监听器以侦听 `UserSubscribed` 事件。我们将在 `EventServiceProvider` 类的 `$listen` 属性中执行此操作（参见 示例 16-17）。

##### 示例 16-17\. 将监听器绑定到 `EventServiceProvider` 中的事件

```php
class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        \App\Events\UserSubscribed::class => [
            \App\Listeners\EmailOwnerAboutSubscription::class,
        ],
    ];
```

正如您所看到的，每个数组条目的键是事件类的类名，值是监听器类名的数组。我们可以在 `UserSubscribed` 键下添加尽可能多的类名，它们都将监听并响应每个 `UserSubscribed` 事件。

### 自动事件发现

您还可以指示 Laravel 自动连接事件及其匹配的监听器，而无需在 `EventServiceProvider` 中手动绑定它们。此功能称为*自动事件发现*，默认情况下禁用，但可以通过在 `EventServiceProvider` 中设置 `shouldDiscoverEvents()` 方法返回 `true` 来启用：

```php
/**
 * Determine if events and listeners should be automatically discovered.
 */
public function shouldDiscoverEvents(): bool
{
    return true;
}
```

如果启用此功能，Laravel 将根据监听器中的类型提示将事件映射到它们的匹配监听器。这将导致每个请求都要进行匹配，这会给您的应用程序引入一小段延迟，但像许多慢速功能一样，您可以使用 `php artisan event:cache` 缓存这些查找，并使用 `php artisan event:clear` 清除缓存。

### 事件订阅者

还有一种结构可以用来定义事件和其监听器之间的关系。Laravel 中有一个称为 *事件订阅者* 的概念，它是一个包含一组方法的类，这些方法充当独立事件的监听器，并包含应该处理哪个事件的映射。在这种情况下，展示比解释更容易理解，请查看 示例 16-18。请注意，事件订阅者不是特别常用的工具。

##### 示例 16-18\. 一个样例事件订阅者

```php
<?php

namespace App\Listeners;

class UserEventSubscriber
{
    public function onUserSubscription($event)
    {
        // Handles the UserSubscribed event
    }

    public function onUserCancellation($event)
    {
        // Handles the UserCanceled event
    }

    public function subscribe($events)
    {
        $events->listen(
            \App\Events\UserSubscribed::class,
            'App\Listeners\UserEventSubscriber@onUserSubscription'
        );

        $events->listen(
            \App\Events\UserCanceled::class,
            'App\Listeners\UserEventSubscriber@onUserCancellation'
        );
    }
}
```

订阅者需要定义一个 `subscribe()` 方法，该方法传递了一个事件分发器的实例。我们将使用它将事件与它们的监听器配对，但在这种情况下，这些监听器是这个类的方法，而不是整个类。

作为提醒，每当你看到像 `@` 这样的内联内容时，它意味着类名在 `@` 的左边，方法名在右边。因此，在 示例 16-18 中，我们定义了该订阅者的 `onUserSubscription()` 方法将监听任何 `UserSubscribed` 事件。

我们还需要做最后一件事情：在 `App\Providers\EventServiceProvider` 中，我们需要将我们的订阅者类名添加到 `$subscribe` 属性中，如 示例 16-19 所示。

##### 示例 16-19\. 注册事件订阅者

```php
...
class EventServiceProvider extends ServiceProvider
{
    ...
    protected $subscribe = [
        \App\Listeners\UserEventSubscriber::class
    ];
}
```

# 通过 WebSockets 广播事件，以及 Laravel Echo

WebSocket（通常称为 WebSockets）是一种协议，由 Pusher（一个托管的 WebSocket SaaS 服务）推广，它简化了在 web 设备之间提供几乎实时通信。与依赖通过 HTTP 请求传递信息不同，WebSockets 库打开了客户端和服务器之间的直接连接。WebSockets 背后的工具如 Gmail 和 Facebook 中的聊天框，您无需等待页面重新加载或 Ajax 请求接收或发送数据；相反，数据实时发送和接收。

WebSockets 最适合使用小块数据以发布/订阅结构传递，就像 Laravel 的事件一样。Laravel 内置了一套工具集，可以轻松定义一个或多个事件应广播到 WebSocket 服务器；例如，很容易定义一个 `MessageWasReceived` 事件，该事件在您的应用程序接收到消息时即时发布到特定用户或一组用户的通知框中。

## 配置与设置

查看 *config/broadcasting.php* 可以找到事件广播的配置设置。Laravel 支持三种驱动程序用于广播：Pusher，一个付费的 SaaS 提供商；Redis，用于本地运行的 WebSocket 服务器；以及 `log`，用于本地开发和调试。

# 队列监听器

为了使事件广播快速进行，Laravel 将广播事件的指令推送到队列上。这意味着您需要运行一个队列工作程序（或者在本地开发时使用 `sync` 队列驱动程序）。请参阅 “运行队列工作程序” 以了解如何运行队列工作程序。

Laravel 建议在队列工作程序寻找新作业之前默认延迟三秒钟。然而，通过事件广播，您可能会注意到某些事件需要一两秒才能广播。为了加快速度，请将队列设置更新为在寻找新作业之前等待一秒钟。

## 广播事件

要广播事件，您需要将该事件标记为广播事件，并使其实现 `Illuminate\Contracts\Broadcasting\ShouldBroadcast` 接口。该接口要求您添加 `broadcastOn()` 方法，该方法将返回一个字符串或 `Channel` 对象数组，每个表示一个 WebSocket 通道。

示例 16-20 展示了我们的 `UserSubscribed` 事件，修改为在两个通道上进行广播：一个用于用户（确认用户的订阅），另一个用于管理员（通知他们有新的订阅）。

##### 示例 16-20\. 在多个通道上进行广播的事件

```php
...
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class UserSubscribed implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $user;
    public $plan;

    public function __construct($user, $plan)
    {
        $this->user = $user;
        $this->plan = $plan;
    }

    public function broadcastOn(): array
    {
        // String syntax
        return [
            'users.' . $this->user->id,
            'admins'
        ];

        // Channel object syntax
        return [
            new Channel('users.' . $this->user->id),
            new Channel('admins'),
            // If it were a private channel: new PrivateChannel('admins'),
            // If it were a presence channel: new PresenceChannel('admins'),
        ];
    }
}
```

默认情况下，事件的任何公共属性将被序列化为 JSON 并作为广播事件的数据发送。这意味着我们的广播 `UserSubscribed` 事件的数据可能类似于 示例 16-21。

##### 示例 16-21\. 示例广播事件数据

```php
{
    'user': {
        'id': 5,
        'name': 'Fred McFeely',
        ...
    },
    'plan': 'silver'
}
```

您可以通过从事件的 `broadcastWith()` 方法返回数据数组来覆盖此行为，如 示例 16-22 所示。

##### 示例 16-22\. 自定义广播事件数据

```php
public function broadcastWith()
{
    return [
        'userId' => $this->user->id,
        'plan' => $this->plan
    ];
}
```

您可以通过在事件类上设置 `$broadcastQueue` 属性来自定义将事件推送到哪个队列：

```php
public $broadcastQueue = 'websockets-for-faster-processing';
```

您可能会选择这样做，以防止其他队列项目减慢事件广播的速度；如果排队中的长时间运行作业使事件无法及时发送，则实时 WebSocket 将不会很有趣。

您还可以通过使其实现 `ShouldBroadcastNow` 合同（如 示例 16-23 所示）来强制使某个事件完全跳过队列（使用“sync”队列驱动程序，由当前 PHP 线程处理）。

##### 示例 16-23\. 强制事件跳过广播队列

```php
use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;

class UserSubscribed implements ShouldBroadcastNow
{
    //
}
```

最后，您可以选择通过为其添加 `broadcastWhen()` 方法来自定义是否应完全广播给定事件，如 示例 16-24 所示：

##### 示例 16-24\. 有条件地确定是否应广播事件

```php
public function broadcastWhen()
{
    // Notify me only when users sign up from the White House
    return Str::contains($this->user->email, 'whitehouse.gov');
}
```

## 接收消息

截至本书出版时，Laravel 开发者最常用的解决方案是 [Pusher](https://pusher.com)。某些规模以上的计划需要付费，但有一个慷慨的免费计划。Pusher 极大地简化了设置简单 WebSocket 服务器的过程，其 JavaScript SDK 几乎不需要你的任何工作即可处理所有的认证和频道管理。SDK 可用于 iOS、Android 和许多其他平台、语言和框架。

如果你想要托管自己的与 Pusher 兼容的 WebSockets 服务器，有两个很好的选择。首先，你可以尝试一个基于 Laravel 的工具，名为 [Laravel WebSockets](https://oreil.ly/p8fyJ)。你可以将这个包安装到你当前的 Laravel 应用程序中（与你正在广播的相同应用程序），或者安装到一个单独的微服务中。

第二，如果你使用 Docker（包括 Sail），你可以安装 [Soketi](https://soketi.app)，这是一个由 TypeScript 开发的免费 Pusher 替代品。

如果你选择与 Pusher 不同的服务器一起工作，你将按照本书中与 Pusher 工作的所有指导进行操作，但是你的配置设置将有所不同。

即使你最终选择使用 Echo，了解如何监听 Laravel 的广播事件也是有帮助的。但由于这里的大部分代码在使用 Echo 时是不必要的，我建议先阅读本节，然后再阅读 “Laravel Echo (JavaScript 部分)”，你可以决定你更喜欢哪种方式，然后从那里开始编写你的代码。

要开始使用，引入 Pusher 的库，从你的 Pusher 帐户获取 API 密钥，并使用类似于 示例 16-25 中的代码订阅任何频道上的任何事件是很有帮助的。

##### 示例 16-25\. Pusher 的基本使用

```php
...
<script src="https://js.pusher.com/4.3/pusher.min.js"></script>
<script>
// Enable Pusher logging - don't include this in production
Pusher.logToConsole = true;

// Globally, perhaps; just a sample of how to get data in
var App = {
    'userId': {{ auth()->id() }},
    'pusherKey': '{{ config('broadcasting.connections.pusher.key') }}'
};

// Locally
var pusher = new Pusher(App.pusherKey, {
    cluster: '{{ config('broadcasting.connections.pusher.options.cluster') }}',
    encrypted: {{ config('broadcasting.connections.pusher.options.encrypted') }}
});

var pusherChannel = pusher.subscribe('users.' + App.userId);

pusherChannel.bind('App\\Events\\UserSubscribed', (data) => {
    console.log(data.user, data.plan);
});
</script>
```

# 在 JavaScript 中转义反斜杠

由于 `\` 是 JavaScript 中的控制字符，你需要写 `\\` 来表示字符串中的反斜杠，这就是为什么在 示例 16-25 中每个命名空间段之间有两个反斜杠的原因。

要从 Laravel 发布到 Pusher，从你的 Pusher 帐户仪表板获取你的 Pusher 密钥、密钥、集群和应用程序 ID，然后在你的 *.env* 文件中分别设置它们为 `PUSHER_KEY`、`PUSHER_SECRET`、`PUSHER_APP_CLUSTER` 和 `PUSHER_APP_ID` 键。

如果你提供你的应用程序，访问一个页面，并在其中嵌入来自 示例 16-25 的 JavaScript，在一个窗口中推送广播事件或从终端中进行操作，有一个队列监听器正在运行或者正在使用 `sync` 驱动，并且所有的认证信息都设置正确，那么你应该能够在几乎实时地在你的 JavaScript 窗口的控制台中看到事件日志弹出。

有了这种能力，你现在可以轻松地在用户在你的应用中时随时更新他们的数据状态。你可以通知用户其他用户的操作，刚刚完成的长时间运行的过程，或者你的应用对外部动作（如传入的电子邮件或 Webhook）的响应。可能性无限。

# 要求

如果你想要使用 Pusher 或 Redis 进行广播，你需要引入以下依赖：

+   Pusher：`pusher/pusher-php-server "~3.0"`

+   Redis：`predis/predis`

## 高级广播工具

Laravel 还有一些工具，可以在事件广播中执行更复杂的交互。这些工具是框架功能和 JavaScript 库的组合，称为*Laravel Echo*。

当你在 JavaScript 前端使用 Laravel Echo 时，这些框架功能表现最佳（我们将在“Laravel Echo (the JavaScript Side)”中介绍），但你仍然可以在不使用 JavaScript 组件的情况下享受 Echo 的一些好处。Echo 可以与 Pusher 和 Redis 一起使用，但我将在示例中使用 Pusher。

### 排除当前用户的广播事件

每个与 Pusher 的连接都分配了一个唯一的“socket ID”，用于标识该套接字连接。很容易定义任何给定套接字（用户）应该被排除在接收特定广播事件之外。

此功能使得可以定义某些事件不应广播给触发它们的用户。假设团队中的每个用户在其他用户创建任务时都会收到通知；你是否希望在刚创建的任务中收到通知？不，这就是我们有`toOthers()`方法的原因。

要实现这一点，需要按照两个步骤进行。首先，当 WebSocket 连接初始化时，你需要设置你的 JavaScript 发送到`/broadcasting/socket`的特定`POST`。这会将你的`socket_id`附加到你的 Laravel 会话中。Echo 会为你完成这一步骤，但你也可以手动完成——查看[Echo 源码](https://oreil.ly/3Ww0U)了解其工作原理。

接下来，你需要更新每个 JavaScript 发出的请求，以包含包含`socket_id`的`X-Socket-ID`头部。示例 16-26 展示了如何在 Axios 或 jQuery 中实现这一点。请注意，你的事件必须使用`Illuminate\Broadcasting\InteractsWithSockets`特性以调用`toOthers()`方法。

##### 示例 16-26\. 使用 Axios 或 jQuery 发送每个 Ajax 请求时发送 socket ID

```php
// Run this right after you initialize Echo
// With Axios
window.axios.defaults.headers.common['X-Socket-Id'] = Echo.socketId();

// With jQuery
$.ajaxSetup({
    headers: {
        'X-Socket-Id': Echo.socketId()
    }
});
```

处理完这些后，你可以使用`broadcast()`全局助手而不是`event()`全局助手，并在其后链式调用`toOthers()`，从而排除任何事件被广播给触发它的用户：

```php
broadcast(new UserSubscribed($user, $plan))->toOthers();
```

### 广播服务提供程序

Echo 提供的所有其他功能都需要你的 JavaScript 与服务器进行身份验证。查看`App\Providers\BroadcastServiceProvider`，在那里你将定义如何授权用户访问你的私有和存在通道。

您可以采取的两个主要操作是定义将在广播授权路由上使用的中间件，以及为您的通道定义授权设置。

如果您要使用这些功能，需要取消注释 *config/app.php* 中的 `App\Providers\BroadcastServiceProvider::class` 行。

如果您要使用这些功能而不使用 Laravel Echo，您将需要手动处理在身份验证请求中发送 CSRF 令牌，或者通过将它们添加到 `VerifyCsrfToken` 中间件的 `$except` 属性中，排除 `/broadcasting/auth` 和 `/broadcasting/socket` 的 CSRF 保护。

#### 绑定 WebSocket 通道的授权定义

私有和存在的 WebSocket 通道需要能够 ping 您的应用程序，以了解当前用户是否对该通道授权。您将使用 `Broadcast::channel()` 方法在 *routes/channels.php* 文件中定义此授权的规则。

# 公共、私有和存在通道

WebSockets 中有三种类型的通道：公共、私有和存在：

公共通道

可由任何用户订阅，无论是否经过身份验证。

私有通道

要求最终用户的 JavaScript 对应用程序进行身份验证，以证明用户既经过身份验证又被授权加入此通道。

存在通道

一种私有通道类型，但不是用于消息传递，而是仅跟踪加入和离开通道的用户，并使此信息可供应用程序的前端使用。

`Broadcast::channel()` 接受两个参数：首先是表示要匹配的通道的字符串，其次是定义如何为匹配该字符串的任何通道授权用户的闭包。闭包将以当前用户的 Eloquent 模型作为其第一个参数，并将任何匹配的 *`variableNameHere`* 段作为附加参数传递。例如，具有字符串 `teams.*teamId*` 的通道授权定义，当与通道 `teams.5` 匹配时，将闭包 `$user` 作为第一个参数，并将 `5` 作为第二个参数传递。

如果您正在定义私有通道的规则，则您的 `Broadcast::channel()` 闭包需要返回一个布尔值：此用户是否对此通道授权？如果您正在为存在通道定义规则，则您的闭包应返回一个数据数组，您希望这些数据在任何要显示在通道中的用户中可用。示例 16-27 演示了如何定义这两种通道的规则。

##### 示例 16-27\. 为私有和存在的 WebSocket 通道定义授权规则

```php
...
// routes/channels.php

// Define how to authenticate a private channel
Broadcast::channel('teams.{teamId}', function ($user, $teamId) {
    return (int) $user->team_id === (int) $teamId;
});

// Define how to authenticate a presence channel; return any data
// you want the app to have about the user in the channel
Broadcast::channel('rooms.{roomId}', function ($user, $roomId) {
    if ($user->rooms->contains($roomId)) {
        return [
            'name' => $user->name
        ];
    }
});
```

您可能想知道如何将此信息从您的 Laravel 应用程序传递到您的 JavaScript 前端。Pusher 的 JavaScript 库将向您的应用程序发送 `POST` 请求；默认情况下，它将命中 `/pusher/auth`，但您可以自定义它（Echo 会自动为您自定义它）以命中 Laravel 的认证路由 `/broadcasting/auth`：

```php
var pusher = new Pusher(App.pusherKey, {
    authEndpoint: '/broadcasting/auth'
});
```

示例 16-28 展示了如何调整 示例 16-25 来处理私有和存在频道，*不使用* Echo 的前端组件。

##### 示例 16-28\. 使用 Pusher 处理私有和存在频道的基本用法

```php
...
<script src="https://js.pusher.com/4.3/pusher.min.js"></script>
<script>
    // Enable Pusher logging - don't include this in production
    Pusher.logToConsole = true;

    // Globally, perhaps; just a sample of how to get data in
    var App = {
        'userId': {{ auth()->id() }},
        'pusherKey': '{{ config('broadcasting.connections.pusher.key') }}'
    };

    // Locally
    var pusher = new Pusher(App.pusherKey, {
        cluster: '{{ config('broadcasting.connections.pusher.options.cluster') }}',
        encrypted: {{ config('broadcasting.connections.pusher.options.encrypted') }},
        authEndpoint: '/broadcasting/auth'
    });

    // Private channel
    var privateChannel = pusher.subscribe('private-teams.1');

    privateChannel.bind('App\\Events\\UserSubscribed', (data) => {
        console.log(data.user, data.plan);
    });

    // Presence channel
    var presenceChannel = pusher.subscribe('presence-rooms.5');

    console.log(presenceChannel.members);
</script>
```

现在我们可以根据用户是否通过给定频道的授权规则发送 WebSocket 消息。我们还可以跟踪哪些用户在站点的特定组或部分活跃，并向每个用户显示有关同一组中其他用户的相关信息。

## Laravel Echo（JavaScript 部分）

Laravel Echo 包含两个部分：我们刚刚介绍过的高级框架功能，以及利用这些功能并大幅减少所需编写的样板代码的 JavaScript 包。Echo JavaScript 包能够轻松处理认证、授权以及订阅私有和存在频道。Echo 可以与 Pusher SDK（适用于 Pusher 或自定义 Pusher 兼容服务器）或 `socket.io` 一起使用。

### 将 Echo 引入到您的项目中

要在项目的 JavaScript 中使用 Echo，请使用 `npm install --save` 将其添加到 *package.json*（确保还引入适当的 Pusher 或 `socket.io` SDK）：

```php
npm install pusher-js laravel-echo --save
```

假设您有一个基本的 Vite 文件来编译您的 *app.js*，就像在 Laravel 的默认安装设置中一样。

Laravel 默认的 *resources/js/app.js* 结构提供了一个很好的示例，展示了如何最佳初始化您的 Echo 安装。看看 示例 16-29 来了解它在该文件和 *resources/js/bootstrap.js* 之间的工作方式。

##### 示例 16-29\. 在 app.js 和 bootstrap.js 中初始化 Echo

```php
// app.js
require('./bootstrap');

// ... lots of Vue stuff ...

// Add your Echo bindings here
```

```php
// bootstrap.js
import Echo from "laravel-echo";

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_PUSHER_APP_KEY,
    cluster: process.env.MIX_PUSHER_APP_CLUSTER
});
```

为了 CSRF 保护，您还需要将 `csrf-token <meta>` 标签添加到 HTML 模板中：

```php
<meta name="csrf-token" content="{{ csrf_token() }}">
```

当然，还要记得在 HTML 模板中链接到编译后的 *app.js*：

```php
<script src="{{ asset('js/app.js') }}"></script>
```

现在我们准备开始了。

# 使用 Laravel WebSockets 服务器包时的配置更改

如果您正在使用 Laravel WebSockets 服务器（使用之前讨论的包，见 “接收消息”），则 示例 16-29 中的配置细节会有些不同。有关更多信息，请参阅 [Laravel WebSockets 包文档](https://oreil.ly/iL6Yl)。

### 使用 Echo 进行基本事件广播

这与我们已经使用 Pusher 的方式没有什么不同，但 示例 16-30 是一个简单的代码示例，展示了如何使用 Echo 监听公共频道获取基本事件信息。

##### 示例 16-30\. 使用 Echo 监听公共频道

```php
var currentTeamId = 5; // Likely set elsewhere

Echo.channel(`teams.${currentTeamId}`)
    .listen('UserSubscribed', (data) => {
        console.log(data);
    });
```

Echo 提供了几种订阅不同类型频道的方法；`channel()`将你订阅到一个公共频道。请注意，当你使用 Echo 监听事件时，你可以忽略完整的事件命名空间，只需监听这个事件的唯一类名即可。

现在我们可以访问传递给事件的公共数据，以`data`对象表示。我们也可以链式使用`listen()`处理程序，就像在示例 16-31 中那样。

##### 示例 16-31\. 在 Echo 中链式事件监听器

```php
Echo.channel(`teams.${currentTeamId}`)
    .listen('UserSubscribed', (data) => {
        console.log(data);
    })
    .listen('UserCanceled', (data) => {
        console.log(data);
    });
```

# 记得编译和包含！

如果你尝试了这些代码示例，却没有看到浏览器中有任何变化，确保运行`npm run dev`（如果你在本地运行）或者`npm run build`（用于构建一次）来编译你的代码。而且，如果还没有的话，确保确实在你的模板中包含*app.js*。

### 私有频道和基本认证

Echo 还有一个订阅私有频道的方法：`private()`。它的工作方式与`channel()`相同，但要求你在*routes/channel.php*中设置频道授权定义，就像我们之前讨论的那样。此外，与 SDK 不同的是，你不需要在频道名前加上`private-`。

示例 16-32 展示了如何监听名为`private-teams.5`的私有频道。

##### 示例 16-32\. 使用 Echo 监听私有频道

```php
var currentTeamId = 5; // Likely set elsewhere

Echo.private(`teams.${currentTeamId}`)
    .listen('UserSubscribed', (data) => {
        console.log(data);
    });
```

### 存在频道

Echo 使加入和监听存在频道的事件变得更简单。这次你会想要使用`join()`方法来绑定到频道，就像在示例 16-33 中那样。

##### 示例 16-33\. 加入存在频道

```php
var currentTeamId = 5; // Likely set elsewhere

Echo.join(`teams.${currentTeamId}`)
    .here((members) => {
        console.log(members);
    });
```

`join()`订阅存在频道，而`here()`允许你定义用户加入时以及其他用户加入或离开时的行为。

你可以把存在频道想象成聊天室中的“在线用户”侧边栏。当你首次加入存在频道时，你的`here()`回调将被调用，并提供那时所有成员的列表。任何成员加入或离开时，该回调将再次被调用，带上更新后的列表。这里没有消息传递，但你可以播放声音、更新页面上的成员列表，或者根据这些动作做任何其他响应。

还有特定事件的特定方法，你可以单独使用或者链式使用（参见示例 16-34）。

##### 示例 16-34\. 监听特定存在事件

```php
var currentTeamId = 5; // Likely set elsewhere

Echo.join('teams.' + currentTeamId)
    .here((members) => {
        // Runs when you join
        console.table(members);
    })
    .joining((joiningMember, members) => {
        // Runs when another member joins
        console.table(joiningMember);
    })
    .leaving((leavingMember, members) => {
        // Runs when another member leaves
        console.table(leavingMember);
    });
```

### 排除当前用户

我们在本章中已经讨论过这个问题，但是如果你想排除当前用户，可以使用`broadcast()`全局助手，而不是`event()`全局助手，然后在广播调用后链式使用`toOthers()`方法。但是使用 Echo 时，这个 JavaScript 部分已经为你处理好了，它会正常工作。

正如你所见，Echo JavaScript 库并不会做任何你自己不能做的事情——但它使许多常见任务变得更简单，并为常见 WebSocket 任务提供了更干净、更表达性强的语法。

### 使用 Echo 订阅通知

Laravel 的通知默认带有广播驱动程序，将通知作为广播事件推送出去。你可以使用 `Echo.notification()` 订阅这些通知，如 Example 16-35 中所示。

##### 示例 16-35\. 使用 Echo 订阅通知

```php
Echo.private(`App.User.${userId}`)
    .notification((notification) => {
        console.log(notification.type);
    });
```

### 客户端事件

如果你想在不让消息甚至触及你的 Laravel 应用程序的情况下，即快速地在用户之间发送性能卓越的消息，例如发送“正在输入…”通知，你可以使用 Echo 的 `whisper()` 方法，如示例 Example 16-36 所示。

##### 示例 16-36\. 使用 Echo 的 `whisper()` 方法绕过 Laravel 服务器

```php
Echo.private('room')
    .whisper('typing', {
        name: this.user.name
    });
```

然后使用 `listenForWhisper()` 来监听，如 Example 16-37 中所示。

##### 示例 16-37\. 使用 Echo 监听 whisper 事件

```php
Echo.private('room')
    .listenForWhisper('typing', (e) => {
        console.log(e.name);
    });
```

# 调度器

如果你以前写过 cron 任务，你很可能已经希望有一个更好的工具。语法不仅繁琐且令人沮丧，难以记住，而且它是你的应用程序中无法存储在版本控制中的重要部分之一。

Laravel 的调度器使处理预定任务变得简单。你会在代码中编写你的预定任务，然后指向你的应用程序一个 cron 任务：每分钟运行 `php artisan schedule:run`。每次运行此 Artisan 命令时，Laravel 都会检查你的调度定义，以查看是否应该运行任何预定任务。

这是定义该命令的 cron 任务：

```php
* * * * * cd /home/myapp.com && php artisan schedule:run >> /dev/null 2>&1
```

你可以安排许多任务类型，并使用许多时间框架来安排它们。

*app/Console/Kernel.php* 中有一个名为 `schedule()` 的方法，你可以在其中定义你想要调度的任何任务。

## 可用的任务类型

首先，让我们看看最简单的选项：一个闭包，每分钟运行一次 (Example 16-38)。每次 cron 任务执行 `schedule:run` 命令时，它都会调用此闭包。

##### 示例 16-38\. 安排一个闭包每分钟运行一次

```php
// app/Console/Kernel.php
public function schedule(Schedule $schedule): void
{
    $schedule->call(function () {
        CalculateTotals::dispatch();
    })->everyMinute();
}
```

你可以安排的另外两种任务类型是 Artisan 和 shell 命令。

你可以通过以与从命令行调用它们完全相同的语法来安排 Artisan 命令：

```php
$schedule->command('scores:tally --reset-cache')->everyMinute();
```

你还可以运行任何你可以用 PHP 的 `exec()` 方法运行的 shell 命令：

```php
$schedule->exec('/home/myapp.com/bin/build.sh')->everyMinute();
```

## 可用的时间框架

调度器的美妙之处不仅在于你可以用代码定义你的任务；同样你也可以用代码调度它们。Laravel 会跟踪时间的流逝，并评估是否到了运行任何给定任务的时间。对于 `everyMinute()` 来说很简单：运行任务的答案始终是简单的。但是 Laravel 也会为你保持其余的简单，即使是对于最复杂的请求也是如此。

让我们从一个简单的 Laravel 定义开始，通过看一个巨大的定义选项：

```php
$schedule->call(function () {
    // Runs once a week on Sunday at 23:50
})->weekly()->sundays()->at('23:50');
```

注意，我们可以链接不同的时间设置：定义频率、指定星期几和时间，当然还可以做更多。

表 16-1 显示了调度作业时可用的潜在日期/时间修饰符列表。

表 16-1\. 用于调度的日期/时间修饰符

| Command | 描述 |
| --- | --- |
| `->timezone('America/Detroit')` | 设置调度任务的时区 |
| `->cron('* * * * * *')` | 使用传统的 cron 表达式定义调度时间 |
| `->everyMinute()` | 每分钟运行 |
| `->everyTwoMinutes()` | 每 2 分钟运行 |
| `->everyThreeMinutes()` | 每 3 分钟运行 |
| `->everyFourMinutes()` | 每 4 分钟运行 |
| `->everyFiveMinutes()` | 每 5 分钟运行 |
| `->everyTenMinutes()` | 每 10 分钟运行 |
| `->everyFifteenMinutes()` | 每 15 分钟运行 |
| `->everyThirtyMinutes()` | 每 30 分钟运行 |
| `->hourly()` | 每小时运行 |
| `->hourlyAt(14)` | 每小时 14 分钟运行 |
| `->everyTwoHours()` | 每 2 小时运行 |
| `->everyThreeHours()` | 每 3 小时运行 |
| `->everyFourHours()` | 每 4 小时运行 |
| `->everySixHours()` | 每 6 小时运行 |
| `->daily()` | 每天午夜运行 |
| `->dailyAt('14:00')` | 每天 14:00 运行 |
| `->twiceDaily(1, 14)` | 每天 1:00 和 14:00 运行 |
| `->twiceDailyAt(1, 14, 6)` | 每天 1:06 和 14:06 运行（第三个参数是开始分钟数） |
| `->weekly()` | 每周运行（每周星期日午夜） |
| `->weeklyOn(5, '10:00')` | 每周五的 10:00 运行 |
| `->monthly()` | 每月运行（每月 1 日午夜） |
| `->monthlyOn(15, '23:00')` | 每月 15 日的 23:00 运行 |
| `->quarterly()` | 每季度运行（每年 1 月、4 月、7 月和 10 月的午夜） |
| `->yearly()` | 每年运行（每年 1 月 1 日午夜） |
| `->yearlyOn(6)` | 每年运行（每年 6 月 1 日午夜） |
| `->when(closure)` | 仅在闭包返回`true`时运行任务 |
| `->skip(closure)` | 仅在闭包返回`false`时运行任务 |
| `->between('8:00', '12:00')` | 仅在指定时间范围内运行任务 |
| `->unlessBetween('8:00', '12:00')` | 除了指定时间范围外的任何时间都可以运行任务 |
| `->weekdays()` | 限制为工作日 |
| `->sundays()` | 限制为星期日 |
| `->mondays()` | 限制为星期一 |
| `->tuesdays()` | 限制为星期二 |
| `->wednesdays()` | 限制为星期三 |
| `->thursdays()` | 限制为星期四 |
| `->fridays()` | 限制为星期五 |
| `->saturdays()` | 限制为星期六 |
| `->days([1,2])` | 限制为星期日和星期一 |
| `->environments(*staging*)` | 限制为仅在演示环境运行 |

大多数修饰符可以链式使用，但当然，任何不能合理链式使用的组合都不能链式使用。

示例 16-39 展示了几种你可以考虑的组合。

##### 示例 16-39. 一些样本调度事件

```php
// Both run weekly on Sunday at 23:50
$schedule->command('do:thing')->weeklyOn(0, '23:50');
$schedule->command('do:thing')->weekly()->sundays()->at('23:50');

// Run once per hour, weekdays, 8am-5pm
$schedule->command('do:thing')->weekdays()->hourly()->when(function () {
    return date('H') >= 8 && date('H') <= 17;
});

// Run once per hour, weekdays, 8am-5pm using the "between" method
$schedule->command('do:thing')->weekdays()->hourly()->between('8:00', '17:00');

// Run every 30 minutes except when directed not to by the SkipDetector
$schedule->command('do:thing')->everyThirtyMinutes()->skip(function () {
    return app('SkipDetector')->shouldSkip();
});
```

## 为计划命令定义时区

你可以使用`timezone()`方法在特定调度命令上定义时区：

```php
$schedule->command('do:it')->weeklyOn(0, '23:50')->timezone('America/Chicago');
```

你也可以设置一个默认的时区（与应用程序时区分开），所有计划任务的时间都可以通过在 `App\Console\Kernel` 中定义 `scheduleTimezone()` 方法来定义：

```php
protected function scheduleTimezone()
{
    return 'America/Chicago';
}
```

## 阻塞和重叠

如果你想避免任务重叠—例如，如果一个任务每分钟运行一次，但有时可能需要超过一分钟才能运行完毕—可以用`withoutOverlapping()`方法结束调度链。这个方法会在前一个任务实例仍在运行时跳过该任务：

```php
$schedule->command('do:thing')->everyMinute()->withoutOverlapping();
```

## 处理任务输出

有时，计划任务的输出很重要，无论是用于日志记录、通知，还是仅仅确保任务运行。

如果你想将任务的返回输出写入文件（有可能覆盖文件中已有内容），可以使用`sendOutputTo()`：

```php
$schedule->command('do:thing')->daily()->sendOutputTo($filePath);
```

如果你想将输出追加到一个文件中，可以使用`appendOutputTo()`：

```php
$schedule->command('do:thing')->daily()->appendOutputTo($filePath);
```

如果你想将输出邮件发送给指定收件人，请先写入文件，然后添加`emailOutputTo()`：

```php
$schedule->command('do:thing')
    ->daily()
    ->sendOutputTo($filePath)
    ->emailOutputTo('me@myapp.com');
```

确保 Laravel 的基本电子邮件配置中正确配置了你的电子邮件设置。

# 闭包调度事件无法发送输出

`sendOutputTo()`、`appendOutputTo()`和`emailOutputTo()`方法仅适用于`command()`调度任务。很遗憾，你不能将它们用于闭包。

你可能还想将一些输出发送到 Webhook 来验证任务是否正确运行。有几种服务提供这种类型的运行时间监控，最显著的是[Laravel Envoyer](https://envoyer.io)，一个零停机时间部署服务，还提供 cron 运行时间监控，以及[Dead Man’s Snitch](https://deadmanssnitch.com)，一个专门用于监控 cron 作业运行时间的工具。

这些服务不希望有人给它们发送电子邮件，而是希望接收一个 HTTP "ping"，所以 Laravel 通过`pingBefore()`和`thenPing()`简化了这一过程：

```php
$schedule->command('do:thing')
    ->daily()
    ->pingBefore($beforeUrl)
    ->thenPing($afterUrl);
```

如果你想使用 ping 功能，需要使用 Composer 导入 Guzzle：

`composer require guzzlehttp/guzzle`

## 任务钩子

谈到在任务*之前*和*之后*运行某些东西，有一些钩子可以做到，比如`before()`和`after()`：

```php
$schedule->command('do_thing')
    ->daily()
    ->before(function () {
        // Prepare
    })
    ->after(function () {
        // Cleanup
    });
```

## 在本地开发中运行调度程序

由于调度程序依赖于 cron，在服务器上设置比在本地机器上设置更简单。如果你想在本地运行调度程序，请运行`schedule:work` Artisan 命令，这将每分钟调用调度程序，就像 cron 作业一样：

```php
php artisan schedule:work
```

# 测试

测试排队作业（或队列中的任何其他内容）很容易。在 *phpunit.xml* 中（这是您测试的配置文件），`QUEUE_DRIVER` 环境变量默认设置为 `sync`。这意味着您的测试将同步运行作业或其他排队任务，直接在您的代码中，而无需依赖任何类型的队列系统。您可以像测试任何其他代码一样测试它们。

但是，您也可以针对特定的作业进行断言，如 示例 16-40 中所示。

##### 示例 16-40\. 使用闭包验证分派的作业是否符合给定条件

```php
use Illuminate\Support\Facades\Bus;
...
public function test_changing_subscriptions_triggers_crunch_job()
{
    // ...

    Bus::fake();

    Bus::assertDispatched(CrunchReports::class, function ($job) {
        return $job->subscriptions->contains(5);
    });

    // Also can use assertNotDispatched()
}
```

还有 `assertPushedWithChain()` 和 `assertPushedWithoutChain()` 方法。

```php
Bus::fake();

Bus::assertPushedWithChain(
    CrunchReports::class,
    [ChainedJob::class],
    function ($job) {
        return $job->subscriptions->contains(5);
    }
);

    // Also can use assertPushedWithoutChain()
    Bus::assertPushedWithChain(CrunchReports::class, function ($job) {
        return $job->subscriptions->contains(5);
    });
```

要测试事件是否触发，您有两个选项。首先，您可以只测试您期望发生的行为，而不必关注事件本身。

其次，您可以针对触发的事件运行测试，如 示例 16-41 中所示。

##### 示例 16-41\. 使用闭包验证触发的事件是否符合给定条件

```php
use Illuminate\Support\Facades\Event;
...
public function test_usersubscribed_event_fires()
{
    Event::fake();

    // ...

    Event::assertDispatched(UserSubscribed::class, function ($e) {
        return $e->user->email = 'user-who-subscribed@mail.com';
    });

    // Also can use assertNotDispatched()
}
```

另一个常见的场景是，您正在测试意外触发事件的代码，并且希望在测试期间禁用事件监听器。您可以使用 `withoutEvents()` 方法禁用事件系统，如 示例 16-42 中所示。

##### 示例 16-42\. 在测试期间禁用事件监听器

```php
public function test_something_subscription_related()
{
    $this->withoutEvents();

    // ...
}
```

# TL;DR

队列允许您将应用程序代码的块从用户交互的同步流中分离出来，转换为由“队列工作者”处理的命令列表。这使得您的用户可以在后台异步处理较慢的进程时恢复与您的应用程序的交互。

作业是结构良好的类，旨在封装应用程序行为的块，以便将其推送到队列中。

Laravel 的事件系统遵循发布/订阅或观察者模式，允许您从应用程序的一部分发送事件的通知，并在其他地方绑定监听器来定义对这些事件的响应行为。使用 WebSockets，事件也可以广播到前端客户端。

Laravel 的调度程序简化了任务的调度。将每分钟的 cron 任务指向 `php artisan schedule:run`，然后使用调度程序安排您的任务，即使是最复杂的时间要求，Laravel 也会为您处理所有的时间安排。
