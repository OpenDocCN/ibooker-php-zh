# 第十七章. 辅助函数与集合

我们已经在整本书中涵盖了许多全局函数：这些是一些小辅助函数，使执行常见任务变得更加容易，例如用于作业的`dispatch()`，用于事件的`event()`，以及用于依赖解析的`app()`。我们还在第五章中讨论了 Laravel 的集合，或称为增强数组的数组。

本章将涵盖一些常见且强大的辅助函数，以及使用集合进行编程的基础知识。本节中的许多“辅助函数”曾经是全局函数，现在是门面的调用；`array_first()`，全局函数，已被`Arr::first()`，授权调用所取代。因此，虽然这些在技术上并非全部是全局函数，因为它们不再是全局函数，但它们在我们的工具箱中仍然占据同样的位置。

# 辅助函数

您可以在[辅助文档](https://oreil.ly/vssfi)中找到 Laravel 提供的所有辅助函数的完整列表，但我们将在此处介绍一些最有用的函数。

## 数组

PHP 的本地数组操作函数为我们提供了很多能力，但有时我们希望进行的标准操作需要笨拙的循环和逻辑检查。Laravel 的数组辅助函数使一些常见的数组操作变得更加简单：

`Arr::first(*$array, $callback, $default = null*)`

返回通过回调闭包定义的测试的第一个数组值。您可以选择将默认值设置为第三个参数。以下是一个例子：

```php
$people = [
    [
        'email' => 'm@me.com',
        'name' => 'Malcolm Me'
    ],
    [
        'email' => 'j@jo.com',
        'name' => 'James Jo'
    ],
];

 $value = Arr::first($people, function ($person, $key) {
     return $person['email'] == 'j@jo.com';
 });
```

`Arr::get(*$array, $key, $default = null*)`

使从数组中获取值变得简单，具有两个附加好处：如果请求不存在的键，它不会抛出错误（并且您可以使用第三个参数提供默认值），并且您可以使用点表示法遍历嵌套数组。例如：

```php
$array = ['owner' => ['address' => ['line1' => '123 Main St.']]];

$line1 = Arr::get($array, 'owner.address.line1', 'No address');
$line2 = Arr::get($array, 'owner.address.line2');
```

`Arr::has(*$array, $keys*)`

使用点表示法来遍历嵌套数组，轻松检查数组是否具有特定值设置。`$keys`参数可以是单个条目或条目数组，将检查数组中的每个条目是否存在：

```php
$array = ['owner' => ['address' => ['line1' => '123 Main St.']]];

if (Arr::has($array, 'owner.address.line2')) {
    // Do stuff
}
```

`Arr::hasAny(*$array, $keys*)`

使用点表示法轻松检查数组是否具有指定键之一。`$keys`参数可以是单个键或键数组，将检查数组中是否存在任何键：

```php
$array = ['owner' => ['address' => ['line1' => '123 Main St.']]];

if (Arr::hasAny($array, ['owner.address', 'default.address'])) {
    // Do stuff
}
```

`Arr::pluck(*$array, $value, $key = null*)`

返回与提供的键对应的值数组：

```php
$array = [
    ['owner' => ['id' => 4, 'name' => 'Tricia']],
    ['owner' => ['id' => 7, 'name' => 'Kimberly']],
];

$array = Arr::pluck($array, 'owner.name');

// Returns ['Tricia', 'Kimberly'];
```

如果您希望返回的数组由源数组的另一个值键入，则可以将该值的点表示法引用作为第三个参数：

```php
$array = Arr::pluck($array, 'owner.name', 'owner.id');

// Returns [4 => 'Tricia', 7 => 'Kimberly'];
```

`Arr::random(*$array, $num = null*)`

从提供的数组中返回一个随机项。如果提供了`$num`参数，则会随机选择那么多个结果的数组：

```php
$array = [
    ['owner' => ['id' => 4, 'name' => 'Tricia']],
    ['owner' => ['id' => 7, 'name' => 'Kimberly']],
];

$randomOwner = Arr::random($array);
```

`Arr::join(*$array, $glue, $finalGlue = ''*)`

将`$array`中的项目连接成一个字符串，在它们之间添加`$glue`。如果提供了`$finalGlue`，它将添加到数组的最后一个元素之前，而不是`$glue`：

```php
$array = ['Malcolm', 'James', 'Tricia', 'Kimberly'];

Arr::join($array, ', ');
// Malcolm, James, Tricia, Kimberly

Arr::join($array, ', ', ', and');
// Malcolm, James, Tricia, and Kimberly
```

## 字符串

就像使用数组一样，使用原生 PHP 函数可以进行一些字符串操作和检查，但这可能很麻烦。Laravel 的帮助函数使一些常见的字符串操作变得更快更简单：

`e(*$string*)`

`htmlentities`()的别名；准备（通常是用户提供的）字符串，以便在 HTML 页面上安全地回显。例如：

```php
e('<script>do something nefarious</script>');

// Returns &lt;script&gt;do something nefarious&lt;/script&gt;
```

`str(*$string*)`

用于转换可字符串化对象；是`Str::of(*$string*)`的别名：

```php
str('http') === Str::of('http');
// true
```

`Str::startsWith(*$haystack, $needle*)`，`Str::endsWith(*$haystack, $needle*)`，`Str::contains(*$haystack, $needle, $ignoreCase*)`

返回一个布尔值，指示提供的`$haystack`字符串是否以提供的`$needle`字符串开头、结尾或包含：

```php
if (Str::startsWith($url, 'https')) {
    // Do something
}

if (Str::endsWith($abstract, '...')) {
    // Do something
}

if (Str::contains($description, '1337 h4x0r')) {
    // Run away
}
```

`Str::limit(*$value, $limit = 100, $end = '...'*)`

将字符串限制为提供的字符数。如果字符串的长度小于限制，则只返回字符串；如果大于，则修剪为提供的字符数，然后附加`...`或提供的`$end`字符串。例如：

```php
$abstract = Str::limit($loremIpsum, 30);
// Returns "Lorem ipsum dolor sit amet, co..."

$abstract = Str::limit($loremIpsum, 30, "&hellip;");
// Returns "Lorem ipsum dolor sit amet, co&hellip;"
```

`Str::words(*$value, $words = 100, $end = '...'*)`

将字符串限制为提供的单词数。如果字符串的长度小于单词数，只返回字符串；如果大于，修剪为提供的单词数，然后附加`...`或提供的`$end`字符串。例如：

```php
$abstract = Str::words($loremIpsum, 3);
// Returns "Lorem ipsum dolor..."

$abstract = Str::words($loremIpsum, 5, " &hellip;");
// Returns "Lorem ipsum dolor sit amet, &hellip;"
```

`Str::before(*$subject, $search*)`，`Str::after(*$subject, $search*)`，`Str::beforeLast(*$subject, $search*)`，`Str::afterLast(*$subject, $search*)`

返回一个字符串的子字符串，在另一个字符串之前或之后，或最后一个实例之后。例如：

```php
Str::before('Nice to meet you!', 'meet you');
// Returns "Nice to "

Str::after('Nice to meet you!', 'Nice');
// Returns " to meet you!"

Str::beforeLast('App\Notifications\WelcomeNotification', '\\');
// Returns "App\Notifications"

Str::afterLast('App\Notifications\WelcomeNotification', '\\');
// Returns "WelcomeNotification"
```

`Str::is(*$pattern, $value*)`

返回一个布尔值，指示给定字符串是否与给定模式匹配。该模式可以是正则表达式模式，或者你可以使用星号表示通配符位置：

```php
Str::is('*.dev', 'myapp.dev');       // true
Str::is('*.dev', 'myapp.dev.co.uk'); // false
Str::is('*dev*', 'myapp.dev');       // true
Str::is('*myapp*', 'www.myapp.dev'); // true
Str::is('my*app', 'myfantasticapp'); // true
Str::is('my*app', 'myapp');          // true
```

# 如何将正则表达式传递给`Str::is()`

如果你想知道可以传递给`Str::is()`的正则表达式模式是什么，请查看这里的方法定义（空间有限，已简化）来了解它的工作原理：

```php
public function is($pattern, $value)
{
    if ($pattern == $value) return true;

    $pattern = preg_quote($pattern, '#');
    $pattern = Str::replace('\*', '.*', $pattern);
    if (preg_match('#^'.$pattern.'\z#u', $value) === 1) {
        return true;
    }

    return false;
}
```

`Str::isUuid(*$value*)`

确定该值是否为有效的 UUID：

```php
Str::isUuid('33f6115c-1c98-49f3-9158-a4a4376dfbe1'); // Returns true
Str::isUuid('laravel-up-and-running'); // Returns false
```

`Str::random(*$length = n*)`

返回指定长度的大小写混合字母数字随机字符串：

```php
$hash = Str::random(64);
// Sample: J40uNWAvY60wE4BPEWxu7BZFQEmxEHmGiLmQncj0ThMGJK7O5Kfgptyb9ul wspmh
```

`Str::slug(*$title, $separator = '-', $language = 'en'*)`

从字符串中创建一个 URL 友好的 slug，通常用于为名称或标题创建 URL 段：

```php
Str::slug('How to Win Friends and Influence People');
// Returns 'how-to-win-friends-and-influence-people'
```

`Str::plural(*$value, $count = n*)`

将字符串转换为其复数形式。该函数目前仅支持英语语言：

```php
Str::plural('book');
// Returns books

Str::plural('person');
// Returns people

Str::plural('person', 1);
// Returns person
```

`__(*$key, $replace = [], $locale = null*)`

使用你的本地化文件翻译给定的翻译字符串或翻译键：

```php
echo __('Welcome to your dashboard');

echo __('messages.welcome');
```

## 应用程序路径

当处理文件系统时，通常很繁琐为获取和保存文件创建链接到某些目录。这些助手函数让你快速访问到你的应用程序中一些最重要目录的完全限定路径。

请注意，这些函数可以不带参数调用，但如果传递参数，它将附加到正常目录字符串的末尾并作为整体返回：

`app_path(*$append = ''*)`

返回你的应用程序中*app*目录的路径：

```php
app_path();
// Returns /home/forge/myapp.com/app
```

`base_path(*$path = ''*)`

返回你的应用程序根目录的路径：

```php
base_path();
// Returns /home/forge/myapp.com
```

`config_path(*$path = ''*)`

返回你的应用程序中配置文件的路径：

```php
config_path();
// Returns /home/forge/myapp.com/config
```

`database_path(*$path = ''*)`

返回你的应用程序中数据库文件的路径：

```php
database_path();
// Returns /home/forge/myapp.com/database
```

`storage_path(*$path = ''*)`

返回你的应用程序中*storage*目录的路径：

```php
storage_path();
// Returns /home/forge/myapp.com/storage
```

`lang_path(*$path = ''*)`

返回你的应用程序中*lang*目录的路径：

```php
lang_path();
// Returns /home/forge/myapp.com/resources/lang
```

## URLs

一些前端文件路径是一致的，但有时输入起来很烦人—例如，资源文件的路径—有方便的快捷方式对它们很有帮助，我们将在这里介绍。但是，一些路径实际上可能会变化，因此确保所有链接和资源正常工作的一些助手函数至关重要：

`action(*$action*, *$parameters = []*, *$absolute = true*)`

假设控制器方法只映射到一个 URL，根据控制器和方法名对（用`@`分隔）或使用元组表示法，返回正确的 URL：

```php
<a href="{{ action('PersonController@index') }}">See all People</a>
// Or, using tuple notation:
<a href="{{ action(
 [App\Http\Controllers\PersonController::class, 'index']
 ) }}">
    See all People
</a>

// Returns <a href="http://myapp.com/people">See all People</a>
```

如果控制器方法需要参数，你可以将它们作为第二个参数传递（如果需要多个参数，则作为数组传递）。如果你想要清晰地标记它们，你可以给它们加上键，但重要的是它们按正确的顺序排列：

```php
<a href="{{ action(
 'PersonController@show',
 ['id => 3]
 ) }}">See Person #3</a>
// or
<a href="{{ action(
 'PersonController@show',
 [3]
 ) }}">See Person #3</a

// Returns <a href="http://myapp.com/people/3">See Person #3</a>
```

如果将第三个参数传递为`false`，你的链接将生成为相对路径（*/people/3*）而不是绝对路径（*http://myapp.com/people/3*）。

`route(*$name*, *$parameters = []*, *$absolute = true*)`

如果路由有名称，返回该路由的 URL：

```php
// routes/web.php
Route::get('people', [PersonController::class, 'index'])
    ->name('people.index');

// A view somewhere
<a href="{{ route('people.index') }}">See all People</a>

// Returns <a href="http://myapp.com/people">See all People</a>
```

如果路由定义需要参数，你可以将它们作为第二个参数传递（如果需要多个参数，则作为数组传递）。同样，如果你想要清晰地标记它们，你可以给它们加上键，但重要的是它们按正确的顺序排列：

```php
<a href="{{ route('people.show', ['id' => 3]) }}">See Person #3</a>
// or
<a href="{{ route('people.show', [3]) }}">See Person #3</a>

// Returns <a href="http://myapp.com/people/3">See Person #3</a>
```

如果将第三个参数传递为`false`，你的链接将生成为相对路径而不是绝对路径。

`url(*$string*)`和`secure_url(*$string*)`

给定任何路径字符串，转换为完全限定的 URL。(`secure_url()`与`url()`相同，但强制使用 HTTPS)：

```php
url('people/3');

// Returns http://myapp.com/people/3
```

如果没有传递参数，这将返回一个`Illuminate``\Routing``\UrlGenerator`的实例，这样就可以进行方法链式调用：

```php
url()->current();
// Returns http://myapp.com/abc

url()->full();
// Returns http://myapp.com/abc?order=reverse

url()->previous();
// Returns http://myapp.com/login

// And many more methods available on the UrlGenerator...
```

## 杂项

有一些其他全局助手我建议你熟悉一下。当然，你应该查看[完整列表](https://oreil.ly/vssfi)，但这里提到的几个绝对值得一看：

`abort(*$code, $message, $headers*)`, `abort_unless(*$boolean, $code, $message, $headers*)`, `abort_if(*$boolean, $code, $message, $headers*)`

抛出 HTTP 异常。`abort()`抛出定义的异常，`abort_unless()`在第一个参数为`false`时抛出异常，`abort_if()`在第一个参数为`true`时抛出异常：

```php
public function controllerMethod(Request $request)
{
    abort(403, 'You shall not pass');
    abort_unless(request()->filled('magicToken'), 403);
    abort_if(request()->user()->isBanned, 403);
}
```

`auth()`

返回 Laravel 认证器的实例。像`Auth`门面一样，您可以使用它来获取当前用户，检查登录状态等：

```php
$user = auth()->user();
$userId = auth()->id();

if (auth()->check()) {
   // Do something
}
```

`back()`

生成一个“重定向回上一页”的响应，将用户发送到先前的位置：

```php
Route::get('post', function () {
    // ...

    if ($condition) {
        return back();
    }
});
```

`collect(*$array*)`

接受一个数组并返回相同数据，转换为集合：

```php
$collection = collect(['Rachel', 'Hototo']);
```

我们稍后会讨论集合。

`config(*$key*)`

返回任何点表示的配置项的值：

```php
$defaultDbConnection = config('database.default');
```

`csrf_field()`, `csrf_token()`

返回一个完整的 HTML 隐藏输入字段（`csrf_field()`）或仅适当的令牌值（`csrf_token()`），用于向您的表单提交添加 CSRF 验证：

```php
<form>
    {{ csrf_field() }}
</form>

// or

<form>
    <input type="hidden" name="_token" value="{{ csrf_token() }}">
</form>
```

`dump(*$variable*)`, `dd(*$variable*...)`

对所有提供的参数运行类似于`var_dump()`的输出；`dd()`还会运行`exit()`来退出应用程序（这用于调试）：

```php
// ...
dump($var1, $var2); // Check the output...
// ...
dd($var1, $var2, $state); // Why is this not working???
```

`env(*$key*, *$default = null*)`

返回给定键的环境变量：

```php
$key = env('API_KEY', '');
```

请记住，在配置文件之外绝不要使用`env()`。

`dispatch(*$job*)`

分派一个作业：

```php
dispatch(new EmailAdminAboutNewUser($user));
```

`event(*$event*)`

触发一个事件：

```php
event(new ContactAdded($contact));
```

`old(*$key = null*, *$default = null*)`

返回此表单键的上次用户表单提交的旧值（如果存在）：

```php
<input name="name" value="{{ old('value', 'Your name here') }}"
```

`redirect(*$path*)`

返回到给定路径的重定向响应：

```php
Route::get('post', function () {
    // ...

    return redirect('home');
});
```

没有参数时，生成`Illuminate\Routing\Redirector`类的实例。

`response(*$content*, *$status = 200*, *$headers*)`

如果传入参数，则返回预先构建的`Response`实例。如果没有参数，则返回`Response`工厂的实例：

```php
return response('OK', 200, ['X-Header-Greatness' => 'Super great']);

return response()->json(['status' => 'success']);
```

`tap(*$value*, *$callback = null*)`

调用闭包（第二个参数），传递第一个参数给闭包，然后返回第一个参数（而不是闭包的输出）：

```php
return tap(Contact::first(), function ($contact) {
    $contact->name = 'Aheahe';
    $contact->save();
});
```

`view(*$viewPath*)`

返回视图实例：

```php
Route::get('home', function () {
    return view('home'); // Gets /resources/views/home.blade.php
});
```

`fake()`

返回一个 Faker 的实例：

```php
@for($i = 0; $i <= 4; $i++)
    <td>Purchased by {{ fake()->unique()->name() }}</td>
@endfor
```

# 集合

集合是 Laravel 提供的最强大但最不被赞赏的工具之一。我们在“Eloquent Collections”中稍作介绍，但这里快速回顾一下。

集合本质上是具有超能力的数组。您通常需要将数组传递给的数组遍历方法（`array_walk()`、`array_map()`、`array_reduce()`等）都可以作为每个集合上一致、清晰、可链式调用的方法使用。您可以尝试使用函数式编程，通过 map、reduce 和 filter 实现更清晰的代码。

我们将在这里讨论 Laravel 集合和集合管道编程的基础知识，但要深入了解，请查看 Adam Wathan 的书《Refactoring to Collections》（Gumroad）。

## 基础知识

Laravel 中的集合并不是一个新概念。许多语言默认支持数组的集合式编程，但在 PHP 中我们就没那么幸运了。

使用 PHP 的 `array*()` 函数，我们可以将 示例 17-1 中显示的混乱怪物转换为稍微不那么混乱的怪物，如 示例 17-2 所示。

##### 示例 17-1\. 一个常见但丑陋的 `foreach` 循环

```php
$users = [...];

$admins = [];

foreach ($users as $user) {
    if ($user['status'] == 'admin') {
        $user['name'] = $user['first'] . ' ' . $user['last'];
        $admins[] = $user;
    }
}

return $admins;
```

##### 示例 17-2\. 使用原生 PHP 函数重构 `foreach` 循环

```php
$users = [...];

return array_map(function ($user) {
    $user['name'] = $user['first'] . ' ' . $user['last'];
    return $user;
}, array_filter($users, function ($user) {
    return $user['status'] == 'admin';
}));
```

在这里，我们去掉了一个临时变量（`$admins`），并将一个令人困惑的 `foreach` 循环转换为两个明确的操作：map 和 filter。

问题在于，PHP 的数组操作函数令人困惑且糟糕。只需看看这个例子；`array_map()` 先接受闭包，再接受数组，但 `array_filter()` 先接受数组，再接受闭包。此外，如果我们增加了任何复杂性，就会有函数嵌套函数嵌套函数的情况。真是一团糟。

Laravel 的集合将 PHP 的数组操作方法的强大之处与简洁流畅的语法结合起来，并添加了许多甚至在 PHP 的数组操作工具箱中不存在的方法。使用 `collect()` 辅助方法将数组转换为 Laravel 集合后，我们可以像 示例 17-3 中展示的那样操作。

##### 示例 17-3\. 使用 Laravel 集合重构 `foreach` 循环

```php
$users = collect([...]);

return $users->filter(function ($user) {
    return $user['status'] == 'admin';
})->map(function ($user) {
    $user['name'] = $user['first'] . ' ' . $user['last'];
    return $user;
});
```

这并不是最极端的例子。还有很多其他例子，减少代码行数和增加简单性会更加强有力。但这种情况*非常常见*。

看看原始示例及其混乱的情况。除非你完全理解整个代码样本，否则不会完全清楚任何给定部分的用途。

集合提供的最大好处是，相比其他任何方法，它将操作数组的行为分解为简单、明确和可理解的任务。现在你可以像这样做：

```php
$users = [...]
$countAdmins = collect($users)->filter(function ($user) {
    return $user['status'] == 'admin';
})->count();
```

或者像这样：

```php
$users = [...];
$greenTeamPoints = collect($users)->filter(function ($user) {
    return $user['team'] == 'green';
})->sum('points');
```

在本章的其余部分中，我们将看到许多例子都是基于我们在这里开始想象的虚构的 `$users` 集合。`$users` 数组中的每个条目都代表一个单独的人类；它们可能都可以通过数组访问。每个用户具体的属性可能会根据示例有所不同。但是无论何时看到这个 `$users` 变量，都知道这是我们正在处理的内容。

## 几个集合操作

比我们迄今为止涵盖的还要多得多。我建议您查看[Laravel 集合文档](https://oreil.ly/i83f4)以了解更多可用的方法，但为了帮助您入门，这里只列出了一些核心方法：

`all()`, `toArray()`

如果你想将集合转换为数组，可以使用`all()`或`toArray()`。`toArray()`不仅将集合转换为数组，还会将其下面的任何 Eloquent 对象扁平化为数组。`all()`仅将集合转换为数组；集合中包含的任何 Eloquent 对象将保持为 Eloquent 对象。以下是一些示例：

```php
$users = User::all();

$users->toArray();

/* Returns
 [
 ['id' => '1', 'name' => 'Agouhanna'],
 ...
 ]
*/

$users->all();

/* Returns
 [
 Eloquent object { id : 1, name: 'Agouhanna' },
 ...
 ]
*/
```

`filter()`, `reject()`

当你想通过检查每个项目是否符合闭包来获取原始集合的一个子集时，你将使用`filter()`（如果闭包返回`true`则保留项目）或`reject()`（如果闭包返回`false`则保留项目）：

```php
$users = collect([...]);
$admins = $users->filter(function ($user) {
    return $user->isAdmin;
});

$paidUsers = $user->reject(function ($user) {
    return $user->isTrial;
});
```

`where()`

`where()`使得提供原始集合的一个子集变得容易，其中给定键等于给定值。你可以用`where()`做的任何事情也可以用`filter()`，但它是一个常见情景的快捷方式：

```php
$users = collect([...]);
$admins = $users->where('role', 'admin');
```

`whereNull()`, `whereNotNull()`

`whereNull()`使得提供原始集合的一个子集变得容易，其中给定键等于`null`；`whereNotNull()`则是其反义：

```php
$users = collect([...]);
$active = $users->whereNull('deleted_at');
$deleted = $users->whereNotNull('deleted_at');
```

`first()`, `last()`

如果你只想从集合中获取单个项目，可以使用`first()`来获取列表的开头或`last()`来获取列表的末尾。

如果你调用`first()`或`last()`而没有参数，它们只会分别给出集合中的第一个或最后一个项目。但如果你传递一个闭包，它们将会给出集合中首个或最后一个在该闭包中返回`true`的项目。

有时你会这样做是因为你想要实际的第一个或最后一个项目。但有时这是获取一个项目的最简单方法，即使你只期望有一个项目：

```php
$users = collect([...]);
$owner = $users->first(function ($user) {
    return $user->isOwner;
});

$firstUser = $users->first();
$lastUser = $users->last();
```

你还可以向每种方法传递第二个参数，即默认值，如果闭包没有提供任何结果，将作为后备提供。

`each()`

如果你想对集合的每个项目执行某些操作，但不包括修改项目或集合本身，则可以使用`each()`：

```php
$users = collect([...]);
$users->each(function ($user) {
    EmailUserAThing::dispatch($user);
});
```

`map()`

如果你想遍历集合中的所有项目，对它们进行更改，并返回带有所有更改的新集合，你应该使用`map()`：

```php
$users = collect([...]);
$users = $users->map(function ($user) {
    return [
        'name' => $user['first'] . ' ' . $user['last'],
        'email' => $user['email'],
    ];
});
```

`reduce()`

如果你想从集合中获取单个结果，如计数或字符串，你可能想使用`reduce()`。该方法通过采用初始值（称为*carry*）并允许集合中的每个项目以某种方式改变该值来工作。你可以为*carry*定义一个初始值，并接受当前*carry*状态和每个项目作为参数的闭包：

```php
$users = collect([...]);

$points = $users->reduce(function ($carry, $user) {
    return $carry + $user['points'];
}, 0); // Start with a carry of 0
```

`pluck()`

如果你想要仅获取集合中每个项目下给定键的值，可以使用`pluck()`：

```php
$users = collect([...]);

$emails = $users->pluck('email')->toArray();
```

`chunk()`, `take()`

`chunk()`使得将集合分割成预定义大小的组变得容易，`take()`则仅获取指定数量的项目：

```php
$users = collect([...]);

$rowsOfUsers = $users->chunk(3); // Separates into groups of 3

$topThree = $users->take(3); // Pulls the first 3
```

`takeUntil()`, `takeWhile()`

`takeUntil()` 返回集合中直到回调函数返回 `true` 的所有项目。`takeWhile()` 返回集合中直到回调函数返回 `false` 的所有项目。如果传递给 `takeUntil()` 的回调函数从未返回 `true`，或者传递给 `takeWhile()` 的回调函数从未返回 `false`，则返回整个集合：

```php
$items = collect([1, 2, 3, 4, 5, 6, 7, 8, 9]);

$subset = $items->takeUntil(function ($item) {
    return $item >= 5;
})->toArray();
// [1, 2, 3, 4]

$subset = $items->takeWhile(function ($item) {
    return $item < 4;
})->toArray();
// [1, 2, 3]
```

`groupBy()`

如果你想要根据它们的某个属性值将所有项目分组到你的集合中，你可以使用 `groupBy()`：

```php
$users = collect([...]);

$usersByRole = $users->groupBy('role');

/* Returns:
 [
 'member' => [...],
 'admin' => [...],
 ]
*/
```

你也可以传递一个闭包，闭包返回的内容将用于分组记录：

```php
$heroes = collect([...]);

$heroesByAbilityType = $heroes->groupBy(function ($hero) {
    if ($hero->canFly() && $hero->isInvulnerable()) {
        return 'Kryptonian';
    }

    if ($hero->bitByARadioactiveSpider()) {
        return 'Spidermanesque';
    }

    if ($hero->color === 'green' && $hero->likesSmashing()) {
        return 'Hulk-like';
    }

    return 'Generic';
});
```

`reverse()`、`shuffle()`

`reverse()` 将你的集合中项目的顺序反转，而 `shuffle()` 将它们随机化：

```php
$numbers = collect([1, 2, 3]);

$numbers->reverse()->toArray(); // [3, 2, 1]
$numbers->shuffle()->toArray(); // [2, 3, 1]
```

`skip()`

`skip()` 返回一个新的集合，其中不包括指定数量的项目：

```php
$numbers = collect([1, 2, 3, 4, 5]);

$numbers->skip(3)->values(); // [4, 5]
```

`skipUntil()`

`skipUntil()` 跳过项目直到回调函数返回 `true`。你也可以传递一个值给 `skipUntil`，它将跳过所有值，直到找到给定的值。如果值从未找到或回调从未返回 `true`，则返回一个空集合：

```php
$numbers = collect([1, 2, 3, 4, 5]);

$numbers->skipUntil(function ($item) {
    return $item > 3;
})->values();
// [4, 5]

$numbers->skipUntil(3)->values();
// [3, 4, 5]
```

`skipWhile()`

`skipWhile()` 跳过项目直到回调函数返回 `true`。如果回调从未返回 `false`，则返回一个空集合：

```php
$numbers = collect([1, 2, 3, 4, 5]);

$numbers->skipWhile(function ($item) {
    return $item <= 3;
})->toArray();
// [4, 5]
```

`sort()`、`sortBy()`、`sortByDesc()`

如果你的项目是简单的字符串或整数，你可以使用 `sort()` 来对它们进行排序：

```php
$sortedNumbers = collect([1, 7, 6])->sort()->toArray(); // [1, 6, 7]
```

如果它们更复杂，你可以将一个字符串（表示属性）或闭包传递给 `sortBy()` 或 `sortByDesc()` 来定义你的排序行为：

```php
$users = collect([...]);

// Sort an array of users by their 'email' property
$users->sort('email');

// Sort an array of users by their 'email' property
$users->sortBy(function ($user, $key) {
    return $user['email'];
});
```

`countBy()`

`countBy` 计算集合中每个值的每次出现：

```php
$collection = collect([10, 10, 20, 20, 20, 30]);

$collection->countBy()->all();

// [10 => 2, 20 => 3, 30 => 1]
```

结果集合中的每个键都是原始值之一；它的配对值是该值在原始集合中出现的次数。

`countBy` 方法还接受一个回调函数，用于自定义用于计算集合中每个项目的值：

```php
$collection = collect(['laravel.com', 'tighten.co']);

$collection->countBy(function ($address) {
    return Str::after($address, '.');
})->all();

// all: ["com" => 1, "co" => 1]
```

`count()`、`isEmpty()`、`isNotEmpty()`

你可以使用 `count()`、`isEmpty()` 或 `isNotEmpty()` 来查看集合中有多少项目：

```php
$numbers = collect([1, 2, 3]);

$numbers->count();   // 3
$numbers->isEmpty(); // false
$numbers->isNotEmpty() // true
```

`avg()`、`sum()`

如果你正在处理一组数字的集合，`avg()` 和 `sum()` 就像它们的方法名所说的那样工作，不需要任何参数：

```php
collect([1, 2, 3])->sum(); // 6
collect([1, 2, 3])->avg(); // 2
```

但如果你正在处理数组，你可以将每个数组中要提取的属性的键传递给操作：

```php
$users = collect([...]);

$sumPoints = $users->sum('points');
$avgPoints = $users->avg('points');
```

`join`

`join()` 将集合值连接为单个输出字符串，使用提供的字符串连接每个值，类似于 PHP 的 `join()` 方法。你也可以（可选地）自定义最终的连接操作符：

```php
$collection = collect(['a', 'b', 'c', 'd', 'e']);
$collection->join(', ', ', and ');

// 'a, b, c, d, and e'
```

# 在 Laravel 之外使用集合

你是否已经爱上了集合，并想在你的非 Laravel 项目中使用它们？

只需使用 `composer require illuminate/collections` 命令，你就可以在你的代码中准备好 `Illuminate\S⁠u⁠p⁠p⁠o⁠r⁠t⁠\​C⁠o⁠l⁠l⁠e⁠c⁠t⁠i⁠o⁠n` 类，以及 `collect()` 辅助函数。

# 简而言之

Laravel 提供了一套全局助手函数，简化各种任务。它们使得操作和检查数组和字符串变得更加容易，方便生成路径和 URL，并提供简单访问一些持久和关键功能。

Laravel 的集合（collections）是强大的工具，为 PHP 带来了集合管道的可能性。
