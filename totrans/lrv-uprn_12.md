# 第十二章 测试

大多数开发者知道测试代码是一件好事。我们应该这样做。我们可能已经知道为什么这样做是好的，并且我们可能甚至已经阅读了一些关于如何进行测试的教程。

但是，了解*为什么*要测试和知道*如何*测试之间的差距很大。幸运的是，诸如 PHPUnit、Mockery 和 PHPSpec 之类的工具为 PHP 提供了大量测试选项，但是要设置好这一切可能仍然会让人感到很困扰。

Laravel 自带与 PHPUnit（单元测试）、Mockery（模拟）和 Faker（生成虚假数据以进行种子和测试）的集成。此外，它还提供了自己简单而强大的应用程序测试工具套件，允许你“爬行”你的站点 URI、提交表单、检查 HTTP 状态码，并针对 JSON 进行验证和断言。它还提供了一个强大的前端测试框架 Dusk，甚至可以与你的 JavaScript 应用程序进行交互并对其进行测试。如果这还不清楚，我们将在本章节中详细介绍。

为了让你轻松上手，Laravel 的测试设置包含可以在创建新应用程序时立即成功运行的示例应用程序测试。这意味着你不需要花时间配置你的测试环境，这就是写你的测试时的一个少了的障碍。

# 测试基础知识

Laravel 的测试位于*tests*文件夹中。根目录中有两个文件：*TestCase.php*，这是所有你的测试将扩展的基本根测试，以及*CreatesApplication.php*，一个 trait（由*TestCase.php*导入），允许任何类启动一个用于测试的示例 Laravel 应用程序。

# Laravel 测试命令

Laravel 有一个 Artisan 命令用于运行你的测试：`php artisan test`。它是`./vendor/bin/phpunit`命令的包装器，它将为每个测试显示更多输出。

还有两个子文件夹：*Features*，用于覆盖多个单元之间交互的测试，以及*Unit*，用于只覆盖你代码的一个单元（类、模块、函数等）的测试。这些文件夹中的每一个都包含一个*ExampleTest.php*文件，其中每个文件中都有一个准备好运行的单个样本测试。

在你的*Unit*目录中的`ExampleTest`包含一个简单的断言：`$this``->``assertTrue(true)`。你的单元测试中的任何内容可能都是相对简单的 PHPUnit 语法（断言值相等或不同，查找数组中的条目，检查布尔值等），所以在这里没有太多需要学习的内容。

# PHPUnit 断言基础知识

在 PHPUnit 中，我们的大多数断言将在`$this`对象上使用这种语法运行：

```php
$this->*`assertWHATEVER`*(*`$expected`*, *`$real`*);

```

因此，例如，如果我们断言两个变量应该相等，我们将首先传入我们预期的结果，然后传入对象或系统实际结果的第二个值进行测试：

```php
$multiplicationResult = $myCalculator->multiply(5, 3);
$this->assertEqual(15, $multiplicationResult);
```

正如你在示例 12-1 中看到的那样，*Feature*目录中的`ExampleTest`模拟了对应用程序根路径的页面的 HTTP 请求，并检查其 HTTP 状态是 200（成功）。如果是，它将通过；如果不是，它将失败。不同于普通的 PHPUnit 测试，我们在对测试 HTTP 调用时返回的`TestResponse`对象上进行这些断言。

##### 示例 12-1. tests/Feature/ExampleTest.php

```php
<?php

namespace Tests\Feature;

// use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class ExampleTest extends TestCase
{
    /**
 * A basic test example.
 */
    public function test_the_application_returns_a_successful_response(): void
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}
```

运行测试时，在应用程序根目录的命令行上运行`php artisan test`。你应该看到类似示例 12-2 的输出。

##### 示例 12-2. 示例`ExampleTest`输出

```php
  PASS  Tests\Unit\ExampleTest
  ✓ that true is true

   PASS  Tests\Feature\ExampleTest
  ✓ the application returns a successful response

  Tests:  2 passed (2 assertions)
  Time:   0.25s
```

你刚刚运行了你的第一个 Laravel 应用程序测试！两个勾号表示你有两个通过的测试。正如你看到的，你不但已默认配置了一个功能完备的 PHPUnit 实例，还配置了一个完整的应用程序测试套件，可以进行模拟 HTTP 调用并测试应用程序的响应。

如果你对 PHPUnit 不太熟悉，让我们看看一个测试失败时的情形。我们将不修改之前的测试，而是自己创建一个。运行`php artisan make:test FailingTest`。这将创建文件*tests/Feature/FailingTest.php*；你可以修改其`testExample()`方法，使其看起来像示例 12-3。

##### 示例 12-3. 修改后的 tests/Feature/FailingTest.php

```php
public function test_example()
{
    $response = $this->get('/');

    $response->assertStatus(301);
}
```

如你所见，它与我们之前运行的测试相同，但我们现在正在测试错误的状态。让我们再次运行 PHPUnit。

# 生成单元测试

如果您   如果你希望测试生成到`Unit`目录而不是`Feature`目录，请传递`--unit`标志：

```php
php artisan make:test SubscriptionTest --unit
```

哎呀！这次的输出看起来可能像是示例 12-4 那样。

##### 示例 12-4. 示例失败测试输出

```php
   PASS  Tests\Unit\ExampleTest
  ✓ that true is true

   PASS  Tests\Feature\ExampleTest
  ✓ the application returns a successful response

   FAIL  Tests\Feature\FailingTest
  ✕ example

  FAILED  Tests\Feature\FailingTest > example
  Expected status code [301] but received 200\. Failed asserting that
    301 is identical to 200.

  at tests/Feature/FailingTest.php:20
    16|     public function test_example()
    17|     {
    18|         $response = $this->get('/');
    19|
  > 20|         $response->assertStatus(301);
    21|     }
    22| }
    23|

  Tests:  1 failed, 2 passed (3 assertions)
  Duration: 1.10s
```

让我们分解一下。上次有两个通过的测试，但这次有一个失败，两个通过。

然后，对于每个错误，我们看到测试名称（在这里是`Test\Feature\FailingTest > example`），错误消息（“期望状态代码...”），以及部分堆栈跟踪，因此我们可以看到错误发生的行。

现在我们已经运行了通过的测试和失败的测试，是时候了解更多关于 Laravel 的测试环境了。

# 命名测试

默认情况下，Laravel 的测试系统会运行任何位于名为*tests*目录的文件，其名称以*Test*结尾。那就是为什么*tests/ExampleTest.php*会被默认运行。

如果你不熟悉 PHPUnit，你可能不知道只有以`test`为前缀的测试方法或带有`@test`文档块的方法或*docblock*才会运行。请参阅示例 12-5，了解哪些方法会运行和不会运行。

##### 示例 12-5. 命名 PHPUnit 方法

```php
class NamingTest
{
    public function test_it_names_things_well()
    {
        // Runs as "It names things well"
    }

    public function testItNamesThingsWell()
    {
        // Runs as "It names things well"
    }

    /** @test */
    public function it_names_things_well()
    {
        // Runs as "It names things well"
    }

    public function it_names_things_well()
    {
        // Doesn't run
    }
}
```

# 测试环境

当 Laravel 应用程序正在运行时，它有一个当前的“环境”名称，表示它正在运行的环境。你可以通过运行`app()->environment()`来检索这个名称，或者你可以运行`if (app()->environment('local'))`或类似的内容来测试当前环境是否与传递的名称匹配。

当你运行测试时，Laravel 会自动将环境设置为`testing`。这意味着你可以测试`if (app()->environment('testing'))`来启用或禁用测试环境中的某些行为。

另外，Laravel 在测试时不会加载来自*.env*的普通环境变量。如果你想为你的测试设置任何环境变量，请编辑*phpunit.xml*，在`<php>`部分为每个想要传递的环境变量添加一个新的`<env>`，例如，`<env name="DB_CONNECTION" value="sqlite"/>`。

# 测试特性

在我们讨论可以用于测试的方法之前，你需要了解可以引入任何测试类的四个测试特性。

## RefreshDatabase

`Illuminate\Foundation\Testing\RefreshDatabase`被导入到每个新生成的测试文件的顶部，是最常用的数据库迁移特性。

这个和其他数据库特性的目的是确保你的数据库表在每个测试开始时正确迁移。

`RefreshDatabase`执行两个步骤来实现这一点。首先，在每次测试运行开始时（当你运行`phpunit`时，而不是每个单独的测试方法），它会在你的测试数据库上运行你的迁移*一次*。其次，它会将每个单独的测试方法包装在一个数据库事务中，并在测试结束时回滚事务。

这意味着你为你的测试迁移了数据库，并且在每次测试运行后清除，而不需要在每个测试之前再次运行你的迁移——这是最快的选项。当你犹豫不决时，坚持使用这个选项。

## DatabaseMigrations

如果你导入`Illuminate\Foundation\Testing\DatabaseMigrations` trait 而不是`RefreshDatabase` trait，在每次测试之前将会重新运行整个数据库迁移集。Laravel 通过在每次测试运行前的`setUp()`方法中运行`php artisan migrate:fresh`来实现这一点。

## DatabaseTransactions

另一方面，`Illuminate\Foundation\Testing\DatabaseTransactions`期望你的数据库在测试开始之前已经正确迁移。它会将每个测试包装在一个数据库事务中，并在每个测试结束时回滚该事务。这意味着，在每次测试结束时，你的数据库将恢复到测试之前的确切状态。

## WithoutMiddleware

如果你在测试类中导入`Illuminate\Foundation\Testing\WithoutMiddleware`，它将为该类中的任何测试禁用所有中间件。这意味着你不需要担心身份验证中间件、CSRF 保护或任何在真实应用中可能有用但在测试中会分散注意力的内容。

如果你想要仅为单个方法而不是整个测试类禁用中间件，请在该测试方法的顶部调用`$this->withoutMiddleware()`。

# 简单单元测试

对于简单的单元测试，你几乎不需要这些特性。你*可能*需要访问数据库或从容器中注入一些内容，但很可能你的应用程序中的单元测试并不会过多依赖框架。参考示例 12-6 来了解一个简单测试可能的样子。

##### 示例 12-6\. 一个简单单元测试

```php
class GeometryTest extends TestCase
{
    public function test_it_calculates_area()
    {
        $square = new Square;
        $square->sideLength = 4;

        $calculator = new GeometryCalculator;

        $this->assertEquals(16, $calculator->area($square));
    }
```

显然，这个示例有点牵强。但你可以看到，我们在这里测试了一个单一类（`GeometryCalculator`）及其单一方法（`area()`），而且我们这样做时并不担心整个 Laravel 应用程序。

一些单元测试可能会测试一些技术上与框架连接的内容，例如 Eloquent 模型，但你仍然可以在不担心框架的情况下进行测试。例如，在示例 12-7 中，我们使用`Package::make()`而不是`Package::create()`，这样对象将在内存中创建和评估，而不会真正触及数据库。

##### 示例 12-7\. 一个更复杂的单元测试

```php
class PopularityTest extends TestCase
{
    use RefreshDatabase;

    public function test_votes_matter_more_than_views()
    {
        $package1 = Package::make(['votes' => 1, 'views' => 0]);
        $package2 = Package::make(['votes' => 0, 'views' => 1]);

        $this->assertTrue($package1->popularity > $package2->popularity);
    }
```

一些人可能会将这称为集成或特性测试，因为这个“单元”在实际使用中可能会触及数据库，并且它连接到整个 Eloquent 代码库。最重要的是，你可以编写简单的测试来测试一个单一类或方法，即使被测试的对象与框架连接。

尽管如此，你的测试——尤其是在开始时——更可能是更广泛和更“应用程序”级别的。因此，在本章的其余部分中，我们将更深入地探讨应用程序测试。

# 应用程序测试：工作原理

在“测试基础”中，我们看到，通过几行代码，我们可以“请求”应用程序中的 URI，并实际检查响应的状态。但是 PHPUnit 如何能够请求页面，就像它是一个浏览器一样？

任何应用程序测试都应该扩展默认包含在 Laravel 中的`TestCase`类（*tests/TestCase.php*）。你的应用程序的`TestCase`类将扩展抽象的`Illuminate\Foundation\Testing\TestCase`类，它带来了许多好处。

两个`TestCase`类（您的类和其抽象父类）的第一件事是为您处理 Illuminate 应用程序实例的引导，因此您可以使用一个完全引导的应用程序实例。它们还在每个测试之间“刷新”应用程序，这意味着它们在测试之间不完全重新创建应用程序，而是确保没有任何残留数据。

父`TestCase`还设置了一个钩子系统，允许在创建应用程序之前和之后运行回调，并导入了一系列提供与应用程序的每个方面交互方法的特性。这些特性包括`InteractsWithContainer`、`MakesHttpRequests`和`InteractsWithConsole`，它们引入了各种自定义断言和测试方法。

因此，您的应用程序测试可以访问一个完全引导的应用程序实例和应用程序测试相关的自定义断言，以及一系列简单而强大的包装器，使它们易于使用。

这意味着你可以编写`$this->get('/')->assertStatus(200)`，确保你的应用程序实际上表现得像响应正常的 HTTP 请求一样，并且完全生成并检查响应，就像浏览器检查一样。考虑到你要做的工作很少，这是非常强大的功能。

# HTTP 测试

让我们来看看编写基于 HTTP 的测试的选项。您已经看到了`$this->get('/')`，但让我们更深入地了解如何使用该调用，如何断言其结果以及您可以进行的其他 HTTP 调用。

## 使用 `$this->get()` 和其他 HTTP 调用测试基本页面

在最基本的层面上，Laravel 的 HTTP 测试允许您进行简单的 HTTP 请求（`GET`、`POST`等），然后对它们的影响或响应进行简单的断言。

我们稍后会介绍更多工具（“使用 Dusk 进行测试”），这些工具允许进行更复杂的页面交互和断言，但让我们从基础开始。以下是您可以进行的调用：

+   `$this->get(*$uri, $headers = []*)`

+   `$this->post(*$uri, $data = [], $headers = []*)`

+   `$this->put(*$uri, $data = [], $headers = []*)`

+   `$this->patch(*$uri, $data = [], $headers = []*)`

+   `$this->delete(*$uri, $data = [], $headers = []*)`

+   `$this->option(*$uri, $data = [], $headers = []*)`

这些方法是 HTTP 测试框架的基础。每个方法至少需要一个 URI（通常是相对路径）和头部，除了`get()`方法外，其他方法还允许传递数据。

并且，每个方法返回一个`$response`对象，代表 HTTP 响应。这个响应对象几乎与 Illuminate `Response`对象相同，我们在控制器中返回相同的对象。然而，它实际上是`Illuminate\Testing\TestResponse`的实例，它用一些断言来进行测试。

查看 示例 12-8 来查看 `post()` 的常见用法和常见响应断言。

##### 示例 12-8\. 测试中使用 `post()` 的简单用法

```php
public function test_it_stores_new_packages()
{
    $response = $this->post(route('packages.store'), [
        'name' => 'The greatest package',
    ]);

    $response->assertOk();
}
```

在大多数类似 示例 12-8 的示例中，您还将测试记录是否存在于数据库中，并显示在索引页面上，除非您定义了包的作者并且已登录。但不用担心，我们将会涉及到所有这些内容。现在，您可以使用多种不同的动词调用您的应用程序路由，并对响应及应用程序状态进行断言。很棒！

## 使用 `$this->getJson()` 和其他 JSON HTTP 调用来测试 JSON API

您还可以使用 JSON API 进行相同类型的所有 HTTP 测试。这也有方便的方法来进行：

+   `$this->getJson(*$uri, $headers = []*)`

+   `$this->postJson(*$uri, $data = [], $headers = []*)`

+   `$this->putJson(*$uri, $data = [], $headers = []*)`

+   `$this->patchJson(*$uri, $data = [], $headers = []*)`

+   `$this->deleteJson(*$uri, $data = [], $headers = []*)`

+   `$this->optionJson(*$uri, $data = [], $headers = []*)`

这些方法的工作方式与普通的 HTTP 调用方法完全相同，但它们还会添加特定于 JSON 的 `Accept`、`CONTENT_LENGTH` 和 `CONTENT_TYPE` 头部。查看 示例 12-9 来查看一个示例。

##### 示例 12-9\. 测试中使用 `postJson()` 的简单用法

```php
public function test_the_api_route_stores_new_packages()
{
    $response = $this->postJson(route('api.packages.store'), [
        'name' => 'The greatest package',
    ], ['X-API-Version' => '17']);

    $response->assertOk();
}
```

## 对 $response 的断言

在 `$response` 对象上有超过 50 个断言可用，因此我会指引您查阅 [测试文档](https://oreil.ly/CXk24) 以获取所有详情。让我们看一些最重要和最常见的断言：

`$response->assertOk()`

断言响应的状态码为 200：

```php
$response = $this->get('terms');
$response->assertOk();
```

`$response->assertSuccessful()`

尽管 `assertOk()` 断言代码确切为 200，`assertSuccessful()` 则检查代码是否属于 200 组中的任意值：

```php
$response = $this->post('articles', [
    'title' => 'Testing Laravel',
    'body'  => 'My article about testing Laravel',
]);
// Assuming this returns 201 CREATED...
$response->assertSuccessful();
```

`$response->assertUnauthorized()`

断言响应的状态码为 401：

```php
$response = $this->patch('settings', ['password' => 'abc']);
$response->assertUnauthorized();
```

`$response->assertForbidden()`

断言响应的状态码为 403：

```php
$response = $this->actingAs($normalUser)->get('admin');
$response->assertForbidden();
```

`$response->assertNotFound()`

断言响应的状态码为 404：

```php
$response = $this->get('posts/first-post');
$response->assertNotFound();
```

`$response->assertStatus(*$status*)`

断言响应的状态码等于提供的 `*$status*`：

```php
$response = $this->get('admin');
$response->assertStatus(401); // Unauthorized
```

`$response->assertSee(*$text*)`, `$response->assertDontSee(*$text*)`

断言响应包含（或不包含）提供的 `*$text*`：

```php
$package = Package::factory()->create();
$response = $this->get(route('packages.index'));
$response->assertSee($package->name);
```

`$response->assertJson(*array $json*)`

断言传递的数组在返回的 JSON 中以 JSON 格式表示：

```php
$this->postJson(route('packages.store'), ['name' => 'GreatPackage2000']);
$response = $this->getJson(route('packages.index'));
$response->assertJson(['name' => 'GreatPackage2000']);
```

`$response->assertViewHas(*$key, $value = null*)`

断言访问页面上的视图在 `*$key*` 处有可用数据，并可选择检查该变量的值是否为 `*$value*`：

```php
$package = Package::factory()->create();
$response = $this->get(route('packages.show'));
$response->assertViewHas('name', $package->name);
```

`$response->assertSessionHas(*$key, $value = null*)`

断言会话中设置了数据在 `*$key*`，并可选择检查该数据的值是否为 `*$value*`：

```php
$response = $this->get('beta/enable');
$response->assertSessionHas('beta-enabled', true);
```

`$response->assertSessionHasInput(*$key, $value = null*)`

断言给定的键和值已在会话数组输入中闪存。在测试验证错误是否返回正确的旧值时，这很有帮助：

```php
$response = $this->post('users', ['name' => 'Abdullah']);
// Assuming it errored, check that the entered name is flashed;
$response->assertSessionHasInput('name', 'Abdullah');
```

`$response->assertSessionHasErrors()`

没有参数时，断言 Laravel 的特殊`errors`会话容器中至少设置了一个错误。它的第一个参数可以是定义应设置的错误的键/值对数组，第二个参数可以是应检查的错误应格式化的字符串格式，正如此处所示：

```php
// Assuming the "/form" route requires an email field, and we're
// posting an empty submission to it to trigger the error
$response = $this->post('form', []);

$response->assertSessionHasErrors();
$response->assertSessionHasErrors([
    'email' => 'The email field is required.',
 ]);
$response->assertSessionHasErrors(
    ['email' => '<p>The email field is required.</p>'],
    '<p>:message</p>'
);
```

如果您正在使用命名错误包，请将错误包名称作为第三个参数传递。

`$response->assertCookie(*$name, $value = null*)`

断言响应包含名称为`*$name*`的 cookie，并可选地检查其值是否为`*$value*`：

```php
$response = $this->post('settings', ['dismiss-warning']);
$response->assertCookie('warning-dismiss', true);
```

`$response->assertCookieExpired(*$name*)`

断言响应包含名称为`*$name*`的 cookie，并且它已过期：

```php
$response->assertCookieExpired('warning-dismiss');
```

`$response->assertCookieNotExpired(*$name*)`

断言响应包含名称为`*$name*`的 cookie，并且它未过期：

```php
$response->assertCookieNotExpired('warning-dismiss');
```

`$response->assertRedirect(*$uri*)`

断言请求的路由返回重定向到指定的 URI：

```php
$response = $this->post(route('packages.store'), [
    'email' => 'invalid'
]);

$response->assertRedirect(route('packages.create'));
```

对于这些断言的每一个，您可以假设有许多相关的断言我没有在此列出。例如，除了`assertSessionHasErrors()`之外，还有`assertSessionHasNoErrors()`和`assertSessionHasErrorsIn()`断言；除了`assertJson()`之外，还有`assertJsonCount()`、`assertJsonFragment()`、`assertJsonPath()`、`assertJsonMissing()`、`assertJsonMissingExact()`、`assertJsonStructure()`和`assertJsonValidationErrors()`断言。再次，查看文档并熟悉整个列表。

## 验证响应

您的应用程序中的一个常见测试部分是身份验证和授权。大多数情况下，您的需求可以通过`actingAs()`链式方法满足，该方法接受一个用户（或其他`Authenticatable`对象，具体取决于系统设置），正如您在示例 12-10 中所见。

##### 示例 12-10\. 测试中的基本身份验证

```php
public function test_guests_cant_view_dashboard()
{
    $user = User::factory()->guest()->create();
    $response = $this->actingAs($user)->get('dashboard');
    $response->assertStatus(401); // Unauthorized
}

public function test_members_can_view_dashboard()
{
    $user = User::factory()->member()->create();
    $response = $this->actingAs($user)->get('dashboard');
    $response->assertOk();
}

public function test_members_and_guests_cant_view_statistics()
{
    $guest = User::factory()->guest()->create();
    $response = $this->actingAs($guest)->get('statistics');
    $response->assertStatus(401); // Unauthorized

    $member = User::factory()->member()->create();
    $response = $this->actingAs($member)->get('statistics');
    $response->assertStatus(401); // Unauthorized
}

public function test_admins_can_view_statistics()
{
    $user = User::factory()->admin()->create();
    $response = $this->actingAs($user)->get('statistics');
    $response->assertOk();
}
```

# 使用工厂状态进行授权

在测试中使用模型工厂（在“模型工厂”中讨论），模型工厂状态使得像创建具有不同访问级别的用户这样的任务变得简单。

## 对 HTTP 测试的一些其他自定义

如果您希望在请求中设置会话变量，还可以链式调用`withSession()`：

```php
$response = $this->withSession([
    'alert-dismissed' => true,
])->get('dashboard');
```

如果您希望流畅地设置请求标头，请链式调用`withHeaders()`：

```php
$response = $this->withHeaders([
    'X-THE-ANSWER' => '42',
])->get('the-restaurant-at-the-end-of-the-universe');
```

## 在应用程序测试中处理异常

通常，当您在应用程序中进行 HTTP 调用时抛出异常，将会被 Laravel 的异常处理程序捕获并处理，就像在普通应用程序中一样。因此，示例 12-11 中的测试和路由仍将通过，因为异常永远不会完全冒泡到我们的测试中。

##### 示例 12-11\. 会被 Laravel 异常处理器捕获并导致测试通过的异常

```php
// routes/web.php
Route::get('has-exceptions', function () {
    throw new Exception('Stop!');
});

// tests/Feature/ExceptionsTest.php
public function test_exception_in_route()
{
    $this->get('/has-exceptions');

    $this->assertTrue(true);
}
```

在很多情况下，这可能是有意义的；也许你期望一个验证异常，并且希望像框架通常情况下一样被捕获。

但是如果你想临时禁用异常处理，也有一个选项；只需运行 `$this->withoutExceptionHandling()`，就像 示例 12-12 中展示的那样。

##### 示例 12-12\. 在单个测试中临时禁用异常处理

```php
// tests/Feature/ExceptionsTest.php
public function test_exception_in_route()
{
    // Now throws an error
    $this->withoutExceptionHandling();

    $this->get('/has-exceptions');

    $this->assertTrue(true);
}
```

如果由于某种原因你需要重新启用异常处理（也许你在 `setUp()` 中关闭了它，但只想在一个测试中重新启用），你可以运行 `$this->withExceptionHandling()`。

## 调试响应

你可以轻松地使用 `dumpHeaders()` 打印出头信息，或者使用 `dump()` 或 `dd()` 打印出主体：

```php
$response = $this->get('/');

$response->dumpHeaders();
$response->dump();
$response->dd();
```

你还可以轻松地将会话中的所有或仅指定键转储出来：

```php
$response = $this->get('/');

$response->dumpSession();
$response->dumpSession(['message']);
```

# 数据库测试

往往，我们在测试运行后想要测试的效果在数据库中。想象一下，你想要测试“创建包”页面是否正确工作。最好的方法是什么？对“存储包”端点进行 HTTP 调用，然后断言该包在数据库中存在。这比检查生成的“列出包”页面更容易且更安全。

我们有四个主要的数据库断言方法和两个特定于 Eloquent 的断言方法。

## 针对数据库的断言

对于直接针对数据库的断言，我们有 `$this->assertDatabaseHas()` 和 `$this->assertDatabaseMissing()`，还有 `$this->assertDeleted()` 和 `$this->assertSoftDeleted()`。对于这些方法，将表名作为第一个参数传递，你要查找的数据作为第二个参数，以及可选的指定数据库连接作为第三个参数。

参考 示例 12-13 了解如何使用它们。

##### 示例 12-13\. 示例数据库测试

```php
public function test_create_package_page_stores_package()
{
    $this->post(route('packages.store'), [
        'name' => 'Package-a-tron',
    ]);

    $this->assertDatabaseHas('packages', ['name' => 'Package-a-tron']);
}
```

正如你所见，`assertDatabaseHas()` 的第二个参数结构类似于 SQL 的 `WHERE` 语句 —— 你传递一个键和一个值（或多个键和值），然后 Laravel 查找与你的键和值匹配的指定数据库表中的任何记录。

如你所料，`assertDatabaseMissing()` 则是其反向操作。

## 针对 Eloquent 模型的断言

当使用 `assertDatabaseHas()` 和 `assertDatabaseMissing()` 时，你可以通过传递键和值来识别行，而 Laravel 也提供了方便的方法直接断言给定的 Eloquent 记录是否存在或不存在：`assertModelExists()` 和 `assertModelMissing()`，正如你在 示例 12-14 中所看到的。

##### 示例 12-14\. 断言模型存在

```php
public function test_undeletable_packages_cant_be_deleted()
{
    // Create undeletable model
    $package = Package::factory()->create([
        'name' => 'Package-a-tron',
        'is_deletable' => false,
    ]);

    $this->post(route('packages.delete', $package));

    // Can check existence or whether it was soft deleted
    $this->assertModelExists($package);
    $this->assertNotSoftDeleted($package);

    $package->update(['is_deletable' => true]);

    $this->post(route('packages.delete', $package));

    // Can check existence or whether it was soft deleted
    $this->assertModelMissing($package);
    $this->assertSoftDeleted($package);
}
```

## 在测试中使用模型工厂

模型工厂是一种令人惊奇的工具，它能够轻松地为测试（或其他目的）种子填充随机化、结构良好的数据库数据。你已经在本章的几个示例中看到它们的使用，我们也已经对其进行了深入的讨论，因此请查看 “模型工厂” 以了解更多信息。

## 在测试中进行种子填充

如果您的应用程序使用种子数据，您可以通过在测试中运行 `$this->seed()` 来运行相当于 `php artisan db:seed` 的操作。

您还可以将种子器类名称传递给仅种子化该类：

```php
$this->seed(); // Seeds all
$this->seed(UserSeeder::class); // Seeds users
```

# 测试其他 Laravel 系统

在测试 Laravel 系统时，通常希望在测试期间暂停其真实功能，而是针对这些系统已发生的情况编写测试。您可以通过“伪造”不同的外观来实现这一点，例如 `Event`、`Mail` 和 `Notification`。我们将在 “模拟” 中详细讨论伪造的内容，但首先让我们看一些示例。在 Laravel 中，所有这些特性在伪造后都有自己的一套断言，但您也可以选择仅仅伪造它们以限制其影响。

## 事件伪造

让我们以事件伪造作为 Laravel 如何使模拟其内部系统成为可能的第一个示例。可能会有时候您希望仅仅为了抑制它们的行为而伪造事件。例如，假设您的应用程序在每次新用户注册时都向 Slack 推送通知。当这种情况发生时，您会发出“用户注册”事件，并且有一个监听器通知 Slack 频道有用户注册。您不希望每次运行测试时这些通知都发送到 Slack，但您可能希望断言事件是否已发送，或者监听器是否已触发，或其他某些内容。这是在测试中伪造 Laravel 某些方面的原因之一：暂停默认行为，而是针对正在测试的系统进行断言。

让我们看看如何通过在 `Illuminate\Support\Facades\Event` 上调用 `fake()` 方法来抑制这些事件，就像在 示例 12-15 中展示的那样。

##### 示例 12-15\. 抑制事件而不添加断言

```php
public function test_controller_does_some_thing()
{
    Event::fake();

    // Call controller and assert it does whatever you want without
    // worrying about it pinging Slack
}
```

一旦我们运行了 `fake()` 方法，我们还可以在 `Event` 外观上调用特殊的断言：即 `assertDispatched()` 和 `assertNotDispatched()`。请查看 示例 12-16 来查看它们的使用方法。

##### 示例 12-16\. 对事件进行断言

```php
public function test_signing_up_users_notifies_slack()
{
    Event::fake();

    // Sign user up

    Event::assertDispatched(UserJoined::class, function ($event) use ($user) {
        return $event->user->id === $user->id;
    });

    // Or sign multiple users up and assert it was dispatched twice

    Event::assertDispatched(UserJoined::class, 2);

    // Or sign up with validation failures and assert it wasn't dispatched

    Event::assertNotDispatched(UserJoined::class);
}
```

请注意，我们传递给 `assertDispatched()` 的（可选）闭包意味着我们不仅仅是断言事件是否已被分派，而且断言已分派的事件是否包含某些数据。

# Event::fake() 禁用了 Eloquent 模型事件

`Event::fake()` 同时还会禁用 Eloquent 模型事件。因此，如果您的代码中有任何重要的代码，例如在模型的 `creating` 事件中，请确保在调用 `Event::fake()` 之前创建您的模型（通过工厂或其他方式）。

## Bus 和 Queue 的伪造

`Bus` 外观代表 Laravel 如何调度作业，其工作方式与 `Event` 相似。您可以在其上运行 `fake()` 来禁用作业的影响，并在伪造后运行 `assertDispatched()` 或 `assertNotDispatched()`。

`Queue`外观表示 Laravel 在将作业推送到队列时如何调度作业。它的可用方法有`assertedPushed()`、`assertPushedOn()` 和 `assertNotPushed()`。

查看 示例 12-17 以了解如何同时使用两者。

##### 示例 12-17\. 伪造作业和排队作业

```php
public function test_popularity_is_calculated()
{
    Bus::fake();

    // Synchronize package data...

    // Assert a job was dispatched
    Bus::assertDispatched(
        CalculatePopularity::class,
        function ($job) use ($package) {
            return $job->package->id === $package->id;
        }
    );

    // Assert a job was not dispatched
    Bus::assertNotDispatched(DestroyPopularityMaybe::class);
}

public function test_popularity_calculation_is_queued()
{
    Queue::fake();

    // Synchronize package data...

    // Assert a job was pushed to any queue
    Queue::assertPushed(
        CalculatePopularity::class,
        function ($job) use ($package) {
        return $job->package->id === $package->id;
        }
    );

    // Assert a job was pushed to a given queue named "popularity"
    Queue::assertPushedOn('popularity', CalculatePopularity::class);

    // Assert a job was pushed twice
    Queue::assertPushed(CalculatePopularity::class, 2);

    // Assert a job was not pushed
    Queue::assertNotPushed(DestroyPopularityMaybe::class);
}
```

## 邮件虚拟

当伪造`Mail`外观时，提供四种方法：`assertSent()`、`assertNotSent()`、`assertQueued()` 和 `assertNotQueued()`。当你的邮件在队列中时，请使用`Queued`方法；当它不在队列中时，请使用`Sent`方法。

就像使用`assertDispatched()`一样，第一个参数将是可发送邮件的名称，第二个参数可以为空，或者是可发送邮件的次数，或者是一个测试可发送邮件是否包含正确数据的闭包。查看 示例 12-18 以看到这些方法的几个示例。

##### 示例 12-18\. 对邮件进行断言

```php
public function test_package_authors_receive_launch_emails()
{
    Mail::fake();

    // Make a package public for the first time...

    // Assert a message was sent to a given email address
    Mail::assertSent(PackageLaunched::class, function ($mail) use ($package) {
        return $mail->package->id === $package->id;
    });

    // Assert a message was sent to given email addresses
    Mail::assertSent(PackageLaunched::class, function ($mail) use ($package) {
        return $mail->hasTo($package->author->email) &&
               $mail->hasCc($package->collaborators) &&
               $mail->hasBcc('admin@novapackages.com');
    });

    // Or, launch two packages...

    // Assert a mailable was sent twice
    Mail::assertSent(PackageLaunched::class, 2);

    // Assert a mailable was not sent
    Mail::assertNotSent(PackageLaunchFailed::class);
}
```

所有检查收件人（`hasTo()`、`hasCc()` 和 `hasBcc()`）的消息可以接受单个电子邮件地址或电子邮件地址数组或集合。

## 通知虚拟

当伪造`Notification`外观时，提供两种方法：`assertSentTo()` 和 `assertNothingSent()`。

与`Mail`外观不同的是，你不需要在闭包中手动测试通知发送给谁。相反，断言本身要求第一个参数是单个可通知对象或其数组或集合。只有在传入所需的通知目标后，你才能测试通知本身的任何内容。

第二个参数是通知的类名，第三个参数（可选）可以是定义通知更多期望的闭包。查看 示例 12-19 以了解更多信息。

##### 示例 12-19\. 通知虚拟

```php
public function test_users_are_notified_of_new_package_ratings()
{
    Notification::fake();

    // Perform package rating...

    // Assert author was notified
    Notification::assertSentTo(
        $package->author,
        PackageRatingReceived::class,
        function ($notification, $channels) use ($package) {
            return $notification->package->id === $package->id;
        }
    );

    // Assert a notification was sent to the given users
    Notification::assertSentTo(
        [$package->collaborators], PackageRatingReceived::class
    );

    // Or, perform a duplicate package rating...

    // Assert a notification was not sent
    Notification::assertNotSentTo(
        [$package->author], PackageRatingReceived::class
    );
}
```

你可能还想要断言你的渠道选择是否有效——即通过正确的渠道发送通知。你也可以进行这方面的测试，正如你可以在 示例 12-20 中看到的那样。

##### 示例 12-20\. 测试通知渠道

```php
public function test_users_are_notified_by_their_preferred_channel()
{
    Notification::fake();

    $user = User::factory()->create(['slack_preferred' => true]);

    // Perform package rating...

    // Assert author was notified via Slack
    Notification::assertSentTo(
        $user,
        PackageRatingReceived::class,
        function ($notification, $channels) use ($package) {
            return $notification->package->id === $package->id
                && in_array('slack', $channels);
        }
    );
}
```

## 存储虚拟

测试文件可能会非常复杂。许多传统方法要求你实际在测试目录中移动文件，并且格式化表单输入和输出可能非常复杂。

幸运的是，如果你使用 Laravel 的`Storage`外观，测试文件上传和其他与存储相关的项目就会简单得多，正如 示例 12-21 所演示的那样。

##### 示例 12-21\. 使用存储虚拟测试存储和文件上传

```php
public function test_package_screenshot_upload()
{
    Storage::fake('screenshots');

    // Upload a fake image
    $response = $this->postJson('screenshots', [
        'screenshot' => UploadedFile::fake()->image('screenshot.jpg'),
    ]);

    // Assert the file was stored
    Storage::disk('screenshots')->assertExists('screenshot.jpg');

    // Or, assert a file does not exist
    Storage::disk('screenshots')->assertMissing('missing.jpg');
}
```

# 在测试中处理时间

在我们测试应用程序的各个部分与时间交互时，通常希望测试这些部分随时间推移的行为不同。

在我们的测试中，我们可以使用`$this->travel()`来在测试进行过程中“旅行”时间。我们可以相对于当前时间向前或向后旅行，旅行到特定时刻，或者冻结时间的流逝，这样可以测试组件在时间看起来不同的情况下的行为。

看看示例 12-22 以了解您可能希望如何使用此功能，或者查看[文档](https://oreil.ly/1PNzc)以了解更多与时间交互的所有方式。

##### 示例 12-22\. 改变测试中的时间

```php
public function test_posts_are_no_longer_editable_after_thirty_minutes()
{
    $post = Post::create();

    $this->assertTrue($post->isEditable());

    $this->travel(30)->seconds();

    $this->assertTrue($post->isEditable());

    $this->travelTo($post->created_at->copy()->addMinutes(31));

    $this->assertFalse($post->isEditable());
}
```

您还可以为每个这些时间旅行方法提供一个闭包；如果这样做，测试的时间只在闭包的持续时间内被修改，允许您更直接地连接您的旅行和结果测试，正如您在示例 12-23 中所看到的。

##### 示例 12-23\. 使用闭包在测试中改变时间

```php
public function test_posts_are_no_longer_editable_after_thirty_minutes()
{
    $post = Post::create();

    $this->assertTrue($post->isEditable());

    $this->travel(30)->seconds(function () {
        $this->assertTrue($post->isEditable());
    });

    $this->travelTo($post->created_at->copy()->addMinutes(31), function () {
        $this->assertFalse($post->isEditable());
    });
}
```

# 模拟

在测试中，模拟（及其同类，如间谍、存根、虚拟和伪造以及其他各种工具）是很常见的。我们在前一节中看到了一些伪造的示例。我不会在这里详细展开，但是在任何规模的应用程序中，要彻底测试一个应用程序几乎不可能不模拟至少一些东西。

因此，让我们快速浏览一下在 Laravel 中如何进行模拟以及如何使用 Mockery，这个模拟库。

## 关于模拟的简短介绍

本质上，模拟和其他类似工具使得可以创建一个在某种程度上模仿真实类的对象，但是出于测试目的并非真实类。有时候这样做是因为实际类太难实例化以便将其注入到测试中，或者可能是因为实际类与外部服务通信。

从接下来的示例中您可能可以看出，Laravel 鼓励尽可能地与真实应用程序一起工作——这意味着避免过多依赖于模拟。但它们确实有其用处，这就是为什么 Laravel 内置了 Mockery，一个模拟库，并且为其核心服务提供了伪造工具的原因。

## 关于 Mockery 的简短介绍

Mockery 允许您快速轻松地从应用程序中的任何 PHP 类创建模拟。想象一下，您有一个依赖于 Slack 客户端的类，但是您不希望调用实际发送到 Slack。Mockery 使得创建一个用于测试的假 Slack 客户端变得简单，就像您在示例 12-24 中所看到的。

##### 示例 12-24\. 在 Laravel 中使用 Mockery

```php
// app/SlackClient.php
class SlackClient
{
    // ...

    public function send($message, $channel)
    {
        // Actually sends a message to Slack
    }
}

// app/Notifier.php
class Notifier
{
    private $slack;

    public function __construct(SlackClient $slack)
    {
        $this->slack = $slack;
    }

    public function notifyAdmins($message)
    {
        $this->slack->send($message, 'admins');
    }
}

// tests/Unit/NotifierTest.php
public function test_notifier_notifies_admins()
{
    $slackMock = Mockery::mock(SlackClient::class)->shouldIgnoreMissing();

    $notifier = new Notifier($slackMock);
    $notifier->notifyAdmins('Test message');
}
```

这里有很多因素在起作用，但如果您逐一查看它们，它们是有道理的。我们有一个名为`Notifier`的类，我们正在测试它。它有一个名为`SlackClient`的依赖项，在我们运行测试时，我们不希望它执行某些操作：发送实际的 Slack 通知。因此，我们将对其进行模拟。

我们使用 Mockery 来获取我们的`SlackClient`类的模拟。如果我们不关心该类发生什么——如果它只是存在以防止我们的测试抛出错误——我们可以简单地使用`shouldIgnoreMissing()`：

```php
$slackMock = Mockery::mock(SlackClient::class)->shouldIgnoreMissing();
```

无论`Notifier`在`$slackMock`上调用什么，它都会接受并返回`null`。

但是看一下`test_notifier_notifies_admins()`。到这一点为止，它实际上并不*测试*任何东西。

我们可以只保留`shouldIgnoreMissing()`，然后在其下写一些断言。通常我们对`shouldIgnoreMissing()`这样做，这使得这个对象成为一个“伪造”或“存根”。

但是如果我们想要实际断言`SlackClient`的`send()`方法是否被调用了呢？这时我们放弃了`shouldIgnoreMissing()`，转而使用其他的`should*`方法（示例 12-25）。

##### 示例 12-25\. 使用 Mockery 模拟的`shouldReceive()`方法

```php
public function test_notifier_notifies_admins()
{
    $slackMock = Mockery::mock(SlackClient::class);
    $slackMock->shouldReceive('send')->once();

    $notifier = new Notifier($slackMock);
    $notifier->notifyAdmins('Test message');
}
```

`shouldReceive('send')->once()`等同于说“断言`$slackMock`的`send()`方法将被调用一次且仅一次。” 因此，我们现在断言`Notifier`在调用`notifyAdmins()`时，会在`SlackClient`上的`send()`方法上做一次调用。

我们也可以使用诸如`shouldReceive('send')->times(3)`或`shouldReceive('send')->never()`之类的方法。我们可以使用`with()`定义我们期望随`send()`调用传递的参数，并使用`andReturn()`定义返回值：

```php
$slackMock->shouldReceive('send')->with('Hello, world!')->andReturn(true);
```

如果我们想要使用 IoC 容器来解析我们的`Notifier`实例会怎么样？如果`Notifier`有几个其他依赖项我们不需要模拟，这可能会很有用。

我们可以这样做！只需在容器上使用`instance()`方法，就像在示例 12-26 中一样，告诉 Laravel 为请求它的任何类提供我们模拟的实例（在本例中将是`Notifier`）。

##### 示例 12-26\. 将 Mockery 实例绑定到容器

```php
public function test_notifier_notifies_admins()
{
    $slackMock = Mockery::mock(SlackClient::class);
    $slackMock->shouldReceive('send')->once();

    app()->instance(SlackClient::class, $slackMock);

    $notifier = app(Notifier::class);
    $notifier->notifyAdmins('Test message');
}
```

还有一个便捷的快捷方式，可以创建并绑定一个 Mockery 实例到容器中（示例 12-27）：

##### 示例 12-27\. 更轻松地将 Mockery 实例绑定到容器

```php
$this->mock(SlackClient::class, function ($mock) {
    $mock->shouldReceive('send')->once();
});
```

使用 Mockery 还有很多事情可以做：你可以使用间谍，部分间谍，等等。深入探讨如何使用 Mockery 超出了本书的范围，但我鼓励你通过阅读[Mockery 文档](https://oreil.ly/EBulp)来更多地了解这个库及其工作原理。

## 伪造其他外观

使用 Mockery 还有一个聪明的事情：您可以在应用程序中的任何外观上使用 Mockery 方法（例如`shouldReceive()`）。

假设我们有一个控制器方法，使用了一个不是我们已经覆盖过的可伪造系统的外观；我们想要测试该控制器方法，并断言某个外观调用确实被执行。

幸运的是，这很简单：我们可以在外观上运行我们 Mockery 风格的方法，正如你在示例 12-28 中所见。

##### 示例 12-28\. 模拟外观

```php
// PersonController
public function index()
{
    return Cache::remember('people', function () {
        return Person::all();
    });
}

// PeopleTest
public function test_all_people_route_should_be_cached()
{
    $person = Person::factory()->create();

    Cache::shouldReceive('remember')
        ->once()
        ->andReturn(collect([$person]));

    $this->get('people')->assertJsonFragment(['name' => $person->name]);
}
```

如您所见，您可以在外观上使用像`shouldReceive()`这样的方法，就像在`Mockery`对象上一样。

您还可以将您的外观用作间谍，这意味着您可以在最后设置您的断言，并使用`shouldHaveReceived()`而不是`shouldReceive()`。示例 12-29 说明了这一点。

##### 示例 12-29\. 外观间谍

```php
public function test_package_should_be_cached_after_visit()
{
    Cache::spy();

    $package = Package::factory()->create();

    $this->get(route('packages.show', [$package->id]));

    Cache::shouldHaveReceived('put')
        ->once()
        ->with('packages.' . $package->id, $package->toArray());
}
```

你还可以部分模拟外观，就像你在 示例 12-30 中看到的那样。

##### 示例 12-30\. 部分模拟外观

```php
// Full mock
CustomFacade::shouldReceive('someMethod')->once();
CustomFacade::someMethod();
CustomFacade::anotherMethod(); // Fails

// Partial mock
CustomFacade::partialMock()->shouldReceive('someMethod')->once();
CustomFacade::someMethod(); // Uses the mocked object
CustomFacade::anotherMethod(); // Uses the method on the actual Facade
```

# 测试 Artisan 命令

在本章中，我们已经涵盖了很多内容，但我们快要完成了！我们还有 Laravel 测试工具的三个要点要讲解：Artisan、并行测试和浏览器测试。

测试 Artisan 命令的最佳方式是使用 `$this->artisan(*$commandName*, *$parameters*)` 来调用它们，然后测试它们的影响，就像在 示例 12-31 中所示。

##### 示例 12-31\. 简单的 Artisan 测试

```php
public function test_promote_console_command_promotes_user()
{
    $user = User::factory()->create();

    $this->artisan('user:promote', ['userId' => $user->id]);

    $this->assertTrue($user->isPromoted());
}
```

你可以对来自 Artisan 的响应代码进行断言，就像在 示例 12-32 中看到的那样。

##### 示例 12-32\. 手动断言 Artisan 退出码

```php
$code = $this->artisan('do:thing', ['--flagOfSomeSort' => true]);
$this->assertEquals(0, $code); // 0 means "no errors were returned"
```

你还可以在 `$this->artisan()` 调用上链式调用三个方法：`expectsQuestion()`、`expectsOutput()` 和 `assertExitCode()`。`expects`* 方法可以处理任何交互提示，包括 `confirm()` 和 `anticipate()`，而 `assertExitCode()` 方法是 示例 12-32 中所见的快捷方式。

看看 示例 12-33 来了解它的工作原理。

##### 示例 12-33\. 基本的 Artisan “expects” 测试

```php
// routes/console.php
Artisan::command('make:post {--expanded}', function () {
    $title = $this->ask('What is the post title?');
    $this->comment('Creating at ' . Str::slug($title) . '.md');

    $category = $this->choice('What category?', ['technology', 'construction'], 0);

    // Create post here

    $this->comment('Post created');
});
```

```php
// Test file
public function test_make_post_console_commands_performs_as_expected()
{
    $this->artisan('make:post', ['--expanded' => true])
        ->expectsQuestion('What is the post title?', 'My Best Post Now')
        ->expectsOutput('Creating at my-best-post-now.md')
        ->expectsQuestion('What category?', 'construction')
        ->expectsOutput('Post created')
        ->assertExitCode(0);
}
```

正如你所见，`expectsQuestion()` 的第一个参数是我们期望从问题中看到的文本，第二个参数是我们的答案文本。`expectsOutput()` 只是测试返回的字符串是否正确。

# 并行测试

默认情况下，Laravel 中的测试在单线程中运行。测试越多，且越复杂，你的测试套件运行时间就越长，这可能会显著影响团队运行测试套件的可能性。

如果你想加快测试套件的运行速度，你可以并行运行你的测试。你需要安装一个叫做 `paratest` 的依赖：

```php
composer require brianium/paratest --dev
```

安装了 `paratest` 后，你可以使用 `--parallel` 标志并行运行你的测试，就像在 示例 12-34 中看到的那样。

##### 示例 12-34\. 并行运行测试

```php
# Use as many processes as your CPU can offer
php artisan test --parallel

# Specify the desired number of processes
php artisan test --parallel --processes=3
```

# 浏览器测试

我们已经到了浏览器测试！这使得你可以实际与页面的 DOM 进行交互：在浏览器测试中，你可以点击按钮、填写并提交表单，甚至与 JavaScript 交互。

## 选择工具

对于非单页面应用的浏览器测试，我建议你使用 Dusk。如果你正在处理单页面应用或一些 JavaScript 重的应用程序，它们可能更适合使用前端测试套件，这超出了本书的范围。

## 使用 Dusk 进行测试

Dusk 是 Laravel 的一个工具（可安装为 Composer 包），它可以轻松地将嵌入式 Google Chrome 实例（称为 ChromeDriver）指向你的应用程序进行交互。Dusk 的 API 简单易用，你可以轻松编写手动与其交互的代码。看一看：

```php
$this->browse(function ($browser) {
    $browser->visit('/register')
        ->type('email', 'test@example.com')
        ->type('password', 'secret')
        ->press('Sign Up')
        ->assertPathIs('/dashboard');
});
```

使用 Dusk，实际上有一个浏览器启动您的整个应用程序并与之交互。这意味着您可以与您的 JavaScript 进行复杂的交互，并获取失败状态的截图——但这也意味着一切都会慢一些，并且比 Laravel 的基本应用程序测试套件更容易出现故障。

就个人而言，我发现 Dusk 最有用作回归测试套件，并且它比像 Selenium 这样的工具更有效。我不是用它来进行任何类型的测试驱动开发，而是用它来断言用户体验在应用程序继续开发过程中没有“退化”。可以将其视为在界面构建完成后编写有关用户界面的测试。

[Dusk 文档](https://oreil.ly/ZqNtP) 非常丰富，因此我在这里不会深入讨论，但我想向您展示如何使用 Dusk 的基础知识。

### 安装 Dusk

要安装 Dusk，请运行以下两个命令：

```php
composer require --dev laravel/dusk
php artisan dusk:install
```

然后编辑您的 *.env* 文件，将 `APP_URL` 变量设置为与您本地浏览器查看站点相同的 URL；例如 `http://mysite.test`。

要运行您的 Dusk 测试，只需运行 `php artisan dusk`。您可以传递从 PHPUnit 中习惯的所有相同参数（例如，`php artisan dusk --filter=my_best_test`）。

### 编写 Dusk 测试

要生成新的 Dusk 测试，请使用以下命令：

```php
php artisan dusk:make RatingTest
```

此测试将放置在 *tests/Browser/RatingTest.php* 中。

# 自定义 Dusk 环境变量

您可以通过创建一个名为 *.env.dusk.local* 的新文件来自定义 Dusk 的环境变量（如果您在不同的环境中工作，如“staging”，则可以替换 *.local*）。

要编写您的 Dusk 测试，请想象您正在指导一个或多个 Web 浏览器访问您的应用程序并执行某些操作。这就是语法的样子，正如您可以在 示例 12-35 中看到的那样。

##### 示例 12-35\. 一个简单的 Dusk 测试

```php
public function testBasicExample()
{
    $user = User::factory()->create();

    $this->browse(function ($browser) use ($user) {
        $browser->visit('login')
            ->type('email', $user->email)
            ->type('password', 'secret')
            ->press('Login')
            ->assertPathIs('/home');
    });
}
```

`$this->browse()` 创建一个浏览器，您将其传递给一个闭包；然后，在闭包内，您指示浏览器执行哪些操作。

需要注意的是——与 Laravel 的其他应用程序测试工具不同，这些工具模仿您的表单行为——Dusk 实际上正在启动浏览器，并向浏览器发送事件以输入这些文字，然后发送事件以按下该按钮。这是一个真实的浏览器，Dusk 完全驱动它。

您还可以通过向闭包添加参数“请求”更多浏览器，这样可以测试多个用户如何与网站交互（例如，使用聊天系统）。请参阅文档中的 示例 12-36。

##### 示例 12-36\. 多个 Dusk 浏览器

```php
$this->browse(function ($first, $second) {
    $first->loginAs(User::find(1))
        ->visit('home')
        ->waitForText('Message');

    $second->loginAs(User::find(2))
        ->visit('home')
        ->waitForText('Message')
        ->type('message', 'Hey Taylor')
        ->press('Send');

    $first->waitForText('Hey Taylor')
        ->assertSee('Jeffrey Way');
});
```

这里有大量的操作和断言可用，我们在此不会详细介绍（请查看文档），但让我们看看 Dusk 提供的其他一些工具。

### 身份验证和数据库

正如您在示例 12-36 中所见，身份验证的语法与 Laravel 应用测试的其余部分有所不同：`$browser->loginAs(*$user*)`。

# 避免与 Dusk 一起使用 RefreshDatabase 特性

不要与 Dusk 一起使用`RefreshDatabase`特性！改用`DatabaseMigrations`特性；`RefreshDatabase`使用的事务在请求间不会持久化。

### 与页面的交互

如果您曾经编写过 jQuery，则使用 Dusk 与页面进行交互会非常自然。查看示例 12-37 以了解使用 Dusk 选择项目的常见模式。

##### 示例 12-37。使用 Dusk 选择项目

```php
<-- Template -->
<div class="search"><input><button id="search-button"></button></div>
<button dusk="expand-nav"></button>
```

```php
// Dusk tests
// Option 1: jQuery-style syntax
$browser->click('.search button');
$browser->click('#search-button');

// Option 2: dusk="selector-here" syntax; recommended
$browser->click('@expand-nav');
```

正如您可以看到的那样，将`dusk`属性添加到您的页面元素中允许您以一种不会在以后显示或布局更改时改变的方式直接引用它们；当任何方法要求选择器时，请传递`@`符号，然后是您的`dusk`属性的内容。

让我们来看看您可以在`$browser`上调用的一些方法。

要处理文本和属性值，请使用以下方法：

`value(*$selector, $value = null*)`

如果只传递一个参数，则返回任何文本输入的值；如果传递第二个参数，则设置输入的值。

`text(*$selector*)`

获取非填充项（如`<div>`或`<span>`）的文本内容。

`attribute(*$selector, $attributeName*)`

返回与匹配`*$selector*`的元素上的特定属性的值。

用于处理表单和文件的方法包括以下内容：

`type(*$selector, $valueToType*)`

类似于`value()`，但实际上输入字符而不是直接设置值。

# Dusk 的选择器匹配顺序

使用诸如`type()`之类的方法来定位输入时，Dusk 将首先尝试匹配一个 Dusk 或 CSS 选择器，然后将查找具有提供的名称的输入，最后将尝试查找具有提供的名称的`<textarea>`。

`select(*$selector, $optionValue*)`

在可由`*$selector*`选择的下拉选择中选择值为`*$optionValue*`的选项。

`check(*$selector*)`、`uncheck(*$selector*)`

检查或取消检查由`*$selector*`选择的复选框。

`radio(*$selector, $optionValue*)`

在可由`*$selector*`选择的单选组中选择值为`*$optionValue*`的选项。

`attach(*$selector, $filePath*)`

将文件附加到由`*$selector*`选择的文件输入中的`*$filePath*`。

用于键盘和鼠标输入的方法有：

`clickLink(*$selector*)`

跟随文本链接到其目标。

`click(*$selector*)`、`mouseover(*$selector*)`

触发鼠标在`*$selector*`上的点击或悬停事件。

`drag(*$selectorToDrag, $selectorToDragTo*)`

将一个项目拖动到另一个项目。

`dragLeft()`、`dragRight()`、`dragUp()`、`dragDown()`

给定选择器的第一个参数和像素数的第二个参数，向给定方向拖动所选项目的这么多像素。

`keys(*$selector, $instructions*)`

根据`*$instructions*`中的指示在`*$selector*`的上下文中发送键按下事件。您甚至可以将修饰符与键入组合：

```php
$browser->keys('selector', 'this is ', ['{shift}', 'great']);
```

这将键入“this is GREAT。”正如您所见，将数组添加到要键入的项目列表中允许您将修饰符（用`{}`包装）与键入组合。您可以在[Facebook WebDriver source](https://oreil.ly/_gKa4)中看到所有可能的修饰符列表。

如果您只想将您的键序列发送到页面（例如触发键盘快捷键），则可以将您的应用或页面的顶级作为选择器。例如，如果它是一个 Vue 应用程序，并且顶级是 ID 为`app`的`<div>`：

```php
$browser->keys('#app', ['{command}', '/']);
```

### 等待中

因为 Dusk 与 JavaScript 交互，并且正在操作实际的浏览器，所以需要解决时间和超时以及“等待”的概念。Dusk 提供了几种方法，可以确保您的测试正确处理时间问题。其中一些方法对于与页面中故意缓慢或延迟的元素交互非常有用，但有些方法也仅仅是为了解决组件初始化时间。可用的方法包括以下内容：

`pause(*$milliseconds*)`

暂停 Dusk 测试的执行，以毫秒为单位。这是最简单的“等待”选项；它会使您发送给浏览器的未来命令在操作之前等待该时间量。

您可以在断言链中间使用此方法和其他等待方法，如下所示：

```php
$browser->click('chat')
    ->pause(500)
    ->assertSee('How can we help?');
```

`waitFor(*$selector, $maxSeconds = null*)`，`waitUntilMissing(*$selector*,` `*$maxSeconds*` `*= null*)`

等待页面上存在给定元素（`waitFor()`）或消失（`waitUntilMissing()`），或在可选的第二个参数的秒数后超时：

```php
$browser->waitFor('@chat', 5);
$browser->waitUntilMissing('@loading', 5);
```

`whenAvailable(*$selector, $callback*)`

类似于`waitFor()`，但接受闭包作为第二个参数，该闭包将定义在指定元素可用时要执行的操作：

```php
$browser->whenAvailable('@chat', function ($chat) {
    $chat->assertSee('How can we help you?');
});
```

`waitForText(*$text, $maxSeconds = null*)`

等待文本显示在页面上，或在可选的第二个参数的秒数后超时：

```php
$browser->waitForText('Your purchase has been completed.', 5);
```

`waitForLink(*$linkText, $maxSeconds = null*)`

等待具有给定链接文本的链接存在，或在可选的第二个参数的秒数后超时：

```php
$browser->waitForLink('Clear these results', 2);
```

`waitForLocation(*$path*)`

等待直到页面 URL 与提供的路径匹配：

```php
$browser->waitForLocation('auth/login');
```

`waitForRoute(*$routeName*)`

等待直到页面 URL 与提供的路由的 URL 匹配：

```php
$browser->waitForRoute('packages.show', [$package->id]);
```

`waitForReload()`

等待页面重新加载。

`waitUntil(*$expression*)`

等待直到提供的 JavaScript 表达式评估为 true：

```php
$browser->waitUntil('App.packages.length > 0', 7);
```

### 其他断言

正如我提到的，您可以使用 Dusk 对应用程序进行大量断言。以下是我最常用的一些——您可以在[Dusk 文档](https://oreil.ly/ZqNtP)中看到完整列表：

+   `assertTitleContains(*$text*)`

+   `assertQueryStringHas(*$keyName*)`

+   `assertHasCookie(*$cookieName*)`

+   `assertSourceHas(*$htmlSourceCode*)`

+   `assertChecked(*$selector*)`

+   `assertSelectHasOption(*$selectorForSelect, $optionValue*)`

+   `assertVisible(*$selector*)`

+   `assertFocused()`

+   `assertVue(*$dataLocation, $dataValue, $selector*)`

### 其他组织结构

到目前为止，我们所覆盖的内容使得我们能够测试页面上的各个元素。但是，我们经常会使用 Dusk 来测试更复杂的应用程序和单页面应用程序，这意味着我们需要围绕我们的断言建立组织结构。

我们遇到的第一个组织结构是 `dusk` 属性（例如，`<div dusk="abc">`，创建一个名为 `@abc` 的选择器，我们稍后可以引用它）和我们可以用来包装代码特定部分的闭包（例如，使用 `when` `Available()`）。

Dusk 提供了另外两个组织工具：页面和组件。让我们从页面开始。

#### 页面

页面是一个你将生成的类，包含两个功能部分：首先是一个 URL 和断言，用来定义应用程序中应该附加到这个 Dusk 页面的页面；其次是像我们在内联中使用的快捷方式（由我们 HTML 中的 `dusk="abc"` 属性生成的 `@abc` 选择器），但只针对这个页面，而无需编辑我们的 HTML。

让我们想象一下我们的应用程序有一个“创建包裹”页面。我们可以按照以下步骤生成一个 Dusk 页面：

```php
php artisan dusk:page CreatePackage
```

查看 示例 12-38 以查看我们生成的类会是什么样子。

##### 示例 12-38\. 生成的 Dusk 页面

```php
<?php

namespace Tests\Browser\Pages;

use Laravel\Dusk\Browser;

class CreatePackage extends Page
{
    /**
 * Get the URL for the page
 *
 * @return string
 */
    public function url()
    {
        return '/';
    }

    /**
 * Assert that the browser is on the page
 *
 * @param  Browser  $browser
 * @return void
 */
    public function assert(Browser $browser)
    {
        $browser->assertPathIs($this->url());
    }

    /**
 * Get the element shortcuts for the page
 *
 * @return array
 */
    public function elements()
    {
        return [
            '@element' => '#selector',
        ];
    }
}
```

`url()` 方法定义了 Dusk 应该期望此页面存在的位置，`assert()` 让您可以运行额外的断言来验证您是否在正确的页面上，而 `elements()` 则为 `@dusk` 风格的选择器提供了快捷方式。

让我们快速修改一下我们的“创建包裹”页面，使其看起来像 示例 12-39。

##### 示例 12-39\. 一个简单的“创建包裹”Dusk 页面

```php
class CreatePackage extends Page
{
    public function url()
    {
        return '/packages/create';
    }

    public function assert(Browser $browser)
    {
        $browser->assertTitleContains('Create Package');
        $browser->assertPathIs($this->url());
    }

    public function elements()
    {
        return [
            '@title' => 'input[name=title]',
            '@instructions' => 'textarea[name=instructions]',
        ];
    }
}
```

现在我们有了一个可用的页面，我们可以导航到它并访问其定义的元素：

```php
// In a test
$browser->visit(new Tests\Browser\Pages\CreatePackage)
    ->type('@title', 'My package title');
```

页面的一个常见用途是定义您希望在测试中执行的常见操作；可以将其视为 Dusk 的宏。您可以在页面上定义一个方法，然后从您的代码中调用它，就像在 示例 12-40 中所看到的。

##### 示例 12-40\. 定义并使用自定义页面方法

```php
class CreatePackage extends Page
{
    // ... url(), assert(), elements()

    public function fillBasicFields(Browser $browser, $packageTitle = 'Best package')
    {
        $browser->type('@title', $packageTitle)
            ->type('@instructions', 'Do this stuff and then that stuff');
    }
}
```

```php
$browser->visit(new CreatePackage)
    ->fillBasicFields('Greatest Package Ever')
    ->press('Create Package')
    ->assertSee('Greatest Package Ever');
```

#### 组件

如果你希望得到与 Dusk 页面提供的相同功能，但不限于特定的 URL，你可能想要使用 Dusk *组件*。这些类与页面非常相似，但不是绑定到 URL，而是绑定到选择器。

在 *NovaPackages.com* 中，我们有一个用于评分包和显示评分的小 Vue 组件。让我们为它创建一个 Dusk 组件：

```php
php artisan dusk:component RatingWidget
```

查看 示例 12-41 以查看将生成什么。

##### 示例 12-41\. 生成的 Dusk 组件的默认源

```php
<?php

namespace Tests\Browser\Components;

use Laravel\Dusk\Browser;
use Laravel\Dusk\Component as BaseComponent;

class RatingWidget extends BaseComponent
{
    /**
 * Get the root selector for the component
 *
 * @return string
 */
    public function selector()
    {
        return '#selector';
    }

    /**
 * Assert that the browser page contains the component
 *
 * @param  Browser  $browser
 * @return void
 */
    public function assert(Browser $browser)
    {
        $browser->assertVisible($this->selector());
    }

    /**
 * Get the element shortcuts for the component
 *
 * @return array
 */
    public function elements()
    {
        return [
            '@element' => '#selector',
        ];
    }
}
```

如你所见，这与 Dusk 页面基本相同，但我们将工作封装到 HTML 元素而不是 URL 中。其他方面基本相同。请看示例 12-42，看看我们的评分小部件在 Dusk 组件形式中的例子。

##### 示例 12-42\. 一个用于评分小部件的 Dusk 组件

```php
class RatingWidget extends BaseComponent
{
    public function selector()
    {
        return '.rating-widget';
    }

    public function assert(Browser $browser)
    {
        $browser->assertVisible($this->selector());
    }

    public function elements()
    {
        return [
            '@5-star' => '.five-star-rating',
            '@4-star' => '.four-star-rating',
            '@3-star' => '.three-star-rating',
            '@2-star' => '.two-star-rating',
            '@1-star' => '.one-star-rating',
            '@average' => '.average-rating',
            '@mine' => '.current-user-rating',
        ];
    }

    public function ratePackage(Browser $browser, $rating)
    {
        $browser->click("@{$rating}-star")
            ->assertSeeIn('@mine', $rating);
    }
}
```

使用组件的方式与使用页面的方式完全相同，就像你在示例 12-43 中看到的那样。

##### 示例 12-43\. 使用 Dusk 组件

```php
$browser->visit('/packages/tightenco/nova-stock-picker')
    ->within(new RatingWidget, function ($browser) {
        $browser->ratePackage(2);
        $browser->assertSeeIn('@average', 2);
    });
```

这是 Dusk 可以做的一个很好的简要概述。还有更多内容——更多断言、更多边缘案例、更多陷阱、更多示例——详见[Dusk 文档](https://oreil.ly/ZqNtP)，如果你计划使用 Dusk，我建议你仔细阅读一下。

# Pest

Pest 是 Laravel 的第三方测试框架。它是建立在 PHPUnit 之上的一层，提供定制的控制台输出、简单的并行测试和代码覆盖率、架构测试等功能。

Pest 还提供了不同的测试语法，受 Ruby 的 RSpec 启发。你可以使用 Pest 并享受其所有好处，而无需切换到其独特的测试语法，但如果你确实想试试看，可以参考示例 12-44 来看看语法是如何的。

##### 示例 12-44\. Pest 语法示例

```php
it('has a welcome page', function () {
    $response = $this->get('/');

    expect($response->status())->toBe(200);
});
```

欲了解更多关于 Pest 的信息，请访问[*pestphp.com*](https://pestphp.com)。

# TL;DR

Laravel 可以与任何现代 PHP 测试框架一起使用，但它对 PHPUnit 进行了优化（特别是如果你的测试扩展了 Laravel 的`TestCase`）。Laravel 的应用程序测试框架使得通过应用程序发送伪造的 HTTP 和控制台请求并检查结果变得简单。

Laravel 中的测试可以轻松而强大地与数据库、缓存、会话、文件系统、邮件和许多其他系统进行交互和断言。其中很多系统都有内置的伪造功能，使测试变得更加简单。你可以使用 Dusk 测试 DOM 和类似浏览器的交互。

Laravel 为了模拟、存根、监听器、虚拟对象或其他任何内容，引入了 Mockery，但 Laravel 的测试哲学是尽可能使用真实的协作对象。除非必要，否则不要伪造。
