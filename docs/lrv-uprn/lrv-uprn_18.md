# 第十八章：Laravel 生态系统

随着 Laravel 的发展，Laravel 团队构建了一套工具来支持和简化 Laravel 开发人员的生活和工作流程。许多新工作已直接进入核心，但还有很多包和 SaaS 提供并不是核心的一部分，但仍然是 Laravel 经验的重要组成部分。

我们已经涵盖了其中相当多的内容，对于那些内容，我将提供指向书中更多信息的指针。对于我们尚未涵盖的工具，我将简要描述每个工具，并提供相关网站的链接。

# 本书中涵盖的工具

我们已经浏览过这些内容，但这里是它们的简要提醒以及您可以找到相关资源的链接。

## Valet

Valet 是一个本地开发服务器（适用于 Mac，并有适用于 Windows 和 Linux 的分支），使得将所有项目快速轻松地提供给浏览器变得非常简单。您将通过 Composer 在本地开发机器上全局安装 Valet。

只需几个命令，您就可以在 `*.test*` 域上为您机器上的每个 Laravel 应用程序提供 Nginx、MySQL、Redis 等服务。

Valet 是在 “Laravel Valet” 中介绍的。

## Homestead

Homestead 是在一个简单的 Vagrant 设置之上的配置层，使得从一个 Laravel 友好的 Vagrant 设置中服务多个 Laravel 应用程序变得简单。

Homestead 是在 “Laravel Homestead” 中简要介绍的。

## Herd

Herd 是一个原生的 macOS 应用程序，将 Valet 及其依赖项打包成一个单独的应用程序，您可以在其中安装，无需处理 Docker、Homebrew 或任何其他依赖管理器。

Herd 是在 “Laravel Herd” 中介绍的。

## Laravel 安装程序

Laravel 安装程序是一个全局安装在您的本地开发机器上（通过 Composer 安装），它可以轻松快速地设置一个新的 Laravel 项目。

安装程序是在 “使用 Laravel 安装程序工具安装 Laravel” 中介绍的。

## Dusk

Dusk 是一个用于测试整个应用程序（包括 JavaScript 等）的前端测试框架。它是一个强大的包，您可以通过 Composer 将其引入您的应用程序，并通过 ChromeDriver 驱动实际的浏览器。

Dusk 是在 “使用 Dusk 进行测试” 中介绍的。

## Passport

Passport 是一个强大而简单易用的 OAuth 2.0 服务器，用于为客户端身份验证您的 API。您将在每个应用程序中安装它作为一个 Composer 包，并且只需很少的工作，就可以为您的用户提供完整的 OAuth 2.0 流程。

Passport 是在 “使用 Laravel Passport 进行 API 认证” 中介绍的。

## Sail

Sail 是由 Docker 驱动的 Laravel 默认的本地开发环境。

Sail 是在 “Laravel Sail” 中介绍的。

## Sanctum

Sanctum 是一个用于支持移动应用程序、SPA 和简单基于令牌的 API 的身份验证系统。它是复杂的 OAuth 的一个简化但仍然功能强大的替代方案。

Sanctum 被介绍在“API Authentication with Sanctum”中。

## Fortify

Fortify 是一个无头身份验证系统。它为 Laravel 需要的所有身份验证功能提供了路由和控制器，从登录和注册到密码重置等等，可供任何你选择的前端消费。

Fortify 被介绍在“Fortify”中。

## Breeze

Breeze 是所有 Laravel 必需身份验证功能的最小路由和控制器集合，并与每个功能配套的前端模板。Breeze 可以通过 Blade、Vue、React 或 Inertia 提供。

Breeze 被介绍在“Laravel Breeze”中。

## Jetstream

Jetstream 是一个强大的应用程序启动套件，提供了 Breeze 提供的所有身份验证功能，以及电子邮件验证、双因素身份验证、会话管理、API 身份验证和团队管理功能。与 Breeze 不同，Jetstream 仅提供两种前端工具选择：Livewire 和 Inertia/Vue。

Jetstream 被介绍在“Laravel Jetstream”中。

## Horizon

Horizon 是一个可以通过 Composer 安装到每个应用程序中的队列监控包。它为监控 Redis 队列作业的健康状况、性能、失败和历史提供了完整的用户界面。

Horizon 简要介绍了“Laravel Horizon”。

## Echo

Echo 是一个 JavaScript 库（随着 Laravel 通知系统的一系列改进而引入），使得通过 WebSockets 订阅来自你的 Laravel 应用的事件和频道变得简单。

Echo 被介绍在“Laravel Echo (the JavaScript Side)”中。

# 本书未涉及的工具

本书未涉及的一些工具，因为它们超出了本书的范围。其中一些只用于特殊情况（例如用于支付的 Cashier，用于社交登录的 Socialite 等），但有些我每天都在使用（尤其是 Forge）。

下面是一个简要介绍，从你在工作中最可能遇到的工具开始。请注意，这个列表并不详尽！

## Forge

[Forge](https://forge.laravel.com) 是一款付费的 SaaS 工具，用于在 DigitalOcean、Linode、AWS 等主机上创建和管理虚拟服务器。它为你提供了一切运行所需的工具，从队列和队列工作者到 Let’s Encrypt SSL 证书。此外，它还可以设置简单的 Shell 脚本，以在你将新代码推送到 GitHub 或 Bitbucket 时自动部署你的站点。

Forge 在快速轻松地启动站点方面非常有用，但它并不是如此简单，你无法在长期或更大规模上也运行你的应用程序。你可以扩展你的服务器大小，添加负载均衡器，并在 Forge 内管理服务器之间的私有网络。

## Vapor

[Vapor](https://vapor.laravel.com) 是一个付费的 SaaS 工具，用于将 Laravel 应用程序部署到 AWS Lambda，使用“无服务器”托管模式。它管理缓存、队列、数据库、资源构建、域指向、自动缩放、内容传递网络、环境管理以及大多数您将需要为 Laravel 应用程序进行无服务器部署的任何其他事项。

## Envoyer

[Envoyer](https://envoyer.io) 是一个付费的 SaaS 工具，被称为“零停机 PHP 部署”。与 Forge 不同，Envoyer 不会启动或管理您的服务器。它的主要任务是监听触发器，通常是在推送新代码时，但您也可以手动触发部署或通过 Webhook 触发。

Envoyer 在以下三个方面远远优于 Forge 的推送部署工具和大多数其他推送部署解决方案：

+   它具有强大的工具集，可以构建简单但强大的多阶段部署流程。

+   它使用类似 Capistrano 的零停机部署方式部署您的应用程序；每个新部署都构建到自己的文件夹中，只有在构建过程成功完成后，才将该部署文件夹的符号链接到您的实际 Web 根目录。因此，在 Composer 安装或 NPM 构建时，服务器不会出现中断。

+   由于这种基于文件夹的系统，可以轻松快速地回滚到以前的版本；Envoyer 只需将符号链接更新回以前的部署文件夹，即可立即提供旧版本的服务。

您还可以设置定期健康检查（对您的服务器进行 ping 测试，如果 ping 测试未返回 200 HTTP 响应，则向您报告错误），期望您的 cron 作业将定期向 Envoyer 发送 ping，并基于聊天的方式通知任何重要事件。

Envoyer 比 Forge 更像一个专业工具。我不认识很多 Laravel 开发者不使用 Forge，但那些愿意支付使用 Envoyer 的人更可能拥有如果不能立即回滚问题提交或者网站流量（或者重要的流量）足够大而会有很多问题的网站。如果您的网站属于这一类别，Envoyer 会让您感到像魔法一样。

## Cashier

[Cashier](https://oreil.ly/25wiq) 是一个免费的包，提供了一个简单的界面，用来管理 Stripe 的订阅计费服务。Cashier 处理订阅用户的大部分基本功能，包括更改他们的计划、提供访问发票、处理来自计费服务的 Webhook 回调、管理取消宽限期等等。

如果你想让用户使用 Stripe 进行订阅注册，Cashier 会让你的生活变得轻松得多。

## Socialite

[Socialite](https://oreil.ly/_fqcc) 是一个免费的包，使得向您的应用程序添加社交登录变得非常简单（例如通过 GitHub 或 Facebook）。

## Nova

[Nova](https://nova.laravel.com)是一个付费包，用于构建管理面板。如果想象一下您平均复杂的 Laravel 应用程序，可能会有几个部分：公共面向客户的网站或视图、用于对核心数据或客户列表进行更改的管理部分，甚至可能还有一个 API。

Nova 极大地简化了使用 Vue 和 Laravel API 构建站点管理面板部分的过程。它可以轻松生成所有资源的 CRUD（创建、读取、更新、删除）页面，以及针对数据的更复杂的自定义视图、每个资源上的自定义操作和关系，甚至用于向相同的一般管理空间添加非 CRUD 工具的自定义工具。

## Spark

[Spark](https://spark.laravel.com)是一个付费包，用于生成接受付款的 SaaS，并且简化用户、团队和订阅的管理。它提供了 Stripe 和 Paddle 集成、发票、基于座位或团队的计费以及完整的独立计费门户，使您不必受限于 Spark 的默认技术栈。

## Envoy

[Envoy](https://oreil.ly/kZMy8)是一个本地任务运行程序，可以轻松定义将在远程服务器上运行的常见任务，将这些任务的定义提交到版本控制，并简单而可预测地运行它们。

查看示例 18-1 以了解常见 Envoy 任务的样子。

##### 示例 18-1\. 一个常见的 Envoy 任务

```php
@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

@task('deploy', ['on' => ['web-1', 'web-2']])
    cd mysite.com
    git pull origin {{ $branch }}
    php artisan migrate
    php artisan route:cache
@endtask
```

要运行示例 18-1，请在本地终端中运行以下命令：

```php
envoy run deploy --branch=master
```

## 望远镜

[望远镜](https://oreil.ly/gc78b)是一个免费的调试工具，可以作为 Laravel 应用程序的包安装。它生成一个仪表板，您可以深入了解作业的当前状态、队列工作者、HTTP 请求、数据库查询等等。

## Octane

[Octane](https://oreil.ly/xCtgq)是一个免费工具，使您能够使用异步、并发的 PHP Web 服务器为您的 Laravel 应用程序提供服务，旨在提供速度和性能。在撰写本书时，有三种这样的工具：Swoole、Open Swoole 和 RoadRunner。使用 Octane，这些工具将一次性加载您的应用程序到内存中，然后以最高效的方式为每个请求提供服务，利用语言和系统级工具来实现并发。

## Pennant

[Pennant](https://oreil.ly/zAFHa)是 Laravel 本地实现的“特性标志”模式，允许您轻松定义每个请求是否应在您的应用程序中看到某个特性—通常是因为请求的用户及其访问权限。Pennant 允许您一次性定义应用哪些指标用于确定是否应该为给定特性服务请求，提供了与 Laravel 访问控制列表层非常相似的语法。

## Folio

Folio 是一个 Laravel 包，允许您基于文件夹中模板文件的排列来构建应用程序的路由。类似于 Next 和 Nuxt，Folio 允许您创建单独的模板（例如 */index.blade.php* 显示在 `mysite.com/`，*/about.blade.php* 显示在 `mysite.com/about`，*/users/index.blade.php* 显示在 `mysite.com/users`），或者定义应与 URL 中占位符匹配的模板（例如 */users/[id].blade.php* 显示在 `mysite.com/users/14`）。

## Volt

Volt 使用单文件功能组件扩展 Livewire 的功能。Volt 还提供了一个指令 `@volt`，允许您将模板的一部分分配给 Livewire 组件定义管理，同时模板的其余部分仍然是普通的 Blade。

## Pint

[Pint](https://oreil.ly/AB21w) 是一个代码风格工具，旨在在你的应用程序中强制执行 Laravel 的默认代码风格。它基于 PHP-CS-Fixer 构建，并提供了一些工具改进，同时还提供了预配置的 Laravel 特定代码规则集。

# 其他资源

我已经提到了很多资源，但这里是一些人们经常转向学习 Laravel 的资源的非详尽列表：

+   [Laravel Bootcamp](https://bootcamp.laravel.com)

+   [Laravel News](https://laravel-news.com)

+   [Laracasts](https://laracasts.com)

+   在 Twitter 上，可以关注[@TaylorOtwell](https://twitter.com/taylorotwell)和[@LaravelPHP](https://twitter.com/laravelphp)。

+   [Adam Wathan 的课程](https://adamwathan.me)

+   [Chris Fidao 的课程](https://fideloper.com)

+   [Laravel Daily](https://laraveldaily.com)

+   [DevDojo](https://devdojo.com)

+   [CodeCourse](https://codecourse.com)

+   [The Laravel Podcast](http://www.laravelpodcast.com)

有许多博客（我在 [*mattstauffer.com*](https://mattstauffer.com/) 有一个，Tighten 在 [*tighten.com*](https://www.tighten.com) 也有一个，还有许多其他非常有用的），许多优秀的 Twitter 用户，许多出色的包作者，以及太多我尊重但无法在这里列出的 Laravel 开发者。这是一个富有多样性和慷慨的社区，充满了乐于分享一切他们学到的开发者；难的不是找到好内容，而是找到时间去消化这一切。

作为 Laravel 开发者的旅程中可能遇到的每一个人或资源我无法一一列举，但如果你从这里列出的资源和人士开始，你将在使用 Laravel 方面迈出良好的第一步。
