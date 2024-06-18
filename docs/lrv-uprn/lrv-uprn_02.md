# 第二章：设置 Laravel 开发环境

PHP 之所以成功的一部分原因是几乎找不到不能运行 PHP 的网络服务器。然而，现代 PHP 工具对于过去的要求更为严格。为了更好地为 Laravel 开发，最好保持代码在本地和远程服务器环境的一致性，幸运的是，Laravel 生态系统为此提供了一些工具。

# 系统要求

本章中涵盖的所有内容在 Windows 机器上都是可能的，但你需要几十页的定制说明和注意事项。我将这些说明和注意事项留给真正的 Windows 用户，因此本书的例子将专注于 Unix/Linux/macOS 开发者。

无论你选择通过在本地机器上安装 PHP 和其他工具来为你的网站提供服务，还是通过 Vagrant 或 Docker 在虚拟机中提供开发环境，或者依赖像 MAMP/WAMP/XAMPP 这样的工具，你的开发环境都需要安装以下所有内容才能为 Laravel 站点提供服务：

+   PHP >= 8.1

+   OpenSSL PHP 扩展

+   PDO PHP 扩展

+   Mbstring PHP 扩展

+   Tokenizer PHP 扩展

+   XML PHP 扩展

+   Ctype PHP 扩展

+   JSON PHP 扩展

+   BCMath PHP 扩展

# Composer

无论你用什么机器开发，都需要全局安装[Composer](https://getcomposer.org)。*Composer*是现代大多数 PHP 开发的基础工具。它是 PHP 的依赖管理器，类似于 Node 的 NPM（Node Package Manager）或 Ruby 的 RubyGems。但与 NPM 一样，Composer 也是我们测试、本地脚本加载、安装脚本等许多基础工具的基础。安装 Laravel、更新 Laravel 和引入外部依赖都需要 Composer。

# 本地开发环境

对于许多项目，使用更简单的工具集来托管你的开发环境可能已经足够了。如果你的系统上已经安装了 MAMP 或 WAMP 或 XAMPP，那很可能能够运行 Laravel。

你也可以通过 PHP 的内置 Web 服务器来运行 Laravel。在你的 Laravel 站点根目录下运行 `php -S localhost:8000 -t public`，PHP 的内置 Web 服务器将在*http://localhost:8000/*上为你提供站点服务。

然而，如果你希望在你的开发环境中拥有更多的功能（每个项目的不同本地域名、像 MySQL 这样的依赖管理等），你将需要一个比 PHP 内置服务器更强大的工具。

Laravel 提供了五种本地开发工具：`Artisan serve`、Sail、Valet、Herd 和 Homestead。我们会简要介绍每一种。如果你不确定该使用哪一种，我个人推荐 Mac 用户使用 Valet，其他人使用 Sail。

## Artisan Serve

如果你在设置 Laravel 应用程序后运行 `php artisan serve`，它将在 [*http://localhost:8000*](http://localhost:8000) 上提供服务，就像我们之前使用 PHP 的内置 Web 服务器设置的那样。在这里你并没有得到其他免费的东西，因此它唯一有意义的好处是更容易记住。

## Laravel Sail

Sail 是开始本地 Laravel 开发的最简单方法，无论你使用的是什么操作系统，它都是相同的。它带有一个 PHP Web 服务器、数据库以及许多其他方便的功能，使得运行单个 Laravel 安装变得非常容易，这对于项目中的每个开发人员都是一致的，不论项目的依赖项或开发人员的工作环境如何。

为什么我不使用 Sail？它使用 Docker 来完成上述任务，而 macOS 上的 Docker 速度刚好足够慢，我更喜欢 Valet。但如果你是 Laravel 的新手，特别是如果你不使用 Mac，Sail 就是最简单的开始构建 Laravel 应用程序的方式。

## Laravel Valet

如果你是 macOS 用户（也有非官方的 Windows 和 Linux 版本），Laravel Valet 可以轻松地为你的每一个本地 Laravel 应用程序（以及大多数其他静态和基于 PHP 的应用程序）提供服务在不同的本地域名上。

你需要使用 Homebrew 安装一些工具，文档将引导你完成这些步骤，但从初始安装到提供服务你的应用程序，步骤非常少。

安装 Valet——请查阅 [Valet 文档](https://laravel.com/docs/valet) 获取最新的安装说明——并将其指向一个或多个存放站点的目录。我从我的 *~/Sites* 目录运行了 `valet park`，这是我放置所有正在开发中的应用程序的地方。现在，你只需在目录名后加上 *.test*，就可以在浏览器中访问它了。

Valet 可以轻松为 Laravel 应用提供服务；我们可以使用 `valet park` 将给定文件夹中的所有子文件夹作为 *{foldername}.test* 提供服务，使用 `valet link` 只服务一个单独的文件夹，使用 `valet open` 打开浏览器显示 Valet 服务的域名，使用 `valet secure` 以 HTTPS 方式提供 Valet 网站，使用 `valet share` 打开一个 ngrok 或 Expose 隧道，这样你可以与他人共享你的站点。

## Laravel Herd

Herd 是一个原生的 macOS 应用程序，它将 Valet 及其所有依赖项捆绑在一个单独的安装程序中。虽然 Herd 不像 Valet CLI 那样可定制，但它省去了使用 Homebrew、Docker 或任何其他依赖管理器的必要，并且允许你通过一个漂亮的图形界面与 Valet 的核心功能进行交互。

## Laravel Homestead

Homestead 是另一个你可能想用来设置本地开发环境的工具。它是一个配置工具，基于 Vagrant（一个管理虚拟机的工具），提供了一个预配置的虚拟机镜像，完美地设置了 Laravel 开发环境，并且反映了许多 Laravel 网站运行的最常见生产环境。

[Homestead 文档](https://laravel.com/docs/homestead)非常全面，并且始终保持更新，所以如果你想了解它的工作原理和设置方法，我建议你直接查阅这些文档。

# 创建一个新的 Laravel 项目

创建新的 Laravel 项目有两种方式，都可以通过命令行运行。第一种是全局安装 Laravel 安装工具（使用 Composer）；第二种是使用 Composer 的`create-project`功能。

你可以在[安装文档页面](https://laravel.com/docs/installation)上详细了解这两种选项，但我建议使用 Laravel 安装工具。

## 使用 Laravel 安装工具安装 Laravel

如果你已经全局安装了 Composer，安装 Laravel 安装工具就像运行以下命令一样简单：

```php
composer global require "laravel/installer"
```

一旦安装了 Laravel 安装工具，启动一个新的 Laravel 项目就很简单。只需从命令行运行此命令：

```php
laravel new projectName
```

这将在当前目录下创建一个名为*{projectName}*的新子目录，并在其中安装一个裸的 Laravel 项目。

## 使用 Composer 的 create-project 功能安装 Laravel

Composer 还提供了一个称为`create-project`的功能，用于使用特定骨架创建新项目。要使用此工具创建新的 Laravel 项目，请发出以下命令：

```php
composer create-project laravel/laravel projectName
```

就像安装工具一样，这将在当前目录下创建一个名为*{projectName}*的子目录，其中包含一个 Laravel 安装的骨架，准备好供您开发。

## 使用 Sail 安装 Laravel

如果你计划使用 Laravel Sail 工作，可以同时安装 Laravel 应用程序并开始其 Sail 安装过程。确保你的计算机上已安装了 Docker，然后使用以下命令，将`*example-app*`替换为你的应用程序名称：

```php
curl -s "https://laravel.build/example-app" | bash
```

这将把 Laravel 安装到当前文件夹下的`*example-app*`文件夹中，然后开始 Sail 安装过程。

安装过程完成后，切换到新目录并启动 Sail：

```php
cd example-app
./vendor/bin/sail up
```

###### 注意

第一次运行`sail up`时，它会比其他安装过程花费更长时间，因为它需要构建初始 Docker 镜像。

# Laravel 的目录结构

当你打开一个包含骨架 Laravel 应用程序的目录时，你会看到以下文件和目录：

```php
app/
bootstrap/
config/
database/
public/
resources/
routes/
storage/
tests/
vendor/
.editorconfig
.env
.env.example
.gitattributes
.gitignore
artisan
composer.json
composer.lock
package.json
phpunit.xml
readme.md
vite.config.js
```

让我们一一详细介绍它们，以便熟悉。

## 文件夹

根目录默认包含以下文件夹：

app

实际应用程序的大部分内容将存放在这里。模型、控制器、命令和 PHP 领域代码都在这里。

bootstrap

包含 Laravel 框架每次运行时使用的文件。

config

所有配置文件所在位置。

database

数据库迁移、种子和工厂所在位置。

public

当服务器为网站提供服务时指向的目录。这包含 *index.php*，它是启动引导过程并适当路由所有请求的前端控制器。也是任何公开文件（如图像、样式表、脚本或下载文件）的位置。

资源

其他脚本所需文件的位置。视图，以及（可选）源 CSS 和源 JavaScript 文件存放在这里。

路由

所有路由定义的位置，包括 HTTP 路由和“控制台路由”或 Artisan 命令。

storage

缓存、日志和编译的系统文件存放位置。

测试

单元测试和集成测试的位置。

vendor

Composer 安装其依赖项的位置。它被 Git 忽略（标记为从版本控制系统中排除），因为 Composer 预期在任何远程服务器上作为部署过程的一部分运行。

## 松散的文件

根目录还包含以下文件：

.editorconfig

给你的 IDE/文本编辑器关于 Laravel 编码标准的指令（例如缩进大小、字符集以及是否修剪尾随空白）。

.env 和 .env.example

指定环境变量（在每个环境中预期不同的变量，因此不提交到版本控制）。*.env.example* 是一个模板，每个环境都应复制它以创建自己的*.env*文件，该文件被 Git 忽略。

.gitignore 和 .gitattributes

Git 配置文件。

artisan

允许您从命令行运行 Artisan 命令（参见第八章）。

composer.json 和 composer.lock

Composer 的配置文件；*composer.json* 可由用户编辑，*composer.lock* 则不可。这些文件共享一些关于项目的基本信息，并定义其 PHP 依赖项。

package.json

类似于 *composer.json*，但用于前端资产和构建系统的依赖项；它指示 NPM 拉取哪些基于 JavaScript 的依赖项。

phpunit.xml

PHPUnit 的配置文件，Laravel 默认使用该工具进行测试。

readme.md

一份关于 Laravel 的基本介绍的 Markdown 文件。如果使用 Laravel 安装程序，您将看不到此文件。

vite.config.js

（可选的）Vite 的配置文件。此文件指示您的构建系统如何编译和处理前端资产。

# 配置

您的 Laravel 应用程序的核心设置——数据库连接设置、队列和邮件设置等——存储在 *config* 文件夹中的文件中。这些文件中的每一个都返回一个 PHP 数组，并且数组中的每个值可以通过由文件名和所有后代键组成的配置键来访问，这些键由点（`.`）分隔。

因此，如果您在 *config/services.php* 创建以下内容的文件：

```php
// config/services.php
<?php
return [
    'sparkpost' => [
        'secret' => 'abcdefg',
    ],
];
```

您可以使用 `config('services.sparkpost.secret')` 访问该配置变量。

任何应该对每个环境都不同的配置变量（因此不应提交到源代码控制）将存储在你的 *.env* 文件中。假设你想为每个环境使用不同的 Bugsnag API 密钥。你可以设置配置文件从 *.env* 中获取它：

```php
// config/services.php
<?php
return [
    'bugsnag' => [
        'api_key' => env('BUGSNAG_API_KEY'),
    ],
];
```

这个 `env()` 辅助函数从你的 *.env* 文件中获取相同键的值。所以现在，将该键添加到你的 *.env*（这个环境的设置）和 *.env.example*（所有环境的模板）文件中：

```php
# In .env
BUGSNAG_API_KEY=oinfp9813410942
```

```php
# In .env.example
BUGSNAG_API_KEY=
```

你的 *.env* 文件已经包含了框架需要的许多环境特定变量，比如你将使用的邮件驱动程序以及基本的数据库设置。

# 在配置文件之外使用 env()

Laravel 中的某些功能，包括一些缓存和优化功能，如果你在配置文件之外的任何地方使用 `env()` 调用，则这些功能将不可用。

最佳方法是引入环境变量是设置为任何你想要的环境特定项目的配置项。让这些配置项读取环境变量，然后在你的应用程序的任何地方引用配置变量即可。

```php
// config/services.php
return [
    'bugsnag' => [
        'key' => env('BUGSNAG_API_KEY'),
    ],
];

// In controller, or whatever
$bugsnag = new Bugsnag(config('services.bugsnag.key'));
```

## .env 文件

让我们快速查看一下 *.env* 文件的默认内容。确切的键名会根据你使用的 Laravel 版本而有所不同，但可以查看 示例 2-1 来了解它们的样子。

##### 示例 2-1\. Laravel 中的默认环境变量

```php
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```

我不会详细讨论所有这些，因为其中有相当多只是各种服务的认证信息组（Pusher、Redis、DB、Mail）。但是，以下是你应该知道的两个重要环境变量：

`APP_KEY`

一个随机生成的字符串，用于加密数据。如果这个值为空，你可能会遇到“未指定应用加密密钥”的错误。在这种情况下，只需运行 `php artisan key:generate`，Laravel 将为你生成一个密钥。

`APP_DEBUG`

一个布尔值，确定你的应用实例的用户是否应该看到调试错误——适用于本地和暂存环境，但对生产环境不适用。

其余的非认证设置（`BROADCAST_DRIVER`、`QUEUE_CONNECTION` 等）都给定了默认值，尽可能少依赖外部服务，这对刚开始使用时非常合适。

当你启动你的第一个 Laravel 应用时，大多数项目你可能唯一想要更改的是数据库配置设置。我使用 Laravel Valet，所以我将 `DB_DATABASE` 更改为我的项目名称，将 `DB_USERNAME` 更改为 `root`，将 `DB_PASSWORD` 更改为空字符串：

```php
DB_DATABASE=myProject
DB_USERNAME=root
DB_PASSWORD=
```

然后，我在我最喜欢的 MySQL 客户端中创建一个与我的项目同名的数据库，然后就可以开始了。

# 运行与启动

现在，你已经使用裸的 Laravel 安装运行起来了。运行 `git init`，使用 `git add .` 和 `git commit` 提交裸文件，然后你就可以开始编码了。就是这样！如果你使用 Valet，你可以运行以下命令，立即在浏览器中看到你的站点上线：

```php
laravel new myProject && cd myProject && valet open
```

每次我启动一个新项目时，我都会执行以下步骤：

```php
laravel new myProject
cd myProject
git init
git add .
git commit -m "Initial commit"
```

我将所有的站点都放在*~/Sites*文件夹中，这是我设置为主要 Valet 目录的地方，所以在这种情况下，我可以立即在浏览器中访问*myProject.test*，而无需额外工作。我可以编辑*.env*并将其指向特定的数据库，在我的 MySQL 应用程序中添加该数据库，然后就可以开始编码了。

# 测试

此后的每一章，“测试”部分都会展示如何为涵盖的功能编写测试。由于本章不涉及可测试的功能，让我们快速讨论一下测试。 （要了解更多关于在 Laravel 中编写和运行测试的内容，请转到第十二章。）

Laravel 默认带有 PHPUnit 作为依赖项，并配置为在*tests*目录中任何以*Test.php*结尾的文件中运行测试（例如*tests/UserTest.php*）。

所以，编写测试的最简单方法是在名为*tests*的目录中创建一个以*Test.php*结尾的文件。而运行它们的最简单方法是从命令行（在项目根目录中）运行`./vendor/bin/phpunit`。

如果任何测试需要访问数据库，请确保从托管数据库的机器上运行您的测试—如果您在 Vagrant 中托管数据库，请确保通过`ssh`连接到您的 Vagrant 盒子来运行测试。同样，您可以在第十二章中了解更多相关内容。

此外，一些测试部分将使用测试语法和功能，如果您是第一次阅读本书，可能会对其中的代码感到困惑。如果测试部分的代码令人困惑，只需跳过它，并在阅读测试章节后再回头查看。

# TL;DR

由于 Laravel 是一个 PHP 框架，因此在本地运行它非常简单。Laravel 还提供了三种工具来管理您的本地开发环境：Sail，一个 Docker 设置；Valet，一个更简单的基于 macOS 的工具；以及 Homestead，一个预配置的 Vagrant 设置。Laravel 依赖于 Composer，并且默认情况下带有一系列反映其约定和与其他开源工具关系的文件和文件夹。
