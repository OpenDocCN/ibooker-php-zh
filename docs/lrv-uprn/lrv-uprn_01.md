# 第一章：为什么选择 Laravel？

在动态网络的早期阶段，编写 Web 应用程序看起来与今天大不相同。当时的开发人员不仅负责编写我们应用程序独特的业务逻辑代码，还负责编写那些在各个站点中如此常见的组件——用户认证、输入验证、数据库访问、模板化等等。

如今，程序员们可以轻松访问数十种应用程序开发框架和数千个组件和库。程序员们常说，当你学会一个框架时，可能已经有三个更新（据称更好）的框架出现，试图取代它。

“因为它存在”可能是攀登山峰的有效理由，但选择使用特定框架——或者根本使用框架——有更好的理由。值得问的问题是，为什么要使用框架？更具体地说，为什么选择 Laravel？

# 为什么使用框架？

显然，使用 PHP 开发者可用的各个组件或包是有益的。通过包，其他人负责开发和维护一个有明确定义作用的隔离代码片段，理论上，这个人对这个单一组件的理解应该比你有时间去深入了解得更多。

像 Laravel、Symfony、Lumen 和 Slim 这样的框架，会将一系列第三方组件与自定义框架“粘合剂”（如配置文件、服务提供者、预定义的目录结构和应用程序引导）打包在一起。因此，使用框架的好处不仅在于有人已经为您做出了关于单独组件的决定，还包括*这些组件如何组合在一起*的决策。

## “我只是自己构建它”

假设您开始一个新的 Web 应用程序，没有框架的帮助。您应该从哪里开始呢？嗯，它可能需要路由 HTTP 请求，所以现在您需要评估所有可用的 HTTP 请求和响应库并选择一个。然后，您将不得不选择一个路由器。哦，您可能还需要设置某种形式的路由配置文件。它应该使用什么语法？应该放在哪里？控制器呢？它们应该放在哪里，如何加载？嗯，您可能需要一个依赖注入容器来解析控制器及其依赖关系。但是选哪一个呢？

此外，如果您确实花时间回答所有这些问题并成功创建您的应用程序，那么对下一个开发者的影响是什么？当您有四个这样的基于定制框架的应用程序，或者十二个时，您需要记住每个应用程序中控制器的位置或路由语法是什么？

## 一致性与灵活性

框架通过提供慎重考虑的答案来解决“我们应该在这里使用哪个组件？”的问题，并确保所选组件能够很好地协同工作。此外，框架提供的约定减少了新项目的开发者需要理解的代码量——例如，如果你理解一个 Laravel 项目中的路由工作原理，那么你就理解了所有 Laravel 项目中它是如何工作的。

当有人建议为每个新项目定制框架时，他们实际上是在主张能够控制应用程序基础中包含和排除什么。这意味着最好的框架不仅会为你提供一个坚实的基础，还会让你有自由进行定制。正如我将在本书的其余部分中展示的那样，这是使 Laravel 如此特别的部分。

# Web 和 PHP 框架的简史

能够回答“为什么选择 Laravel？”的一个重要部分是了解 Laravel 的历史——以及它之前的发展。在 Laravel 兴起之前，PHP 和其他 Web 开发领域有各种框架和其他运动。

## Ruby on Rails

David Heinemeier Hansson 于 2004 年发布了 Ruby on Rails 的第一个版本，自那以后，很难找到一种 Web 应用框架不受 Rails 影响的情况。

Rails 推广了 MVC、RESTful JSON API、约定优于配置、ActiveRecord 等许多工具和惯例，对 Web 开发者处理他们的应用程序的方式产生了深远影响——特别是快速应用程序开发方面。

## PHP 框架的激增

大多数开发者很清楚，Rails 和类似的 Web 应用框架是未来的趋势，包括那些明显模仿 Rails 的 PHP 框架迅速涌现。

CakePHP 是 2005 年的第一款，很快又有 Symfony、CodeIgniter、Zend Framework 和 Kohana（CodeIgniter 的一个分支）。Yii 在 2008 年出现，Aura 和 Slim 则在 2010 年。2011 年推出的 FuelPHP 和 Laravel 既不是 CodeIgniter 的分支，而是提出的替代方案。

这些框架中有些更像 Rails，专注于数据库对象关系映射（ORM）、MVC 结构和其他旨在快速开发的工具。另一些像 Symfony 和 Zend 则更专注于企业设计模式和电子商务。

## CodeIgniter 的优劣

CakePHP 和 CodeIgniter 是最早公开承认从 Rails 获得灵感的两个早期 PHP 框架。CodeIgniter 迅速走红，并且到 2010 年，可以说是独立 PHP 框架中最受欢迎的。

CodeIgniter 简单易用，拥有出色的文档和强大的社区。但它的现代技术和模式使用进展缓慢；随着框架世界的发展和 PHP 工具的进步，CodeIgniter 在技术进步和开箱即用功能方面开始落后。与许多其他框架不同，CodeIgniter 由一家公司管理，它在适应 PHP 5.3 的新功能（如命名空间以及后来的 GitHub 和 Composer）方面进展缓慢。正是在 2010 年，Laravel 的创始人 Taylor Otwell 对 CodeIgniter 不满意到足以自己动手写框架。

## Laravel 1、2 和 3

Laravel 1 的第一个 beta 版于 2011 年 6 月发布，完全从头开始编写。它具有自定义 ORM（Eloquent）、基于闭包的路由（受 Ruby Sinatra 启发）、用于扩展的模块系统以及表单、验证、认证等助手。

早期的 Laravel 开发进展迅速，Laravel 2 和 3 分别于 2011 年 11 月和 2012 年 2 月发布。它们引入了控制器、单元测试、命令行工具、控制反转（IoC）容器、Eloquent 关系和迁移。

## Laravel 4

到了 Laravel 4，Taylor 从头重新编写了整个框架。此时的 Composer，PHP 现在普遍使用的包管理器，显示出成为行业标准的迹象，Taylor 看到了将框架重写为 Composer 分发和捆绑的组件集合的价值。

Taylor 开发了一组名为*Illuminate*的组件，并于 2013 年 5 月发布了全新结构的 Laravel 4。现在，Laravel 不再将大部分代码捆绑为下载包，而是通过 Composer 从 Symfony（另一个将其组件释放供他人使用的框架）和 Illuminate 组件中拉取大部分组件。

Laravel 4 还引入了队列、邮件组件、门面和数据库种子。因为 Laravel 现在依赖于 Symfony 组件，因此宣布 Laravel 将会（并非完全一样，但很快）效仿 Symfony 的六个月发布计划。

## Laravel 5

Laravel 4.3 原定于 2014 年 11 月发布，但随着开发的进展，其变化的重要性变得明显，因此 Laravel 5 于 2015 年 2 月发布。

Laravel 5 采用了全新的目录结构，删除了表单和 HTML 助手，引入了契约接口，大量新的视图，Socialite 用于社交媒体认证，Elixir 用于资产编译，Scheduler 简化了 cron 任务，dotenv 简化了环境管理，还有全新的 REPL（read–evaluate–print loop）。从那时起，它在功能和成熟度上都有所增长，但没有像以前版本那样有重大变化。

## Laravel 6

在 2019 年 9 月，引入了 Laravel 6，并带来了两个主要变化：首先，移除了 Laravel 提供的字符串和数组全局助手（改用 Facade）；其次，转向语义化版本控制（SemVer）进行版本编号。这种变化的实际影响意味着，对于 5 之后的所有 Laravel 版本，无论是主要版本（6、7 等）还是次要版本（6.1、6.2 等），发布频率都大大增加了。

## Laravel 在新 SemVer 世界（6+）的版本

从版本 6 开始，由于新的 SemVer 发布计划，Laravel 的发布不再像过去那样具有里程碑意义。因此，未来的发布将更多地关注时间流逝，而不是非常具体的全新大功能。

# Laravel 有什么特别之处？

那么，是什么让 Laravel 与众不同？为什么在任何时候都值得拥有超过一个 PHP 框架？他们毕竟都使用 Symfony 的组件，不是吗？让我们稍微谈谈是什么让 Laravel 如此“tick”。

## Laravel 的哲学

您只需阅读 Laravel 的市场材料和 README 就可以开始看到它的价值。Taylor 使用像“Illuminate”和“Spark”这样与光有关的词语。然后还有这些：“Artisans”。“优雅”。还有这些：“一股清新的空气”。“新的开始”。最后还有：“快速”。“光速”。

框架最强烈传达的两个价值观是提高开发速度和开发者的幸福感。Taylor 将“Artisan”语言描述为故意与更实用主义的价值观对立。你可以在[他 2011 年在 StackExchange 上的提问](https://oreil.ly/q0tgM)中看到这种思维方式的起源，他说：“有时我花费了大量时间（几个小时）来苦恼地让代码‘看起来漂亮’”，仅仅是为了改善看代码本身的体验。他经常谈论简化和加快开发者将想法变成现实的过程的价值，摒弃创建优秀产品的不必要障碍。

Laravel 的核心是装备和使开发者能力。其目标是提供清晰、简单和优美的代码和功能，帮助开发者快速学习、启动、开发和编写简单、清晰且持久的代码。

Laravel 文档明确表达了面向开发者的概念。"开心的开发者编写最佳代码" 已经写入文档。"从下载到部署的开发者幸福" 曾是一段时间内的非官方口号。当然，任何工具或框架都会声称希望开发者开心。但将开发者幸福作为*首要*关注点，而不是次要，对 Laravel 的风格和决策进展产生了巨大影响。其他框架可能将架构纯净性作为首要目标，或者与企业开发团队的目标和价值兼容，但 Laravel 的主要关注点是为个体开发者服务。这并不意味着你不能在 Laravel 中编写架构纯净或企业级的应用程序，但这不会以损害代码库的可读性和理解性为代价。

## Laravel 如何实现开发者的幸福感

仅仅说你想让开发者开心是一回事。实现它是另一回事，它要求你质疑框架中最有可能让开发者不开心的因素，以及最有可能让他们开心的因素。Laravel 试图通过多种方式使开发者的生活更轻松。

首先，Laravel 是一个快速应用开发框架。这意味着它专注于浅显易懂的学习曲线，并尽量减少从开始新应用到发布的步骤。Laravel 提供了构建 Web 应用程序中最常见任务的所有组件，从数据库交互到身份验证、队列、电子邮件到缓存，都由 Laravel 提供简化。但 Laravel 的组件不仅仅在其自身上很棒；它们在整个框架中提供了一致的 API 和可预测的结构。这意味着，当你在 Laravel 中尝试新事物时，你很可能会说，“...它就这样运行了。”

这并不仅限于框架本身。Laravel 提供了一个完整的工具生态系统，用于构建和发布应用程序。你有 Sail 和 Valet 和 Homestead 用于本地开发，Forge 用于服务器管理，Envoyer 和 Vapor 用于高级部署。还有一套附加包：Cashier 用于付款和订阅，Echo 用于 WebSockets，Scout 用于搜索，Sanctum 和 Passport 用于 API 认证，Dusk 用于前端测试，Socialite 用于社交登录，Horizon 用于监控队列，Nova 用于构建管理面板，以及 Spark 用于启动你的 SaaS。Laravel 努力减少开发者工作中的重复性工作，以便他们可以做一些独特的事情。

其次，Laravel 注重“约定优于配置”——这意味着如果你愿意使用 Laravel 的默认设置，你将比其他需要你声明所有设置的框架少做很多工作，即使你使用推荐的配置。使用 Laravel 构建的项目所需时间比大多数其他 PHP 框架构建的项目要少。

Laravel 也深入关注简洁性。如果你愿意，你可以在 Laravel 中使用依赖注入、模拟和数据映射器模式、仓储模式和命令查询职责分离等各种复杂的架构模式。但是，而其他框架可能建议在每个项目中使用这些工具和结构，Laravel 及其文档和社区更倾向于从最简单的实现开始——这里用一个全局函数，那里用一个外观模式，再在那里用 ActiveRecord。这使开发人员能够创建最简单的应用程序来满足他们的需求，而不会限制其在复杂环境中的有用性。

Laravel 与其他 PHP 框架不同的一个有趣之处在于，它的创作者和社区更多地受到 Ruby 和 Rails 以及函数式编程语言的影响和启发，而不是 Java。在现代 PHP 中，有一种强烈的潮流倾向于冗长和复杂，接纳 PHP 更类似于 Java 的方面。但是 Laravel 往往站在另一边，拥抱富有表现力、动态和简单的编码实践和语言特性。

## Laravel 社区

如果这本书是你第一次接触 Laravel 社区，你有特别的期待。Laravel 的一个显著特点之一是其欢迎、教育氛围，这一点促成了它的成长和成功。从 Jeffrey Way 的[Laracasts 视频教程](https://laracasts.com)到[Laravel News](https://laravel-news.com)，再到 Slack、IRC 和 Discord 频道，从 Twitter 的朋友到博客作者、播客到 Laracon 大会，Laravel 拥有一个充满活力和丰富多样的社区，其中既有从一开始就参与的人，也有刚刚开始的新人。“这并非偶然：

> 从 Laravel 的最初开始，我就有这样一个想法，即所有人都希望感觉自己是某个群体的一部分。想要归属并被其他志同道合的人接受，是人类的自然本能。因此，通过在 web 框架中注入个性并积极参与社区，这种感觉可以在社区中蔓延。
> 
> Taylor Otwell，产品与支持采访

Taylor 从 Laravel 早期就明白，一个成功的开源项目需要两样东西：良好的文档和一个友好的社区。而这两点现在已成为 Laravel 的标志性特征。

# 如何运作

直到现在，我在这里分享的一切都是完全抽象的。你可能会问，关于代码呢？让我们深入一个简单的应用程序（示例 1-1）来看看日常使用 Laravel 到底是怎样的体验。

##### 示例 1-1\. 在 routes/web.php 中的“Hello, World”

```php
<?php

Route::get('/', function () {
   return 'Hello, World!';
});
```

在 Laravel 应用中，你可以采取的最简单的操作就是定义一个路由并在访问该路由时返回结果。如果在你的机器上初始化一个全新的 Laravel 应用程序，在 示例 1-1 中定义路由，然后从 *public* 目录提供站点服务，你将拥有一个完全运作的“Hello, World”示例（见 图 1-1）。

![白色屏幕上的“Hello, World”文字](img/lur3_0101.png)

###### 图 1-1\. 使用 Laravel 返回“Hello, World!”

它看起来非常类似于控制器，正如你在 示例 1-2 中所看到的（如果你想立即测试，请首先运行 `php artisan make:controller WelcomeController` 创建控制器）。

##### 示例 1-2\. 使用控制器打印“Hello, World”

```php
// File: routes/web.php
<?php

use App\Http\Controllers\WelcomeController;

Route::get('/', [WelcomeController::class, 'index']);
```

```php
// File: app/Http/Controllers/WelcomeController.php
<?php

namespace App\Http\Controllers;

class WelcomeController extends Controller
{
    public function index()
    {
        return 'Hello, World!';
    }
}
```

如果你将问候语存储在数据库中，它看起来也会非常相似（参见 示例 1-3）。

##### 示例 1-3\. 多问候语“Hello, World”并访问数据库

```php
// File: routes/web.php
<?php

use App\Greeting;

Route::get('create-greeting', function () {
    $greeting = new Greeting;
    $greeting->body = 'Hello, World!';
    $greeting->save();
});

Route::get('first-greeting', function () {
    return Greeting::first()->body;
});
```

```php
// File: app/Models/Greeting.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Greeting extends Model
{
    use HasFactory;
}
```

```php
// File: database/migrations/2023_03_12_192110_create_greetings_table.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
 * Run the migrations.
 */
    public function up(): void
    {
        Schema::create('greetings', function (Blueprint $table) {
            $table->id();
            $table->string('body');
            $table->timestamps();
        });
    }

    /**
 * Reverse the migrations.
 */
    public function down(): void
    {
        Schema::dropIfExists('greetings');
    }
};
```

示例 1-3 可能会有些令人不知所措，如果是这样，请跳过。在后面的章节中你将了解到这里发生的一切，但你已经可以看到，仅仅几行代码就可以设置数据库迁移和模型，并提取记录。就是这么简单。

# 为什么选择 Laravel？

所以，为什么选择 Laravel？

因为 Laravel 能帮助你将想法变为现实，无需编写多余的代码，采用现代编码标准，支持活跃的社区，并拥有强大的工具生态系统。

还有因为你，亲爱的开发者，值得快乐。
