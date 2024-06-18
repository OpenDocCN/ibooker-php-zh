# 第七章：收集和处理用户数据

像 Laravel 这样的框架受益于的网站通常不仅提供静态内容。许多处理复杂和混合数据源，其中最常见（也最复杂）的是各种形式的用户输入：URL 路径、查询参数、`POST` 数据和文件上传。

Laravel 提供了一组工具，用于收集、验证、规范化和过滤用户提供的数据。我们将在这里看看这些工具。

# 注入请求对象

在 Laravel 中访问用户数据最常见的工具是注入 `Illuminate\Http\Request` 对象的实例。它为您提供了轻松访问用户在您的站点上提供输入的所有方式：`POST` 表单数据或 JSON、`GET` 请求（查询参数）和 URL 段。

# 其他访问请求数据的选项

还有一个 `request()` 全局助手和一个 `Request` 门面，两者都公开相同的方法。每个选项都公开了整个 Illuminate `Request` 对象，但现在我们只会涵盖与用户数据特别相关的方法。

因为我们计划注入一个 `Request` 对象，让我们快速看一下如何获取我们将在其上调用所有这些方法的 `$request` 对象：

```php
Route::post('form', function (Illuminate\Http\Request $request) {
    // $request->etc()
});
```

## `$request->all()`

就像名字所暗示的那样，`$request->all()` 提供了一个包含用户从每个来源提供的所有输入的数组。假设出于某种原因，您决定让一个表单 `POST` 到一个带有查询参数的 URL——例如，向 *http://myapp.com/signup?utm=12345* 发送一个 `POST`。查看 Example 7-1 来看看从 `$request->all()` 中得到了什么。(`$request->all()` 也包含有关上传的任何文件的信息，但我们将在本章后面介绍这部分内容。)

##### Example 7-1\. `$request->all()`

```php
<!-- GET route form view at /get-route -->
<form method="post" action="/signup?utm=12345">
    @csrf
    <input type="text" name="first_name">
    <input type="submit">
</form>
```

```php
// routes/web.php
Route::post('signup', function (Request $request) {
    var_dump($request->all());
});

// Outputs:
/**
 * [
 *     '_token' => 'CSRF token here',
 *     'first_name' => 'value',
 *     'utm' => 12345,
 * ]
 */
```

## $request->except() 和 ->only()

`$request->except()` 提供与 `$request->all()` 相同的输出，但您可以选择排除一个或多个字段——例如 `_token`。您可以将其传递为字符串或字符串数组。

Example 7-2 显示了在我们使用 `$request->except()` 在 Example 7-1 中相同表单时的情况。

##### Example 7-2\. `$request->except()`

```php
Route::post('post-route', function (Request $request) {
    var_dump($request->except('_token'));
});

// Outputs:
/**
 * [
 *     'firstName' => 'value',
 *     'utm' => 12345
 * ]
 */
```

`$request->only()` 是 `$request->except()` 的反义词，如您在 Example 7-3 中所见。

##### Example 7-3\. `$request->only()`

```php
Route::post('post-route', function (Request $request) {
    var_dump($request->only(['firstName', 'utm']));
});

// Outputs:
/**
 * [
 *     'firstName' => 'value',
 *     'utm' => 12345
 * ]
 */
```

## $request->has() 和 ->missing()

使用 `$request->has()`，您可以检测特定的用户输入是否可用，而不管输入中是否实际包含值。查看 Example 7-4 来查看前面示例中我们的 `utm` 查询字符串参数的分析示例。

##### Example 7-4\. `$request->has()`

```php
// POST route at /post-route
if ($request->has('utm')) {
    // Do some analytics work
}
```

`$request->missing()` 是它的反义词。

## $request->whenHas()

使用 `$request->whenHas()`，您可以定义当请求提供了字段或未提供字段时的行为。第一个闭包参数在字段存在时返回，第二个在字段不存在时返回。

查看带有`utm`查询字符串参数的示例 7-5。

##### 示例 7-5\. `$request->whenHas()`

```php
// POST route at /post-route
$utm = $request->whenHas('utm', function($utm) {
    return $utm;
}, function() {
    return 'default';
});
```

## `$request->filled()`

使用`$request->filled()`方法，可以检查请求中是否存在并填充了特定字段。`filled()`与`has()`相同，但它还要求字段中实际存在值。在示例 7-6 中，您可以看到如何使用此方法的示例。

##### 示例 7-6\. `$request->filled()`

```php
// POST route at /post-route
if ($request->filled('utm')) {
    // Do some analytics work
}
```

## `$request->whenFilled()`

与`whenHas()`方法类似，`$request->whenFilled()`方法允许您在字段填充或未填充时定义值。第一个闭包参数在字段填充时运行，第二个在未填充时运行。请参阅示例 7-7 了解如何使用此方法的示例。

##### 示例 7-7\. `$request->whenFilled()`

```php
// POST route at /post-route
$utm = $request->whenFilled('utm', function ($utm) {
    return $utm;
}, function() {
    return 'default';
});
```

## `$request->mergeIfMissing()`

使用`mergeIfMissing()`方法，您可以在请求中添加字段，当字段不存在时定义其值。例如，当字段来自复选框时，只有在选中时才存在。您可以在示例 7-8 中看到一个实现。

##### 示例 7-8\. `$request->mergeIfMissing()`

```php
// POST route at /post-route
$shouldSend = $request->mergeIfMissing('send_newsletter', 0);
```

## `$request->input()`

而`$request->all()`、`$request->except()`和`$request->only()`操作于用户提供的完整输入数组上，`$request->input()`允许您仅获取单个字段的值。示例 7-9 提供了一个例子。注意第二个参数是默认值，所以如果用户没有传递值，您可以有一个合理（且不会中断流程）的回退。

##### 示例 7-9\. `$request->input()`

```php
Route::post('post-route', function (Request $request) {
    $userName = $request->input('name', 'Matt');
});
```

## `$request->method()` 和 `->isMethod()`

`$request->method()`返回请求的 HTTP 动词，`$request->isMethod()`检查它是否与指定的动词匹配。示例 7-10 说明了它们的用法。

##### 示例 7-10\. `$request->method()` 和 `$request->isMethod()`

```php
$method = $request->method();

if ($request->isMethod('patch')) {
    // Do something if request method is PATCH
}
```

## `$request->integer()`、`->float()`、`->string()`和`->enum()`

当您分别使用这些方法时，它们将直接将输入转换为整数、浮点数、字符串或枚举。查看示例 7-11 获取使用示例。

##### 示例 7-11\. `$request->integer()`、`$request->float()`、`$request->string()`和`$request->enum()`

```php
dump(is_int($request->integer('some_integer'));
// true

dump(is_float($request->float('some_float'));
// true

dump(is_string($request->string('some_string'));
// true

dump($request->enum('subscription', SubscriptionStatusEnum::class));
// 'active', assuming that's a valid status for the SubscriptionStatusEnum
```

## `$request->dump()` 和 `->dd()`

`$request->dump()` 和 `$request->dd()` 是用于展示请求的辅助方法。对于两者，您可以通过不传递任何参数来展示整个请求，或者通过传递数组来展示选择的字段。`$request->dump()`展示后继续执行，而`$request->dd()`展示后停止脚本的执行。示例 7-12 展示了它们的用法。

##### 示例 7-12\. `$request->dump()` 和 `$request->dd()`

```php
// dumping the whole request
$request->dump()
$request->dd();

// dumping just two fields
$request->dump(['name', 'utm']);
$request->dd(['name', 'utm']);
```

## 数组输入

Laravel 还提供了方便的帮助程序，用于访问来自用户提供的数组输入的数据。只需使用“点”符号来指示进入数组结构，例如在示例 7-13 中。

##### 示例 7-13\. 用于访问用户数据中数组值的点符号表示法

```php
<!-- GET route form view at /employees/create -->
<form method="post" action="/employees/">
    @csrf
    <input type="text" name="employees[0][firstName]">
    <input type="text" name="employees[0][lastName]">
    <input type="text" name="employees[1][firstName]">
    <input type="text" name="employees[1][lastName]">
    <input type="submit">
</form>
```

```php
// POST route at /employees
Route::post('employees', function (Request $request) {
    $employeeZeroFirstName = $request->input('employees.0.firstName');
    $allLastNames = $request->input('employees.*.lastName');
    $employeeOne = $request->input('employees.1');
    var_dump($employeeZeroFirstname, $allLastNames, $employeeOne);
});

// If forms filled out as "Jim" "Smith" "Bob" "Jones":
// $employeeZeroFirstName = 'Jim';
// $allLastNames = ['Smith', 'Jones'];
// $employeeOne = ['firstName' => 'Bob', 'lastName' => 'Jones'];
```

## JSON 输入（和 `$request->json()`）

到目前为止，我们已经涵盖了从查询字符串（`GET`）和表单提交（`POST`）获取输入的内容。但是随着 JavaScript SPA 的出现，还有一种更常见的用户输入形式：JSON 请求。它本质上只是一个 `POST` 请求，但其主体设置为 JSON，而不是传统的表单 `POST`。

让我们看一下向 Laravel 路由提交 JSON 的情况以及如何使用 `$request->input()` 提取数据（参见 示例 7-14）。

##### 示例 7-14\. 使用 `$request->input()` 从 JSON 中获取数据

```php
POST /post-route HTTP/1.1
Content-Type: application/json

{
    "firstName": "Joe",
    "lastName": "Schmoe",
    "spouse": {
        "firstName": "Jill",
        "lastName":"Schmoe"
    }
}
```

```php
// Post-route
Route::post('post-route', function (Request $request) {
    $firstName = $request->input('firstName');
    $spouseFirstname = $request->input('spouse.firstName');
});
```

由于 `$request->input()` 足够智能，可以从 `GET`、`POST` 或 JSON 中提取用户数据，你可能会想知道为什么 Laravel 还提供 `$request->json()`。你可能更喜欢 `$request->json()` 的两个原因。首先，你可能希望对项目中其他程序员更明确地表明你期望数据来自何处。其次，如果 `POST` 没有正确的 `application/json` 头部，`$request->input()` 将无法将其识别为 JSON，但 `$request->json()` 可以。

# 路由数据

当你想象“用户数据”时，URL 可能不是你首先想到的，但是在本章中，URL 与其他任何内容一样都是用户数据。

从 URL 获取数据的两种主要方式是通过 `Request` 对象和路由参数。

## 来自请求

注入的 `Request` 对象（以及 `Request` 外观和 `request()` 助手）有几种方法可用于表示当前页面 URL 的状态，但现在让我们专注于获取关于 URL 片段的信息。

URL 中域名后的每组字符称为 *片段*。因此，[*http://www.myapp.com/users/15*](http://www.myapp.com/users/15) 具有两个片段：*users* 和 *15*。

正如你可能猜到的，我们有两种可用的方法：`$request->segments()` 返回所有片段的数组，并且 `$request->segment($segmentId)` 允许我们获取单个片段的值。请注意，片段是基于 1 的索引返回的，因此在上面的示例中，`$request->segment(1)` 将返回 *users*。

`Request` 对象、`Request` 外观和 `request()` 全局助手提供了更多方法来帮助我们从 URL 中获取数据。要了解更多，请参阅 第十章。

## 来自路由参数

获取关于 URL 的另一种主要方法是从路由参数中获取，这些参数被注入到正在服务当前路由的控制器方法或闭包中，如 示例 7-15 所示。

##### 示例 7-15\. 从路由参数获取 URL 详细信息

```php
// routes/web.php
Route::get('users/{id}', function ($id) {
    // If the user visits myapp.com/users/15/, $id will equal 15
});
```

要了解更多关于路由和路由绑定的信息，请参阅 第三章。

# 上传的文件

我们已经讨论了与用户文本输入的不同交互方式，但还有要考虑的文件上传问题。`Request`对象使用`$request->file()`方法提供对任何上传文件的访问，该方法以文件的输入名称作为参数，并返回`Symfony\Component\HttpFoundation\File\UploadedFile`的实例。让我们通过一个示例来说明。首先，我们的表单在示例 7-16 中。

##### 示例 7-16\. 用于上传文件的表单

```php
<form method="post" enctype="multipart/form-data">
    @csrf
    <input type="text" name="name">
    <input type="file" name="profile_picture">
    <input type="submit">
</form>
```

现在让我们看看运行`$request->all()`后我们得到什么，如示例 7-17 所示。请注意，`$request->input('profile_picture')`将返回`null`；我们需要使用`$request->file('profile_picture')`。

##### 示例 7-17\. 提交表单后的输出在示例 7-16 中

```php
Route::post('form', function (Request $request) {
    var_dump($request->all());
});

// Output:
// [
//     "_token" => "token here",
//     "name" => "asdf",
//     "profile_picture" => UploadedFile {},
// ]

Route::post('form', function (Request $request) {
    if ($request->hasFile('profile_picture')) {
        var_dump($request->file('profile_picture'));
    }
});

// Output:
// UploadedFile (details)
```

Laravel 还提供了文件特定的验证规则，允许您要求文件上传匹配特定的 mime 类型、文件大小或长度等。查看[验证文档](https://oreil.ly/bamub)以了解更多信息。

Symfony 的`UploadedFile`类通过允许您轻松检查和操作文件的方法扩展了 PHP 的本机`SplFileInfo`。这个列表并不详尽，但它让您体验到了您可以做什么的一部分：

+   `guessExtension()`

+   `getMimeType()`

+   `store(*$path*, *$storageDisk = default disk*)`

+   `storeAs(*$path*, *$newName*, *$storageDisk = default disk*)`

+   `storePublicly(*$path*, *$storageDisk = default disk*)`

+   `storePubliclyAs(*$path*, *$newName*, *$storageDisk = default disk*)`

+   `move(*$directory*, *$newName = null*)`

+   `getClientOriginalName()`

+   `getClientOriginalExtension()`

+   `getClientMimeType()`

+   `guessClientExtension()`

+   `getClientSize()`

+   `getError()`

+   `isValid()`

如您所见，大多数方法与获取上传文件的信息有关，但有一个您可能比其他所有方法都更常用：`store()`，它接受通过请求上传的文件，并将其存储在服务器上指定的目录中。它的第一个参数是目标目录，可选的第二个参数是要用于存储文件的存储磁盘（`s3`，`local`等）。您可以在示例 7-18 中看到一个常见的工作流程。

##### 示例 7-18\. 常见文件上传工作流程

```php
if ($request->hasFile('profile_picture')) {
    $path = $request->profile_picture->store('profiles', 's3');
    auth()->user()->profile_picture = $path;
    auth()->user()->save();
}
```

如果需要指定文件名，您可以使用`storeAs()`代替`store()`。第一个参数仍然是路径；第二个是文件名，可选的第三个参数是要使用的存储磁盘。

# 文件上传的正确表单编码

如果在尝试从请求中获取文件的内容时得到`null`，可能是您忘记在表单上设置编码类型。确保在表单上添加属性 `enctype="multipart/form-data"`：

```php
<form method="post" enctype="multipart/form-data">
```

# 验证

Laravel 有很多方法可以验证传入的数据。我们将在下一节中讨论表单请求，因此我们现在有两个主要选项：手动验证或在`Request`对象上使用`validate()`方法。让我们从更简单、更常见的`validate()`方法开始。

## 在请求对象上进行验证()

`Request` 对象有一个 `validate()` 方法，提供了最常见的验证工作流的便捷方式。 请参阅示例 7-19。

##### 示例 7-19. 请求验证的基本用法

```php
// routes/web.php
Route::get('recipes/create', [RecipeController::class, 'create']);
Route::post('recipes', [RecipeController::class, 'store']);
```

```php
// app/Http/Controllers/RecipeController.php
class RecipeController extends Controller
{
    public function create()
    {
        return view('recipes.create');
    }

    public function store(Request $request)
    {
        $request->validate([
            'title' => 'required|unique:recipes|max:125',
            'body' => 'required'
        ]);

        // Recipe is valid; proceed to save it
    }
}
```

我们在这里只有四行代码运行我们的验证，但它们确实做了很多事情。

首先，我们明确定义我们期望的字段，并分别应用规则（这里用管道字符`|`分隔）。

接下来，`validate()`方法检查来自`$request`的传入数据，并确定其是否有效。

如果数据有效，则`validate()`方法结束，我们可以继续使用控制器方法保存数据或其他操作。

但是，如果数据无效，则会抛出`ValidationException`。 这包含有关如何处理此异常的路由器指令。 如果请求来自 JavaScript（或者请求 JSON 作为响应），则异常将创建一个包含验证错误的 JSON 响应。 如果不是，则异常将返回一个重定向到前一页的页面，以及所有用户输入和验证错误，非常适合重新填充失败的表单并显示一些错误。

## 更多关于 Laravel 验证规则的信息

在我们这里的示例中（就像文档中一样），我们使用“管道”语法：`'*fieldname*': '*rule*|*otherRule*|*anotherRule*'`。 但是，您也可以使用数组语法来执行相同的操作：`'*fieldname*': ['*rule*', '*otherRule*', '*anotherRule*']`。

此外，您还可以验证嵌套属性。 如果您使用 HTML 的数组语法，这就很重要，该语法允许您例如，在 HTML 表单上具有多个“用户”，每个用户都有一个关联的名称。 以下是验证方法：

```php
$request->validate([
    'user.name' => 'required',
    'user.email' => 'required|email',
]);
```

我们没有足够的空间来涵盖这里的每一个可能的验证规则，但以下是一些最常见规则及其功能：

要求字段

`required`; `required_if:*anotherField,equalToThisValue*`;

`required_unless:*anotherField,equalToThisValue*`

排除请求输出中的字段

`exclude_if:*anotherField,equalToThisValue*`;

`exclude_unless:*anotherField,equalToThisValue*`

字段必须包含某些类型的字符

`alpha`; `alpha_dash`; `alpha_num`; `numeric`; `integer`

字段必须包含特定的模式

`email`; `active_url`; `ip`

日期

`after:*date*`; `before:*date*`（`*date*`可以是`strtotime()`可以处理的任何有效字符串）

数字

`between:*min*`,`*max*`; `min:*num*`; `max:*num*`; `size:*num*`（`size`测试字符串长度、整数值、数组计数或文件大小（KB））

图像尺寸

`dimensions:min_width=*XXX*`; 也可以与`max_width`、`min_height`、`max_height`、`width`、`height`和`ratio`结合使用

数据库

`exists:*tableName*`; `unique:*tableName*`（期望在与字段名相同的表列中查找；请查看[验证文档](https://oreil.ly/JmbQC)以了解如何自定义）

你可以在数据库验证规则中指定 Eloquent 模型，而不是表名：

```php
'name' => 'exists:App\Models\Contact,name',
'phone' => 'unique:App\Models\Contact,phone',
```

## 手动验证

如果您不在控制器中工作，或者由于某些其他原因，先前描述的流程不适合，您可以使用`Validator`门面手动创建`Validator`实例，并像示例 7-20 中那样检查成功或失败。

##### 示例 7-20\. 手动验证

```php
Route::get('recipes/create', function () {
    return view('recipes.create');
});

Route::post('recipes', function (Illuminate\Http\Request $request) {
    $validator = Validator::make($request->all(), [
        'title' => 'required|unique:recipes|max:125',
        'body' => 'required'
    ]);

    if ($validator->fails()) {
        return redirect('recipes/create')
            ->withErrors($validator)
            ->withInput();
    }

    // Recipe is valid; proceed to save it
});
```

如您所见，我们通过将输入作为第一个参数和验证规则作为第二个参数传递来创建验证器的实例。验证器公开了一个`fails()`方法，我们可以进行检查，并可以将其传递给重定向的`withErrors()`方法。

## 使用验证数据

在验证数据后，您可以从请求中提取数据，确保只使用验证过的数据。有两个主要选项：`validated()`和`safe()`。您可以在`$request`对象上运行这些方法，或者如果您创建了手动验证器，则在`$validator`实例上运行。

`validated()`方法返回已验证数据的数组，如示例 7-21 所示。

##### 示例 7-21\. 使用`validated()`获取验证数据

```php
// Both return an array of validated user input
$validated = $request->validated();
$validated = $validator->validated();
```

另一方面，`safe()`方法返回一个对象，该对象使您可以访问`all()`、`only()`和`except()`方法，正如您在示例 7-22 中所看到的。

##### 示例 7-22\. 使用`safe()`获取验证数据

```php
$validated = $request->safe()->only(['name', 'email']);

$validated = $request->safe()->except(['password']);

$validated = $request->safe()->all();
```

## 自定义规则对象

如果 Laravel 中不存在您需要的验证规则，您可以创建自己的规则。要创建自定义规则，请运行`php artisan make:rule *RuleName*`，然后编辑位于*app/Rules/{RuleName}.php*中的文件。

您将获得默认提供的`validate()`方法。`validate()`方法应接受属性名称作为第一个参数，用户提供的值作为第二个参数，并在验证失败时接受一个闭包；您可以在消息中使用`:attribute`作为属性名称的占位符。

请查看示例 7-23 作为示例。

##### 示例 7-23\. 示例自定义规则

```php
class AllowedEmailDomain implements ValidationRule
{
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        if(! in_array(Str::after($value, '@'), ['tighten.co'])){
            $fail('The :attribute field is not from an allowed email provider.');
        }
    }
}
```

要使用此规则，只需将规则对象的实例传递给您的验证器：

```php
$request->validate([
    'email' => new AllowedEmailDomain,
]);
```

## 显示验证错误消息

我们已经在第六章中详细介绍了这一点，但这里是如何从验证中显示错误的快速复习。

请求上的`validate()`方法（以及它依赖的重定向的`withErrors()`方法）将任何错误闪存到会话中。这些错误可以在您重定向到的视图中通过`$errors`变量访问。请记住，作为 Laravel 魔术的一部分，即使是空的，每次加载视图时`$errors`变量也会可用，因此您不必使用`isset()`来检查其是否存在。

这意味着您可以在每个页面上执行类似示例 7-24 的操作。

##### 示例 7-24\. 回显验证错误

```php
@if ($errors->any())
    <ul id="errors">
        @foreach ($errors->all() as $error)
            <li>{{ $error }}</li>
        @endforeach
    </ul>
@endif
```

您还可以有条件地回显单个字段的错误消息。为此，您将使用`@error` Blade 指令来检查给定字段是否存在错误。

```php
@error('first_name')
    <span>{{ $message }}</span>
@enderror
```

# 表单请求

在构建应用程序时，您可能会注意到控制器方法中出现了一些模式。有些模式是重复的——例如，输入验证、用户身份验证和授权以及可能的重定向。如果您希望有一种结构来规范化和提取这些常见行为，以便从控制器方法中提取出来，您可能会对 Laravel 的表单请求感兴趣。

表单请求是一个自定义的请求类，旨在映射到表单的提交，请求负责验证请求、授权用户，并在验证失败时可选择重定向用户。每个表单请求通常但并非总是显式映射到单个 HTTP 请求，例如，“创建评论”。

## 创建表单请求

您可以通过命令行创建一个新的表单请求：

```php
php artisan make:request CreateCommentRequest
```

现在你可以在 *app/Http/Requests/Create**Comment**Request.php* 中使用表单请求对象。

每个表单请求类都提供一个或两个公共方法。第一个是 `rules()`，它需要返回此请求的验证规则数组。第二个（可选）方法是 `authorize()`；如果返回 `true`，则用户被授权执行此请求，如果返回 `false`，则用户被拒绝。查看 示例 7-25 以查看一个表单请求的样本。

##### 示例 7-25\. 样本表单请求

```php
<?php

namespace App\Http\Requests;

use App\BlogPost;
use Illuminate\Foundation\Http\FormRequest;

class CreateCommentRequest extends FormRequest
{
    public function authorize(): bool
    {
        $blogPostId = $this->route('blogPost');

        return auth()->check() && BlogPost::where('id', $blogPostId)
            ->where('user_id', auth()->id())->exists();
    }

    public function rules(): array
    {
        return [
            'body' => 'required|max:1000',
        ];
    }
}
```

示例 7-25 的 `rules()` 部分非常简单明了，但让我们简要了解一下 `authorize()`。

我们正在从名为 `blogPost` 的路由中获取段落。这暗示了该路由的定义可能看起来像这样：``Route::post('blogPosts/*`blogPost`*', function () *`{ // Do stuff }`*)``。正如您所见，我们将路由参数命名为 `blogPost`，这使得它可以在我们的 `Request` 中通过 `$⁠t⁠h⁠i⁠s​-⁠>⁠r⁠o⁠u⁠t⁠e⁠('blogPost')` 访问。

然后我们检查用户是否已登录，如果是，则查看是否存在以该标识符标记的任何博客文章，这些文章是当前登录用户拥有的。您已经学会了一些更简单的方法来检查所有权，例如 第五章 中的方法，但我们在这里保持更加明确以保持清洁。我们将很快讨论这样做的影响，但重要的是要知道，返回 `true` 表示用户被授权执行指定的操作（在本例中是创建评论），而 `false` 表示用户未被授权。

## 使用表单请求

现在我们已经创建了一个表单请求对象，那么我们如何使用它呢？这是 Laravel 的一点魔法。任何路由（闭包或控制器方法），如果将表单请求作为其参数类型提示，将从该表单请求的定义中受益。

让我们试试，在 示例 7-26 中。

##### 示例 7-26\. 使用表单请求

```php
Route::post('comments', function (App\Http\Requests\CreateCommentRequest $request) {
    // Store comment
});
```

您可能想知道我们何时调用表单请求，但 Laravel 会为我们执行这一步骤。它验证用户输入并授权请求。如果输入无效，它会像 `Request` 对象的 `validate()` 方法一样操作，重定向用户到前一页并保留其输入，同时传递适当的错误消息。如果用户未经授权，则 Laravel 将返回“403 禁止访问”错误并不执行路由代码。

# Eloquent 模型大规模赋值

到目前为止，我们一直在控制器级别进行验证，这绝对是开始的最佳位置。但您也可以在模型级别过滤传入的数据。

将表单的整体输入直接传递给数据库模型是一种常见（但不推荐）的模式。在 Laravel 中，可能看起来像 Example 7-27。

##### 示例 7-27\. 将表单的整体内容传递给 Eloquent 模型

```php
Route::post('posts', function (Request $request) {
    $newPost = Post::create($request->all());
});
```

我们在这里假设最终用户是善良的，而非恶意的，并且仅保留了我们希望他们编辑的字段——也许是帖子的 `title` 或 `body`。

但是，如果我们的最终用户能够猜测或识别出我们在 `posts` 表中有一个 `author_id` 字段，该怎么办呢？如果他们使用浏览器工具添加了一个 `author_id` 字段，并将 ID 设置为别人的 ID，然后冒充其他人创建假的博客文章呢？

Eloquent 有一个称为“大规模赋值”的概念，允许您通过将数组传递给 `create()` 或 `update()` 方法，要么定义应填充的字段列表（使用模型的 `$fillable` 属性），要么定义不应填充的字段列表（使用模型的 `$guarded` 属性）。有关更多信息，请参阅“大规模赋值”。

在我们的示例中，我们可能想要像 Example 7-28 中那样填充模型，以确保我们的应用程序安全。

##### 示例 7-28\. 保护 Eloquent 模型免受恶意的大规模赋值攻击

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    // Disable mass assignment on the author_id field
    protected $guarded = ['author_id'];
}
```

通过将 `author_id` 设置为 `guarded`，我们确保恶意用户将无法通过将其手动添加到发送给我们应用程序的表单内容中，覆盖此字段的值。

# 双重保护使用 `$request->only()`

尽管我们需要保护我们的模型免受大规模赋值的攻击，但在赋值端也值得小心。与其使用 `$request->all()`，不如考虑使用 `$request->only()`，这样您可以指定要传递到模型中的字段：

```php
Route::post('posts', function (Request $request) {
    $newPost = Post::create($request->only([
        'title',
        'body',
    ]));
});
```

# {{ 与 {!!

每当在网页上显示由用户创建的内容时，您都需要防范恶意输入，例如脚本注入。

假设您允许用户在您的网站上撰写博客文章。您可能不希望他们能够注入恶意的 JavaScript 代码，从而在您的访客浏览器中运行，对吗？因此，您需要转义任何显示在页面上的用户输入，以避免这种情况发生。

幸运的是，这几乎完全为您覆盖了。如果您使用 Laravel 的 Blade 模板引擎，默认的“echo”语法（`{{ *$stuffToEcho* }}`）会自动通过 `htmlentities()`（PHP 最佳的安全输出用户内容的方式）运行输出。实际上，您需要*额外*的工作来避免转义输出，方法是使用 `{!! *$stuffToEcho* !!}` 语法。

# 测试

如果您对测试用户输入交互感兴趣，您可能最关心的是模拟有效和无效的用户输入，并确保如果输入无效，则用户被重定向，如果输入有效，则进入正确的位置（例如数据库）。

Laravel 的应用程序测试框架使得这变得简单。

# Laravel Dusk 用于测试用户交互

这些测试测试的是您应用程序的 HTTP 层，但并非实际的表单字段和交互。如果您想要测试页面上特定的用户交互及其与您的表单的交互，您将需要引入 Laravel 的 Dusk 测试包。

查看 “使用 Dusk 进行测试” 了解如何在您的测试中安装和使用 Dusk。

让我们从一个无效的路由开始，我们预期会被拒绝，就像 示例 7-29 中一样。

##### 示例 7-29\. 测试无效输入是否被拒绝

```php
public function test_input_missing_a_title_is_rejected()
{
    $response = $this->post('posts', ['body' => 'This is the body of my post']);
    $response->assertRedirect();
    $response->assertSessionHasErrors();
}
```

在这里，我们断言在无效输入后，用户会被重定向，并附加错误信息。您可以看到我们在这里使用了一些 Laravel 添加的自定义 PHPUnit 断言。

那么，我们如何测试我们路由的成功？请查看 示例 7-30。

##### 示例 7-30\. 测试处理有效输入

```php
public function test_valid_input_should_create_a_post_in_the_database()
{
    $this->post('posts', ['title' => 'Post Title', 'body' => 'This is the body']);
    $this->assertDatabaseHas('posts', ['title' => 'Post Title']);
}
```

请注意，如果您正在使用数据库进行测试，您需要了解更多关于数据库迁移和事务的知识。关于这方面的更多内容请参见 第 12 章。

# 简而言之

有很多方法可以获取相同的数据：使用 `Request` 门面、使用 `request()` 全局助手函数以及注入 `Illuminate\Http\Request` 实例。每个都提供了获取所有输入、部分输入或特定数据片段的能力，并且对于文件和 JSON 输入可能存在一些特殊考虑。

URL 路径段也是用户输入的一个可能来源，并且它们也可以通过请求工具访问。

可以使用 `Validator::make()` 手动执行验证，也可以使用 `validate()` 请求方法或表单请求自动执行验证。每个自动工具在验证失败时都会将用户重定向到上一页，并传递所有旧输入和错误信息。

视图和 Eloquent 模型也需要保护免受恶意用户输入的影响。您可以通过使用双花括号语法（`{{ }}`）来转义用户输入来保护 Blade 视图。您可以通过仅将特定字段传递给模型的批量方法（使用 `$request->only()`）并在模型本身上定义批量赋值规则来保护模型。
