# 第九章：用户认证与授权

设置基本的用户认证系统，包括注册、登录、会话、密码重置和访问权限，通常是创建应用程序基础的更耗时的部分之一。这是将功能提取到库中的一个主要候选项，而且有许多这样的库可供选择。

但由于项目的认证需求可能存在较大差异，大多数认证系统很快就会变得笨重且难以使用。幸运的是，Laravel 已经找到了一种方法，可以创建一套易于使用和理解的认证系统，同时灵活到可以适应各种设置。

Laravel 的每一个新安装都包含一个 `create_users_table` 迁移和一个内置的 `User` 模型。如果引入了 Breeze（参见 “Laravel Breeze”）或 Jetstream（参见 “Laravel Jetstream”），它们将为您的应用程序提供一系列与认证相关的视图、路由、控制器/动作和其他功能。API 是清晰易懂的，所有约定都协同工作，提供了一个简单且无缝的认证和授权系统。

# 用户模型与迁移

当您创建一个新的 Laravel 应用程序时，您将看到的第一个迁移和模型是 `create_users_table` 迁移和 `App\User` 模型。示例 9-1 直接展示了从迁移中获取的 `users` 表中的字段。

##### 示例 9-1\. Laravel 的默认用户迁移

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->timestamp('email_verified_at')->nullable();
    $table->string('password');
    $table->rememberToken();
    $table->timestamps();
});
```

我们有一个自增主键 ID，一个名称，一个唯一的电子邮件，一个密码，一个“记住我”令牌，以及创建和修改的时间戳。这涵盖了大多数应用程序中处理基本用户认证所需的一切内容。

# 认证与授权的区别

*认证* 意味着验证某人是谁，并允许他们在您的系统中以此身份行事。这包括登录和注销过程，以及任何允许用户在使用应用程序期间识别自己的工具。

*授权* 意味着确定经过身份验证的用户是否*被允许*（授权）执行特定行为。例如，授权系统允许您禁止非管理员查看站点的收入情况。

`User` 模型略微复杂，您可以在 示例 9-2 中看到。`App\User` 类本身很简单，但它扩展了 `Illuminate\Foundation\Auth\User` 类，后者引入了几个特性。

##### 示例 9-2\. Laravel 的默认 `User` 模型

```php
<?php
// App\User

namespace App\Models;

// use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;

    /**
 * The attributes that are mass assignable.
 *
 * @var array<int, string>
 */
    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    /**
 * The attributes that should be hidden for serialization.
 *
 * @var array<int, string>
 */
    protected $hidden = [
        'password',
        'remember_token',
    ];

    /**
 * The attributes that should be cast.
 *
 * @var array<string, string>
 */
    protected $casts = [
        'email_verified_at' => 'datetime',
    ];
}
```

```php
<?php
// Illuminate\Foundation\Auth\User

namespace Illuminate\Foundation\Auth;

use Illuminate\Auth\Authenticatable;
use Illuminate\Auth\MustVerifyEmail;
use Illuminate\Auth\Passwords\CanResetPassword;
use Illuminate\Contracts\Auth\Access\Authorizable as AuthorizableContract;
use Illuminate\Contracts\Auth\Authenticatable as AuthenticatableContract;
use Illuminate\Contracts\Auth\CanResetPassword as CanResetPasswordContract;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Foundation\Auth\Access\Authorizable;

class User extends Model implements
    AuthenticatableContract,
    AuthorizableContract,
    CanResetPasswordContract
{
    use Authenticatable, Authorizable, CanResetPassword, MustVerifyEmail;
}
```

# Eloquent 模型刷新器

如果这些内容对您完全陌生，请考虑在继续学习如何使用 Eloquent 模型之前阅读第五章。

那么，我们从这个模型中能学到什么？首先，用户存储在 `users` 表中；Laravel 将从类名推断出这一点。创建新用户时，我们可以填写 `name`、`email` 和 `password` 属性，而在将用户输出为 JSON 时，则会排除 `password` 和 `remember_token` 属性。目前看起来一切都很好。

我们还可以从 `Illuminate\Foundation\Auth` 版本的 `User` 中的合约和特性中看到，框架中有一些功能（例如身份验证、授权和密码重置的能力），理论上可以应用于其他模型，而不仅仅是 `User` 模型，并且可以单独或集体应用。

`Authenticatable` 合约要求方法（例如 `getAuthIdentifier()`），允许框架对此模型的实例进行身份验证到身份验证系统；`Authenticatable` 特性包含了满足普通 Eloquent 模型此合约所需的方法。

`Authorizable` 合约要求一个方法 (`can()`)，允许框架在不同上下文中授权此模型的实例以获取其访问权限。毫不奇怪，`Authorizable` 特性提供了方法，这些方法将为普通的 Eloquent 模型满足 `Authorizable` 合约。

最后，`CanResetPassword` 合约要求方法 (`g⁠⁠e⁠t⁠E⁠m⁠a⁠i⁠l⁠F⁠o⁠r​P⁠a⁠s⁠s⁠w⁠o⁠r⁠d⁠R⁠e⁠s⁠e⁠t⁠(⁠)`、`sendPasswordResetNotification()`)，允许框架重置任何满足此合约的实体的密码。`CanResetPassword` 特性提供了方法，以满足普通 Eloquent 模型的这一合约。

到目前为止，我们能够轻松地在数据库中表示个别用户（通过迁移），并使用可以进行身份验证（登录和注销）、授权（检查对特定资源的访问权限）和发送密码重置电子邮件的模型实例。

# 使用 `auth()` 全局辅助函数和 Auth 门面

`auth()` 全局辅助函数是在整个应用程序中与已验证用户的状态交互的最简单方法。您还可以注入一个 `Illuminate\Auth\AuthManager` 实例并获得相同的功能，或者使用 `Auth` 门面。

最常见的用法是检查用户是否已登录（如果当前用户已登录，则 `auth()->check()` 返回 `true`；如果用户未登录，则 `auth()->guest()` 返回 `true`）以及获取当前已登录用户（使用 `auth()->user()`，或仅获取 ID 使用 `auth()->id()`；如果没有用户登录，则两者都返回 `null`）。

查看示例 Example 9-3 了解控制器中全局辅助函数的示例用法。

##### 示例 9-3. 在控制器中使用 `auth()` 全局辅助函数的示例用法

```php
public function dashboard()
{
    if (auth()->guest()) {
        return redirect('sign-up');
    }

    return view('dashboard')
        ->with('user', auth()->user());
}
```

# 路由 `routes/auth.php`，Auth 控制器和 Auth 操作

如果你正在使用 Laravel 的其中一个入门工具包，你会发现使用内置的身份验证路由（例如登录、注册和重置密码）需要路由、控制器和视图。

Breeze 和 Jetstream 都使用自定义路由文件定义您的路由：*routes/auth.php*。它们并不完全相同，但可以查看 示例 9-4 以了解 Breeze 的认证路由文件的一部分，以便了解它们的一般情况。

##### 示例 9-4\. Breeze 的路由/auth.php 的一部分

```php
Route::middleware('guest')->group(function () {
    Route::get('register', [RegisteredUserController::class, 'create'])
                ->name('register');

    Route::post('register', [RegisteredUserController::class, 'store']);

    Route::get('login', [AuthenticatedSessionController::class, 'create'])
                ->name('login');

    Route::post('login', [AuthenticatedSessionController::class, 'store']);

    Route::get('forgot-password', [PasswordResetLinkController::class, 'create'])
                ->name('password.request');

    Route::post('forgot-password', [PasswordResetLinkController::class, 'store'])
                ->name('password.email');

    Route::get('reset-password/{token}', [NewPasswordController::class, 'create'])
                ->name('password.reset');

    Route::post('reset-password', [NewPasswordController::class, 'store'])
                ->name('password.store');
});
```

Breeze 在 `Auth` 命名空间下发布控制器，您可以根据需要进行配置：

+   *AuthenticatedSessionController.php*

+   *ConfirmablePasswordController.php*

+   *EmailVerificationNotificationController.php*

+   *EmailVerificationPromptController.php*

+   *NewPasswordController.php*

+   *PasswordController.php*

+   *PasswordResetLinkController.php*

+   *RegisteredUserController.php*

+   *VerifyEmailController.php*

Jetstream（以及它依赖的 Fortify）不发布控制器，而是发布您可以自定义的“操作”：

```php
app/Actions/Fortify/CreateNewUser.php
app/Actions/Fortify/PasswordValidationRules.php
app/Actions/Fortify/ResetUserPassword.php
app/Actions/Fortify/UpdateUserPassword.php
app/Actions/Fortify/UpdateUserProfileInformation.php
app/Actions/Jetstream/DeleteUser.php
```

# Breeze 和 Jetstream 的前端模板

到此为止，您的认证系统已经有了迁移、模型、控制器/操作和路由。但是您的视图呢？

您可以在 “Laravel Breeze” 和 “Laravel Jetstream” 中了解更多信息，但每个工具都提供多种不同的堆栈，并且每个堆栈将其模板放置在不同的位置。

一般来说，基于 JavaScript 的堆栈将其模板放置在 *resources/js* 中，而基于 Blade 的堆栈将其放置在 *resources/views* 中。

每个功能（登录、注册、重置密码等）至少有一个视图，并且它们都采用了流畅的基于 Tailwind 的设计生成，可以直接使用或自定义。

# “记住我”

Breeze 和 Jetstream 都已经默认实现了此功能，但是了解其工作原理以及如何在自己的项目中使用仍然是值得的。如果您想要实现“记住我”风格的长期访问令牌，请确保您的 `users` 表中有一个 `remember_token` 列（如果您使用了默认迁移，那么这个列应该已经存在）。

当您正常登录用户时（这是 `LoginController` 使用 `AuthenticatesUsers` trait 所做的方式），您将“尝试”使用用户提供的信息进行认证，就像在 示例 9-5 中所示。

##### 示例 9-5\. 尝试用户认证

```php
if (auth()->attempt([
    'email' => request()->input('email'),
    'password' => request()->input('password'),
])) {
    // Handle the successful login
}
```

这为您提供了一个与用户会话同久的用户登录。如果您希望 Laravel 使用 Cookie 无限期延长登录时间（只要用户在同一台计算机上且不退出登录），您可以将布尔值 `true` 作为 `auth()->attempt()` 方法的第二个参数传递。查看 示例 9-6 以了解该请求的外观。

##### 示例 9-6\. 使用“记住我”复选框进行用户认证尝试

```php
if (auth()->attempt([
    'email' => request()->input('email'),
    'password' => request()->input('password'),
], request()->filled('remember'))) {
    // Handle the successful login
}
```

您可以看到，我们检查了输入是否具有非空（“filled”）`remember` 属性，该属性将返回一个布尔值。这允许我们的用户通过登录表单中的复选框决定是否要记住登录状态。

后来，如果你需要手动检查当前用户是否通过记住令牌进行了认证，有一个方法可以做到：`auth()->viaRemember()` 返回一个布尔值，指示当前用户是否通过记住令牌进行了认证。这使你可以防止通过记住令牌访问某些更高敏感度功能；而是，你可以要求用户重新输入他们的密码。

# 密码确认

在你的应用程序的某些部分访问之前，用户可能需要重新确认他们的密码。例如，如果用户已经登录了一段时间，然后尝试访问你站点的账单部分，你可能希望他们验证他们的密码。

你可以在你的路由上附加 `password.confirm` 中间件来强制这种行为。一旦他们确认了密码，用户将被发送到他们最初尝试访问的路由。此后，用户在 3 小时内不需要重新确认密码；你可以在 `auth.password_timeout` 配置设置中更改这个时间。

# 手动认证用户

用户认证的最常见情况是，允许用户提供他们的凭证，然后使用 `auth()->attempt()` 来查看提供的凭证是否与任何真实用户匹配。如果匹配，则登录他们。

但有时候，在某些情境下，你能够选择自己选择性地登录一个用户，这是非常有价值的。例如，你可能希望允许管理员用户切换用户。

有四种方法可以实现这一点。首先，你可以只传递一个用户 ID：

```php
auth()->loginUsingId(5);
```

其次，你可以传递一个 `User` 对象（或者任何实现 `Illuminate\Contracts\Auth\Authenticatable` 合约的对象）：

```php
auth()->login($user);
```

第三和第四，你可以选择仅为当前请求验证给定用户，这不会影响你的会话或者 cookie，可以使用 `once()` 或 `onceUsingId()`：

```php
auth()->once(['username' => 'mattstauffer']);
// or
auth()->onceUsingId(5);
```

请注意，你传递给 `once()` 方法的数组可以包含任何键值对来唯一标识你想要认证的用户。如果适合你的项目，你甚至可以传递多个键和值。例如：

```php
auth()->once([
    'last_name' => 'Stauffer',
    'zip_code' => 90210,
])
```

# 手动登出用户

如果你需要手动登出用户，只需调用 `logout()`：

```php
auth()->logout();
```

## 使其他设备上的会话失效

如果你想要在任何其他设备上登出用户的当前会话 —— 例如，在他们更改密码后 —— 你需要提示用户输入他们的密码并将其传递给 `logoutOtherDevices()` 方法。为此，你需要将 `auth.session` 中间件应用到你想让他们退出登录的所有路由上（对于大多数项目而言，这是整个应用程序）。

然后你可以在任何需要的地方内联使用它：

```php
auth()->logoutOtherDevices($password);
```

如果你想让用户详细查看其他活动会话，Jetstream（参见“Laravel Jetstream”）默认提供了一个页面，列出所有活动会话，并提供一个按钮可以登出所有会话。

# 认证中间件

在示例 9-3 中，您看到如何检查访客是否已登录，并在未登录时重定向他们。您可以在应用程序的每个路由上执行这些检查，但很快会变得乏味。事实证明，路由中间件（详见第 10 章以了解其工作原理）非常适合将某些路由限制为仅限访客或经过身份验证的用户。

再次，Laravel 默认即可提供我们所需的中间件。您可以查看您在`App\Http\Kernel`中定义的路由中间件：

```php
protected $middlewareAliases = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'auth.session' => \Illuminate\Session\Middleware\AuthenticateSession::class,
    'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
    'signed' => \App\Http\Middleware\ValidateSignature::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```

六种默认的路由中间件与身份验证相关：

`auth`

限制路由访问权限仅限经过身份验证的用户

`auth.basic`

通过 HTTP 基本身份验证限制仅限经过身份验证的用户访问

`auth.session`

使路由可供其他设备登出使用`Auth::logoutOtherDevices`

`can`

用于授权用户访问指定路由

`guest`

限制未经身份验证的用户访问

`password.confirm`

要求用户最近重新确认其密码

对于需要仅限经过身份验证的用户访问的部分，最常见的做法是使用`auth`，而对于不希望经过身份验证的用户看到的任何路由（如登录表单），则使用`guest`。`auth.basic`和`auth.session`用于认证的中间件则较少使用。

示例 9-7 展示了几个由`auth`中间件保护的路由示例。

##### 示例 9-7\. 受`auth`中间件保护的示例路由

```php
Route::middleware('auth')->group(function () {
    Route::get('account', [AccountController::class, 'dashboard']);
});

Route::get('login', [LoginController::class, 'getLogin'])->middleware('guest');
```

# 电子邮件验证

如果您希望要求用户验证他们注册时使用的电子邮件地址的访问权限，则可以使用 Laravel 的电子邮件验证功能。

要启用电子邮件验证，请更新您的`App\User`类，并使其实现`Illuminate\Contracts\Auth\MustVerifyEmail`合同，如示例 9-8 所示。

##### 示例 9-8\. 将`MustVerifyEmail`特性添加到`Authenticatable`模型中

```php
class User extends Authenticatable implements MustVerifyEmail
{
    use Notifiable;

    // ...
}
```

`users`表还必须包含一个名为`email_verified_at`的可空时间戳列，这是默认的`CreateUsersTable`迁移已经为您提供的。

最后，您需要在控制器中启用电子邮件验证路由。最简单的方法是在路由文件中使用`Auth::routes()`，并将`verify`参数设置为`true`：

```php
Auth::routes(['verify' => true]);
```

现在，您可以保护任何希望不被尚未验证其电子邮件地址的任何用户访问的路由：

```php
Route::get('posts/create', function () {
    // Only verified users may enter...
})->middleware('verified');
```

您可以自定义在验证后重定向用户的路由`Verification``Controller`：

```php
protected $redirectTo = '/profile';
```

# Blade 身份验证指令

如果您想检查用户是否经过身份验证，而不是在路由级别进行检查，而是在视图中进行检查，您可以使用`@auth`和`@guest`（参见示例 9-9）。

##### 示例 9-9\. 在模板中检查用户的身份验证状态

```php
@auth
    // The user is authenticated
@endauth

@guest
    // The user is not authenticated
@endguest
```

您还可以通过将守卫名称作为参数传递给这两种方法来指定您想要使用的守卫，如示例 9-10 所示。

##### 示例 9-10\. 在模板中检查特定认证保护的身份验证

```php
@auth('trainees')
    // The user is authenticated
@endauth

@guest('trainees')
    // The user is not authenticated
@endguest
```

# 守卫

Laravel 认证系统的每个方面都通过称为*守卫*的东西路由。每个守卫由两个部分组成：定义它如何持久化和检索认证状态的*驱动程序*（例如 `session`），以及允许你按某些条件获取用户的*提供者*（例如 `users`）。

开箱即用，Laravel 有两个守卫：`web` 和 `api`。`web` 是更传统的认证样式，使用 `session` 驱动程序和基本用户提供者。`api` 使用相同的用户提供者，但它使用 `token` 驱动程序而不是 `session` 在每个请求中进行认证。

如果你想以不同方式处理用户身份的识别和持久性（例如，从长时间运行的会话更改为每页加载提供的令牌），你会更改驱动程序；如果你想更改用户的存储类型或检索方法（例如，将用户存储在 Mongo 而不是 MySQL 中），你会更改提供者。

## 更改默认守卫

守卫在 *config/auth.php* 中定义，你可以在那里更改它们、添加新的守卫，并定义默认的守卫。就其价值而言，这是一种相对不常见的配置；大多数 Laravel 应用程序只使用一个守卫。

“默认”守卫是在没有指定守卫的情况下使用任何认证功能时将使用的守卫。例如，`auth()->user()` 将使用默认守卫拉取当前认证的用户。你可以通过更改 *config/auth.php* 中的 `auth.defaults.guard` 设置来更改此守卫：

```php
'defaults' => [
    'guard' => 'web', // Change the default here
    'passwords' => 'users',
],
```

# 配置约定

你可能已经注意到，我用 `auth.defaults.guard` 等引用来引用配置部分。这意味着在 *config/auth.php* 中，在以 `defaults` 键为键的数组部分中，应该有一个以 `guard` 为键的属性。

## 在不更改默认值的情况下使用其他守卫

如果你想使用另一个守卫但*不*更改默认值，你可以在 `auth()` 调用中以 `guard()` 开头：

```php
$apiUser = auth()->guard('api')->user();
```

这将在此调用中仅获取使用 `api` 守卫的当前用户。

## 添加新的守卫

你可以随时在 *config/auth.php* 的 `auth.guards` 设置中添加新的守卫：

```php
'guards' => [
    'trainees' => [
        'driver' => 'session',
        'provider' => 'trainees',
    ],
],
```

在这里，我们创建了一个新的守卫（除了 `web` 和 `api`）名为 `trainees`。假设在接下来的这一节中，我们正在构建一个应用程序，其中我们的用户是体育教练，每个教练都有自己的*用户*——受训者——他们可以登录到他们的子域名。因此，我们需要一个单独的守卫来处理他们。

`driver` 的唯二选项是 `token` 和 `session`。开箱即用，`provider` 的唯一选项是 `users`，支持对默认的 `users` 表进行认证，但你可以轻松创建自己的提供者。

## 闭包请求守卫

如果您想定义一个自定义守卫，并且您的守卫条件（如何查找给定用户的请求）可以简单地在任何给定的 HTTP 请求中响应，您可能只想将用户查找代码放入一个闭包中，而不必创建一个新的自定义守卫类。

`viaRequest()` 认证方法允许仅通过闭包（定义在第二个参数中）来定义一个守卫（第一个参数中命名的），该闭包接受 HTTP 请求并返回适当的用户。要在 `AuthServiceProvider` 的 `boot()` 方法中注册一个闭包请求守卫，如 示例 9-11 所示。

##### 示例 9-11\. 定义闭包请求守卫

```php
public function boot(): void
{
    Auth::viaRequest('token-hash', function ($request) {
        return User::where('token-hash', $request->token)->first();
    });
}
```

## 创建自定义用户提供程序

在 *config/auth.php* 中定义守卫的位置下方，有一个 `auth.providers` 部分，定义了可用的提供程序。让我们创建一个名为 `trainees` 的新提供程序：

```php
'providers' => [
    'users' => [
        'driver' => 'eloquent',
        'model' => App\User::class,
    ],

    'trainees' => [
        'driver' => 'eloquent',
        'model' => App\Trainee::class,
    ],
],
```

`driver` 的两个选项是 `eloquent` 和 `database`。如果使用 `eloquent`，您将需要一个包含 Eloquent 类名的 `model` 属性（用于 `User` 类的模型）；如果使用 `database`，则需要一个 `table` 属性来定义应该对其进行身份验证的表。

在我们的示例中，您可以看到此应用程序有一个 `User` 和一个 `Trainee`，它们需要分别进行身份验证。这样，代码可以区分 `auth()->guard('users')` 和 `auth()->guard('trainees')`。

最后一点：`auth` 路由中间件可以接受一个参数，即守卫名称。因此，您可以使用特定的守卫保护某些路由：

```php
Route::middleware('auth:trainees')->group(function () {
    // Trainee-only routes here
});
```

## 非关系数据库的自定义用户提供程序

刚才描述的用户提供程序创建流程仍然依赖于相同的 `UserProvider` 类，这意味着它期望从关系数据库中提取标识信息。但是，如果您使用的是 Mongo 或 Riak 或类似的东西，实际上您需要创建自己的类。

要做到这一点，请创建一个新的类，实现 `Illuminate\Contracts\Auth\UserProvider` 接口，然后在 `AuthServiceProvider@boot` 中绑定它：

```php
auth()->provider('riak', function ($app, array $config) {
    // Return an instance of Illuminate\Contracts\Auth\UserProvider...
    return new RiakUserProvider($app['riak.connection']);
});
```

# 认证事件

我们将在 第十六章 中更多地讨论事件，但 Laravel 的事件系统是一个基本的发布/订阅框架。有系统生成的事件和用户生成的事件进行广播，并且用户可以创建事件监听器以响应某些事件。

那么，如果您想在用户因登录尝试失败次数过多而被锁定后，每次都向特定的安全服务发送一个 ping 呢？也许此服务监视某些地理区域的某个特定数量的登录失败或其他内容。当然，您可以在适当的控制器中注入一个调用。但是通过事件，您可以创建一个监听器来监听“用户被锁定”的事件，并注册它。

查看 示例 9-12 以查看身份验证系统发出的所有事件。

##### 示例 9-12\. 框架生成的认证事件

```php
protected $listen = [
    'Illuminate\Auth\Events\Attempting' => [],
    'Illuminate\Auth\Events\Authenticated' => [],
    'Illuminate\Auth\Events\CurrentDeviceLogout' => [],
    'Illuminate\Auth\Events\Failed' => [],
    'Illuminate\Auth\Events\Lockout' => [],
    'Illuminate\Auth\Events\Login' => [],
    'Illuminate\Auth\Events\Logout' => [],
    'Illuminate\Auth\Events\OtherDeviceLogout' => [],
    'Illuminate\Auth\Events\PasswordReset' => [],
    'Illuminate\Auth\Events\Registered' => [],
    'Illuminate\Auth\Events\Validated' => [],
    'Illuminate\Auth\Events\Verified' => [],
];
```

如你所见，有“用户注册”、“用户尝试登录”、“用户验证但未登录”、“用户已认证”、“成功登录”、“登录失败”、“登出”、“从其他设备登出”、“从当前设备登出”、“锁定”、“重置密码”和“用户邮箱验证”等监听器。要了解如何为这些事件构建事件监听器，请查看第十六章。

# 授权与角色

最后，让我们介绍一下 Laravel 的授权系统。它使你能够确定用户是否*被授权*执行特定操作，你将使用几个主要动词进行检查：`can`、`cannot`、`allows`和`denies`。

大部分授权控制都将使用`Gate`外观进行，但在你的控制器、`User`模型、中间件和 Blade 指令中也有便捷的辅助功能可用。查看示例 9-13 可以体验我们能做到什么。

##### 示例 9-13\. `Gate`外观的基本用法

```php
if (Gate::denies('edit-contact', $contact)) {
    abort(403);
}

if (! Gate::allows('create-contact', Contact::class)) {
    abort(403);
}
```

## 定义授权规则

定义授权规则的默认位置是在`AuthServiceProvider`的`boot()`方法中，在这里你将调用`Auth`外观的方法。

授权规则称为*ability*，由两部分组成：一个字符串键（例如，`update-contact`）和返回布尔值的闭包。示例 9-14 展示了更新联系人的 ability。

##### 示例 9-14\. 用于更新联系人的样例 ability

```php
class AuthServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        Gate::define('update-contact', function ($user, $contact) {
            return $user->id == $contact->user_id;
        });
    }
}
```

让我们来看看定义 ability 的步骤。

首先，你需要定义一个键。在命名这个键时，你应该考虑在你的代码流中哪个字符串对于引用你所提供给用户的 ability 是有意义的。你可以在示例 9-14 中看到代码使用了`{*verb*}-{*modelName*}`的约定：`create-contact`、`update-contact`等。

其次，你要定义闭包。第一个参数将是当前已认证的用户，之后的所有参数将是你要检查访问权限的对象——在本例中是联系人。

因此，考虑到这两个对象，我们可以检查用户是否有权限更新这个联系人。你可以按自己的逻辑编写这段代码，但在我们查看的应用中（在示例 9-14 中），授权取决于是否是联系人行的创建者。如果当前用户创建了联系人，闭包将返回`true`（授权），否则返回`false`（未授权）。

就像路由定义一样，你也可以使用类和方法而不是闭包来解析这个定义：

```php
$gate->define('update-contact', 'ContactACLChecker@updateContact');
```

## `Gate`外观（和注入`Gate`）

现在你已经定义了一个 ability，是时候测试它了。最简单的方法是使用`Gate`外观，就像在示例 9-15 中一样（或者你可以注入`Illuminate\Contracts\Auth\Access\Gate`的实例）。

##### 示例 9-15\. `Gate`外观的基本用法

```php
if (Gate::allows('update-contact', $contact)) {
    // Update contact
}

// or
if (Gate::denies('update-contact', $contact)) {
    abort(403);
}
```

您可能还可以定义一个具有多个参数的能力 —— 也许联系人可以分组，并且您希望授权用户是否有权限将联系人添加到组中。Example 9-16 展示了如何做到这一点。

##### Example 9-16\. 具有多个参数的能力

```php
// Definition
Gate::define('add-contact-to-group', function ($user, $contact, $group) {
    return $user->id == $contact->user_id && $user->id == $group->user_id;
});

// Usage
if (Gate::denies('add-contact-to-group', [$contact, $group])) {
    abort(403);
}
```

如果您需要检查不是当前认证用户的用户的授权，请尝试`forUser()`，就像 Example 9-17 中那样。

##### Example 9-17\. 指定`Gate`的用户

```php
if (Gate::forUser($user)->denies('create-contact')) {
    abort(403);
}
```

## 资源门

访问控制列表最常见的用途是定义对单个“资源”的访问权限（想想一个 Eloquent 模型，或者您允许用户从其管理面板管理的东西）。

`resource()` 方法使得可以一次将四个最常见的门控（`view`、`create`、`update` 和 `delete`）应用于单个资源：

```php
Gate::resource('photos', 'App\Policies\PhotoPolicy');
```

这相当于定义以下内容：

```php
Gate::define('photos.view', 'App\Policies\PhotoPolicy@view');
Gate::define('photos.create', 'App\Policies\PhotoPolicy@create');
Gate::define('photos.update', 'App\Policies\PhotoPolicy@update');
Gate::define('photos.delete', 'App\Policies\PhotoPolicy@delete');
```

## 授权中间件

如果您想授权整个路由，可以使用`Authorize`中间件（有一个`can`的快捷方式），就像在 Example 9-18 中那样。

##### Example 9-18\. 使用`Authorize`中间件

```php
Route::get('people/create', function () {
    // Create a person
})->middleware('can:create-person');

Route::get('people/{person}/edit', function () {
    // Edit person
})->middleware('can:edit,person');
```

这里，`{person}` 参数（无论它是作为字符串定义还是作为绑定路由模型）将作为附加参数传递给能力方法。

Example 9-18 中的第一个检查是一个普通的能力，但第二个是一个策略，我们将在“策略”中讨论它。

如果您需要检查不需要模型实例的操作（例如 `create`，与 `edit` 不同，不会传递实际的路由模型绑定实例），您可以只传递类名：

```php
Route::post('people', function () {
    // Create a person
})->middleware('can:create,App\Person');
```

## 控制器授权

Laravel 中的父类 `App\Http\Controllers\Controller` 导入了 `AuthorizesRequests` 特性，提供了三种授权方法：`authorize()`、`authorizeForUser()` 和 `authorizeResource()`。

`authorize()` 接受一个能力键和一个对象（或对象数组）作为参数，如果授权失败，它将以 403（未经授权）状态码退出应用程序。这意味着这个特性可以将三行授权代码转换为一行，正如您在 Example 9-19 中所看到的。

##### Example 9-19\. 使用`authorize()`简化控制器授权

```php
// From this:
public function edit(Contact $contact)
{
    if (Gate::cannot('update-contact', $contact)) {
        abort(403);
    }

    return view('contacts.edit', ['contact' => $contact]);
}

// To this:
public function edit(Contact $contact)
{
    $this->authorize('update-contact', $contact);

    return view('contacts.edit', ['contact' => $contact]);
}
```

`authorizeForUser()` 是相同的，但允许您传递一个 `User` 对象，而不是默认为当前认证用户：

```php
$this->authorizeForUser($user, 'update-contact', $contact);
```

`authorizeResource()` 在控制器构造函数中调用一次，将预定义的一组授权规则映射到该控制器中的每个 RESTful 控制器方法 —— 类似于 Example 9-20。

##### Example 9-20\. `authorizeResource()` 方法的授权到方法映射

```php
...
class ContactController extends Controller
{
    public function __construct()
    {
        // This call does everything you see in the methods below.
        // If you put this here, you can remove all authorize()
        // calls in the individual resource methods here.
        $this->authorizeResource(Contact::class);
    }

    public function index()
    {
        $this->authorize('viewAny', Contact::class);
    }

    public function create()
    {
        $this->authorize('create', Contact::class);
    }

    public function store(Request $request)
    {
        $this->authorize('create', Contact::class);
    }

    public function show(Contact $contact)
    {
        $this->authorize('view', $contact);
    }

    public function edit(Contact $contact)
    {
        $this->authorize('update', $contact);
    }

    public function update(Request $request, Contact $contact)
    {
        $this->authorize('update', $contact);
    }

    public function destroy(Contact $contact)
    {
        $this->authorize('delete', $contact);
    }
}
```

## 检查用户实例

如果您不在控制器中，更有可能检查特定用户的能力而不是当前认证的用户。使用`Gate`外观可以使用`forUser()`方法实现这一点，但有时语法可能有些奇怪。

`User` 类上的 `Authorizable` 特性提供了四种方法来实现更可读的授权功能：`$user->can()`、`$user->canAny()`、`$user->cant()` 和 `$user->cannot()`。你可以大概猜到，`cant()` 和 `cannot()` 是一样的，而 `can()` 则完全相反。使用 `canAny()`，你传递一个权限数组，该方法检查用户是否可以执行其中任何一个。

这意味着你可以做像 示例 9-21 这样的事情。

##### 示例 9-21\. 检查 `User` 实例的授权

```php
$user = User::find(1);

if ($user->can('create-contact')) {
    // Do something
}
```

在幕后，这些方法只是将参数传递给 `Gate`；在前面的示例中，`Gate::forUser($user)->check('create-contact')`。

## Blade 检查

Blade 还有一个小方便的助手：`@can` 指令。示例 9-22 展示了它的使用方式。

##### 示例 9-22\. 使用 Blade 的 `@can` 指令

```php
<nav>
    <a href="/">Home</a>
    @can('edit-contact', $contact)
        <a href="{{ route('contacts.edit', [$contact->id]) }}">Edit This Contact</a>
    @endcan
</nav>
```

你还可以在 `@can` 和 `@endcan` 之间使用 `@else`，以及像 示例 9-23 中使用 `@cannot` 和 `@endcannot`。

##### 示例 9-23\. 使用 Blade 的 `@cannot` 指令

```php
<h1>{{ $contact->name }}</h1>
@cannot('edit-contact', $contact)
    LOCKED
@endcannot
```

## 拦截检查

如果你曾经用过管理员用户类构建过应用程序，你可能已经看过本章节中所有简单授权闭包，并考虑过如何添加一个超级用户类，在任何情况下都覆盖这些检查。幸运的是，已经有一个工具可以做到这一点。

在 `AuthServiceProvider` 中，你已经在定义你的能力，你还可以添加一个 `before()` 检查，该检查在所有其他检查之前运行，可以选择性地覆盖它们，就像 示例 9-24 中一样。

##### 示例 9-24\. 使用 `before()` 覆盖 `Gate` 检查

```php
Gate::before(function ($user, $ability) {
    if ($user->isOwner()) {
        return true;
    }
});
```

注意，也会传递能力的字符串名称，因此你可以根据你的能力命名方案区分你的 `before()` 钩子。

## 策略

到目前为止，所有的访问控制都要求你手动将 Eloquent 模型与能力名称关联起来。你可以创建一个名为 `visit-dashboard` 的能力，该能力与特定的 Eloquent 模型无关，但你可能已经注意到，我们大多数示例都涉及*对某物做某事*，在大多数情况下，受到操作的*某物*是一个 Eloquent 模型。

授权策略是组织结构，帮助你根据你正在控制访问的资源将授权逻辑分组。它们使得能够轻松管理定义针对特定 Eloquent 模型（或其他 PHP 类）行为的授权规则，全部在一个地方。

### 生成策略

策略是 PHP 类，可以通过 Artisan 命令生成：

```php
php artisan make:policy ContactPolicy
```

生成后，需要注册它们。`AuthServiceProvider` 有一个 `$policies` 属性，它是一个数组。每个项目的键是受保护资源的类名（几乎总是一个 Eloquent 类），值是策略类名。示例 9-25 显示了这将是什么样子。

##### 示例 9-25\. 在`AuthServiceProvider`中注册策略

```php
class AuthServiceProvider extends ServiceProvider
{
    protected $policies = [
        Contact::class => ContactPolicy::class,
    ];
```

由 Artisan 生成的策略类没有任何特殊属性或方法。但是您添加的每个方法现在都映射为此对象的能力键。

# 策略自动发现

Laravel 尝试“猜测”您的策略及其相应模型之间的链接。例如，它将自动将`PostPolicy`应用于您的`Post`模型。

如果您需要自定义 Laravel 用于猜测此映射的逻辑，请查看[策略文档](https://oreil.ly/P5gC2)。

让我们定义一个`update()`方法来看看它的工作原理（参见示例 9-26）。

##### 示例 9-26\. 一个样本`update()`策略方法

```php
<?php

namespace App\Policies;

class ContactPolicy
{
    public function update($user, $contact)
    {
        return $user->id == $contact->user_id;
    }
}
```

注意，该方法的内容看起来与在`Gate`定义中的内容完全相同。

# 不需要使用实例的政策方法

如果您需要定义一个与类相关但不是特定实例的策略方法——例如，“此用户是否可以创建任何联系人？”而不仅仅是“此用户是否可以查看此特定联系人？”——您可以像处理普通策略方法一样处理它：

```php
...
class ContactPolicy
{
    public function create($user)
    {
        return $user->canCreateContacts();
    }
```

### 检查政策

如果为资源类型定义了策略，则`Gate`外观将使用第一个参数来确定要在策略上检查哪个方法。如果您运行`Gate::allows('update', $contact)`，它将检查`ContactPolicy@update`方法的授权情况。

这也适用于`Authorize`中间件和`User`模型检查以及 Blade 检查，如示例 9-27 所示。

##### 示例 9-27\. 对策略进行授权检查

```php
// Gate
if (Gate::denies('update', $contact)) {
    abort(403);
}

// Gate if you don't have an explicit instance
if (! Gate::check('create', Contact::class)) {
    abort(403);
}

// User
if ($user->can('update', $contact)) {
    // Do stuff
}

// Blade
@can('update', $contact)
    // Show stuff
@endcan
```

此外，还有一个`policy()`辅助程序，允许您检索策略类并运行其方法：

```php
if (policy($contact)->update($user, $contact)) {
    // Do stuff
}
```

### 覆盖政策

就像普通能力定义一样，政策可以定义一个`before()`方法，允许您在处理之前覆盖任何调用（参见示例 9-28）。

##### 示例 9-28\. 使用`before()`方法覆盖策略

```php
public function before($user, $ability)
{
    if ($user->isAdmin()) {
        return true;
    }
}
```

# 测试

应用程序测试经常需要代表特定用户执行特定行为。因此，在应用程序测试中进行身份验证并测试授权规则和身份验证路由是必要的。

当然，您可以编写一个应用程序测试，手动访问登录页面，然后填写表单并提交，但这并不是必需的。相反，最简单的选项是使用`->be()`方法模拟作为用户登录。请参阅示例 9-29。

##### 示例 9-29\. 在应用程序测试中作为用户进行身份验证

```php
public function test_it_creates_a_new_contact()
{
    $user = User::factory()->create();
    $this->be($user);

    $this->post('contacts', [
        'email' => 'my@email.com',
    ]);

    $this->assertDatabaseHas('contacts', [
        'email' => 'my@email.com',
        'user_id' => $user->id,
    ]);
}
```

您还可以使用并链`actingAs()`方法，而不是`be()`，如果您更喜欢其阅读方式：

```php
public function test_it_creates_a_new_contact()
{
    $user = User::factory()->create();

    $this->actingAs($user)->post('contacts', [
        'email' => 'my@email.com',
    ]);

    $this->assertDatabaseHas('contacts', [
        'email' => 'my@email.com',
        'user_id' => $user->id,
    ]);
}
```

我们也可以像在示例 9-30 中那样测试授权。

##### 示例 9-30\. 测试授权规则

```php
public function test_non_admins_cant_create_users()
{
    $user = User::factory()->create([
        'admin' => false,
    ]);
    $this->be($user);

    $this->post('users', ['email' => 'my@email.com']);

    $this->assertDatabaseMissing('users', [
        'email' => 'my@email.com',
    ]);
}
```

或者，我们可以像在示例 9-31 中那样测试 403 响应。

##### 示例 9-31\. 通过检查状态代码测试授权规则

```php
public function test_non_admins_cant_create_users()
{
    $user = User::factory()->create([
        'admin' => false,
    ]);
    $this->be($user);

    $response = $this->post('users', ['email' => 'my@email.com']);

    $response->assertStatus(403);
}
```

我们还需要测试我们的认证（注册和登录）路由是否正常工作，如示例 9-32 所示。

##### 示例 9-32\. 测试认证路由

```php
public function test_users_can_register()
{
    $this->post('register', [
        'name' => 'Sal Leibowitz',
        'email' => 'sal@leibs.net',
        'password' => 'abcdefg123',
        'password_confirmation' => 'abcdefg123',
    ]);

    $this->assertDatabaseHas('users', [
        'name' => 'Sal Leibowitz',
        'email' => 'sal@leibs.net',
    ]);
}

public function test_users_can_log_in()
{
    $user = User::factory()->create([
        'password' => Hash::make('abcdefg123')
    ]);

    $this->post('login', [
        'email' => $user->email,
        'password' => 'abcdefg123',
    ]);

    $this->assertTrue(auth()->check());
    $this->assertTrue($user->is(auth()->user()));
}
```

我们还可以使用集成测试功能来直接测试“点击”认证字段并“提交”字段以测试整个流程。关于这一点，我们将在第十二章中详细讨论。

# TL;DR

在默认的`User`模型、`create_users_table`迁移以及 Jetstream 和 Breeze 之间，Laravel 提供了开箱即用的完整用户认证系统选项。Breeze 在控制器中处理认证功能，Jetstream 在操作中处理认证功能，两者都可以根据每个应用程序进行定制。这两个工具还发布了配置文件和模板以进行定制。

`Auth`门面和全局助手`auth()`提供了访问当前用户（`auth()->user()`）的途径，并且轻松检查用户是否已登录（`auth()->check()`和`auth()->guest()`）。

Laravel 还内置了一个授权系统，允许您定义特定的权限（`create-contact`、`visit-secret-page`）或为用户与整个模型的交互定义策略。

使用`Gate`门面、`User`类上的`can()`和`cannot()`方法、Blade 模板中的`@can`和`@cannot`指令、控制器中的`authorize()`方法或`can`中间件来检查授权。
