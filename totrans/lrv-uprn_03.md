# 第三章 路由和控制器

任何 Web 应用程序框架的基本功能是接收用户请求并传递响应，通常通过 HTTP(S)完成。这意味着定义应用程序路由是学习 Web 框架时首先要解决的最重要的项目；没有路由，您几乎无法与最终用户进行交互。

在本章中，我们将探讨 Laravel 中的路由；您将看到如何定义它们，如何将它们指向应执行的代码，并如何使用 Laravel 的路由工具处理各种各样的路由需求。

# MVC、HTTP 动词和 REST 的快速介绍

我们在本章中讨论的大部分内容涉及如何组织 Model–View–Controller（MVC）应用程序的结构，我们将查看许多示例使用类 REST 的路由名称和动词，因此让我们快速看一下两者。

## 什么是 MVC？

在 MVC 中，您有三个主要概念：

模型

表示一个单独的数据库表（或来自该表的记录）—想象“公司”或“狗”。

视图

表示将数据输出到最终用户的模板—​想象“带有给定 HTML、CSS 和 JavaScript 集的登录页面模板”。

控制器

类似于交通警察，从浏览器接收 HTTP 请求，从数据库和其他存储机制获取正确的数据，验证用户输入，并最终向用户发送响应。

在图 3-1 中，您可以看到，最终用户将首先通过其浏览器发送 HTTP 请求与控制器进行交互。控制器响应该请求后，可能会向模型（数据库）写入数据和/或从模型中拉取数据。然后，控制器很可能会向视图发送数据，然后将视图返回给最终用户在其浏览器中显示。

![模型、视图和控制器以及用户的图表，彼此之间都有指向的箭头](img/lur3_0301.png)

###### 图 3-1. MVC 的基本示意图

我们将涵盖一些不符合这种相对简单的应用架构方式的 Laravel 用例，所以不要陷入 MVC，但这将至少让您准备好在我们讨论视图和控制器时接近本章的其余部分。

## HTTP 动词

最常见的 HTTP 动词是`GET`和`POST`，其次是`PUT`和`DELETE`。还有`HEAD`，`OPTIONS`和`PATCH`，以及两个在正常网页开发中几乎不使用的其他动词，`TRACE`和`CONNECT`。

这里是一个快速概述：

`GET`

请求资源（或资源列表）。

`HEAD`

请求`GET`响应的仅包含头信息的版本。

`POST`

创建资源。

`PUT`

覆盖资源。

`PATCH`

修改资源。

`DELETE`

删除资源。

`OPTIONS`

询问服务器此 URL 允许哪些动词。

Table 3-1 显示了资源控制器上可用的操作（更多详情见 “资源控制器”）。每个动作期望你使用特定的 URL 模式和特定的动词调用，因此你可以了解每个动词的用途。

表 3-1\. Laravel 资源控制器的方法

| 动词 | URL | 控制器方法 | 名称 | 描述 |
| --- | --- | --- | --- | --- |
| `GET` | `tasks` | `index()` | `tasks.index` | 显示所有任务 |
| `GET` | `tasks/create` | `create()` | `tasks.create` | 显示创建任务表单 |
| `POST` | `tasks` | `store()` | `tasks.store` | 接受创建任务表单的表单提交 |
| `GET` | `tasks/{task}` | `show()` | `tasks.show` | 显示一个任务 |
| `GET` | `tasks/{task}/edit` | `edit()` | `tasks.edit` | 编辑一个任务 |
| `PUT/PATCH` | `tasks/{task}` | `update()` | `tasks.update` | 接受编辑任务表单的表单提交 |
| `DELETE` | `tasks/{task}` | `destroy()` | `tasks.destroy` | 删除一个任务 |

## 什么是 REST？

我们将在 “REST-Like JSON APIs 基础” 中详细讨论 REST，但简要介绍一下，它是一种构建 API 的架构风格。在本书中讨论 REST 时，主要指一些特征，例如：

+   每次围绕一个主要资源结构化（例如，`tasks`）

+   由使用 HTTP 动词与可预测的 URL 结构进行交互组成（如 Table 3-1 中所见）

+   返回 JSON 数据并经常被请求为 JSON

这是更复杂的内容，但通常情况下，本书中使用的 “RESTful” 将意味着 “基于这些基于 URL 结构的模式，因此我们可以像 `GET /tasks/14/edit` 这样进行可预测的调用”。这很重要（即使不构建 API）因为 Laravel 的路由结构是基于类似 REST 的结构，正如你可以在 Table 3-1 中看到的。

基于 REST 的 API 主要遵循相同的结构，除了它们没有 *create* 路由或 *edit* 路由，因为 API 只表示动作，而不是为动作准备页面。

# 路由定义

在 Laravel 应用中，你将在 *routes/web.php* 中定义你的 web 路由，而在 *routes/api.php* 中定义 API 路由。*Web 路由* 是终端用户访问的路由；*API 路由* 是你的 API 的路由（如果有的话）。目前，我们主要关注 *routes/web.php* 中的路由。

定义路由的最简单方法是将路径（例如 `/`）与闭包匹配，如 Example 3-1 中所示。

##### Example 3-1\. 基本路由定义

```php
// routes/web.php
Route::get('/', function () {
    return 'Hello, World!';
});
```

现在你已经定义了如果有人访问 `/`（你域名的根），Laravel 的路由器应该运行在那里定义的闭包并返回结果。注意我们是 `return` 我们的内容，而不是 `echo` 或 `print` 它。

# 中间件的快速介绍

你可能会想：“为什么我返回 ‘Hello, World!’ 而不是回显它？”

有很多答案，但最简单的答案是 Laravel 的请求和响应周期周围有很多包装，包括一种称为*中间件*的东西。当路由闭包或控制器方法完成时，现在还不是将输出发送到浏览器的时间；返回内容允许它继续通过响应堆栈和中间件流动，然后再返回给用户。

许多简单的网站完全可以在 web 路由文件中定义。通过一些简单的`GET`路由结合一些模板，如示例 3-2 所示，您可以轻松地提供经典网站服务。

##### 示例 3-2\. 示例网站

```php
Route::get('/', function () {
    return view('welcome');
});

Route::get('about', function () {
    return view('about');
});

Route::get('products', function () {
    return view('products');
});

Route::get('services', function () {
    return view('services');
});
```

# 静态调用

如果您有 PHP 开发经验，您可能会惊讶地看到在`Route`类上进行静态调用。这实际上不是静态方法本身，而是使用 Laravel 的门面进行的服务定位，我们将在第 11 章中介绍。

如果您喜欢避免使用门面，您可以通过以下方式完成相同的定义：

```php
$router->get('/', function () {
    return 'Hello, World!';
});
```

## 路由动词

你可能已经注意到，我们在路由定义中一直在使用`Route::get()`。这意味着我们告诉 Laravel 只有当 HTTP 请求使用`GET`动作时才匹配这些路由。但是如果是表单的`POST`，或者可能是一些 JavaScript 发送的`PUT`或`DELETE`请求呢？在路由定义中调用的方法还有几个其他选项，如示例 3-3 所示。

##### 示例 3-3\. 路由动词

```php
Route::get('/', function () {
    return 'Hello, World!';
});

Route::post('/', function () {
    // Handle someone sending a POST request to this route
});

Route::put('/', function () {
    // Handle someone sending a PUT request to this route
});

Route::delete('/', function () {
    // Handle someone sending a DELETE request to this route
});

Route::any('/', function () {
    // Handle any verb request to this route
});

Route::match(['get', 'post'], '/', function () {
    // Handle GET or POST requests to this route
});
```

## 路由处理

正如您可能已经猜到的那样，将闭包传递给路由定义并不是教它如何解析路由的唯一方法。闭包快速简单，但是随着应用程序的规模变大，将所有路由逻辑放在一个文件中变得越来越笨拙。此外，使用路由闭包的应用程序无法利用 Laravel 的路由缓存（稍后详述），这可以减少每个请求高达数百毫秒的响应时间。

另一个常见选项是在闭包的位置以字符串形式传递控制器名称和方法，如示例 3-4 所示。

##### 示例 3-4\. 调用控制器方法的路由

```php
use App\Http\Controllers\WelcomeController;

Route::get('/', [WelcomeController::class, 'index']);
```

这告诉 Laravel 将请求传递给该路径的`index()`方法，该方法位于`App\Http\Controllers\WelcomeController`控制器中。此方法将接收相同的参数并像您可能替代放置在其中的闭包一样对待它。

## 路由参数

如果你定义的路由有参数——URL 结构中的可变段落——那么在路由中定义它们并传递给闭包非常简单（见示例 3-5）。

##### 示例 3-5\. 路由参数

```php
Route::get('users/{id}/friends', function ($id) {
    //
});
```

您还可以通过在参数名后面加上问号（`?`）使路由参数变为可选，如示例 3-6 所示。在这种情况下，您还应为路由的对应变量提供默认值。

##### 示例 3-6\. 可选路由参数

```php
Route::get('users/{id?}', function ($id = 'fallbackId') {
    //
});
```

你还可以使用正则表达式（regexes）来定义一个路由只有在参数满足特定要求时才匹配，就像在 示例 3-7 中一样。

##### 示例 3-7\. 正则表达式路由约束

```php
Route::get('users/{id}', function ($id) {
    //
})->where('id', '[0-9]+');

Route::get('users/{username}', function ($username) {
    //
})->where('username', '[A-Za-z]+');

Route::get('posts/{id}/{slug}', function ($id, $slug) {
    //
})->where(['id' => '[0-9]+', 'slug' => '[A-Za-z]+']);
```

正如你可能猜到的那样，如果访问的路径匹配了路由字符串但正则表达式不匹配参数，它将不会被匹配。由于路由从上到下匹配，`users/abc`会跳过 示例 3-7 的第一个闭包，但它将会被第二个闭包匹配，因此会被路由到那里。另一方面，`posts/abc/123`不会匹配任何闭包，因此会返回 404（未找到）错误。

Laravel 还提供了方便的方法来匹配常见的正则表达式模式，正如你在 示例 3-8 中看到的那样。

##### 示例 3-8\. 正则表达式路由约束辅助函数

```php
Route::get('users/{id}/friends/{friendname}', function ($id, $friendname) {
    //
})->whereNumber('id')->whereAlpha('friendname');

Route::get('users/{name}', function ($name) {
    //
})->whereAlphaNumeric('name');

Route::get('users/{id}', function ($id) {
    //
})->whereUuid('id');

Route::get('users/{id}', function ($id) {
    //
})->whereUlid('id');

Route::get('friends/types/{type}', function ($type) {
    //
})->whereIn('type', ['acquaintance', 'bestie', 'frenemy']);
```

## 路由名称

在应用程序中最简单的引用这些路由的方式只是使用它们的路径。如果需要的话，在视图中有一个`url()`全局辅助函数来简化链接；例如，查看 示例 3-9。这个辅助函数会在你的路由前加上你的站点的完整域名。

##### 示例 3-9\. `url()`辅助函数

```php
<a href="<?php echo url('/'); ?>">
// Outputs <a href="http://myapp.com/">
```

然而，Laravel 也允许你为每个路由命名，这样你就可以在不显式引用 URL 的情况下引用它们。这很有帮助，因为这意味着你可以给复杂的路由起一个简单的昵称，同时通过名称链接它们意味着如果路径变化，你不必重写前端链接（参见 示例 3-10）。

##### 示例 3-10\. 定义路由名称

```php
// Defining a route with name() in routes/web.php:
Route::get('members/{id}', [\App\Http\Controller\MemberController::class, 'show'])
    ->name('members.show');

// Linking the route in a view using the route() helper:
<a href="<?php echo route('members.show', ['id' => 14]); ?>">
```

这个例子展示了一些新概念。首先，我们使用流畅的路由定义通过在`get()`方法后链接`name()`方法来添加名称。这种方法允许我们为路由命名，为其提供一个简短的别名，以便在其他地方更容易引用。

在我们的例子中，我们将这个路由命名为`members.show`；`*resourcePlural*.*action*`是 Laravel 中用于路由和视图名称的常见约定。

本例还介绍了`route()`辅助函数。就像`url()`一样，它旨在在视图中使用，简化链接到命名路由。如果路由没有参数，你可以简单地传递路由名称（`route('members.index')`），会得到一个路由字符串（`"http://myapp.com/members"`）。如果有参数，就像我们在 示例 3-10 中所做的那样，将它们作为数组传递到第二个参数中。

一般来说，我建议使用路由名称而不是路径来引用你的路由，因此建议使用`route()`辅助函数而不是`url()`辅助函数。有时会显得有些笨拙，比如当你使用多个子域名时，但它提供了极大的灵活性，以便稍后更改应用程序的路由结构而不会受到重大惩罚。

# 路由组

通常，一组路由共享特定的特征—​特定的身份验证要求、路径前缀或者控制器命名空间。在每个路由上重复定义这些共享特征不仅显得乏味，而且还可能使路由文件的结构混乱，并且模糊了应用程序的一些结构。

*路由组* 允许您通过将多个路由分组在一起并一次性应用任何共享的配置设置来减少这种重复。此外，路由组对未来开发人员（以及您自己）是视觉线索，表明这些路由是一组的。

要将两个或多个路由分组在一起，您可以通过路由组定义周围的路由定义，如 Example 3-11 中所示。实际上，您正在将一个闭包传递给组定义，并在该闭包中定义分组的路由。

##### Example 3-11\. 定义一个路由组

```php
Route::group(function () {
    Route::get('hello', function () {
        return 'Hello';
    });
    Route::get('world', function () {
        return 'World';
    });
});
```

默认情况下，路由组实际上并不执行任何操作。在 Example 3-11 中使用组与在路由中使用代码注释分离的效果没有区别。

## 中间件

路由组最常见的用途之一是对一组路由应用中间件。您将在 Chapter 10 中学习更多关于中间件的内容，但它们主要用于 Laravel 中对用户进行身份验证和限制访客用户访问站点某些部分。

在 Example 3-12 中，我们围绕 `dashboard` 和 `account` 视图创建了一个路由组，并将 `auth` 中间件应用于两者。在此示例中，这意味着用户必须登录应用程序才能查看仪表板或帐户页面。

##### Example 3-12\. 限制一组路由仅供已登录用户使用

```php
Route::middleware('auth')->group(function() {
    Route::get('dashboard', function () {
        return view('dashboard');
    });
    Route::get('account', function () {
        return view('account');
    });
});
```

通常，将中间件附加到控制器中的路由比在路由定义时更清晰、更直接。您可以通过在控制器的构造函数中调用 `middleware()` 方法来实现这一点。传递给 `middleware()` 方法的字符串是中间件的名称，您还可以可选地链式调用修饰方法 (`only()` 和 `except()`) 来定义哪些方法将接收该中间件：

```php
class DashboardController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');

        $this->middleware('admin-auth')
            ->only('editUsers');

        $this->middleware('team-member')
            ->except('editUsers');
    }
}
```

注意，如果您经常进行“only”和“except”自定义，这通常是需要为异常路由新建一个控制器的标志。

## 路径前缀

如果一组路由共享其路径的一部分—​例如，如果您的站点的仪表板以 `/dashboard` 为前缀—​您可以使用路由组来简化此结构（参见 Example 3-13）。

##### Example 3-13\. 为一组路由添加前缀

```php
Route::prefix('dashboard')->group(function () {
    Route::get('/', function () {
        // Handles the path /dashboard
    });
    Route::get('users', function () {
        // Handles the path /dashboard/users
    });
});
```

注意，每个带前缀的组还有一个表示前缀根的 `/` 路由—​在 Example 3-13 中即为 `/dashboard`。

## 子域路由

子域路由与路由前缀相同，但其范围限定为子域而不是路由前缀。这有两个主要用途。首先，您可能希望为应用程序的不同部分（或完全不同的应用程序）提供不同的子域。示例 3-14 展示了如何实现这一点。

##### 示例 3-14\. 子域路由

```php
Route::domain('api.myapp.com')->group(function () {
    Route::get('/', function () {
        //
    });
});
```

其次，您可能希望将子域的一部分设置为参数，如示例 3-15 所示。这在多租户情况下最常见（想想 Slack 或 Harvest，每个公司都有自己的子域，如*tighten.slack.co*）。

##### 示例 3-15\. 参数化子域路由

```php
Route::domain('{account}.myapp.com')->group(function () {
    Route::get('/', function ($account) {
        //
    });
    Route::get('users/{id}', function ($account, $id) {
        //
    });
});
```

注意，组的任何参数都作为第一个参数传递给组内路由的方法。

## 名称前缀

路由名称通常会反映路径元素的继承链，因此`users/comments/5`将由名为`users.comments.show`的路由提供服务。在这种情况下，通常在所有属于`users.comments`资源下的路由周围使用路由组。

就像我们可以为 URL 段添加前缀一样，我们也可以为路由名称添加前缀字符串。使用路由组名称前缀，我们可以定义该组内的每个路由名称都应该以给定的字符串前缀`"users."`开头，然后是`"comments."`（参见示例 3-16）。

##### 示例 3-16\. 路由组名称前缀

```php
Route::name('users.')->prefix('users')->group(function () {
    Route::name('comments.')->prefix('comments')->group(function () {
        Route::get('{id}', function () {
            // ...
        })->name('show'); // Route named 'users.comments.show'

        Route::destroy('{id}', function () {})->name('destroy');
    });
});
```

## 路由组控制器

当您对由同一控制器提供服务的路由进行分组时，例如我们显示、编辑和删除用户时，可以使用路由组的`controller()`方法，如示例 3-17 所示，避免为每个路由定义完整的元组。

##### 示例 3-17\. 路由组控制器

```php
use App\Http\Controllers\UserController;

Route::controller(UserController::class)->group(function () {
    Route::get('/', 'index');
    Route::get('{id}', 'show');
});
```

## 回退路由

在 Laravel 中，您可以定义一个“回退路由”（需要在路由文件末尾定义），以捕获所有未匹配的请求：

```php
Route::fallback(function () {
    //
});
```

# 签名路由

许多应用程序定期发送关于一次性操作的通知（如重置密码、接受邀请等），并提供简单的链接执行这些操作。让我们想象发送一封电子邮件，确认收件人愿意加入邮件列表。

有三种方式发送该链接：

+   将该 URL 公开，并希望没有其他人发现批准 URL 或修改自己的批准 URL 以批准其他人。

+   将操作放在身份验证后，链接到操作，并要求用户在尚未登录的情况下登录（在这种情况下，许多邮件列表接收者可能不会是具有帐户的用户，因此可能不可能登录）。

+   “签名”链接，以唯一证明用户从您的电子邮件收到了链接，而无需登录——类似于*http://myapp.com/invitations/5816/yes?signature=030ab0ef6a8237bd86a8b8*。

实现最后一种选项的一种简单方法是使用称为 *signed URLs* 的功能，它使得为发送验证链接的人员构建签名身份验证系统变得简单。这些链接由正常路由链接组成，附加一个“签名”，证明自链接发送以来未更改该 URL（因此没有人修改了 URL 以访问他人的信息）。

## 签名路由

要构建一个签名 URL 来访问给定路由，该路由必须有一个名称：

```php
Route::get('invitations/{invitation}/{answer}', InvitationController::class)
    ->name('invitations');
```

要生成到此路由的普通链接，您可以使用我们已经介绍的 `route()` 辅助函数，但您也可以使用 `URL` 外观执行相同的操作：`URL::route('invitations', ['invitation' => 12345, 'answer' => 'yes'])`。要生成带有 *signed* 的链接到此路由，只需使用 `signedRoute()` 方法。如果您想生成带有过期时间的签名路由，请使用 `temporarySignedRoute()`：

```php
// Generate a normal link
URL::route('invitations', ['invitation' => 12345, 'answer' => 'yes']);

// Generate a signed link
URL::signedRoute('invitations', ['invitation' => 12345, 'answer' => 'yes']);

// Generate an expiring (temporary) signed link
URL::temporarySignedRoute(
    'invitations',
    now()->addHours(4),
    ['invitation' => 12345, 'answer' => 'yes']
);
```

# 使用 now() 辅助函数

Laravel 提供了 `now()` 辅助函数，相当于 `Carbon::now()`；它返回一个代表当前时间的 Carbon 对象。

Carbon 是 Laravel 自带的日期时间库。

## 修改路由以允许签名链接

现在您已经生成了到您的签名路由的链接，您需要保护免受任何未签名的访问。最简单的选择是应用 `signed` 中间件：

```php
Route::get('invitations/{invitation}/{answer}', InvitationController::class)
    ->name('invitations')
    ->middleware('signed');
```

如果您愿意，您可以手动验证使用 `Request` 对象上的 `hasValidSignature()` 方法，而不是使用 `signed` 中间件：

```php
class InvitationController
{
    public function __invoke(Invitation $invitation, $answer, Request $request)
    {
        if (! $request->hasValidSignature()) {
            abort(403);
        }

        //
    }
}
```

# 视图

在我们之前查看的一些路由闭包中，我们看到类似 `return view('account')` 的代码。这里发生了什么？

在 MVC 模式中（见图 3-1），*视图*（或模板）是描述特定输出应该如何看起来的文件。您可能有输出 JSON、XML 或电子邮件的视图，但在 Web 框架中，最常见的视图输出是 HTML。

在 Laravel 中，您可以使用两种视图格式：纯 PHP 和 Blade 模板（参见第四章）。区别在于文件名：*about.php* 将使用 PHP 引擎呈现，而 *about.blade.php* 将使用 Blade 引擎呈现。

# 加载视图的三种方式

有三种方法可以返回视图。目前只需要关注 `view()`，但如果你看到 `View::make()`，它是一样的，或者你可以注入 `Illuminate\View\ViewFactory` 如果你更喜欢。

一旦您使用 `view()` 辅助函数“加载”视图，您可以选择简单地返回它（如示例 3-18），如果视图不依赖于控制器中的任何变量，这将运行良好。

##### 示例 3-18\. 简单的 `view()` 使用方法

```php
Route::get('/', function () {
   return view('home');
});
```

此代码查找 *resources/views/home.blade.php* 或 *resources/views/home.php* 中的视图，并加载其内容并解析任何内联 PHP 或控制结构，直到只剩下视图的输出。一旦返回它，它将传递给响应堆栈的其余部分，并最终返回给用户。

但是如果需要传入变量怎么办？看一下示例 3-19。

##### 示例 3-19\. 将变量传递给视图

```php
Route::get('tasks', function () {
    return view('tasks.index')
        ->with('tasks', Task::all());
});
```

此闭包加载 *resources/views/tasks/index.blade.php* 或 *resources/views/tasks/index.php* 视图，并传递一个名为 `tasks` 的单一变量，其中包含 `Task::all()` 方法的结果。 `Task::all()` 是您将在第五章学习的 Eloquent 数据库查询。

## 使用 Route::view() 直接返回简单路由

因为路由只返回视图而不传递自定义数据非常常见，所以 Laravel 允许您将路由定义为“视图”路由，甚至不传递闭包或控制器/方法引用，正如您可以在示例 3-20 中看到的那样。

##### 示例 3-20\. `Route::view()`

```php
// Returns resources/views/welcome.blade.php
Route::view('/', 'welcome');

// Passing simple data to Route::view()
Route::view('/', 'welcome', ['User' => 'Michael']);
```

## 使用视图组合器与每个视图共享变量

有时反复传递相同变量可能会变得很麻烦。可能有一个您希望每个网站视图或某个视图类或某个包含的子视图都可以访问的变量，例如与任务相关的所有视图或页眉部分。

可以与每个模板或仅某些模板共享某些变量，如以下代码所示：

```php
view()->share('variableName', 'variableValue');
```

想了解更多，请参阅“视图组合器和服务注入”。

# 控制器

我已经多次提到控制器，但直到现在，大多数示例都展示了路由闭包。在 MVC 模式中，控制器本质上是组织一个或多个路由逻辑的类，集中在一个地方。控制器倾向于将类似的路由组合在一起，特别是如果您的应用程序结构类似于传统的 CRUD 格式；在这种情况下，控制器可能处理与特定资源相关的所有操作。

# 什么是 CRUD？

CRUD 是 *创建*、*读取*、*更新*、*删除* 的缩写，这是 Web 应用程序对资源提供的四种主要操作。例如，您可以创建新的博客文章，可以阅读该文章，可以更新它，或者可以删除它。

或许把应用程序所有的逻辑都塞进控制器中是很诱人的，但是将控制器视为路由 HTTP 请求在应用程序中导航的交通警察会更好。由于请求可以通过其他方式进入应用程序——如定时任务、Artisan 命令行调用、队列作业等——因此不要依赖控制器处理太多行为是明智的。这意味着控制器的主要工作是捕获 HTTP 请求的意图并将其传递给应用程序的其他部分。

所以，让我们创建一个控制器。一个简单的方法是使用 Artisan 命令，在命令行中运行以下命令：

```php
php artisan make:controller TaskController
```

# Artisan 和 Artisan 生成器

Laravel 捆绑了一个称为 Artisan 的命令行工具。 Artisan 可用于运行迁移，手动创建用户和其他数据库记录，并执行许多其他一次性手动任务。

在`make`命名空间下，Artisan 提供了生成各种系统文件骨架文件的工具。这就是我们能够运行`php artisan make:controller`的原因。

要了解更多关于这个和其他 Artisan 功能的信息，请参阅第八章。

这将在*app/Http/Controllers*目录下创建一个名为*TaskController.php*的新文件，并显示示例 3-21 中的内容。

##### 示例 3-21\. 默认生成的控制器

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TaskController extends Controller
{
    //
}
```

修改这个文件，如示例 3-22 所示，创建一个名为`index()`的新公共方法。我们将在那里返回一些文本。

##### 示例 3-22\. 简单的控制器示例

```php
<?php

namespace App\Http\Controllers;

class TaskController extends Controller
{
    public function index()
    {
        return 'Hello, World!';
    }
}
```

然后，就像我们之前学到的那样，我们将把一个路由链接到它，如示例 3-23 所示。

##### 示例 3-23\. 简单控制器的路由

```php
// routes/web.php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TaskController;

Route::get('/', [TaskController::class, 'index']);
```

就这样。访问`/`路由，你将看到“Hello, World!”这几个字。

控制器方法最常见的用法之一将是像示例 3-24 那样，它提供了与我们在示例 3-19 中路由闭包相同的功能。

##### 示例 3-24\. 常见的控制器方法示例

```php
// TaskController.php
...
public function index()
{
    return view('tasks.index')
        ->with('tasks', Task::all());
}
```

这个控制器方法加载*resources/views/tasks/index.blade.php*或*resources/views/tasks/index.php*视图，并传递一个名为`tasks`的单个变量，其中包含`Task::all()` Eloquent 方法的结果。

# 生成资源控制器

如果你想要创建一个资源控制器，并为所有基本的资源路由（如`create()`和`update()`）生成自动生成的方法，你可以在使用`php artisan make:controller`时传递`--resource`标志：

```php
php artisan make:controller TaskController --resource
```

## 获取用户输入

控制器方法中执行的第二个最常见操作是从用户获取输入并对其进行操作。这引入了一些新概念，所以让我们看一些示例代码，并逐步了解新的内容。

首先，让我们绑定我们的路由；参见示例 3-25。

##### 示例 3-25\. 绑定基本表单操作

```php
// routes/web.php
Route::get('tasks/create', [TaskController::class, 'create']);
Route::post('tasks', [TaskController::class, 'store']);
```

注意我们正在绑定`tasks/create`的`GET`动作（显示创建新任务的表单）和`tasks`的`POST`动作（我们创建新任务时将`POST`到的地方）。我们可以假设我们控制器中的`create()`方法只是显示一个表单，所以让我们看看示例 3-26 中的`store()`方法。

##### 示例 3-26\. 常见的表单输入控制器方法

```php
// TaskController.php
...
public function store()
{
    Task::create(request()->only(['title', 'description']));

    return redirect('tasks');
}
```

这个示例使用了 Eloquent 模型和`redirect()`功能，稍后我们会详细讲解它们，但现在让我们快速看看我们如何在这里获取我们的数据。

我们使用`request()`助手来表示 HTTP 请求（稍后详细介绍），并使用它的`only()`方法来提取用户提交的`title`和`description`字段。

然后，我们将这些数据传递给我们的`Task`模型的`create()`方法，该方法使用传入的标题设置一个新的`Task`实例，并将传入的描述设置为`description`。最后，我们重定向回显示所有任务的页面。

这里有几层抽象在起作用，我们稍后会详细介绍，但需要知道来自 `only()` 方法的数据来自于 `Request` 对象上所有常用方法使用的同一组数据池，包括 `all()` 和 `get()`。每个方法提取的数据集代表了所有用户提供的数据，无论是来自查询参数还是 `POST` 值。所以，我们的用户在“添加任务”页面上填写了两个字段：“标题”和“描述”。

简单解释一下，`request()->only()`接受一个输入名称的关联数组并返回它们：

```php
request()->only(['title', 'description']);
// returns:
[
    'title' => 'Whatever title the user typed on the previous page',
    'description' => 'Whatever description the user typed on the previous page',
]
```

而 `Task::create()` 接受一个关联数组并根据此数组创建一个新任务：

```php
Task::create([
    'title' => 'Buy milk',
    'description' => 'Remember to check the expiration date this time, Norbert!',
]);
```

结合它们一起，只需用户提供的“标题”和“描述”字段就可以创建一个任务。

## 将依赖项注入到控制器中

Laravel 的外观和全局助手为 Laravel 代码库中最有用的类提供了一个简单的接口。你可以获取关于当前请求和用户输入、会话、缓存等的信息。

但是如果你更喜欢注入你的依赖项，或者想使用一个没有外观或助手的服务，你需要找到一些方法将这些类的实例引入到你的控制器中。

这是我们第一次接触 Laravel 的服务容器。如果现在这个概念还不熟悉，可以把它想象成是一点点 Laravel 的魔法；或者，如果你想更深入地了解它的实际运作方式，可以跳到第十一章。

所有控制器方法（包括构造函数）都是通过 Laravel 的容器解析出来的，这意味着任何你在容器中能够解析的类型提示将被自动注入。

# PHP 中的类型提示

*PHP 中的类型提示* 意味着在方法签名中的变量前放置类或接口的名称：

```php
public function __construct(Logger $logger) {}
```

这个类型提示告诉 PHP，传递到方法中的任何东西 *必须* 是 `Logger` 类型，这可以是接口或类。

作为一个好的示例，如果你更喜欢使用 `Request` 对象的实例而不是使用全局助手，只需在你的方法参数中进行类型提示 `Illuminate\Http\Request`，就像在示例 3-27 中一样。

##### 示例 3-27\. 通过类型提示进行控制器方法注入

```php
// TaskController.php
...
public function store(\Illuminate\Http\Request $request)
{
    Task::create($request->only(['title', 'description']));

    return redirect('tasks');
}
```

所以，你定义了一个必须传递到 `store()` 方法中的参数。由于你进行了类型提示，并且由于 Laravel 知道如何解析该类名，你将在方法中直接得到 `Request` 对象，而不需要额外工作。没有显式绑定，没有其他任何东西——它只是作为 `$request` 变量存在。

而且，通过比较示例 3-26 和 3-27，可以看出 `request()` 助手和 `Request` 对象的行为完全一样。

## 资源控制器

有时候，在编写控制器时，为控制器中的方法命名可能是最困难的部分。幸运的是，Laravel 对传统的 REST/CRUD 控制器（在 Laravel 中称为 *资源控制器*）有一些约定，此外，它还提供了一个默认生成器和一个便捷的路由定义，允许你一次性绑定整个资源控制器。

要查看 Laravel 期望资源控制器的方法，请从命令行生成一个新的控制器：

```php
php artisan make:controller MySampleResourceController --resource
```

现在打开 *app/Http/Controllers/MySampleResourceController.php*。你会看到它已经预先填充了许多方法。让我们逐一看看每个方法代表什么。我们将以一个`Task`为例。

### Laravel 资源控制器的方法

还记得前面的表格吗？Table 3-1 显示了每个默认生成的方法的 HTTP 动词、URL、控制器方法名以及名称。

### 绑定资源控制器

所以，我们已经看到这些是在 Laravel 中使用的传统路由名称，也看到可以轻松生成一个带有每个默认路由方法的资源控制器。幸运的是，如果你不想手动为每个控制器方法生成路由，也不必如此。有一个名为 *资源控制器绑定* 的技巧，看看 Example 3-28。

##### 示例 3-28\. 资源控制器绑定

```php
// routes/web.php
Route::resource('tasks', TaskController::class);
```

这将自动将资源中列出的所有路由绑定到指定控制器上相应的方法名。它还会适当地命名这些路由；例如，`tasks`资源控制器上的`index()`方法将被命名为`tasks.index`。

# artisan route:list

如果你发现自己想知道当前应用程序有哪些可用路由，请使用工具来解决这个问题：从命令行运行 `php artisan route:list`，你将得到所有可用路由的列表。我更喜欢 `php artisan route:list --exclude-vendor`，这样我就不会看到所有我依赖项注册的怪异路由（参见图 3-2）。

![lur3 0302](img/lur3_0302.png)

###### 图 3-2\. artisan route:list

## API 资源控制器

当你使用 RESTful API 时，资源的潜在操作列表与 HTML 资源控制器不同。例如，你可以向 API 发送 `POST` 请求来创建资源，但在 API 中你不能真正“显示创建表单”。

要生成一个 *API 资源控制器*，它的结构与资源控制器相同，但不包括 *create* 和 *edit* 操作，请在创建控制器时传递 `--api` 标志：

```php
php artisan make:controller MySampleResourceController --api
```

要绑定一个 API 资源控制器，请使用 `apiResource()` 方法，而不是 `resource()` 方法，如 Example 3-29 所示。

##### 示例 3-29\. API 资源控制器绑定

```php
// routes/web.php
Route::apiResource('tasks', TaskController::class);
```

## 单一操作控制器

在你的应用程序中会有时候需要一个控制器只服务一个路由。你可能会想知道如何为该路由命名控制器方法。幸运的是，你可以将单个路由指向单个控制器，而无需担心命名该方法。

正如你可能已经知道的那样，`__invoke()` 方法是 PHP 的一个魔术方法，允许你“调用”类的实例，像调用函数一样调用它。这是 Laravel 的 *单操作控制器* 使用的工具，允许你将路由指向单个控制器，正如你可以在 示例 3-30 中看到的那样。

##### 示例 3-30\. 使用 `__invoke()` 方法

```php
// \App\Http\Controllers\UpdateUserAvatar.php
public function __invoke(User $user)
{
    // Update the user's avatar image
}

// routes/web.php
Route::post('users/{user}/update-avatar', UpdateUserAvatar::class);
```

# 路由模型绑定

最常见的路由模式之一是任何控制器方法的第一行尝试查找具有给定 ID 的资源，例如 示例 3-31。

##### 示例 3-31\. 为每个路由获取资源

```php
Route::get('conferences/{id}', function ($id) {
    $conference = Conference::findOrFail($id);
});
```

Laravel 提供了一个简化此模式的功能称为 *路由模型绑定*。这允许你定义一个特定的参数名称（例如 `{conference}`），表示路由解析器应该查找具有该 ID 的 Eloquent 数据库记录，然后将其作为参数传递给闭包或控制器方法 *而不是* 仅仅传递 ID。

有两种类型的路由模型绑定：隐式和自定义（或显式）。

## 隐式路由模型绑定

使用路由模型绑定的最简单方法是将路由参数命名为该模型独有的名称（例如，将其命名为 `$conference` 而不是 `$id`），然后在闭包/控制器方法中对该参数进行类型提示并在那里使用相同的变量名。展示起来比描述容易，因此请查看 示例 3-32。

##### 示例 3-32\. 使用隐式路由模型绑定

```php
Route::get('conferences/{conference}', function (Conference $conference) {
    return view('conferences.show')->with('conference', $conference);
});
```

因为路由参数（`{conference}`）与方法参数（`$conference`）相同，并且方法参数是用 `Conference` 模型进行类型提示的（`Conference $conference`），Laravel 将其视为路由模型绑定。每次访问此路由时，应用程序将假定传入 URL 中的任何内容代替 `{conference}` 都是一个 ID，应该用于查找 `Conference`，然后将生成的模型实例传递给你的闭包或控制器方法。

# 自定义 Eloquent 模型的路由键

每当通过 URL 段查找 Eloquent 模型（通常是因为路由模型绑定），Eloquent 将使用其主键（ID）进行查找。

要更改你的 Eloquent 模型在所有路由中用于 URL 查找的列，请在模型中添加一个名为 `getRouteKeyName()` 的方法：

```php
public function getRouteKeyName()
{
    return 'slug';
}
```

现在，像 `conferences/{conference}` 这样的 URL 将期望从 slug 列获取条目而不是 ID，并且将根据其进行查找。

# 在特定路由中自定义路由键

在 Laravel 中，你还可以通过在路由定义中追加冒号和列名来在特定路由上更改路由键：

```php
Route::get(
    'conferences/{conference:slug}',
    function (Conference $conference) {
        return view('conferences.show')
            ->with('conference', $conference);
    });
```

如果你的 URL 中有两个动态段（例如：`organizers/{organizer}/conferences/{conference:slug}`），Laravel 将自动尝试将第二个模型的查询范围限定为仅与第一个相关联的内容。因此，它将检查 `Organizer` 模型是否具有 `conferences` 关系，如果存在，则只返回与第一个段找到的 `Organizer` 相关联的 Conferences。

```php
use App\Models\Conference;
use App\Models\Organizer;

Route::get(
    'organizers/{organizer}/conferences/{conference:slug}',
    function (Organizer $organizer, Conference $conference) {
        return $conference;
    });
```

## 自定义路由模型绑定

要手动配置路由模型绑定，请在 `App\Providers\RouteServiceProvider` 的 `boot()` 方法中添加类似 Example 3-33 的行。

##### 示例 3-33\. 添加路由模型绑定

```php
    public function boot()
    {
        // Perform the binding
        Route::model('event', Conference::class);
    }
```

现在你指定了当路由定义中有一个名为 `{event}` 的参数时（例如 Example 3-34 中演示的），路由解析器将返回一个带有该 URL 参数 ID 的 `Conference` 类的实例。

##### 示例 3-34\. 使用显式路由模型绑定

```php
Route::get('events/{event}', function (Conference $event) {
    return view('events.show')->with('event', $event);
});
```

# 路由缓存

如果你想要尽可能减少加载时间，你可能需要关注 *路由缓存*。 Laravel 启动过程中的一个步骤是解析 *routes/** 文件，可能需要几十到几百毫秒，而路由缓存可以极大加快这一过程。

要缓存你的路由文件，你需要使用所有控制器、重定向、视图和资源路由（不使用路由闭包）。如果你的应用程序不使用任何路由闭包，你可以运行 `php artisan route:cache`，Laravel 将序列化你的 *routes/** 文件的结果。如果想删除缓存，请运行 `php artisan route:clear`。

这里的问题是：Laravel 现在将匹配路由与缓存文件而不是实际的 *routes/** 文件。你可以对路由文件进行无限更改，但在再次运行 `route:cache` 之前，这些更改不会生效。这意味着每次更改都需要重新缓存，这可能会带来很多混乱的潜力。

我建议的替代方案是：由于 Git 默认会忽略路由缓存文件，因此考虑仅在生产服务器上使用路由缓存，并在每次部署新代码时运行 `php artisan route:cache` 命令（无论是通过 Git 的后处理挂钩、Forge 部署命令还是其他部署系统的一部分）。这样，你不会在本地开发时遇到混乱的问题，但远程环境仍然可以从路由缓存中受益。

# 表单方法欺骗

有时你需要手动定义表单应发送的 HTTP 动词。HTML 表单只允许 `GET` 或 `POST`，所以如果你想使用其他动词，就需要自行指定。

## Laravel 中的 HTTP 动词

正如我们已经看到的，你可以使用 `Route::get()`、`Route::post()`、`Route::any()` 或 `Route::match()` 来定义路由匹配的动词。你还可以使用 `Route::patch()`、`Route::put()` 和 `Route::delete()` 进行匹配。

但是如何通过 Web 浏览器发送除了 `GET` 之外的请求呢？首先，HTML 表单中的 `method` 属性决定了它的 HTTP 动词：如果你的表单的 `method` 是 `"GET"`，它将通过查询参数和 `GET` 方法提交；如果表单的 `method` 是 `"POST"`，它将通过 post body 和 `POST` 方法提交。

JavaScript 框架使得发送其他请求变得容易，比如 `DELETE` 和 `PATCH`。但是如果你发现自己需要在 Laravel 中提交除了 `GET` 或 `POST` 之外的动词的 HTML 表单，你需要使用 *表单方法伪造*（form method spoofing），这意味着在 HTML 表单中伪造 HTTP 方法。

## HTML 表单中的 HTTP 方法伪造

要告诉 Laravel 当前提交的表单应被视为非 `POST` 方法的请求，需添加一个名为 `_method` 的隐藏变量，并赋值为 `"PUT"`, `"PATCH"` 或 `"DELETE"`，Laravel 将会根据该动词匹配和路由该表单提交。

示例 3-35 中的表单，因为它传递了方法 `"DELETE"` 给 Laravel，将匹配使用 `Route::delete()` 定义的路由，但不会匹配使用 `Route::post()` 定义的路由。

##### 示例 3-35\. 表单方法伪造

```php
<form action="/tasks/5" method="POST">
    <input type="hidden" name="_method" value="DELETE">
    <!-- or: -->
    @method('DELETE')
</form>
```

# CSRF 保护

如果你已经尝试在 Laravel 应用中提交表单，包括 示例 3-35 中的表单，你很可能遇到了可怕的 `TokenMismatchException`。

在 Laravel 中，默认情况下，除了“只读”路由（使用 `GET`、`HEAD` 或 `OPTIONS`）之外的所有路由都受到跨站请求伪造（CSRF）攻击的保护，要求在每个请求中传递一个名为 `_token` 的输入。此 token 在每个会话开始时生成，并且每个非“只读”路由会将提交的 `_token` 与会话 token 进行比较。

# 什么是 CSRF？

*跨站请求伪造* 是指一个网站假装成另一个网站。其目的是通过在已登录用户的浏览器中从 *他们* 的网站提交到 *你* 的网站上的表单，来劫持你用户对你网站的访问权限。

避免 CSRF 攻击的最佳方法是默认保护所有入站路由——`POST`、`DELETE` 等——通过一个 token，Laravel 已经内置支持。

你有两种方法可以解决 CSRF 错误。第一种，也是首选的方法，是在每个提交中添加 `_token` 输入。在 HTML 表单中，有一种简单的方法，如 示例 3-36 中所示。

##### 示例 3-36\. CSRF tokens

```php
<form action="/tasks/5" method="POST">
    @csrf
</form>
```

在 JavaScript 应用中，需要更多的工作，但并不复杂。对于使用 JavaScript 框架的站点，最常见的解决方案是在每个页面的 `<meta>` 标签中存储 token，例如：

```php
<meta name="csrf-token" content="<?php echo csrf_token(); ?>">
```

将 token 存储在 `<meta>` 标签中使得可以轻松地绑定到正确的 HTTP 头部，你可以像在 示例 3-37 中一样，全局为你的 JavaScript 框架的所有请求绑定它。

##### 示例 3-37\. 全局绑定 CSRF 的头部

```php
// In jQuery:
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});

// With Axios: it automatically retrieves it from a cookie. Nothing to do!
```

Laravel 将在每个请求中检查 `X-CSRF-TOKEN`（以及 Axios 和其他 JavaScript 框架如 Angular 使用的 `X-XSRF-TOKEN`），并且传递有效的令牌将标记 CSRF 保护为满足状态。

# 使用 Vue Resource 绑定 CSRF 令牌的方法有些不同于 Laravel；查看 [Vue Resource 文档](https://oreil.ly/YT0Nb) 获取示例。

将 CSRF 令牌引导到 Vue Resource 看起来与 Laravel 的方式有所不同；查看 [Vue Resource 文档](https://oreil.ly/YT0Nb) 获取示例。

# 重定向

到目前为止，我们明确讨论过从控制器方法或路由定义中返回的仅仅是视图。但是还有一些其他结构可以返回，以便向浏览器提供行为指令。

首先，让我们来讨论 *重定向*。你在其他示例中已经看到了一些这样的示例。生成重定向有两种常见的方法；我们将在这里使用 `redirect()` 全局辅助方法，但你可能更喜欢门面。两者都会创建一个 `Illuminate\Http\RedirectResponse` 实例，对其执行一些便捷方法，然后返回它。你也可以手动执行这些操作，但你将需要做更多的工作。看看 示例 3-38，你可以看到几种返回重定向的方法。

##### 示例 3-38\. 返回重定向的不同方式

```php
// Using the global helper to generate a redirect response
Route::get('redirect-with-helper', function () {
    return redirect()->to('login');
});

// Using the global helper shortcut
Route::get('redirect-with-helper-shortcut', function () {
    return redirect('login');
});

// Using the facade to generate a redirect response
Route::get('redirect-with-facade', function () {
    return Redirect::to('login');
});

// Using the Route::redirect shortcut
Route::redirect('redirect-by-route', 'login');
```

请注意，`redirect()` 辅助方法暴露了与 `Redirect` 门面相同的方法，但它还有一个快捷方式；如果你直接将参数传递给辅助方法，而不是在其后链接方法，那么这是 `to()` 重定向方法的快捷方式。

还要注意，`Route::redirect()` 路由辅助方法的（可选的）第三个参数可以是重定向的状态码（例如，302）。

## `redirect()->to()`

用于重定向的 `to()` 方法的方法签名如下：

```php
function to($to = null, $status = 302, $headers = [], $secure = null)
```

`$to` 是有效的内部路径，`$status` 是 HTTP 状态（默认为 `302`），`$headers` 允许你定义随重定向发送的 HTTP 头，`$secure` 允许你覆盖默认的 `http` 或 `https` 选择（通常基于当前请求 URL 设置）。示例 3-39 展示了其使用示例。

##### 示例 3-39\. `redirect()->to()`

```php
Route::get('redirect', function () {
    return redirect()->to('home');

    // Or same, using the shortcut:

    return redirect('home');
});
```

## `redirect()->route()`

`route()` 方法与 `to()` 方法相同，但不是指向特定路径，而是指向特定路由名称（参见 示例 3-40）。

##### 示例 3-40\. `redirect()->route()`

```php
Route::get('redirect', function () {
    return redirect()->route('conferences.index');
});
```

请注意，由于某些路由名称需要参数，其参数顺序有点不同。`route()` 方法有一个可选的第二个参数用于路由参数：

```php
function route($to = null, $parameters = [], $status = 302, $headers = [])
```

因此，使用它可能看起来有点像 示例 3-41。

##### 示例 3-41\. `redirect()->route()` 带有参数

```php
Route::get('redirect', function () {
    return to_route('conferences.show', [
        'conference' => 99,
    ];
});
```

# 使用 `to_route()` 辅助方法

你可以使用 `to_route()` 辅助方法作为 `redirect()->route()` 方法的别名。它们的签名都是一样的：

```php
Route::get('redirect', function () {
    return to_route('conferences.show', ['conference' => 99]);
});
```

## `redirect()->back()`

由于 Laravel 会话实现的一些内置便利性，您的应用程序始终知道用户之前访问的页面是什么。这就开启了`redirect()->back()`重定向的机会，它简单地将用户重定向到他们来自的页面。这也有一个全局快捷方式：`back()`。

## 其他重定向方法

重定向服务提供了其他一些较少使用但仍可用的方法：

`refresh()`

重定向到用户当前正在访问的同一页面。

`away()`

允许重定向到外部 URL，而不进行默认 URL 验证。

`secure()`

像`to()`一样，`secure`参数设置为`"true"`。

`action()`

允许您以两种方式之一链接到控制器和方法：作为字符串（`redirect()->action('MyController@myMethod')`）或作为元组（`r⁠e⁠d⁠i⁠r⁠e⁠c⁠t⁠(⁠)​\-⁠>⁠a⁠c⁠t⁠i⁠o⁠n⁠([MyController::class, 'myMethod'])`）。

`guest()`

在认证系统内部使用（在第九章中讨论）；当用户访问他们未经身份验证的路由时，这会捕获“预期”路由，然后重定向用户（通常是到登录页面）。

`intended()`

也在认证系统内部使用；成功认证后，它会获取`guest()`方法存储的“预期”URL，并将用户重定向到那里。

## `redirect()->with()`

虽然它的结构与您在`redirect()`上调用的其他方法类似，但`with()`不同之处在于它不定义您要重定向到哪里，而是定义您要传递的数据。当您将用户重定向到不同页面时，通常希望将某些数据传递给他们。您可以手动将数据闪存到会话中，但 Laravel 提供了一些便利方法来帮助您完成这些操作。

最常见的是，您可以使用`with()`传递键和值的数组或单个键和值，就像示例 3-42 中的一样。这会将您的`with()`数据保存到会话中，只用于下一次页面加载。

##### 示例 3-42\. 带数据的重定向

```php
Route::get('redirect-with-key-value', function () {
    return redirect('dashboard')
        ->with('error', true);
});

Route::get('redirect-with-array', function () {
    return redirect('dashboard')
        ->with(['error' => true, 'message' => 'Whoops!']);
});
```

# 在重定向上链接方法

与许多其他门面一样，`Redirect`门面的大多数调用可以接受流畅的方法链，就像示例 3-42 中的`with()`调用一样。您将在“什么是流畅接口？”中了解更多信息。

您还可以使用`withInput()`，如示例 3-43 中所示，以闪存用户的表单输入重定向；这在验证错误的情况下最常见，您希望将用户发送回他们刚刚来自的表单。

##### 示例 3-43\. 带表单输入的重定向

```php
Route::get('form', function () {
    return view('form');
});

Route::post('form', function () {
    return redirect('form')
        ->withInput()
        ->with(['error' => true, 'message' => 'Whoops!']);
});
```

获取通过`withInput()`传递的闪存输入的最简单方法是使用`old()`辅助函数，它可以用于获取所有旧输入（`old()`）或只是特定键的值，如下例所示，如果没有旧值，则第二个参数作为默认值。你通常会在视图中看到这一点，这使得这段 HTML 可以在此表单的“创建”和“编辑”视图中通用：

```php
<input name="username" value="<?=
 old('username', 'Default username instructions here');
?>">
```

谈到验证，还有一种有用的方法可以将错误与重定向响应一起传递：`withErrors()`。你可以传递任何“错误提供者”，它可以是一个错误字符串，一个错误数组，或者，最常见的情况是，Illuminate `Validator` 的一个实例，我们将在第十章中详细介绍。示例 3-44 展示了其使用示例。

##### 示例 3-44\. 带错误的重定向

```php
Route::post('form', function (Illuminate\Http\Request $request) {
    $validator = Validator::make($request->all(), $this->validationRules);

    if ($validator->fails()) {
        return back()
            ->withErrors($validator)
            ->withInput();
    }
});
```

`withErrors()`会自动与它重定向到的页面的视图共享一个`$errors`变量，以便你可以按照自己的意愿处理。

# 请求对象上的 validate()方法

不喜欢示例 3-44 的外观？有一个简单而强大的工具，可以帮助你轻松清理代码。在“请求对象上的 validate()”中详细了解更多。

# 中止请求

除了返回视图和重定向之外，退出路由的最常见方式是中止。有几种全局可用的方法（`abort()`、`abort_if()`和`abort_unless()`），可以选择使用 HTTP 状态码、消息和头数组作为参数。

如示例 3-45 所示，`abort_if()`和`abort_unless()`接受一个首要参数，该参数根据其真实性进行评估，并根据结果执行中止。

##### 示例 3-45\. 403 Forbidden 中止

```php
Route::post('something-you-cant-do', function (Illuminate\Http\Request $request) {
    abort(403, 'You cannot do that!');
    abort_unless($request->has('magicToken'), 403);
    abort_if($request->user()->isBanned, 403);
});
```

# 自定义响应

对于我们返回的几种其他选项，让我们先了解一下最常见的视图、重定向和中止响应之后的响应。与重定向一样，你可以在`response()`辅助函数或`Response`外观上运行这些方法。

## response()->make()

如果你想手动创建 HTTP 响应，只需将你的数据传递给`response()->make()`的第一个参数：例如 `return response()->make(*Hello, World!*)`。再次提醒，第二个参数是 HTTP 状态代码，第三个是你的头部。

## response()->json()和->jsonp()

要手动创建 JSON 编码的 HTTP 响应，请将你的可 JSON 化内容（数组、集合或其他内容）传递给`json()`方法：例如 `return response()->json(User::all())`。它与`make()`类似，只是`json_encode`了你的内容并设置了适当的头。

## response()->download()、->streamDownload()和->file()

要发送文件供最终用户下载，请将`download()`传递给`SplFileInfo`实例或字符串文件名，第二个可选参数是下载文件名：例如，`return response()->download('file501751.pdf', 'myFile.pdf')`，这将发送名为*file501751.pdf*的文件，并在发送时重命名为*myFile.pdf*。

要在浏览器中显示相同的文件（如果是 PDF 或浏览器可以处理的图像或其他内容），请改用`response()->file()`，它接受与`response->download()`相同的参数。

如果您希望将外部服务的某些内容作为下载可用，而无需直接将其写入服务器磁盘，则可以使用`response()->streamDownload()`来流式下载。该方法期望的参数包括一个回调函数（回显一个字符串）、一个文件名，以及可选的头部数组；参见示例 3-46。

##### 示例 3-46\. 从外部服务器进行流式下载

```php
return response()->streamDownload(function () {
    echo DocumentService::file('myFile')->getContent();
}, 'myFile.pdf');
```

# 测试

在其他一些社区中，单元测试控制器方法的想法很常见，但在 Laravel（以及大多数 PHP 社区）中，通常依赖*应用程序测试*来测试路由的功能。

例如，要验证`POST`路由是否正常工作，我们可以编写类似于示例 3-47 的测试。

##### 示例 3-47\. 编写简单的`POST`路由测试

```php
// tests/Feature/AssignmentTest.php
public function test_post_creates_new_assignment()
{
    $this->post('/assignments', [
        'title' => 'My great assignment',
    ]);

    $this->assertDatabaseHas('assignments', [
        'title' => 'My great assignment',
    ]);
}
```

我们直接调用了控制器方法吗？没有。但我们确保了该路由的目标——接收`POST`并将其重要信息保存到数据库中——得到了实现。

您还可以使用类似的语法访问一个路由，并验证页面上是否显示了某些文本，或者点击某些按钮是否执行了某些操作（参见示例 3-48）。

##### 示例 3-48\. 编写简单的`GET`路由测试

```php
// AssignmentTest.php
public function test_list_page_shows_all_assignments()
{
    $assignment = Assignment::create([
        'title' => 'My great assignment',
    ]);

    $this->get('/assignments')
        ->assertSee('My great assignment');
}
```

# 简而言之

Laravel 的路由定义在*routes/web.php*和*routes/api.php*中。您可以为每个路由定义预期的路径，哪些段是静态的，哪些是参数，哪些 HTTP 动词可以访问路由，以及如何解析它。您还可以将中间件附加到路由上，对它们进行分组，并为它们命名。

路由闭包或控制器方法返回的内容决定了 Laravel 如何响应用户。如果是字符串或视图，它会呈现给用户；如果是其他类型的数据，它会转换为 JSON 并呈现给用户；如果是重定向，它会强制进行重定向。

Laravel 提供了一系列工具和便利功能，用于简化常见的与路由相关的任务和结构。这些包括资源控制器、路由模型绑定和表单方法欺骗。
