# 第十四章：存储和检索

我们在 第五章 中讨论了如何在关系数据库中存储数据，但可以在本地和远程存储中存储更多数据。本章将涵盖文件系统和内存存储、文件上传和操作、非关系数据存储、会话、缓存、日志记录、Cookie 和全文搜索。

# 本地和云文件管理器

Laravel 通过 `Storage` 门面和一些辅助函数提供一系列文件操作工具。

Laravel 的文件系统访问工具可以连接到本地文件系统以及 S3、Rackspace 和 FTP。S3 和 Rackspace 文件驱动程序由 [Flysystem](https://oreil.ly/2lP4P) 提供，并且可以简单地添加额外的 Flysystem 提供者，如 Dropbox 或 WebDAV，到您的 Laravel 应用程序中。

## 配置文件访问

Laravel 文件管理器的定义位于 *config/filesystems.php* 中。每个连接称为“磁盘”，示例 14-1 列出了开箱即用的磁盘。

##### 示例 14-1 默认可用的存储磁盘

```php
...
'disks' => [
    'local' => [
        'driver' => 'local',
        'root' => storage_path('app'),
        'throw' => false,
    ],
    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'url' => env('APP_URL').'/storage',
        'visibility' => 'public',
        'throw' => false,
    ],
    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'url' => env('AWS_URL'),
        'endpoint' => env('AWS_ENDPOINT'),
        'use_path_style_endpoint' => env('AWS_USE_PATH_STYLE_ENDPOINT', false),
        'throw' => false,
    ],
],
```

# `storage_path()` 辅助函数

在 示例 14-1 中使用的 `storage_path()` 辅助函数链接到 Laravel 配置的存储目录，即 *storage/*。将任何内容传递给它，都将添加到目录名称的末尾，因此 `storage_path('public')` 将返回字符串 `storage/public`。

`local` 磁盘连接到您的本地存储系统，并假定它将与存储路径的 *app* 目录进行交互，即 *storage/app*。

`public` 磁盘也是一个本地磁盘（虽然您可以根据需要更改它），用于应用程序提供的文件。它默认为 *storage/app/public* 目录，如果您希望使用此目录向公众提供文件，则需要在 *public/* 目录中添加符号链接（*symlink*）。幸运的是，有一个 Artisan 命令将 *public/storage* 映射为从 *storage/app/public* 读取文件的服务：

```php
php artisan storage:link
```

`s3` 磁盘显示了 Laravel 如何连接到基于云的文件存储系统。如果您曾连接到 S3 或任何其他云存储提供者，这将很熟悉；传递您的密钥和秘密以及定义您正在使用的“文件夹”的一些信息，S3 中是区域和存储桶。

## 使用 Storage 门面

在 *config/filesystem.php* 中，您可以设置默认磁盘，这将在未指定磁盘时使用任何时候调用 Storage 门面。要指定磁盘，请在门面上调用 `disk('*diskname*')`：

```php
Storage::disk('s3')->get('file.jpg');
```

所有文件系统都提供以下方法：

`get('*file.jpg*')`

检索 `*file.jpg*` 文件。

`json('*file.json*', $flags)`

检索 `*file.json*` 文件并解码其 JSON 内容。

`put('*file.jpg*', *$contentsOrStream*)`

将给定的文件内容放到 `*file.jpg*`。

`putFile('*myDir*', *$file*)`

将提供文件的内容（以 `Illuminate\Http\File` 或 `Illuminate\Http\UploadedFile` 实例的形式）放置到 `*myDir*` 目录中，但 Laravel 管理整个流程和文件命名

`exists('*file.jpg*')`

返回一个布尔值，指示 `*file.jpg*` 是否存在

`getVisibility('*myPath*')`

获取给定路径的可见性（“public”或“private”）

`setVisibility('*myPath*')`

设置给定路径的可见性（“public”或“private”）

`copy('*file.jpg*', '*newfile.jpg*')`

将 `*file.jpg*` 复制到 `*newfile.jpg*`

`move('*file.jpg*', '*newfile.jpg*')`

将 `*file.jpg*` 移动到 `*newfile.jpg*`

`prepend('*my.log*', '*log text*')`

在 `*my.log*` 的开头添加 *`log text`* 内容

`append('*my.log*', '*log text*')`

在 `*my.log*` 的末尾添加 *`log text`* 内容

`delete('*file.jpg*')`

删除 `*file.jpg*`

`size('*file.jpg*')`

返回 `*file.jpg*` 的字节大小

`lastModified('*file.jpg*')`

返回 `*file.jpg*` 上次修改的 Unix 时间戳

`files('*myDir*')`

返回目录 `*myDir*` 中的文件名数组

`allFiles('*myDir*')`

返回目录 `*myDir*` 及其所有子目录中的文件名数组

`directories('*myDir*')`

返回目录 `*myDir*` 中的目录名数组

`allDirectories('*myDir*')`

返回目录 `*myDir*` 及其所有子目录中的目录名数组

`makeDirectory('*myDir*')`

创建一个新目录

`deleteDirectory('*myDir*')`

删除 `*myDir*`

`readStream('*my.log*')`

获取用于读取 `*my.log*` 的资源

`writeStream('*my.log*', $resource)`

使用流写入新文件（`*my.log*`）

# 注入一个实例

如果您希望注入一个实例而不是使用 `File` 门面，可以类型提示或注入 `Illuminate\Filesystem\Filesystem`，您将拥有相同的所有方法可用。

## 添加额外的 Flysystem 提供者

如果您想添加额外的 Flysystem 提供者，您需要“扩展” Laravel 的本机存储系统。在某个服务提供者中——可以是 `AppServiceProvider` 的 `boot()` 方法，但为每个新绑定创建一个唯一的服务提供者会更合适——使用 `Storage` 门面添加新的存储系统，如 示例 14-2 中所示。

##### 示例 14-2\. 添加额外的 Flysystem 提供者

```php
// Some service provider
public function boot(): void
{
    Storage::extend('dropbox', function ($app, $config) {
        $client = new DropboxClient(
            $config['accessToken'], $config['clientIdentifier']
        );

        return new Filesystem(new DropboxAdapter($client));
    });
}
```

# 基本文件上传和操作

`Storage` 门面的更常见用法之一是接受来自应用程序用户的文件上传。让我们看一下在 示例 14-3 中的常见工作流程。

##### 示例 14-3\. 常见用户上传工作流程

```php
...
class DogController
{
    public function updatePicture(Request $request, Dog $dog)
    {
        Storage::put(
            "dogs/{$dog->id}",
            file_get_contents($request->file('picture')->getRealPath())
        );
    }
}
```

我们将`put()`到名为*dogs/id*的文件中，并且我们从上传的文件中获取我们的内容。每个上传的文件都是`SplFileInfo`类的后代，它提供了一个`getRealPath()`方法，返回文件位置的路径。因此，我们获取用户上传文件的临时上传路径，用`file_get_contents()`读取它，并传递给`Storage::put()`。

由于我们在这里可以使用此文件，我们可以在存储之前对文件进行任何操作——如果是图像，则使用图像处理包进行调整大小，验证并拒绝不符合我们标准的文件，或者其他任何操作。

如果我们想要将同一文件上传到 S3，并且我们的凭据存储在*config/filesystems.php*中，我们可以简单地调整示例 14-3 以调用`Storage::disk('s3')->put()`；现在我们将上传到 S3。查看示例 14-4 以查看更复杂的上传示例。

##### 示例 14-4\. 使用 Intervention 的文件上传的更复杂示例

```php
...
class DogController
{
    public function updatePicture(Request $request, Dog $dog)
    {
        $original = $request->file('picture');

        // Resize image to max width 150
        $image = Image::make($original)->resize(150, null, function ($constraint) {
            $constraint->aspectRatio();
        })->encode('jpg', 75);

        Storage::put(
            "dogs/thumbs/{$dog->id}",
            $image->getEncoded()
        );
    }
```

我在示例 14-4 中使用了一个称为[Intervention](http://image.intervention.io)的图像库，仅作为示例；您可以使用任何您想要的库。重要的是，在存储之前，您有自由对文件进行任意操作。

# 使用 Uploaded File 的 store()和 storeAs()方法

您还可以使用文件本身存储已上传的文件。在示例 7-18 中了解更多信息。

# 简单文件下载

就像`Storage`简化了接受用户上传的任务一样，它也简化了将文件返回给用户的任务。查看示例 14-5 以获取最简单的示例。

##### 示例 14-5\. 简单文件下载

```php
public function downloadMyFile()
{
    return Storage::download('my-file.pdf');
}
```

# 会话

会话存储是我们在 Web 应用程序中用于在页面请求之间存储状态的主要工具。Laravel 的会话管理器支持使用文件、Cookie、数据库、Memcached 或 Redis、DynamoDB 或内存数组作为会话驱动程序（在页面请求后过期，仅适用于测试）。

您可以在*config/session.php*中配置所有会话设置和驱动程序。您可以选择是否加密会话数据，选择使用哪个驱动程序（`file`是默认值），并指定更多特定于连接的详细信息，如会话存储的长度以及要使用哪些文件或数据库表。查看[会话文档](https://oreil.ly/AMp4T)以了解您需要为所选择的驱动程序准备的特定依赖关系和设置。

会话工具的一般 API 允许您基于单个键保存和检索数据：例如`session()->put('*user_id*')`和`session()->get('*user_id*')`。确保避免将任何内容保存到`flash`会话键中，因为 Laravel 在内部使用它进行闪存（仅在下一个页面请求中可用）会话存储。

## 访问会话

访问会话的最常见方法是使用`Session`门面：

```php
Session::get('user_id');
```

但是你也可以在任何给定的 Illuminate `Request` 对象上使用 `session()` 方法，就像在 示例 14-6 中所示的那样。

##### 示例 14-6\. 在 `Request` 对象上使用 `session()` 方法

```php
Route::get('dashboard', function (Request $request) {
    $request->session()->get('user_id');
});
```

或者你可以像在 示例 14-7 中那样，注入 `Illuminate\Session\Store` 的实例。

##### 示例 14-7\. 注入会话支持类

```php
Route::get('dashboard', function (Illuminate\Session\Store $session) {
    return $session->get('user_id');
});
```

最后，你可以使用全局 `session()` 辅助函数。使用无参数获取会话实例，使用单个字符串参数从会话中“获取”，或者使用数组“存入”会话，如 示例 14-8 中所示。

##### 示例 14-8\. 使用全局的 `session()` 辅助函数

```php
// Get
$value = session()->get('key');
$value = session('key');
// Put
session()->put('key', 'value');
session(['key', 'value']);
```

如果你是 Laravel 新手并不确定该使用哪个方法，我建议使用全局辅助函数。

## 会话实例上可用的方法

最常见的两个方法是 `get()` 和 `put()`，但让我们来看看每个可用方法及其参数：

`session()->get(*$key*, *$fallbackValue*)`

`get()` 方法从会话中获取提供的键的值。如果该键没有对应的值，它将返回备用值（如果没有提供备用值，则返回 `null`）。备用值可以是一个简单的值或一个闭包，正如下面的示例所示：

```php
$points = session()->get('points');

$points = session()->get('points', 0);

$points = session()->get('points', function () {
    return (new PointGetterService)->getPoints();
});
```

`session()->put(*$key*, *$value*)`

`put()` 将提供的值存储在会话中的提供键下：

```php
session()->put('points', 45);

$points = session()->get('points');
```

`session()->push(*$key*, *$value*)`

如果你的会话值是数组，你可以使用 `push()` 方法向数组中添加一个值：

```php
session()->put('friends', ['Saúl', 'Quang', 'Mechteld']);

session()->push('friends', 'Javier');
```

`session()->has(*$key*)`

`has()` 检查是否在提供的键上设置了一个值：

```php
if (session()->has('points')) {
    // Do something
}
```

你也可以传递一个键的数组，只有所有键存在时它才返回 `true`。

# `session()->has()` 和空值

如果设置了会话值但该值为 `null`，`session()``->``has()` 将返回 `false`。

`session()->exists(*$key*)`

`exists()` 检查是否在提供的键上设置了一个值，类似于 `has()`，但不同的是，即使设置的值为 `null`，它也将返回 `true`：

```php
if (session()->exists('points')) {
    // returns true even if 'points' is set to null
}
```

`session()->all()`

`all()` 返回会话中的所有内容的数组，包括框架设置的值。你可能会在键如 `_token`（CSRF 令牌）、`_previous`（上一页，用于 `back()` 重定向）和 `flash`（闪存存储）下看到值。

`session()->only()`

`only()` 返回会话中仅指定值的数组。

`session()->forget(*$key*)`，`session()->flush()`

`forget()` 删除先前设置的会话值。`flush()` 删除每个会话值，即使是框架设置的值也会被删除。

```php
session()->put('a', 'awesome');
session()->put('b', 'bodacious');

session()->forget('a');
// a is no longer set; b is still set
session()->flush();
// Session is now empty
```

`session()->pull(*$key*, *$fallbackValue*)`

`pull()` 和 `get()` 相同，不同之处在于后者在从会话中获取值后将其删除。

`session()->regenerate()`

并不常见，但如果你需要重新生成会话 ID，可以使用 `regenerate()` 方法。

## 闪存会话存储

还有三种我们尚未介绍的方法，它们都与 *闪存会话存储* 有关。

会话存储的一种非常常见的模式是设置一个值，你希望它仅在下一个页面加载时可用。例如，你可能想存储像“成功更新帖子。”这样的消息。你可以手动获取该消息，然后在下一个页面加载时清除它，但如果你经常使用这种模式，会变得浪费。引入闪存会话存储：预期仅在单个页面请求期间存在的键。

Laravel 会为你处理这些工作，你只需使用`flash()`而不是`put()`。以下是这里的有用方法：

`session()->flash(*$key*, *$value*)`

`flash()`设置会话键为提供的值，仅用于下一个页面请求。

`session()->reflash()`, `session()->keep(*$key*)`

如果你需要上一页的闪存会话数据保留一个更多的请求，你可以使用`reflash()`来恢复所有数据到下一个请求或`keep(*$key*)`来只恢复一个单一的闪存值到下一个请求。`keep()`也可以接受一个键的数组来刷新。

# 缓存

缓存的结构与会话非常相似。你提供一个键，Laravel 为你存储它。最大的区别在于缓存中的数据是应用程序级别的，而会话中的数据是用户级别的。这意味着缓存更常用于存储来自数据库查询、API 调用或其他可以稍微“过时”的缓慢查询的结果。

缓存配置设置可以在*config/cache.php*中找到。就像会话一样，你可以为任何驱动程序设置特定的配置详细信息，并选择哪一个将成为默认值。Laravel 默认使用`file`缓存驱动程序，但你也可以使用 Memcached 或 Redis、APC、DynamoDB 或数据库，或编写自己的缓存驱动程序。查看[缓存文档](https://laravel.com/docs/cache)了解你选择使用的驱动程序需要准备的特定依赖和设置。

## 访问缓存

就像会话一样，有几种访问缓存的方法。你可以使用外观：

```php
$users = Cache::get('users');
```

或者你可以从容器中获取一个实例，如示例 14-9。

##### 示例 14-9\. 注入缓存实例

```php
Route::get('users', function (Illuminate\Contracts\Cache\Repository $cache) {
    return $cache->get('users');
});
```

你还可以使用全局`cache()`助手，如示例 14-10。

##### 示例 14-10\. 使用全局`cache()`助手

```php
// Get from cache
$users = cache('key', 'default value');
$users = cache()->get('key', 'default value');
// Put for $seconds duration
$users = cache(['key' => 'value'], $seconds);
$users = cache()->put('key', 'value', $seconds);
```

如果你是 Laravel 的新手，不确定该使用哪个，我建议使用全局助手。

## 缓存实例上可用的方法

让我们看看可以在`Cache`实例上调用的方法：

`cache()->get(*$key*, *$fallbackValue*)`,

`cache()->pull(*$key*, *$fallbackValue*)`

`get()`使得轻松检索任何给定键的值。`pull()`与`get()`相同，只是在检索后移除缓存的值。

`cache()->put(*$key*, *$value*, *$secondsOrExpiration*)`

`put()` 为指定的键设置值，并在给定秒数后过期。如果你愿意设置一个到期日期/时间而不是秒数，你可以将 Carbon 对象作为第三个参数传递：

```php
cache()->put('key', 'value', now()->addDay());
```

`cache()->add(*$key*, *$value*)`

`add()` 类似于 `put()`，但如果值已存在，`add()` 不会设置它。此外，该方法返回一个布尔值，指示值是否实际被添加：

```php
$someDate = now();
cache()->add('someDate', $someDate); // returns true
$someOtherDate = now()->addHour();
cache()->add('someDate', $someOtherDate); // returns false
```

`cache()->forever(*$key*, *$value*)`

`forever()` 会将一个值永久保存在缓存中，对应特定的键；它和 `put()` 相同，除了这些值永远不会过期（直到用 `forget()` 移除它们）。

`cache()->has(*$key*)`

`has()` 返回一个布尔值，指示提供的键是否存在值。

`cache()->remember(*$key*, *$seconds*,` `*$closure*)`,

`cache()->rememberForever(*$key*,` `*$closure*)`

`remember()` 提供了一个单一的方法来处理非常常见的流程：查看是否存在某个键的缓存值，如果不存在，则以某种方式获取该值，保存到缓存中，并返回它。

`remember()` 允许你提供一个键来查找，应该保存的秒数以及一个闭包来定义如何查找它，以防该键没有设置值。`rememberForever()` 相同，只是它不需要你设置应保存的秒数。看下面的例子，了解 `remember()` 的常见用户场景：

```php
// Either returns the value cached at "users" or gets "User::all()",
// caches it at "users", and returns it
$users = cache()->remember('users', 7200, function () {
    return User::all();
});
```

`cache()->increment(*$key*, *$amount*)`, `cache()->decrement(*$key*, *$amount*)`

`increment()` 和 `decrement()` 允许你在缓存中增加和减少整数值。如果给定键没有值，它将被视为 `0`，如果你向增加或减少传递第二个参数，它将按该数量增加或减少，而不是按 `1`。

`cache()->forget(*$key*)`, `cache()->flush()`

`forget()` 的工作方式与 `Session` 的 `forget()` 方法相同：传递一个键，它将清除该键的值。`flush()` 将清空整个缓存。

# Cookies

你可能期望 cookie 能像会话和缓存一样工作。对于这三者，我们都提供了一个外观和全局助手，而我们对它们的心理模型也是相似的：你可以以同样的方式获取或设置它们的值。

但由于 cookie 本质上与请求和响应相关联，你需要以不同的方式与 cookie 交互。让我们简要看看使 cookie 不同的地方。

## Laravel 中的 Cookies

在 Laravel 中，cookie 可以存在三个地方。它们可以通过请求进入，这意味着用户在访问页面时拥有 cookie。你可以使用 `Cookie` 外观或从请求对象中读取它。

它们还可以与响应一起发送，这意味着响应将指示用户的浏览器保存 cookie 以备将来访问。在返回响应对象之前，你可以通过将 cookie 添加到响应对象中来实现这一点。

最后，一个 cookie 可以被排队。如果您使用 `Cookie` 门面设置一个 cookie，您必须将它放入“CookieJar”队列中，并且它将由 `AddQueuedCookiesToResponse` 中间件从响应对象中移除并添加。

## 访问 cookie 工具

您可以在三个位置获取和设置 cookie：`Cookie` 门面、`cookie()` 全局辅助函数以及请求和响应对象。

### cookie 门面

`Cookie` 门面提供了最全面的选项，不仅可以读取和创建 cookie，还可以将它们排队以添加到响应中。它提供以下方法：

`Cookie::get(*$key*)`

要获取请求中带有的 cookie 值，只需运行 `Cookie::get('*cookie-name*')`。这是最简单的选择。

`Cookie::has(*$key*)`

您可以使用 `Cookie::has('*cookie-name*')` 检查请求中是否带有 cookie，该方法返回一个布尔值。

`Cookie::make(*...params*)`

如果您想要在任何地方制作一个 cookie 而不将其排队，可以使用 `Cookie::make()`。这样做的最可能用途是制作一个 cookie，然后手动将其附加到响应对象，我们稍后会讨论这一点。

下面是 `make()` 方法的参数顺序：

+   `$name` 是 cookie 的名称。

+   `$value` 是 cookie 的内容。

+   `$minutes` 指定 cookie 应该存活多少分钟。

+   `$path` 是 cookie 应该有效的路径。

+   `$domain` 列出 cookie 应该工作的域。

+   `$secure` 表示 cookie 是否只能通过安全的（HTTPS）连接传输。

+   `$httpOnly` 表示 cookie 是否仅通过 HTTP 协议访问。

+   `$raw` 表示是否应无需 URL 编码地发送 cookie。

+   `$sameSite` 表示 cookie 是否可供跨站点请求使用；选项有 `lax`、`strict` 或 `null`。

`Cookie::make()`

返回一个 `Symfony\Component\HttpFoundation\Cookie` 的实例。

# cookie 的默认设置

`Cookie` 门面实例使用的 `CookieJar` 从会话配置中读取其默认值。因此，如果您在 *config/session.php* 中更改会话 cookie 的任何配置值，那么您使用 `Cookie` 门面创建的所有 cookie 都将应用相同的默认值。

`Cookie::queue(*Cookie || params*)`

如果你使用 `Cookie::make()`，仍然需要将 cookie 附加到响应中，我们稍后会讨论这个问题。`Cookie::queue()` 与 `Cookie::make()` 的语法相同，但是它会将创建的 cookie 加入队列，由中间件自动附加到响应中。

如果您愿意，您也可以将您自己创建的 cookie 直接传递给 `Cookie::queue()`。

这是在 Laravel 中向响应添加 cookie 的最简单方法：

```php
Cookie::queue('dismissed-popup', true, 15);
```

# 当您排队的 cookie 不会被设置时

Cookies 只能作为响应的一部分返回。因此，如果您使用 `Cookie` 门面添加了 cookie，然后响应未正确返回——例如，如果使用 PHP 的 `exit()` 或其他停止执行脚本的方法——则不会设置 cookie。

### `cookie()` 全局辅助函数

如果调用 `cookie()` 时不带参数，`cookie()` 全局辅助函数将返回一个 `CookieJar` 实例。然而，`Cookie` 门面上存在的两个最方便的方法——`has()` 和 `get()`——仅存在于门面上，而不是 `CookieJar` 上。因此，在这种情况下，我认为全局辅助函数实际上不如其他选项有用。

`cookie()` 全局辅助函数最有用的任务是创建一个 cookie。如果将参数传递给 `cookie()`，它们将直接传递给 `Cookie::make()` 的等效函数，因此这是创建 cookie 的最快方法：

```php
$cookie = cookie('dismissed-popup', true, 15);
```

# 注入实例

您还可以在应用程序的任何地方注入一个 `Illuminate\Cookie\CookieJar` 实例，但您将面临此处讨论的相同限制。

### 请求和响应对象中的 Cookies

由于 cookie 作为请求的一部分进入，并作为响应的一部分设置，这些 Illuminate 对象实际上是它们实际存在的位置。`Cookie` 门面的 `get()`、`has()` 和 `queue()` 方法只是与 `Request` 和 `Response` 对象交互的代理。

因此，与 cookie 交互的最简单方法是从请求中获取 cookie 并将其设置到响应中。

#### 从请求对象中读取 Cookies

一旦您有了 `Request` 对象的副本——如果您不知道如何获取它，只需尝试 `app('request')`——您可以使用 `Request` 对象的 `cookie()` 方法读取其 cookie，如 示例 14-11 中所示。

##### 示例 14-11\. 从 `Request` 对象中读取 cookie

```php
Route::get('dashboard', function (Illuminate\Http\Request $request) {
    $userDismissedPopup = $request->cookie('dismissed-popup', false);
});
```

如您在本例中所见，`cookie()` 方法有两个参数：cookie 的名称和可选的回退值。

#### 在响应对象上设置 Cookies

一旦您的 `Response` 对象准备就绪，您可以像 示例 14-12 中那样，在其上使用 `cookie()` 方法向响应添加 cookie。

##### 示例 14-12\. 在 `Response` 对象上设置 cookie

```php
Route::get('dashboard', function () {
    $cookie = cookie('saw-dashboard', true);

    return Response::view('dashboard')
        ->cookie($cookie);
});
```

如果您是 Laravel 的新手，并且不确定使用哪种选项，我建议您在 `Request` 和 `Response` 对象上设置 cookie。这需要更多工作，但如果未来的开发人员不理解 `CookieJar` 队列，会导致更少的意外。

# 日志记录

到目前为止，在本书中我们已经看到了一些关于日志的简短示例，当我们讨论其他概念如容器和门面时，让我们简要看看除了 `Log::info('Message')` 之外的日志选项。

日志的目的是增加*可发现性*，或者说增加您理解应用程序当前状态的能力。

日志是您的代码为了理解应用程序执行过程中发生的事情而生成的短消息，有时会以人类可读的形式嵌入一些数据。每个日志必须以特定的*级别*捕获，这可以从`emergency`（发生了非常严重的事情）到`debug`（几乎没有意义的事情发生）不等。

没有任何修改，您的应用程序将会将任何日志语句写入到位于*storage/logs/laravel.log*的文件中，并且每个日志语句看起来都会有点像这样：

```php
[2018-09-22 21:34:38] local.ERROR: Something went wrong.
```

您可以看到我们在一行上有日期、时间、环境、错误级别和消息。但是，默认情况下，Laravel 还会记录任何未捕获的异常，这种情况下您将在一行中看到整个堆栈跟踪。

在接下来的部分中，我们将介绍如何记录、为何记录以及如何在其他地方（例如 Slack）记录。

## 何时以及为何使用日志

日志最常见的用例是作为一种准一次性记录的记录，记录了您后来*可能*关心的事情，但您明确不需要程序化访问的事物。日志更多地用于了解应用程序中正在发生的情况，而不是创建您的应用程序可以消费的结构化数据。

例如，如果您希望编写代码以记录每次用户登录并对其进行有趣的处理，那么这是一个*logins*数据库表的使用案例。但是，如果您对这些登录有一种随意的兴趣，但又不确定您是否在编程上关心或需要这些信息，您可以只是在其上放置一个`debug`或`info`级别的日志并忘记它。

当您需要在发生问题时或在某个特定时间点查看某些东西的值，或者其他情况下时，日志也很常见。在代码中放置一个日志语句，从日志中获取您需要的数据，然后要么将其保留在代码中以备后用，要么再次删除它。

## 写入日志

在 Laravel 中编写日志条目的最简单方法是使用`Log`门面，并使用该门面上与您希望记录的严重级别匹配的方法。这些级别与[RFC 5424](https://oreil.ly/6ODcf)中定义的相同：

```php
Log::emergency($message);
Log::alert($message);
Log::critical($message);
Log::error($message);
Log::warning($message);
Log::notice($message);
Log::info($message);
Log::debug($message);
```

可选的，您还可以传递第二个参数，这是一个连接数据的数组：

```php
Log::error('Failed to upload user image.', ['user' => $user]);
```

不同的日志目标可能会以不同的方式捕获此附加信息，但是在默认的本地日志中看起来像这样（尽管它将只是日志中的一行）：

```php
[2018-09-27 20:53:31] local.ERROR: Failed to upload user image. {
    "user":"[object] (App\\User: {
        \"id\":1,
        \"name\":\"Matt\",
        \"email\":\"matt@tighten.co\",
        \"email_verified_at\":null,
        \"api_token\":\"long-token-here\",
        \"created_at\":\"2018-09-22 21:39:55\",
        \"updated_at\":\"2018-09-22 21:40:08\"
    })"
}
```

## 日志通道

与 Laravel 的许多其他方面（文件存储、数据库、邮件等）一样，您可以配置日志以使用一个或多个预定义的日志类型，这些类型在配置文件中定义。使用每种类型涉及向特定的日志驱动程序传递各种配置详细信息。

这些日志类型被称为*频道*，并且您将有`stack`、`single`、`daily`、`slack`、`stderr`、`syslog`和`errorlog`等选项。每个频道连接到一个驱动程序；可用的驱动程序包括`stack`、`single`、`daily`、`slack`、`syslog`、`errorlog`、`monolog`和`custom`。

我们将在这里介绍最常见的频道：`single`、`daily`、`slack`和`stack`。要了解更多有关驱动程序和可用频道的完整列表，请查看[日志文档](https://oreil.ly/vrJvj)。

### 单一频道

`single`频道将每个日志条目写入单个文件，您将在`path`键中定义它。您可以在示例 14-13 中查看其默认配置：

##### 示例 14-13\. `single`频道的默认配置

```php
'single' => [
    'driver' => 'single',
    'path' => storage_path('logs/laravel.log'),
    'level' => env('LOG_LEVEL', 'debug'),
],
```

这意味着它只会记录`debug`级别或更高级别的事件，并将它们全部写入单个文件*storage/logs/laravel.log*。

### 日志频道

`daily`频道每天生成一个新文件。你可以在示例 14-14 中查看其默认配置。

##### 示例 14-14\. `daily`频道的默认配置

```php
'daily' => [
    'driver' => 'daily',
    'path' => storage_path('logs/laravel.log'),
    'level' => env('LOG_LEVEL', 'debug'),
    'days' => 14,
],
```

它类似于`single`，但现在我们可以设置在清理之前要保留多少天的日志，并且日期将附加到我们指定的文件名中。例如，前述配置将生成名为*storage/logs/laravel-<yyyy-mm-dd>.log*的文件。

### Slack 频道

`slack`频道使得将你的日志（或更可能的是特定的日志）发送到 Slack 变得简单。

它还说明您不仅限于 Laravel 默认提供的处理程序。我们将在下一节中介绍这一点，但这不是自定义 Slack 实现；这只是 Laravel 构建一个连接到 Monolog Slack 处理程序的日志驱动程序，如果您可以使用任何 Monolog 处理程序，那么您有*很多*选项可用。

默认配置显示在示例 14-15 中。

##### 示例 14-15\. `slack`频道的默认配置

```php
'slack' => [
    'driver' => 'slack',
    'url' => env('LOG_SLACK_WEBHOOK_URL'),
    'username' => 'Laravel Log',
    'emoji' => ':boom:',
    'level' => env('LOG_LEVEL', 'critical'),
],
```

### stack 频道

`stack`频道是应用程序默认启用的频道。其默认配置显示在示例 14-16 中。

##### 示例 14-16\. `stack`频道的默认配置

```php
'stack' => [
    'driver' => 'stack',
    'channels' => ['single'],
    'ignore_exceptions' => false,
],
```

`stack`频道允许你将所有日志发送到多个频道（列在`channels`数组中）。因此，虽然这是默认在你的 Laravel 应用中配置的频道，因为它的`channels`数组默认设置为`single`，实际上你的应用只是使用了`single`日志频道。

但是如果您希望所有`info`级别及以上的内容都进入日常文件，而`critical`及更高级别的日志消息进入 Slack，使用`stack`驱动程序非常简单，正如示例 14-17 所示。

##### 示例 14-17\. 自定义`stack`驱动器

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily', 'slack'],
    ],

    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'info',
        'days' => 14,
    ],

    'slack' => [
        'driver' => 'slack',
        'url' => env('LOG_SLACK_WEBHOOK_URL'),
        'username' => 'Laravel Log',
        'emoji' => ':boom:',
        'level' => 'critical',
    ],
]
```

### 写入特定日志频道

有时您可能希望在调用`Log`门面时精确控制哪些日志消息应放在哪里。您可以通过指定频道来实现这一点：

```php
Log::channel('slack')->info("This message will go to Slack.");
```

# 高级日志配置

如果您想要自定义如何将每个日志发送到每个频道，或实现自定义 Monolog 处理程序，请查看 [logging docs](https://oreil.ly/vrJvj) 以了解更多。

# 使用 Laravel Scout 进行全文搜索

Laravel Scout 是一个独立的包，您可以将其引入您的 Laravel 应用程序中，以为您的 Eloquent 模型添加全文搜索功能。Scout 可以轻松地索引和搜索您的 Eloquent 模型内容；它配备了用于 Algolia、Meilisearch 和数据库（MySQL/PostgreSQL）的驱动程序，但也有其他提供者的社区包。我假设您正在使用 Algolia。

## 安装 Scout

首先，在任何 Laravel 应用程序中引入包：

```php
composer require laravel/scout
```

接下来，您将需要设置您的 Scout 配置。运行此命令：

```php
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
```

并将您的 Algolia 凭证粘贴到 *config/scout.php* 中。

最后，安装 Algolia SDK：

```php
composer require algolia/algoliasearch-client-php
```

## 标记您的模型以进行索引

在您的模型中（我们将使用 `Review`，例如书评），导入 `Laravel\Scout\Searchable` 特性。

您可以使用 `toSearchableArray()` 方法定义哪些属性可搜索（默认镜像 `toArray()`），并使用 `searchableAs()` 方法定义模型索引的名称（默认为表名）。

Scout 订阅您标记模型上的创建/删除/更新事件。当您创建、更新或删除任何行时，Scout 将同步这些更改到 Algolia。它将根据您的配置使用队列，将这些更改同步或异步进行队列处理。

## 搜索您的索引

Scout 的语法很简单。例如，要查找任何包含 `Llew` 一词的 `Review`：

```php
Review::search('Llew')->get();
```

您也可以像使用常规的 Eloquent 调用那样修改您的查询：

```php
// Get all records from the Review that match the term "Llew",
// limited to 20 per page and reading the page query parameter,
// just like Eloquent pagination
Review::search('Llew')->paginate(20);

// Get all records from the Review that match the term "Llew"
// and have the account_id field set to 2
Review::search('Llew')->where('account_id', 2)->get();
```

这些搜索返回什么？一组从数据库中重新生成的 Eloquent 模型。ID 存储在 Algolia 中，Algolia 返回匹配的 ID 列表；然后 Scout 从数据库中提取这些记录，并将它们作为 Eloquent 对象返回。

您无法完全访问 SQL `WHERE` 命令的复杂性，但它提供了比较检查的基本框架，如此处代码示例中所示。

## 队列和 Scout

此时，您的应用程序将在修改任何数据库记录时向 Algolia 发送 HTTP 请求。这可能会迅速减慢您的应用程序，这就是为什么 Scout 使得将其所有操作推送到队列中变得容易。

在 *config/scout.php* 中，将 `queue` 设置为 `true`，使这些更新可以异步索引。现在，您的全文索引在“最终一致性”下运行；您的数据库记录将立即接收更新，并且搜索索引的更新将被排队并根据队列工作程序的速度快速更新。

## 执行无索引操作

如果您需要执行一组操作并避免触发响应的索引，请在您的模型上使用 `withoutSyncingToSearch()` 方法包装这些操作。

```php
Review::withoutSyncingToSearch(function () {
    // Make a bunch of reviews, e.g.
    Review::factory()->count(10)->create();
});
```

## 有条件地对模型进行索引

有时，您可能只想在满足某些条件时索引记录。您可以在模型类上使用`shouldBeSearchable()`方法来实现这一点：

```php
public function shouldBeSearchable()
{
    return $this->isApproved();
}
```

## 通过代码手动触发索引

如果您想手动触发对模型的索引，可以在应用程序中使用代码或通过命令行完成。

要从代码中手动触发索引，请在任何 Eloquent 查询的末尾添加`searchable()`，它将索引该查询中找到的所有记录：

```php
Review::all()->searchable();
```

你还可以选择将查询范围限制为只索引你想要的记录。但是，Scout 足够智能，可以插入新记录并更新旧记录，因此你可能选择重新索引模型数据库表的整个内容。

您还可以在关系方法上运行`searchable()`：

```php
$user->reviews()->searchable();
```

如果要取消索引任何符合相同查询链条件的记录，只需使用`unsearchable()`即可：

```php
Review::where('sucky', true)->unsearchable();
```

## 通过 CLI 手动触发索引

您还可以使用 Artisan 命令触发索引：

```php
php artisan scout:import "App\Review"
```

这将分块处理所有`Review`模型并对其进行索引。

# HTTP 客户端

Laravel 的 HTTP 客户端并不完全是存储机制，但它是检索机制，说实话，我不确定它在这本书中还适合什么其他位置。让我们开始吧！

HTTP 客户端使您的 Laravel 应用程序可以通过简单而清晰的界面调用`POST`、`GET`等方式与外部 Web 服务和 API 进行通信。

如果您曾经使用过 Guzzle，您会理解它可以做什么，您还可能理解为什么简单的界面值得一提：Guzzle 功能强大，但也非常复杂，多年来变得越来越复杂。

## 使用 HTTP Facade

大多数时候，如果您正在使用 HTTP 客户端，您将依赖于其外观，直接在外观上调用`get()`和`post()`等方法。查看示例 14-18 以获取示例。

##### 示例 14-18\. HTTP Facade 的基本用法示例

```php
use Illuminate\Support\Facades\Http;

$response = Http::get('http://my-api.com/posts');

$response = Http::post('http://my-api.com/posts/2/comments', [
    'title' => 'I loved this post!',
]);
```

从 HTTP Facade 调用返回的`$response`是`Illuminate\Http\Client\Response`的一个实例，它提供了一套方法来检查响应。您可以查看[文档](https://oreil.ly/N4XXS)获取完整列表，但也可以在示例 14-19 中看到一些常见方法。

##### 示例 14-19\. HTTP `Client Response` 对象上常用的方法

```php
$response = Http::get('http://my-api.com/posts');

$response->body(); // string
$response->json(); // array
$response->json('key', 'default') // string
$response->successful(); // bool
```

正如你从示例 14-18 中看到的，你可以在`POST`请求中发送数据，但还有许多其他方法可以在请求中发送数据。

再次，这里是一些常见示例，您可以在文档中看到更多：

```php
$response = Http::withHeaders([
    'X-Custom-Header' => 'header value here'
])->post(/* ... */);

$response = Http::withToken($authToken)->post(/* ... */);

$response = Http::accept('application/json')->get('http://my-api.com/users');
```

## 处理错误和超时以及检查状态

默认情况下，HTTP 客户端在请求失败时将等待 30 秒，并且不会重试。但您可以自定义客户端响应意外情况的许多方面。

要定义超时时间，请链接`timeout()`并传递应等待的秒数：

```php
$response = Http::timeout(120)->get(/* ... */);
```

如果您期望尝试失败，您可以定义客户端应该重试每个请求的次数，使用`retry()`链式方法：

```php
$response = Http::retry($retries, $millisecondsBetweenRetries)->post(/* ... */);
```

响应对象上的其他一些方法允许我们检查请求是否成功以及我们收到了什么 HTTP 状态码；以下是其中一些：

```php
$response->successful(); // 200 or 300
$response->failed(); // 400 or 500 errors
$response->clientError(); // 400 errors
$response->serverError(); // 500 errors

// A few of the specific checks we can run for given status codes
$response->ok(); // 200 OK
$response->movedPermanently(); // 301 Moved Permanently
$response->unauthorized(); // 401 Unauthorized
$response->serverError(); // 500 Internal Server Error
```

您还可以定义一个回调函数，在发生错误时运行：

```php
$response->onError(function (Response $response) {
    // handle error
});
```

# 测试

测试大多数这些功能就像在您的测试中使用它们一样简单；无需模拟或存根。默认配置已经可以工作了—​例如，查看*phpunit.xml*，查看您的会话驱动程序和缓存驱动程序已设置为适合测试的值。

但是，在您尝试测试它们之前，有一些方便的方法和一些需要注意的地方。

## 文件存储

测试文件上传可能有点麻烦，但是按照这些步骤进行操作，一切将变得清晰。

### 上传虚假文件

首先，让我们看看如何手动创建一个`Illuminate\Http\UploadedFile`对象，以便在我们的应用程序测试中使用（示例 14-20）。

##### 示例 14-20\. 创建用于测试的假`UploadedFile`对象

```php
public function test_file_should_be_stored()
{
    Storage::fake('public');

    $file = UploadedFile::fake()->image('avatar.jpg');

    $response = $this->postJson('/avatar', [
        'avatar' => $file,
    ]);

    // Assert the file was stored
    Storage::disk('public')->assertExists("avatars/{$file->hashName()}");

    // Assert a file does not exist
    Storage::disk('public')->assertMissing('missing.jpg');
}
```

我们已经创建了一个新的`UploadedFile`实例，引用我们的测试文件，现在我们可以使用它来测试我们的路由。

### 返回虚假文件

如果您的路由期望真实文件存在，有时使其可测试的最佳方法是使该真实文件实际存在。假设每个用户都必须有个人资料图片。

首先，让我们为用户设置模型工厂，使用 Faker 复制图片，如在示例 14-21 中所见。

##### 示例 14-21\. 使用 Faker 返回虚假文件

```php
public function definition ()
{
    return [
        'picture' => fake()->file(
            base_path('tests/stubs/images'), // Source directory
            storage_path('app'), // Target directory
            false, // Return just filename, not full path
        ),
        'name' => fake()->name(),
    ];
};
```

Faker 的`file()`方法从源目录中选择一个随机文件，将其复制到目标目录，然后返回文件名。因此，我们刚刚从*tests/stubs/images*目录中选择了一个随机文件，将其复制到*storage/app*目录，并将其文件名设置为我们的`User`上的`picture`属性。此时，我们可以在期望`User`具有图片的路由测试中使用`User`，如示例 14-22 中所示。

##### 示例 14-22\. 断言图像的 URL 已回显

```php
public function test_user_profile_picture_echoes_correctly()
{
    $user = User::factory()->create();

    $response = $this->get(route('users.show', $user->id));

    $response->assertSee($user->picture);
}
```

当然，在许多情况下，您可以只在那里生成一个随机字符串，甚至不复制文件。但是，如果您的路由检查文件是否存在或对文件运行任何操作，则这是您的最佳选择。

## 会话

如果您需要断言会话中已设置了某些内容，可以在每个测试中使用 Laravel 提供的一些方便方法。所有这些方法都在`Illuminate\Testing\TestResponse`对象的测试中可用：

`assertSessionHas(*$key*, *$value = null*)`

断言会话对特定键有值，并且如果传递了第二个参数，则该键具有特定值：

```php
public function test_some_thing()
{
    // Do stuff that ends up with a $response object...
    $response->assertSessionHas('key', 'value');
}
```

`assertSessionHasAll(*array $bindings*)`

如果传递了一个键/值对的数组，断言所有键都等于所有值。如果一个或多个数组条目只是一个值（具有 PHP 的默认数值键），则仅检查该值是否存在于会话中：

```php
$check = [
    'has',
    'hasWithThisValue' => 'thisValue',
];

$response->assertSessionHasAll($check);
```

`assertSessionMissing(*$key*)`

断言会话对于特定键*没有*值。

`assertSessionHasErrors(*$bindings = []*, *$format = null*)`

断言会话具有一个`errors`值。这是 Laravel 用于从验证失败中返回错误的关键。

如果数组只包含键，它将检查这些键是否设置了错误：

```php
$response = $this->post('test-route', ['failing' => 'data']);
$response->assertSessionHasErrors(['name', 'email']);
```

你还可以传递这些键的值，并且可选地传递一个*`$format`*，以验证这些错误消息是否按预期返回：

```php
$response = $this->post('test-route', ['failing' => 'data']);
$response->assertSessionHasErrors([
    'email' => '<strong>The email field is required.</strong>',
], '<strong>:message</strong>');
```

## 缓存

对于使用缓存的功能进行测试并没有什么特别的地方 —— 只需要去做：

```php
Cache::put('key', 'value', 900);

$this->assertEquals('value', Cache::get('key'));
```

Laravel 默认在您的测试环境中使用`array`缓存驱动程序，它只是将您的缓存值存储在内存中。

## Cookies

如果您需要在应用程序测试中测试路由之前设置 cookie 怎么办？您可以使用`withCookies()`方法在请求中设置 cookies。要了解更多，请查看第十二章。

# 在测试期间排除您的 cookie 加密

如果你的测试中的 cookies 不起作用，除非你将它们排除在 Laravel 的 cookie 加密中间件之外。您可以通过教`EncryptCookies`中间件暂时禁用这些 cookies 来实现这一点：

```php
use Illuminate\Cookie\Middleware\EncryptCookies;
...

$this->app->resolving(
    EncryptCookies::class,
    function ($object) {
        $object->disableFor('cookie-name');
    }
);

// ...run test
```

这意味着您可以设置一个 cookie，并使用类似示例 14-23 来检查它。

##### 示例 14-23\. 对 cookies 运行单元测试

```php
public function test_cookie()
{
    $this->app->resolving(EncryptCookies::class, function ($object) {
        $object->disableFor('my-cookie');
    });

    $response = $this->call(
        'get',
        'route-echoing-my-cookie-value',
        [],
        ['my-cookie' => 'baz']
    );
    $response->assertSee('baz');
}
```

如果您想测试响应是否设置了 cookie，可以使用`assertCookie()`来检查该 cookie：

```php
$response = $this->get('cookie-setting-route');
$response->assertCookie('cookie-name');
```

或者您可以使用`assertPlainCookie()`来测试 cookie 并断言它未加密。

## 日志

测试某个特定日志是否已写入的最简单方法是针对`Log`外观进行断言（详细了解请参阅“模拟其他外观”）。示例 14-24 展示了这个工作原理。

##### 示例 14-24\. 对`Log`外观进行断言

```php
// Test file
public function test_new_accounts_generate_log_entries()
{
    Log::shouldReceive('info')
        ->once()
        ->with('New account created!');

    // Create a new account
    $this->post(route('accounts.store'), ['email' => 'matt@mattstauffer.com']);
}

// AccountController
public function store()
{
    // Create account

    Log::info('New account created!');
}
```

也有一个名为[Log Fake](https://oreil.ly/TCBMm)的包，扩展了此处展示的外观测试可以做的事情，并允许您针对日志编写更多定制的断言。

## Scout

如果您需要测试使用 Scout 数据的代码，您可能不希望您的测试触发索引操作或从 Scout 读取数据。只需向您的 *phpunit.xml* 添加一个环境变量来禁用 Scout 与 Algolia 的连接：

```php
<env name="SCOUT_DRIVER" value="null"/>
```

## HTTP 客户端

使用 Laravel 的 HTTP 客户端的一个不可思议的好处是，它使您能够在测试中以最小的配置来伪造响应。

最简单的选项是运行`Http::fake()`，它将为您每次调用返回一个空的成功响应。

不过，您还可以自定义您希望从 HTTP 客户端调用返回的具体响应，就像您在示例 14-25 中看到的那样。

##### 示例 14-25\. 通过 URL 自定义对 HTTP 客户端的响应

```php
Http::fake([
    // Return a JSON response for a particular API
    'my-api.com/*' => Http::response(['key' => 'value'], 200, $headersArray),

    // Return a string response for all other endpoints
    '*' => Http::response('This is a fake API response', 200, $headersArray),
]);
```

如果需要定义针对特定端点（或符合特定端点模式）的请求遵循特定顺序，可以如 示例 14-26 所示进行定义。

##### 示例 14-26\. 定义针对特定端点的响应序列

```php
Http::fake([
    // Return a sequence of responses for consecutive calls to this API
    'my-api.com/*' => Http::sequence()
        ->push('Initial string response', 200)
        ->push(['secondary' => 'response'], 200)
        ->pushStatus(404),
]);
```

您还可以对应用程序发送到特定端点的数据进行断言，如 示例 14-27 所示。

##### 示例 14-27\. 对应用程序发出的调用进行断言

```php
Http::fake();

Http::assertSent(function (Request $request) {
    return $request->hasHeader('X-Custom-Header', 'certain-value') &&
        $request->url() == 'http://my-api.com/users/2/comments' &&
        $request['name'] == 'New User';
});
```

# TL;DR

Laravel 提供了简单的接口来执行许多常见的存储操作：文件系统访问、会话、Cookie、缓存和搜索。无论您使用哪个提供者，每个 API 都是相同的，这是 Laravel 通过允许多个“驱动程序”提供相同公共接口实现的。这使得根据环境或应用程序需求的变化简单切换提供者成为可能。
