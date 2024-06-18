# 第四章 刀片模板

与大多数其他后端语言相比，PHP 实际上作为模板语言运行相对良好。但它也有其缺点，而且在代码中到处使用 `<?php` 简直丑陋不堪，因此你可以期待大多数现代框架提供一种模板语言。

Laravel 提供了一个名为*Blade*的自定义模板引擎，受.NET 的 Razor 引擎启发。它拥有简洁的语法、浅显的学习曲线、强大直观的继承模型以及易于扩展的特点。

要快速了解 Blade 的编写方式，请查看示例 4-1。

##### 示例 4-1 刀片示例

```php
<h1>{{ $group->title }}</h1>
{!! $group->heroImageHtml() !!}

@forelse ($users as $user)
    • {{ $user->first_name }} {{ $user->last_name }}<br>
@empty
    No users in this group.
@endforelse
```

正如你所见，Blade 使用花括号来进行“echo”，并引入了一种约定：其自定义标签称为“指令”，以`@`作为前缀。你将使用指令来处理所有的控制结构，还可以用于继承以及任何想要添加的自定义功能。

Blade 的语法简洁明了，因此在核心上，与其他选择相比，使用起来更加愉悦和整洁。但是一旦在模板中需要复杂的任何内容——如嵌套继承、复杂条件或递归——Blade 便开始展现其真正的优势。就像最好的 Laravel 组件一样，它可以处理复杂的应用程序需求，使其变得简单易操作。

另外，由于所有 Blade 语法都会被编译成普通的 PHP 代码并进行缓存，因此它非常快速，并且如果需要的话，可以在 Blade 文件中使用原生 PHP。但我建议尽可能避免使用 PHP——通常，如果需要做任何 Blade 或自定义 Blade 指令无法实现的事情，那就不应该放在模板中。

# 使用 Twig 与 Laravel

与许多基于 Symfony 的框架不同，Laravel 默认不使用 Twig。但是如果你非常喜欢 Twig，可以使用[TwigBridge 包](https://oreil.ly/9z_3t)，它可以轻松地在 Blade 之外使用 Twig。

# 输出数据

正如在示例 4-1 中所见，`{{` 和 `}}` 用于包装你想要输出的 PHP 部分。 ``{{ `*$variable*` }}`` 类似于纯 PHP 中的 `<?= $variable ?>`。

但有一个方面是不同的，你可能已经猜到了：Blade 默认使用 PHP 的`htmlentities()`来转义所有的输出，以保护用户免受恶意脚本的插入。这意味着 ``{{ `*$variable*` }}`` 在功能上等同于 `<?=` ``htmlentities(`*$variable*`)`` `?>`。如果希望不转义地进行输出，请改用 `{!!` 和 `!!}`。

# 控制结构

Blade 中的大多数控制结构都非常熟悉，很多直接回显相同 PHP 标签的名称和结构。

Blade 提供了一些便利的辅助函数，但总体来说，控制结构看起来比在 PHP 中更清晰。

## 条件语句

首先，让我们看一下允许逻辑的控制结构。

### @if

Blade 的 `@if (*$condition*)` 编译为 `<?php if (*$condition*): ?>`。`@else`、`@elseif` 和 `@endif` 也编译成 PHP 中完全相同的语法样式。查看 示例 4-2 获取一些示例。

##### 示例 4-2\. `@if`、`@else`、`@elseif` 和 `@endif`

```php
@if (count($talks) === 1)
    There is one talk at this time period.
@elseif (count($talks) === 0)
    There are no talks at this time period.
@else
    There are {{ count($talks) }} talks at this time period.
@endif
```

就像原生 PHP 条件语句一样，你可以随意混合和匹配这些。它们没有任何特殊逻辑；字面上有一个解析器寻找 ``@if (`*$condition*`)`` 的形状，并用适当的 PHP 代码替换它。

### `@unless` 和 `@endunless`

`@unless`，另一种新语法，PHP 中没有直接的等价物。它是 `@if` 的直接反义词。`@unless (*$condition*)` 等同于 `<?php if (! *$condition*): ?>`。你可以在 示例 4-3 中看到它的使用。

##### 示例 4-3\. `@unless` 和 `@endunless`

```php
@unless ($user->hasPaid())
    You can complete your payment by switching to the payment tab.
@endunless
```

## 循环

接下来，让我们看看循环。

### `@for`、`@foreach` 和 `@while`

`@for`、`@foreach` 和 `@while` 在 Blade 中与 PHP 中的工作方式相同；参见示例 4-4、4-5 和 4-6。

##### 示例 4-4\. `@for` 和 `@endfor`

```php
@for ($i = 0; $i < $talk->slotsCount(); $i++)
    The number is {{ $i }}<br>
@endfor
```

##### 示例 4-5\. `@foreach` 和 `@endforeach`

```php
@foreach ($talks as $talk)
    • {{ $talk->title }} ({{ $talk->length }} minutes)<br>
@endforeach
```

##### 示例 4-6\. `@while` 和 `@endwhile`

```php
@while ($item = array_pop($items))
    {{ $item->orSomething() }}<br>
@endwhile
```

### `@forelse` 和 `@endforelse`

`@forelse` 是 `@foreach`，如果你正在迭代的对象为空，还允许你编写回退。我们在本章开始时看到它的运作；示例 4-7 展示了另一个例子。

##### 示例 4-7\. `@forelse`

```php
@forelse ($talks as $talk)
    • {{ $talk->title }} ({{ $talk->length }} minutes)<br>
@empty
    No talks this day.
@endforelse
```

# 模板继承

Blade 提供了一种模板继承结构，允许视图扩展、修改和包含其他视图。

让我们看看 Blade 如何结构化继承。

## 使用 @section/@show 和 @yield 定义区段

让我们从顶层 Blade 布局开始，就像在 示例 4-8 中一样。这是一个通用页面包装器的定义，稍后我们将在其中放置特定页面内容。

##### 示例 4-8\. Blade 布局

```php
<!-- resources/views/layouts/master.blade.php -->
<html>
    <head>
        <title>My Site | @yield('title', 'Home Page')</title>
    </head>
    <body>
        <div class="container">
            @yield('content')
        </div>
        @section('footerScripts')
            <script src="app.js"></script>
        @show
    </body>
</html>
```

这看起来有点像普通的 HTML 页面，但你可以看到我们在两个地方 *yielded*（`title` 和 `content`），并在第三个地方定义了 *section*（`footerScripts`）。这里有三个 Blade 指令：`@yield('content')` 单独使用，`@yield('title', 'Home Page')` 带有定义的默认值，以及带有实际内容的 `@section/@show`。

尽管它们看起来各有些不同，*这三者本质上是一样的*。它们都定义了一个具有给定名称的区段（第一个参数），稍后可以扩展，并且都定义了如果未扩展该部分要执行的操作。它们可以通过提供字符串回退（`'Home Page'`）、无回退（如果未扩展，则什么也不显示）、或整个块回退（在这种情况下是 `<script src="app.js"></script>`）来做到这一点。

有什么不同？显然，`@yield('content')` 没有默认内容。但是另外，`@yield('title')` 中的默认内容只有在未被扩展时才会显示。如果已被扩展，其子部分将无法通过程序访问默认值。另一方面，`@section/@show` 不仅定义了一个默认值，而且以这种方式定义的默认内容将通过`@parent`提供给其子级。

一旦你有了像这样的父布局，你可以在新的模板文件中扩展它，就像在示例 4-9 中一样。

##### 示例 4-9\. 扩展 Blade 布局

```php
<!-- resources/views/dashboard.blade.php -->
@extends('layouts.master')

@section('title', 'Dashboard')

@section('content')
    Welcome to your application dashboard!
@endsection

@section('footerScripts')
    @parent
    <script src="dashboard.js"></script>
@endsection
```

# @show 与 @endsection 的区别

你可能已经注意到，示例 4-8 使用了`@section/@show`，但示例 4-9 使用了`@section/@endsection`。它们有什么区别？

在父模板中定义一个部分的位置时，请使用`@show`。在子模板中定义模板的内容时，请使用`@endsection`。

此子视图允许我们涵盖 Blade 继承中的一些新概念。

### @extends

在示例 4-9 中，通过`@extends('layouts.master')`，我们定义了这个视图不应单独呈现，而是应扩展另一个视图。这意味着它的角色是定义各个部分的内容，而不是独立存在。它几乎更像是一系列内容桶，而不是一个 HTML 页面。这行还定义了它扩展的视图位于*resources/views/layouts/master.blade.php*。

每个文件应仅扩展另一个文件，并且`@extends`调用应为文件的第一行。

### @section 和 @endsection

使用`@section('title', '仪表板')`，我们为第一个部分`title`提供了内容。由于内容很短，我们不是使用`@section`和`@endsection`，而是直接使用了一个快捷方式。这允许我们将内容作为`@section`的第二个参数传递，然后继续进行。如果看到没有`@endsection`的`@section`有点令人不安，可以使用常规语法。

对于`@section('content')`及其后续内容，我们使用常规语法来定义`content`部分的内容。现在我们只是简单地添加一个问候语。请注意，当在子视图中使用`@section`时，应使用`@endsection`（或其别名`@stop`），而不是保留用于在父视图中定义部分的`@show`。

### @parent

最后，使用`@section('footerScripts')`及其后续内容，我们使用常规语法来定义`footerScripts`部分的内容。

但请记住，我们实际上已经在主布局中定义了该内容（或者至少是它的“默认”）。所以这一次，我们有两个选择：我们可以覆盖父视图中的内容，或者我们可以添加到其中。

你可以看到，通过在部分内部使用`@parent`指令，我们可以选择包含父级的内容。如果没有这样做，此部分的内容将完全覆盖父级为该部分定义的任何内容。

## 包含视图部分

现在我们已经建立了继承的基础，我们可以执行一些更多的技巧。

### `@include`

如果我们在一个视图中并且想要引入另一个视图，该怎么办？也许我们有一个“注册”按钮的呼叫到动作，我们想在整个站点重复使用它。也许我们想每次使用时自定义按钮文本。看看示例 4-10。

##### 示例 4-10\. 使用`@include`包含视图局部文件

```php
<!-- resources/views/home.blade.php -->
<div class="content" data-page-name="{{ $pageName }}">
    <p>Here's why you should sign up for our app: <strong>It's Great.</strong></p>

    @include('sign-up-button', ['text' => 'See just how great it is'])
</div>

<!-- resources/views/sign-up-button.blade.php -->
<a class="button button--callout" data-page-name="{{ $pageName }}">
    <i class="exclamation-icon"></i> {{ $text }}
</a>
```

`@include`引入局部文件，并可选择将数据传递给它。请注意，不仅可以通过`@include`的第二个参数显式地向包含传递数据，而且还可以引用包含文件中任何可用于包含视图的变量（例如本示例中的`$pageName`）。再次强调，您可以随心所欲地做任何操作，但是我建议您始终明确传递您打算使用的每个变量，以确保清晰性。

您还可以使用`@includeIf`、`@includeWhen`和`@includeFirst`指令，如示例 4-11 所示。

##### 示例 4-11\. 有条件地包含视图

```php
{{-- Include a view if it exists --}}
@includeIf('sidebars.admin', ['some' => 'data'])

{{-- Include a view if a passed variable is truth-y --}}
@includeWhen($user->isAdmin(), 'sidebars.admin', ['some' => 'data'])

{{-- Include the first view that exists from a given array of views --}}
@includeFirst(['customs.header', 'header'], ['some' => 'data'])
```

### `@each`

您可能会想象一些情况，您需要遍历数组或集合并`@include`每个项目的局部文件。有一个指令可以做到：`@each`。

假设我们有一个由模块组成的侧边栏，并且我们想包含多个模块，每个模块都有不同的标题。看看示例 4-12。

##### 示例 4-12\. 在循环中使用视图局部文件`@each`

```php
<!-- resources/views/sidebar.blade.php -->
<div class="sidebar">
    @each('partials.module', $modules, 'module', 'partials.empty-module')
</div>

<!-- resources/views/partials/module.blade.php -->
<div class="sidebar-module">
    <h1>{{ $module->title }}</h1>
</div>

<!-- resources/views/partials/empty-module.blade.php -->
<div class="sidebar-module">
    No modules :(
</div>
```

考虑`@each`语法。第一个参数是视图局部文件的名称。第二个是要迭代的数组或集合。第三个是每个项目的变量名（在这种情况下，是`$modules`数组中的每个元素），将作为视图传递给视图。第四个可选参数是在数组或集合为空时显示的视图（或者，您可以在此处传递一个字符串，该字符串将用作模板）。

## 使用组件

Laravel 还提供了另一种在视图之间包含内容的模式：*组件*。组件在您发现自己使用视图局部文件并将大块内容作为变量传递给它们的情况下最有意义。看看示例 4-13，以查看一个模态框或弹出窗口的示例，可能会在响应错误或其他操作时向用户发出警告。

##### 示例 4-13\. 将模态框作为一个奇怪的视图局部文件

```php
<!-- resources/views/partials/modal.blade.php -->
<div class="modal">
    <h2>{{ $title }}</h2>
    <div>{!! $content !!}</div>
    <div class="close button etc">...</div>
</div>

<!-- in another template -->
@include('partials.modal', [
    'title' => 'Insecure password',
    'content' => '<p>The password you have provided is not valid. Here are the rules
    for valid passwords: [...]</p><p><a href="#">...</a></p>'
])
```

这对于这些可怜的变量来说太多了，它正好适合成为一个组件。

Laravel 的组件是结构化视图局部文件的另一种方式，更接近于前端框架（如 Vue）中组件的工作方式。它们可能更熟悉于前端开发者，但与视图局部文件相比，它们也有一些显著的好处，包括更容易将大段的模板代码传递给它们。

看看示例 4-14，看看如何使用组件重构示例 4-13。

##### 示例 4-14\. 一个更合适的组件作为模态框

```php
<!-- resources/views/components/modal.blade.php -->
<div class="modal">
    <h2>{{ $title }}</h2>
    <div>{{ $slot }}</div>
    <div class="close button etc">...</div>
</div>

<!-- in another template -->
<x-modal title="Insecure password">
    <p>The password you have provided is not valid.
    Here are the rules for valid passwords: [...]</p>

    <p><a href="#">...</a></p>
</x-modal>
```

正如您在示例 4-14 中所看到的，组件允许我们将 HTML 从拥挤的变量字符串中提取出来，并重新回到模板空间。

让我们深入了解组件的更多功能、它们的结构以及我们如何编写它们。

### 创建组件

组件可以存在为纯粹的 Blade 模板（*匿名组件*），或者作为由 PHP 类支持的 Blade 模板，该类注入数据和功能（*基于类的组件*）。

如果您只需要模板，可以使用`--view`标志生成您的组件：

```php
php artisan make:component modal --view
```

如果您还想生成 PHP 类，请排除该标志：

```php
php artisan make:component modal
```

如果您希望将组件分组到文件夹中，可以使用`.`分隔符：

```php
# To create it:
php artisan make:component modals.cancellation
```

```php
// To use it:
<x-modals.cancellation />
```

### 将数据传递给组件

有四种方法可以将数据传递给组件：字符串属性、PHP 属性、默认插槽和命名插槽。

#### 通过属性将数据传递给组件

让我们从属性开始。您可以通过不带前缀的属性直接将字符串传递给组件，或者您可以使用冒号前缀传递 PHP 变量和表达式，就像您在示例 4-15 中看到的那样。

##### 示例 4-15\. 通过属性将数据传递给组件

```php
<!-- Passing the data in -->
<x-modal title="Title here yay" :width="$width" />
```

```php
<!-- Accessing the data in the template -->
<div style="width: {{ $width }}">
    <h1>{{ $title }}</h1>
</div>
```

对于基于类的组件，您需要在 PHP 类中定义每个属性，并将其设置为类的公共属性，就像在示例 4-16 中所示。

##### 示例 4-16\. 在组件类上将属性定义为公共属性

```php
class Modal extends Component
{
    public function __construct(
        public string $title,
        public string $width,
    ) {}
}
```

对于匿名组件，您需要在模板顶部的`props`数组中定义属性：

```php
@props([
    'width',
    'title',
])

<div style="width: {{ $width }}">
    <h1>{{ $title }}</h1>
</div>
```

#### 通过插槽将数据传递给组件

在示例 4-14 中，您可能已经注意到模态框的内容被称为变量`$slot`。但是这是从哪里来的？

默认情况下，每个组件在引用时具有开放和关闭标记时都有一个`$slot`变量，并且它填充了这两个标记之间的所有 HTML。在示例 4-14 中，`$slot`变量包含两个`<p>`标记及其之间的所有内容。

但是如果您需要两个或更多插槽怎么办？您可以添加不止一个默认插槽，为每个插槽分配自己的名称和变量。让我们重新设计示例 4-14，假设我们想在一个插槽中定义标题；看看示例 4-17。

##### 示例 4-17\. 定义多个插槽

```php
<x-modal>
    <x-slot:title>
        <h2 class="uppercase">Password requirements not met</h2>
    </x-slot>

    <p>The password you have provided is not valid.
    Here are the rules for valid passwords: [...]</p>

    <p><a href="#">...</a></p>
</x-modal>
```

这个新的`$slot`变量的内容将作为`$title`变量在组件模板中可访问，就像之前的属性一样。

### 组件方法

有时，在组件中添加一个执行某些逻辑的辅助方法可能会有所帮助。一个常见的模式是将这些方法用于您希望保持模板之外的复杂逻辑检查。

组件允许您在模板中通过在方法名称前加上`$`来调用其关联的 PHP 类上的任何公共方法，就像您在示例 4-18 中看到的那样。

##### 示例 4-18\. 定义和调用组件方法

```php
// in the component definition
public function isPromoted($item)
{
    return $item->promoted_at !== null && ! $item->promoted_at->isPast();
}
```

```php
<!-- in the template -->
<div>
    @if ($isPromoted($item))
        <!-- show promoted badge -->
    @endif
    <!-- ... -->
</div>
```

### 属性收集袋

我们将传递给组件的大多数属性都将是命名的、具体的，并且类似于向 PHP 函数传递参数。

但有时我们只需传递一些松散的 HTML 属性，几乎总是为了将它们分配给模板的根元素。

使用组件，你可以一次性获取所有这些属性，使用`$attributes`变量。这个变量捕获所有未定义为属性的属性，并允许你输出它们（将其视为字符串）或者使用它的一些方法来获取或检查数据。

查看[文档](https://oreil.ly/JWEjK)以了解与`$attributes`对象交互的所有方式，但这里有一个非常有用的技巧：

```php
<!-- Merge default classes with passed-in classes  -->
<!-- Definition -->
<div {{ $attributes->merge(['class' => 'p-4 m-4']) }}>
    {{ $message }}
</div>

<!-- Usage -->
<x-notice class="text-blue-200">
    Message here
</x-notice>

<!-- Outputs: -->
<div class="p-4 m-4 text-blue-200">
    Message here
</div>
```

## 使用堆栈

一个常见的模式是使用基本的 Blade 包含时可能难以管理的情况，即每个 Blade 包含层次结构中的视图都需要向某个特定部分添加内容 —— 几乎就像向数组中添加条目一样。

这种情况最常见的情况是某些页面（有时更广泛地说是网站的某些部分）需要加载特定的、唯一的 CSS 和 JavaScript 文件。想象一下，你有一个全站“全局”CSS 文件，一个“职位部分”CSS 文件，和一个“申请职位”页面的 CSS 文件。

Blade 的*堆栈*专门为这种情况设计。在父模板中定义一个堆栈，这只是一个占位符。然后，在每个子模板中，你可以使用`@push/@endpush`将条目“推送”到堆栈中，在最终渲染时它们被添加到堆栈的底部。你也可以使用`@prepend/@endprepend`将它们添加到堆栈的顶部。例 4-19 进行了说明。

##### 例 4-19\. 使用 Blade 堆栈

```php
<!-- resources/views/layouts/app.blade.php -->
<html>
<head>
    <link href="/css/global.css">
    <!-- the placeholder where stack content will be placed -->
    @stack('styles')
</head>
<body>
    <!-- // -->
</body>
</html>

<!-- resources/views/jobs.blade.php -->
@extends('layouts.app')

@push('styles')
    <!-- push something to the bottom of the stack -->
    <link href="/css/jobs.css">
@endpush

<!-- resources/views/jobs/apply.blade.php -->
@extends('jobs')

@prepend('styles')
    <!-- push something to the top of the stack -->
    <link href="/css/jobs--apply.css">
@endprepend
```

这些生成以下结果：

```php
<html>
<head>
    <link href="/css/global.css">
    <!-- the placeholder where stack content will be placed -->
    <!-- push something to the top of the stack -->
    <link href="/css/jobs--apply.css">
    <!-- push something to the bottom of the stack -->
    <link href="/css/jobs.css">
</head>
<body>
    <!-- // -->
</body>
</html>
```

# 视图组合器和服务注入

正如我们在第三章中介绍的那样，从路由定义向视图传递数据是很简单的（参见例 4-20）。

##### 例 4-20\. 如何向视图传递数据的提醒

```php
Route::get('passing-data-to-views', function () {
    return view('dashboard')
        ->with('key', 'value');
});
```

然而，有时你可能发现自己需要将相同的数据多次传递给多个视图。或者你可能发现自己使用了一个需要某些数据的页眉部分或类似部件；你是否需要从每个可能加载该页眉部分的路由定义中传递数据？

## 使用视图组合器将数据绑定到视图

幸运的是，有一种更简单的方法。解决方案称为*视图组合器*，它允许你定义，*每次加载特定视图时，都应该将某些数据传递给它* —— 而不必显式从路由定义中传递该数据。

假设你在每个页面上都有一个侧边栏，在一个名为`partials.sidebar`的部分中定义（*resources/views/partials/sidebar.blade.php*），然后在每个页面上包含它。这个侧边栏显示了你网站上发布的最近七篇帖子的列表。如果它出现在每个页面上，每个路由定义通常都需要获取该列表并传递它，就像在例 4-21 中一样。

##### 示例 4-21\. 从每个路由中传递侧边栏数据

```php
Route::get('home', function () {
    return view('home')
        ->with('posts', Post::recent());
});

Route::get('about', function () {
    return view('about')
        ->with('posts', Post::recent());
});
```

这可能很快就会变得很烦人。因此，我们将使用视图组合器来“共享”这个变量给一组预定的视图。我们可以通过几种方式来实现这一点，所以让我们从简单的方式开始，然后逐步提升。

### 全局共享变量

首先，最简单的选项：只需在你的应用程序的每个视图中全局“共享”一个变量，就像在示例 4-22 中一样。

##### 示例 4-22\. 全局共享一个变量

```php
// Some service provider
public function boot()
{
    ...
    view()->share('recentPosts', Post::recent());
}
```

如果你想使用`view()->share()`，最好的地方是服务提供者的`boot()`方法，这样绑定就会在每次页面加载时运行。你可以创建一个自定义的`ViewComposerServiceProvider`（详见“服务提供者”），但现在只需将其放在`App\Providers\AppServiceProvider`的`boot()`方法中即可。

使用`view()->share()`使得该变量在整个应用程序的每个视图中都可以访问，但可能有些过头了。

### 使用闭包的视图作用域视图组合器

下一个选项是使用基于闭包的视图组合器来与单个视图共享变量，就像在示例 4-23 中一样。

##### 示例 4-23\. 创建基于闭包的视图组合器

```php
view()->composer('partials.sidebar', function ($view) {
    $view->with('recentPosts', Post::recent());
});
```

如你所见，我们在第一个参数中定义了要与之共享的视图的名称（`partials.sidebar`），然后在第二个参数中传递了一个闭包；在闭包中，我们使用`$view->with()`来共享一个变量，但只针对特定视图。

### 使用类的视图作用域视图组合器

最后，最灵活但也最复杂的选项是为你的视图组合器创建一个专用的类。

首先，让我们创建视图组合器类。虽然没有正式定义视图组合器的存放位置，但文档建议放在`App\Http\ViewComposers`中。因此，让我们创建`App\Http\ViewComposers\RecentPostsComposer`，就像在示例 4-24 中一样。

##### 示例 4-24\. 一个视图组合器

```php
<?php

namespace App\Http\ViewComposers;

use App\Post;
use Illuminate\Contracts\View\View;

class RecentPostsComposer
{
    public function compose(View $view)
    {
        $view->with('recentPosts', Post::recent());
    }
}
```

如你所见，当调用这个组合器时，它会执行`compose()`方法，在该方法中，我们将`recentPosts`变量绑定到运行`Post`模型的`recent()`方法的结果上。

就像其他共享变量的方法一样，这个视图组合器需要在某处进行绑定。你很可能会创建一个自定义的`ViewComposerServiceProvider`，但现在，就像在示例 4-25 中看到的那样，我们将它放在`App\Providers\AppServiceProvider`的`boot()`方法中。

##### 示例 4-25\. 在`AppServiceProvider`中注册视图组合器

```php
public function boot(): void
{
    view()->composer(
        'partials.sidebar',
        \App\Http\ViewComposers\RecentPostsComposer::class
    );
}
```

注意，这种绑定方式与基于闭包的视图组合器相同，但不是传递闭包，而是传递我们视图组合器的类名。现在，每当 Blade 渲染`partials.sidebar`视图时，它将自动运行我们的提供者，并将一个`recentPosts`变量传递给视图，该变量设置为在我们的`Post`模型上运行`recent()`方法的结果。

## Blade 服务注入

视图中最有可能注入的数据主要有三种类型：需要迭代的数据集合、在页面上展示的单个对象以及生成数据或视图的服务。

使用服务时，模式很可能会像示例 4-26 那样，我们通过在路由定义的方法签名中进行类型提示将我们的分析服务的实例注入路由，并将其传递到视图中。

##### 示例 4-26\. 通过路由定义的构造函数将服务注入视图

```php
Route::get('backend/sales', function (AnalyticsService $analytics) {
    return view('backend.sales-graphs')
        ->with('analytics', $analytics);
});
```

就像视图组合器一样，Blade 的服务注入为你的路由定义提供了一个便捷的快捷方式来减少重复。通常情况下，使用我们的分析服务的视图内容可能会像示例 4-27 那样。

##### 示例 4-27\. 在视图中使用注入的导航服务

```php
<div class="finances-display">
     {{ $analytics->getBalance() }} / {{ $analytics->getBudget() }}
</div>
```

Blade 服务注入使得直接将容器中的类的实例注入到视图中变得容易，就像示例 4-28 中展示的一样。

##### 示例 4-28\. 将服务直接注入到视图中

```php
@inject('analytics', 'App\Services\Analytics')

<div class="finances-display">
     {{ $analytics->getBalance() }} / {{ $analytics->getBudget() }}
</div>
```

正如你所看到的，这个 `@inject` 指令实际上已经使得 `$analytics` 变量可用，我们稍后在视图中使用它。

`@inject` 的第一个参数是你要注入的变量名称，第二个参数是你想要注入实例的类或接口。这与在 Laravel 中在构造函数中声明依赖项的方式相似；如果你对其工作原理不熟悉，请查看第十一章以了解更多信息。

就像视图组合器一样，Blade 服务注入使得可以很容易地使特定数据或功能对每个视图实例可用，而无需每次通过路由定义来注入。

# 自定义 Blade 指令

到目前为止，我们已经介绍了 Blade 的所有内置语法——`@if`、`@unless`等等都称为*指令*。每个 Blade 指令都是一个模式（例如 `@if (*$condition*)`）与 PHP 输出（例如 `<?php if (*$condition*): ?>`）之间的映射。

指令不仅仅局限于核心功能；你实际上可以创建自己的指令。你可能认为指令适合用来创建代码的小快捷方式——比如，使用 `@button('buttonName')` 并将其扩展为更大的按钮 HTML 代码块。这并不是一个*糟糕*的想法，但对于这样简单的代码扩展，包含视图部分可能会更好一些。

当定制指令能简化某种重复逻辑时，它们往往最有用。假设我们厌倦了需要用 `@if (auth()->guest())` 来包装代码（用于检查用户是否已登录），我们想要一个自定义的 `@ifGuest` 指令。就像视图组合器一样，值得注意的是，也许最好有一个自定义服务提供者来注册这些指令，但现在让我们将其放在 `App\Providers\AppServiceProvider` 的 `boot()` 方法中。查看示例 4-29 以查看这个绑定的具体内容。

##### 示例 4-29\. 在服务提供者中绑定自定义 Blade 指令

```php
public function boot(): void
{
    Blade::directive('ifGuest', function () {
        return "<?php if (auth()->guest()): ?>";
    });
}
```

我们现在注册了一个自定义指令 `@ifGuest`，它将被替换为 PHP 代码 `<?php if (auth()->guest()): ?>`。

这可能会感觉奇怪。你正在编写一个*字符串*，它将被返回并作为 PHP 执行。但这意味着你现在可以将 PHP 模板代码中复杂、丑陋、不清晰或重复的部分隐藏在清晰、简单和表达性强的语法背后。

# 自定义指令结果缓存

你可能会想要通过在绑定中执行操作来加速你的自定义指令，并将结果嵌入返回的字符串中：

```php
Blade::directive('ifGuest', function () {
    // Antipattern! Do not copy.
    $ifGuest = auth()->guest();
    return "<?php if ({$ifGuest}): ?>";
});
```

这个想法的问题在于，它假设这个指令将在每次页面加载时重新创建。然而，Blade 会进行积极的缓存，所以如果你尝试这样做，你会发现自己处于一个困境之中。

## 自定义 Blade 指令中的参数

如果你想在自定义逻辑中接受参数，可以查看示例 4-30。

##### 示例 4-30\. 创建带参数的 Blade 指令

```php
// Binding
Blade::directive('newlinesToBr', function ($expression) {
    return "<?php echo nl2br({$expression}); ?>";
});

// In use
<p>@newlinesToBr($message->body)</p>
```

闭包接收的 `$expression` 参数表示括号内的内容。正如你所看到的，我们生成了一个有效的 PHP 代码片段并返回它。

如果你发现自己一遍又一遍地写相同的条件逻辑，那么你应该考虑使用 Blade 指令。

## 示例：在多租户应用程序中使用自定义 Blade 指令

假设我们正在构建一个支持*多租户*的应用程序，这意味着用户可能会从*www.myapp.com*、*client1.myapp.com*、*client2.myapp.com*或其他地方访问该网站。

假设我们编写了一个类来封装某些多租户逻辑，并命名为 `Context`。该类将捕获有关当前访问上下文的信息和逻辑，例如认证用户是谁，以及用户是访问公共网站还是客户子域。

我们可能会经常在视图中解析 `Context` 类，并对其执行条件操作，就像示例 4-31 中那样。`app('context')` 是从容器中获取类实例的快捷方式，我们将在第十一章中进一步学习容器。

##### 示例 4-31\. 在上下文中进行条件判断，无需自定义 Blade 指令

```php
@if (app('context')->isPublic())
    &copy; Copyright MyApp LLC
@else
    &copy; Copyright {{ app('context')->client->name }}
@endif
```

如果我们能将 `@if (app('context')->isPublic())` 简化为 `@ifPublic`，该有多好？让我们来做吧。查看示例 4-32。

##### 示例 4-32\. 使用自定义 Blade 指令进行上下文条件判断

```php
// Binding
Blade::directive('ifPublic', function () {
    return "<?php if (app('context')->isPublic()): ?>";
});

// In use
@ifPublic
    &copy; Copyright MyApp LLC
@else
    &copy; Copyright {{ app('context')->client->name }}
@endif
```

由于这解析为简单的 `if` 语句，我们仍然可以依赖于原生的 `@else` 和 `@endif` 条件。但如果我们愿意，我们也可以创建一个自定义 `@elseIfClient` 指令，或者单独的 `@ifClient` 指令，或者任何其他我们想要的东西。

## 更容易定制的“if”语句指令

尽管自定义 Blade 指令功能强大，但它们最常见的用途是处理 `if` 语句。所以创建自定义`if`指令的简单方法是使用 `Blade::if()`。示例 4-33 展示了我们如何使用 `Blade::if()` 方法重构示例 4-32：

##### Example 4-33\. 定义自定义“if” Blade 指令

```php
// Binding
Blade::if('ifPublic', function () {
    return (app('context'))->isPublic();
});
```

你将以完全相同的方式使用这些指令，但是正如你所看到的，定义它们要简单一些。你不必手动键入 PHP 大括号，只需编写返回布尔值的闭包即可。

# 测试

测试视图的最常见方法是通过应用程序测试，这意味着你实际上调用显示视图的路由，并确保视图包含特定内容（参见示例 4-34）。你还可以点击按钮或提交表单，并确保你被重定向到某个页面或看到某个特定的错误。（你将在第十二章中了解更多关于测试的内容。）

##### Example 4-34\. 测试一个视图是否显示特定内容

```php
// EventsTest.php
public function test_list_page_shows_all_events()
{
    $event1 = Event::factory()->create();
    $event2 = Event::factory()->create();

    $this->get('events')
        ->assertSee($event1->title)
        ->assertSee($event2->title);
}
```

你还可以测试某个视图是否已经传递了特定的数据，如果达到了你的测试目标，这比检查页面上的某些文本更加健壮。示例 4-35 演示了这种方法。

##### Example 4-35\. 测试一个视图是否传递了特定内容

```php
// EventsTest.php
public function test_list_page_shows_all_events()
{
    $event1 = Event::factory()->create();
    $event2 = Event::factory()->create();

    $response = $this->get('events');

    $response->assertViewHas('events', Event::all());
    $response->assertViewHasAll([
        'events' => Event::all(),
        'title' => 'Events Page',
    ]);
    $response->assertViewMissing('dogs');
}
```

使用`assertViewHas()`，我们可以传递一个闭包，这意味着我们可以自定义如何检查更复杂的数据结构。示例 4-36 说明了我们可能如何使用这个功能。

##### Example 4-36\. 向 `assertViewHas()` 传递闭包

```php
// EventsTest.php
public function test_list_page_shows_all_events()
{
    $event1 = Event::factory()->create();

    $response = $this->get("events/{ $event1->id }");

    $response->assertViewHas('event', function ($event) use ($event1) {
        return $event->id === $event1->id;
    });
}
```

# 简而言之

Blade 是 Laravel 的模板引擎。它的主要关注点是清晰、简洁和表达力强大的语法，具有强大的继承性和可扩展性。它的“安全输出”括号是`{{`和`}}`，其不受保护的输出括号是`{!!`和`!!}`，它还有一系列称为“指令”的自定义标签，所有这些标签都以`@`开头（例如`@if`和`@unless`）。

你可以定义一个父模板，并在其中留下“洞口”以用于内容，使用`@yield`和`@section`/`@show`。然后，你可以教会其子视图通过`@extends('*parent.view*')`扩展父视图，并使用`@section`/`@endsection`定义它们的部分。你可以使用`@parent`引用块的父级内容。

视图组合器使得在每次加载特定视图或子视图时定义特定信息变得轻松。服务注入允许视图本身直接从应用程序容器请求数据。
