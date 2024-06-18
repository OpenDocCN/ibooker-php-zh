# 第六章：前端组件

Laravel 主要被认为是一个 PHP 框架，但它也是 *全栈* 的，意味着它有一系列组件和约定，专注于生成前端代码。其中一些组件，如分页和消息包，是针对前端的 PHP 帮助程序，但 Laravel 还提供基于 Vite 的前端构建系统，一些非 PHP 资产的约定以及几个起始套件。

# Laravel 起始套件

在开箱即用的情况下，Laravel 提供了一个完整的构建系统，我们很快就会介绍，但它还包括易于安装的起始套件，其中包含模板、认证、样式、JavaScript 和用户注册及管理工作流程。

Laravel 的两个起始套件称为 Breeze 和 Jetstream。

**Breeze** 是一个更简单的选择；它提供了 Laravel 认证系统所需的所有路由、视图和样式，包括注册、登录、密码重置、密码确认、电子邮件确认以及“编辑个人资料”页面。Breeze 包含 Tailwind 样式，您可以选择使用 Blade 模板或者 Inertia 配合 React 或 Vue。

**Jetstream** 更加复杂和强大；它提供了 Breeze 所有的功能，但还增加了双因素认证、会话管理、API 令牌管理和团队管理功能。Jetstream 包含 Tailwind 样式，您可以选择 Livewire 或者 Inertia 配合 Vue。

###### 注意

**Inertia** 是一个前端工具，允许您在 JavaScript 中构建单页面应用程序，同时使用 Laravel 路由和控制器为每个视图提供路由和数据，就像传统的服务器渲染应用程序一样。了解更多，请访问 [*inertiajs.com*](https://inertiajs.com)。

如果您刚开始使用 Laravel，Breeze 更容易理解，并且可以仅使用 Blade。大多数 Laravel 应用程序仅使用 Breeze 就能正常工作。

**Jetstream** 没有仅限于 Blade 的选项，也没有 React 的选项；您需要使用某种前端框架。您的选择是 Vue/Inertia 或者 Livewire，后者允许您主要编写后端代码，但在 Laravel 应用程序中获取前端交互性。然而，Jetstream 更为强大，因此如果您熟悉 Laravel 并且了解 Livewire 或 Inertia，并且您的项目需要这些额外的功能，Jetstream 可能是您的最佳选择。

## **Laravel Breeze**

**Laravel Breeze** 是一个简单的起始套件，为普通的 Laravel 应用程序提供了一切所需，允许用户注册、登录和管理他们的个人资料。

### 安装 **Breeze**

**Breeze** 旨在安装在新应用程序上，因此通常是您启动新应用程序时安装的第一个内容：

```php
laravel new myProject
cd myProject
composer require laravel/breeze --dev
```

一旦将**Breeze**添加到您的项目中，您将运行其安装程序：

```php
php artisan breeze:install
```

一旦运行安装程序，您将提示选择一个堆栈：Blade、Inertia 配合 React、Inertia 配合 Vue 或者 API，后者用于支持像 Next.js 这样的非 Inertia 前端。这些堆栈在下一节中有详细解释。

安装了 **Breeze** 后，请确保运行您的迁移并构建您的前端：

```php
php artisan migrate
npm install
npm run dev
```

### Breeze 所带来的内容

Breeze 自动注册了用于注册、登录、注销、密码重置、电子邮件验证和密码确认页面的路由。这些路由位于新的 *routes/auth.php* 文件中。

Breeze 的非 API 形式还为用户仪表板和“编辑个人资料”页面注册了路由，并直接添加到 *routes/web.php* 文件中。

Breeze 的非 API 形式还发布了用于“编辑个人资料”页面、电子邮件验证、密码重置以及其他几个与身份验证相关的功能的控制器。此外，它还添加了 Tailwind、Alpine.js 和 PostCSS（用于 Tailwind）。除了这些共享的文件和依赖项外，每个堆栈还根据自身需求添加了独特的文件：

Breeze Blade

Breeze Blade 包含一系列 Blade 模板，涵盖了上述所有功能，你可以在 *resources/views/auth*、*resources/view/components*、*resources/views/profile* 等位置找到它们。

Breeze Inertia

两种 Inertia 堆栈都引入了 Inertia、Ziggy（用于在 JavaScript 中生成到 Laravel 路由的 URL 的工具）、Tailwind 的“forms”组件，以及使它们各自的前端框架功能正常运行所需的 JavaScript 包。它们还都发布了一个基本的 Blade 模板，该模板加载了 Inertia，以及在 *resources/js* 目录中发布页面的一系列 React/Vue 组件。

Breeze API

Breeze 的 API 堆栈安装的代码和包明显较少，但它也删除了所有新 Laravel 应用程序的现有引导文件。API 堆栈旨在准备一个应用程序仅作为独立的 Next.js 应用程序的 API 后端，因此它删除了 *package.json*、所有 JavaScript 和 CSS 文件，以及所有前端模板。

## Laravel Jetstream

Jetstream 延续了 Breeze 的功能，并增加了更多用于启动新应用的工具；但是，它的设置更复杂，配置选项更少，因此在选择 Jetstream 而不是 Breeze 之前，您需要知道自己确实需要它。

与 Breeze 类似，Jetstream 也发布路由、控制器、视图和配置文件。与 Breeze 一样，Jetstream 使用 Tailwind，并提供不同的技术“堆栈”选项。

然而，与 Breeze 不同，Jetstream 需要互动性，因此没有仅限于 Blade 的堆栈。相反，您有两个选择：Livewire（这是带有一些由 PHP 驱动的 JavaScript 互动功能的 Blade）或 Inertia/Vue（Jetstream 没有 React 形式）。

Jetstream 还通过引入团队管理功能、双因素认证、会话管理和个人 API 令牌管理扩展了 Breeze 的功能。

### 安装 Jetstream

Jetstream 旨在安装到新的 Laravel 应用程序中，您可以使用 Composer 安装它：

```php
laravel new myProject
cd myProject
composer require laravel/jetstream
```

一旦将 Jetstream 添加到您的项目中，您将运行其安装程序。与 Breeze 不同的是，您不会被要求选择堆栈；相反，您需要将堆栈 (`livewire` 或 `inertia`) 作为第一个参数传入。

```php
php artisan jetstream:install livewire
```

如果你想在 Jetstream 安装中添加团队管理功能，请在安装步骤中加入`--teams`标志：

```php
php artisan jetstream:install livewire --teams
```

安装完 Jetstream 后，请确保运行你的迁移并构建你的前端：

```php
php artisan migrate
npm install
npm run dev
```

### Jetstream 包含了什么

Jetstream 发布了大量的代码；以下是一个快速总结：

+   为用户模型添加双因素认证和个人资料照片功能（并添加/修改所需的迁移）

+   登录用户的仪表板

+   Tailwind, Tailwind forms, Tailwind typography

+   Laravel Fortify，Jetstream 构建在其上的后端身份验证组件

+   *app/Actions* 中的 Fortify 和 Jetstream 的“操作”

+   *resources/markdown* 中的条款和政策页面的 Markdown 文本

+   一个庞大的测试套件

# Fortify

Fortify 是一个无头身份验证系统。它为 Laravel 所需的所有身份验证功能提供路由和控制器，从登录和注册到密码重置等，供你选择的任何前端消费。

Jetstream 建立在 Fortify 之上，因此你实际上可以将 Jetstream 视为 Fortify 的众多可能前端之一。Jetstream 还添加了后端功能，因此显示了 Fortify 支持的身份验证系统可以有多强大。

Jetstream 的 Livewire 和 Inertia 配置分别具有稍有不同的依赖项和模板位置：

Jetstream Livewire

Jetstream 的 Livewire 模板为你的应用程序设置了与 Livewire 和 Alpine 协作的基础，并发布了前端的 Livewire 组件。它提供了：

+   Livewire

+   Alpine.js

+   *app/View/Components* 中的 Livewire 组件

+   *resources/views* 中的前端模板

Jetstream Inertia

Jetstream 的 Inertia 模板为你的应用程序设置了与 Inertia 和 Vue 协作的基础，并发布了前端的 Vue 组件。它提供了：

+   Inertia

+   Vue

+   *resources/js* 中的 Vue 模板

### 自定义你的 Jetstream 安装

Jetstream 构建在 Fortify 基础之上，因此有时自定义 Jetstream 就意味着要自定义 Fortify。你可以在 *config/fortify.php*, *config/jetstream.php*, *FortifyServiceProvider* 和 *JetstreamServiceProvider* 中更新任何配置设置。

虽然 Breeze 为你发布控制器以修改其行为，Jetstream 发布了动作，每个动作都是一个一次性的行为块，名称如 *ResetUserPassword.php* 和 *DeleteUser.php*。

### 更多 Jetstream 特性

Jetstream 使你的应用程序能够管理团队、个人 API 令牌、双因素认证以及跟踪和断开所有活动会话。你还可以在自己的代码中使用 Jetstream 的一些 UI 精美功能，如自定义闪存横幅。

要了解更多关于这一切是如何工作的信息，请查看详尽的 [Laravel Jetstream 文档](https://jetstream.laravel.com)。

# Laravel 的 Vite 配置

Vite 是一个本地前端开发环境，结合了开发服务器和基于 Rollup 的构建工具链。这听起来可能很多，但在 Laravel 中，主要用于将 CSS 和 JavaScript 资源捆绑在一起。

Laravel 提供了一个 NPM 插件和一个 Blade 指令，使得与 Vite 协作变得容易。它们默认包含在 Laravel 应用程序中，还有一个配置文件：*vite.config.js*。

看一下 [Example 6-1](https://example.org/#EX523) 来查看默认 *vite.config.js* 文件的内容。

##### 示例 6-1\. 默认的 *vite.config.js*

```php
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```

我们定义插件应该从中构建的文件 (`input`)，并说我们希望启用 “每次保存视图文件时刷新我的页面” 的功能 (`refresh`)。

默认情况下，Vite 从 [Example 6-1](https://example.org/#EX523) 中列出的两个文件中拉取，并在这些文件夹中的任何文件更改时自动刷新：

+   *app/View/Components/*

+   *lang/*

+   *resources/lang/*

+   *resources/views/*

+   *routes/*

现在，我们已经将 Vite 配置指向我们的 CSS 和 JavaScript 入口文件，我们将使用 `@vite` Blade 指令引用这些文件，正如你在 [Example 6-2](https://example.org/#EX524) 中所看到的。

##### 示例 6-2\. 使用 `@vite` Blade 指令

```php
<html>
<head>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
```

就这样！接下来，让我们看看如何使用 Vite 打包文件。

###### 注意

如果你的本地开发域名是安全的（HTTPS），你需要修改你的 *vite.config.js* 文件，指向你的凭据。如果你使用 Valet，这里有一个特殊的配置选项：

```php
// ...
export default defineConfig({
    plugins: [
        laravel({
            // ...
            valetTls: 'name-of_my-app-here.test',
        }),
    ],
});
```

## 使用 Vite 打包文件

最后，是时候打包我们的资产了。使用 Vite 有两种打包资产的方式：“build” 和 “dev”。

如果你想构建文件一次，无论是交付到生产环境还是进行本地测试，运行 `npm run build`，Vite 将会打包你的资产。然而，如果你在本地开发，可能更喜欢让 Vite 启动一个进程，监视你的视图文件变化，每当检测到视图文件变化时重新触发构建，并在浏览器中刷新页面。这就是 `npm run dev` 为你做的事情。

你的构建文件将会存放在应用的 *public/build/assets* 文件夹中，同时还有一个位于 *public/build/manifest.json* 的文件，它告诉 Laravel 和 Vite 如何从非构建路径引用到每一个构建文件。

###### 注意

默认情况下，Laravel 的 *.gitignore* 忽略 *public/build* 文件夹，所以确保在部署过程中运行 `npm run build`。

## Vite 开发服务器

当你运行 `npm run dev` 时，你会启动一个由 Vite 提供支持的实际 HTTP 服务器。Vite Blade 辅助程序会重写你的资产 URL，指向开发服务器上的相同位置，而不是你的本地域名，这使得 Vite 能够更快地更新和刷新你的依赖。

这意味着，如果你编写以下 Blade 调用：

```php
@vite(['resources/css/app.css', 'resources/js/app.js'])
```

在你的生产应用程序上看起来会是这样：

```php
<link rel="preload" as="style"
    href="http://my-app.test/build/assets/app-1c09da7e.css" />
<link rel="modulepreload"
    href="http://my-app.test/build/assets/app-ea0e9592.js" />
<link rel="stylesheet"
    href="http://my-app.test/build/assets/app-1c09da7e.css" />
<script type="module"
    src="http://my-app.test/build/assets/app-ea0e9592.js"></script>
```

但是，如果你的 Vite 服务器在本地运行，情况会是这样的：

```php
<script type="module" src="http://127.0.0.1:5173/@vite/client"></script>
<link rel="stylesheet" href="http://127.0.0.1:5173/resources/css/app.css" />
<script type="module" src="http://127.0.0.1:5173/resources/js/app.js"></script>
```

## 使用静态资产和 Vite

到目前为止，我们只覆盖了使用 Vite 加载 JavaScript 和 CSS。但是 Laravel 的 Vite 配置可以处理和版本化你的静态资产（如图像）。

如果你在 JavaScript 模板中工作，Vite 将会抓取任何*相对*静态资产的链接，并处理和版本化它们。任何*绝对*静态资产 Vite 都会忽略。

这意味着如果它们在 JavaScript 模板中，以下图像将会接受不同的处理。

```php
<!-- Ignored by Vite -->
<img src="/resources/images/soccer.jpg">
<!-- Processed by Vite -->
<img src="../resources/images/soccer.jpg">
```

如果你在 Blade 模板中工作，你需要采取两步来让 Vite 处理你的静态资产。首先，你需要使用`Vite::asset`门面调用来链接你的资产：

```php
<img src="{{ Vite::asset('resources/images/soccer.jpg') }}">
```

其次，你需要在*resources/js/app.js*文件中添加配置步骤，向 Vite 展示要导入的文件或文件夹：

```php
import.meta.glob([
  // Imports all the files in /resources/images/
  '../images/**',
]);
```

###### 警告

如果你使用`npm run dev`运行 Vite 服务器，服务器可以加载你的静态资产，*无需*你添加`import.meta.glob`配置。这意味着你可能认为它会出现，但在你的生产构建中将会失败。

## 与 JavaScript 框架和 Vite 一起工作

如果你想要与 Vue、React、Inertia 和/或单页面应用程序（SPA）一起工作，你可能需要引入一些特定的插件或设置一些特定的配置项。这里是你在最常见场景下所需的基本内容。

### Vite 和 Vue

要与 Vite 和 Vue 一起工作，首先安装 Vite 的 Vue 插件：

```php
npm install --save-dev @vitejs/plugin-vue
```

然后，你需要修改你的*vite.config.js*文件来调用 Vue 插件，并向其传递两个配置设置。第一个，`template.transformAssetUrls.base=null`，允许 Laravel 插件而不是 Vue 插件处理重写 URL。第二个，`template.transformAssetUrls.includeAbsolute=false`，允许 Vue 模板内的 URL 引用公共目录中的文件：

```php
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
  plugins: [
    laravel(['resources/js/app.js']),
    vue({
      template: {
        transformAssetUrls: {
          base: null,
          includeAbsolute: false,
        },
      },
    }),
  ],
});
```

### Vite 和 React

要与 Vite 和 React 一起工作，首先安装 Vite 的 React 插件：

```php
npm install --save-dev @vitejs/plugin-react
```

然后，你需要修改你的*vite.config.js*文件来调用 React 插件：

```php
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [
    laravel(['resources/js/app.js']),
    react(),
  ],
});
```

最后，在你导入 JavaScript 文件之前，在模板中添加`@viteReactRefresh` Blade 指令：

```php
@viteReactRefresh
@vite('resources/js/app.jsx')
```

### Vite 和 Inertia

如果你正在自行设置 Inertia，你需要 Inertia 能够解析你的页面组件。

这里是你可能会在*resources/js/app.js*文件中编写的代码示例，但你最好的选择是使用 Breeze、Jetstream 或 Inertia 文档安装 Inertia。

```php
import { createApp, h } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'

createInertiaApp({
  resolve: name => {
    const pages = import.meta.glob('./Pages/**/*.vue', { eager: true })
    return pages[`./Pages/${name}.vue`]
  },
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .mount(el)
  },
})
```

### Vite 和 SPAs

如果你正在构建 SPA，请从你的*vite.config.js*文件中移除*resources/css/app.css*，这将它从入口点中移除。

相反，通过在*resources/js/app.js*文件中在引入 bootstrap 之下添加这一行，将你的 CSS 导入到 JavaScript 中：

```php
import './bootstrap';
import '../css/app.css';
```

## 在 Vite 中使用环境变量

如果你想在你的 JavaScript 文件中使用环境变量，请以`VITE_`为前缀变量名，就像你在示例 6-3 中看到的那样。

##### 示例 6-3\. 在 vite.config.js 中引用环境变量

```php
// .env
VITE_BASE_URL=http://local-development-url.test
```

```php
// resources/js/app.js
const baseUrl = import.meta.env.VITE_BASE_URL;
```

每次运行`npm run dev`或`npm run build`时，它都会从*.env*中加载该环境变量，并将其注入到你的脚本中。

# 分页

尽管在 Web 应用程序中分页是如此常见，但实现起来仍然可能非常复杂。幸运的是，Laravel 默认已经集成了分页的概念，并且默认已经与 Eloquent 结果和路由器关联起来。

## 分页数据库结果

您最常见到分页的地方是当您显示数据库查询结果并且结果太多以至于无法在单个页面中显示时。Eloquent 和查询构建器都从当前页面请求的 `page` 查询参数中读取，并使用它在任何结果集上提供一个 `paginate()` 方法；您应该传递给 `paginate()` 的单个参数指示您希望每页显示多少结果。查看示例 6-4 了解其工作原理。

##### 示例 6-4\. 对查询构建器响应进行分页

```php
// PostController
public function index()
{
   return view('posts.index', ['posts' => DB::table('posts')->paginate(20)]);
}
```

示例 6-4 指定该路由应每页返回 20 篇文章，并根据 URL 的 `page` 查询参数定义当前用户所在的“页面”结果。所有 Eloquent 模型都具有相同的 `paginate()` 方法。

当您在视图中显示结果时，您的集合现在将具有一个 `links()` 方法，该方法将输出分页控件。（参见示例 6-5，我已将其简化以便包含在本书中。）

##### 示例 6-5\. 在模板中呈现分页链接

```php
// posts/index.blade.php
<table>
@foreach ($posts as $post)
    <tr><td>{{ $post->title }}</td></tr>
@endforeach
</table>

{{ $posts->links() }}

// By default, $posts->links() will output something like this:
<div class="...">
    <div>
        <p class="...">
            Showing
            <span class="...">1</span>
            to
            <span class="...">2</span>
            of
            <span class="...">5</span>
            results
        </p>
    </div>
    <div>
        <span class="...">
            <span aria-disabled="true" aria-label="&amp;laquo; Previous">
                <!-- SVG here for the ... ellipsis -->
            </span>
            <span class="...">1</span>
            <a href="http://myapp.com/posts?page=2" class="..." aria-label="...">
                2
            </a>
            <a href="http://myapp.com/posts?page=3" class="..." aria-label="...">
                3
            </a>
            <a href="http://myapp.com/posts?page=2" class="..."
                rel="next" aria-label="Next &amp;raquo;">
                <!-- SVG here for the ... ellipsis -->
            </a>
        </span>
    </div>
</div>
```

分页器使用 TailwindCSS 进行默认样式设置。如果您想使用 Bootstrap 样式，请在 `AppServiceProvider` 中调用 `Paginator::useBootstrap()`：

```php
use Illuminate\Pagination\Paginator;

public function boot(): void
{
    Paginator::useBootstrap();
}
```

# 自定义分页链接的数量

如果您希望控制当前页面两侧显示多少链接，您可以使用 `onEachSide()` 方法轻松自定义此数字：

```php
DB::table('posts')->paginate(10)->onEachSide(3);

// Outputs:
// 5 6 7 [8] 9 10 11
```

## 手动创建分页器

如果您不使用 Eloquent 或查询构建器，或者正在使用复杂的查询（例如使用 `groupBy` 的查询），您可能会发现自己需要手动创建分页器。幸运的是，您可以使用 `Illuminate\Pagination\Paginator` 或 `Illuminate\Pagination\LengthAwarePaginator` 类来实现这一点。

这两个类的区别在于 `Paginator` 只提供上一页和下一页按钮，但没有每一页的链接；`LengthAwarePaginator` 需要知道完整结果的长度，以便可以为每个单独的页面生成链接。您可能会发现在大结果集上使用 `Paginator` 是有用的，这样您的分页器不必了解可能昂贵运行的大量结果数量。

`Paginator` 和 `LengthAwarePaginator` 都要求您手动提取您希望传递给视图的内容子集。查看示例 6-6 了解示例。

##### 示例 6-6\. 手动创建分页器

```php
use Illuminate\Http\Request;
use Illuminate\Pagination\Paginator;

Route::get('people', function (Request $request) {
    $people = [...]; // huge list of people

    $perPage = 15;
    $offsetPages = $request->input('page', 1) - 1;

    // The Paginator will not slice your array for you
    $people = array_slice(
        $people,
        $offsetPages * $perPage,
        $perPage
    );

    return new Paginator(
        $people,
        $perPage
    );
});
```

# 消息包

Another common-but-painful feature in web applications is passing messages between various components of the app, when the end goal is to share them with the user. Your controller, for example, might want to send a validation message: “The `email` field must be a valid email address.” However, that particular message doesn’t just need to make it to the view layer; it actually needs to survive a redirect and then end up in the view layer of a different page. How do you structure this messaging logic?

The `Illuminate\Support\MessageBag` class is tasked with storing, categorizing, and returning messages that are intended for the end user. It groups all messages by key, where the keys are likely to be something like `errors` or `messages`, and it provides convenience methods for getting either all its stored messages or only those for a particular key, and outputting these messages in various formats.

You can spin up a new instance of `MessageBag` manually like in Example 6-7. To be honest, though, you likely won’t ever do this manually—​this is just a thought exercise to show how it works.

##### Example 6-7\. Manually creating and using a message bag

```php
$messages = [
    'errors' => [
        'Something went wrong with edit 1!',
    ],
    'messages' => [
        'Edit 2 was successful.',
    ],
];
$messagebag = new \Illuminate\Support\MessageBag($messages);

// Check for errors; if there are any, decorate and echo
if ($messagebag->has('errors')) {
    echo '<ul id="errors">';
    foreach ($messagebag->get('errors', '<li><b>:message</b></li>') as $error) {
        echo $error;
    }
    echo '</ul>';
}
```

Message bags are also closely connected to Laravel’s validators (you’ll learn more about these in “Validation”): when validators return errors, they actually return an instance of `MessageBag`, which you can then pass to your view or attach to a redirect using `redirect('route')->withErrors($messagebag)`.

Laravel passes an empty instance of `MessageBag` to every view, assigned to the variable `$errors`; if you’ve flashed a message bag using `withErrors()` on a redirect, it will get assigned to that `$errors` variable instead. That means every view can always assume it has an `$errors` `MessageBag` that it can check wherever it handles validation, which leads to Example 6-8 as a common snippet developers place on every page.

##### Example 6-8\. Error bag snippet

```php
// partials/errors.blade.php
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
        @foreach ($errors as $error)
            <li>{{ $error }}</li>
        @endforeach
        </ul>
    </div>
@endif
```

# Missing $errors Variable

If you have any routes that aren’t under the `web` middleware group, they won’t have the session middleware, which means they won’t have this `$errors` variable available.

Sometimes you need to differentiate message bags not just by key (`notices` versus `errors`) but also by component. Maybe you have a login form and a signup form on the same page; how do you differentiate them?

When you send errors along with a redirect using `withErrors()`, the second parameter is the name of the bag: `redirect('dashboard')->withErrors($validator, 'login')`. Then, on the dashboard, you can use `$errors->login` to call all of the methods you saw before: `any()`, `count()`, and more.

# String Helpers, Pluralization, and Localization

As developers, we tend to look at blocks of text as big placeholder divs, waiting for the client to put real content into them. Seldom are we involved in any logic inside these blocks.

但在一些情况下，你会感谢 Laravel 提供的字符串操作工具。

## 字符串助手和复数形式

Laravel 提供了一系列用于操作字符串的助手。它们作为 `Str` 类的方法可用（例如，`Str::plural()`）。

# Laravel 字符串和数组全局助手

旧版本的 Laravel 包括全局辅助函数，它们是 `Str` 和 `Arr` 方法的别名。这些全局的 `str_` 和 `array_` 辅助函数在 Laravel 6 版本中被移除，并导出到一个单独的包中。如果你愿意，你可以通过 Composer 安装 `laravel/helpers` 包：`composer require laravel/helpers`。

Laravel 的 [文档](https://oreil.ly/vssfi) 详细介绍了所有字符串助手，但以下是一些常用的助手：

`e()`

`html_entities()` 的快捷方式；对所有 HTML 实体进行编码，以确保安全性。

`Str::startsWith()`, `Str::endsWith()`, `Str::contains()`

检查字符串（第一个参数）是否以另一个字符串（第二个参数）开头、结尾或包含。

`Str::is()`

检查字符串（第二个参数）是否与特定模式（第一个参数）匹配 —— 例如，`foo*` 将匹配 `foobar` 和 `foobaz`。

`Str::slug()`

将字符串转换为带连字符的 URL 类型的 slug。

`Str::plural(*word*, *count*)`, `Str::singular()`

使单词变为复数或单数；仅支持英语（例如，`Str::plural('dog')` 返回 `dogs`； `Str::plural('dog,' 1'))` 返回 `dog`）。

`Str::camel()`, `Str::kebab()`, `Str::snake()`, `Str::studly()`, `Str::title()`

将提供的字符串转换为不同的大小写 "case"。

`Str::after()`, `Str::before()`, `Str::limit()`

对字符串进行修剪并提供子字符串。`Str::after()` 返回给定字符串之后的所有内容，而 `Str::before()` 返回给定字符串之前的所有内容（两者都接受完整的字符串作为第一个参数，以及作为第二个参数用于切割的字符串）。`Str::limit()` 将字符串（第一个参数）截断为指定数量的字符（第二个参数）。

`Str::markdown(*string*, *options*)`

将 Markdown 转换为 HTML。您可以在 [PHP League 网站](https://oreil.ly/3zOdm) 上阅读有关您可以传递的选项的更多信息。

`Str::replace(*search*, *replace*, *subject*, *caseSensitive*)`

在主题字符串中查找搜索字符串出现的位置，并用替换字符串替换它。如果 caseSensitive 参数为 true，则只有当出现与搜索案例匹配时才进行替换（例如，`Str::replace('Running', 'Going', 'Laravel Up and Running', true)` 返回 `'Laravel Up and Going'`）。

## 本地化

本地化使您能够定义多种语言，并将任何字符串标记为翻译目标。您可以设置一个回退语言，甚至处理复数形式的变化。

在 Laravel 中，您需要在页面加载过程中的某个时刻设置“应用程序区域设置”，以便本地化助手知道从哪个翻译桶中获取翻译。每个“区域设置”通常与一个翻译相关联，通常看起来像“en”（英语）。您可以使用`App::setLocale($localeName)`来实现这一点，您可能会将其放在服务提供程序中。现在，您可以将其放在`AppServiceProvider`的`boot()`方法中，但如果您最终拥有多个与区域设置相关的绑定，可能需要创建一个`LocaleServiceProvider`。

您可以在*config/app.php*中定义您的回退区域设置，那里应该有一个`fallback_locale`键。这允许您为应用程序定义一个默认语言，在无法找到请求的区域设置的翻译时，Laravel 将使用它。

### 基本本地化

那么，我们如何调用翻译字符串呢？有一个辅助函数，`__($key)`，它将为传递的键在当前区域设置中获取字符串，或者如果不存在，则从默认区域设置中获取。在 Blade 中，您还可以使用`@lang()`指令。示例 6-9 演示了基本翻译的工作方式。我们将使用“返回仪表板”链接的示例作为详细页面顶部的示例。

##### 示例 6-9\. `__()` 的基本使用

```php
// Normal PHP
<?php echo __('navigation.back'); ?>
```

```php
// Blade
{{ __('navigation.back') }}

// Blade directive
@lang('navigation.back')
```

假设我们现在正在使用`es`区域设置。首先，我们需要发布用于修改的`lang`文件：

```php
php artisan lang:publish
```

此命令会将默认的 Laravel `lang`文件发布到应用程序的根目录。您需要创建一个文件来定义与导航相关的翻译，*lang/en/navigation.php*，并返回一个包含键名为`back`的 PHP 数组，如示例 6-10 所示。

##### 示例 6-10\. 示例 lang/en/navigation.php 文件

```php
<?php

return [
    'back' => 'Return to dashboard',
];
```

现在，为了使其可翻译，让我们在*lang*下创建一个*es*目录，并创建其自己的*navigation.php*文件，正如您在示例 6-11 中所见。

##### 示例 6-11\. 示例 lang/es/navigation.php 文件

```php
<?php

return [
    'back' => 'Volver al panel',
];
```

现在让我们尝试在我们的应用程序中使用该翻译键，在示例 6-12 中。

##### 示例 6-12\. 使用翻译

```php
// routes/web.php
Route::get('/es/contacts/show/{id}', function () {
    // Set the locale manually, for this example, instead of in a service provider
    App::setLocale('es');
    return view('contacts.show');
});

// resources/views/contacts/show.blade.php
<a href="/contacts">{{ __('navigation.back') }}</a>
```

### 本地化中的参数

前面的示例比较简单。让我们深入了解一些更复杂的内容。如果我们想要定义我们要返回的*哪一个*仪表板呢？看看示例 6-13。

##### 示例 6-13\. 翻译中的参数

```php
// lang/en/navigation.php
return [
    'back' => 'Back to :section dashboard',
];

// resources/views/contacts/show.blade.php
{{ __('navigation.back', ['section' => 'contacts']) }}
```

如您所见，用冒号(`:section`)标记一个单词，将其视为可以替换的占位符。`__()`的第二个可选参数是一个替换占位符的值数组。

### 本地化中的复数形式

我们已经讨论了复数形式，现在想象一下您正在定义自己的复数形式规则。有两种方法可以做到这一点；我们将从最简单的方法开始，如示例 6-14 所示。

##### 示例 6-14\. 定义具有复数形式选项的简单翻译

```php
// lang/en/messages.php
return [
    'task-deletion' => 'You have deleted a task|You have successfully deleted tasks',
];

// resources/views/dashboard.blade.php
@if ($numTasksDeleted > 0)
    {{ trans_choice('messages.task-deletion', $numTasksDeleted) }}
@endif
```

正如您所见，我们有一个`trans_choice()`方法，它将受影响项目的计数作为其第二个参数；从中，它将确定要使用哪个字符串。

你还可以使用与 Symfony 的更复杂的`Translation`组件兼容的任何翻译定义；参见示例 6-15 作为示例。

##### 示例 6-15\. 使用 Symfony `Translation`组件

```php
// lang/es/messages.php
return [
    'task-deletion' => "{0} You didn't manage to delete any tasks.|" .
        "[1,4] You deleted a few tasks.|" .
        "[5,Inf] You deleted a whole ton of tasks.",
];
```

### 使用 JSON 存储默认字符串作为键

本地化的一个常见难点是确保有一个良好的定义键命名空间的系统——例如，记住三四级嵌套的键或不确定站点中使用两次的短语应该使用哪个键。

与基于 slug 键/字符串值对系统的另一种选择是使用主语言字符串作为键存储您的翻译，而不是使用虚构的 slug。您可以通过在*lang*目录中以 JSON 格式存储翻译文件，并使用反映区域设置的文件名来指示 Laravel，以表明您正在这样工作（参见示例 6-16）。

##### 示例 6-16\. 使用 JSON 翻译和`__()`助手

```php
// In Blade
{{ __('View friends list') }}
```

```php
// lang/es.json
{
  'View friends list': 'Ver lista de amigos'
}
```

这是利用了`__()`翻译助手的一个事实，如果它找不到当前语言的匹配键，它将只显示键。如果您的键是应用程序默认语言中的字符串，那么这比如`widgets.friends.title`之类的做法更合理。

# 测试

在本章中，我们主要关注了 Laravel 的前端组件。这些组件不太可能成为单元测试的对象，但有时可能会在集成测试中使用。

## 测试消息和错误包

传递消息和错误包的消息的主要测试方法有两种。首先，您可以在应用程序测试中执行一个行为，设置最终将在某个地方显示的消息，然后重定向到该页面，并断言显示适当的消息。

其次，对于错误（这是最常见的用例），您可以使用`$this->assertSessionHasErrors($bindings = [])`断言会话存在错误。看一下示例 6-17，看看可能的显示方式。

##### 示例 6-17\. 断言会话存在错误

```php
public function test_missing_email_field_errors()
{
    $this->post('person/create', ['name' => 'Japheth']);
    $this->assertSessionHasErrors(['email']);
}
```

为了使示例 6-17 通过，您需要在该路由上添加输入验证。我们将在第七章中介绍这个。

## 翻译和本地化

测试本地化的最简单方法是使用应用程序测试。设置适当的上下文（无论是通过 URL 还是会话），使用`get()`“访问”页面，并断言您看到适当的内容。

## 在测试中禁用 Vite

如果您想在测试期间禁用 Vite 的资产解析，可以通过在测试顶部调用`withoutVite()`方法完全禁用 Vite：

```php
public function test_it_runs_without_vite()
{
    $this->withoutVite();

    // Test stuff
}
```

# TL;DR

作为一个全栈框架，Laravel 提供了用于前端和后端的工具和组件。

Vite 是一个构建工具和开发服务器，Laravel 在其上构建，帮助处理、压缩和版本化 JavaScript、CSS 和静态资源（如图像）。

Laravel 还提供了针对前端的其他内部工具，包括用于实现分页、消息和错误包以及本地化的工具。
