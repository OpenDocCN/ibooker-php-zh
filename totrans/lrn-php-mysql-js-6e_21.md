# 第十八章\. 使用异步通信

*Ajax* 这个术语首次在 2005 年被创造出来。它代表 *Asynchronous JavaScript and XML*，简而言之，就是使用内置在 JavaScript 中的一组方法在浏览器和服务器之间后台传输数据。这个术语现在大多已被放弃，转而简单地讨论异步通信。

这项技术的一个绝佳例子是谷歌地图（见 图 18-1），在需要时从服务器下载地图的新部分，而无需刷新页面。

使用异步通信不仅大幅减少了需要来回发送的数据量，而且使网页能够无缝动态地运行，使其更像是独立的应用程序。结果是大大改进的用户界面和更好的响应能力。

![谷歌地图是 Ajax 的一个绝佳例子](img/pmj6_1801.png)

###### 图 18-1\. 谷歌地图是异步通信的一个优秀例子

# 什么是异步通信？

当今使用的异步通信始于 1999 年发布的 Internet Explorer 5，引入了一个新的 ActiveX 对象，`XMLHttpRequest`。ActiveX 是微软的技术，用于签名插件，安装额外的软件到您的计算机上。其他浏览器开发者随后效仿，但是他们都将这个功能作为 JavaScript 解释器的本地部分实现，而不是使用 ActiveX。

然而，早在此之前，一种早期形式的技术已经出现，它在页面上使用隐藏框架与服务器在后台交互。聊天室是早期的采用者，使用它来轮询并显示新的消息帖子，而不需要页面重新加载。

所以，让我们看看如何通过 JavaScript 实现异步通信。

# 使用 XMLHttpRequest

在过去，由于各种浏览器之间的不同实现，特别是在不同版本的 Microsoft Internet Explorer 之间，制作 Ajax 调用是一件真正的痛苦。幸运的是，如今情况大为改善，只需简单的 `XMLHttpRequest` 对象即可统一处理。

例如，要进行 GET 请求，您可以使用以下代码：

```php
let XHR = new XMLHttpRequest()

XHR.open("`GET`", "resource.info", true)
XHR.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
XHR.send()
```

或者，对于 POST 请求，只需将 `GET` 替换为 `POST`；就是这么简单。

## 您的第一个异步程序

输入并保存代码到 示例 18-1 中，文件名为 *urlpost.html*，但不要立即加载到浏览器中。

##### 示例 18-1\. urlpost.html

```php
<!DOCTYPE html>
<html> <!-- urlpost.html -->
  <head>
    <title>Asynchronous Communication Example</title>
  </head>
  <body style='text-align:center'>
    <h1>Loading a web page into a DIV</h1>
    <div id='info'>This sentence will be replaced</div>

    <script>
      let XHR = new XMLHttpRequest()

      XHR.open("POST", "http://127.0.0.1/18/urlpost.php", true)
      XHR.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
      XHR.send("url=news.com")

      XHR.onreadystatechange = function()
      {
        if (this.readyState == 4 && this.status == 200)
        {
          document.getElementById("info").innerHTML = this.responseText
        }
      }      
    </script>
  </body>
</html>

```

让我们逐步浏览这份文档，看看它做了什么，从第一行开始，简单地设置了一个 HTML 文档并显示了一个标题。接下来的一行创建了一个带有 ID `info` 的 `<div>`，其中包含文本 `This sentence will be replaced`。稍后，调用返回的文本将被插入到这里。

接下来，创建一个名为 `XHR` 的新 `XMLHttpRequest` 对象。使用它，通过调用 `XHR.open` 打开要加载的资源。在本例中，为了避免现代浏览器中的跨域 Ajax 问题，选择了 `http://127.0.0.1` 的本地主机 IP 地址，然后是章节文件夹 `18`，接着是 PHP 程序 `urlpost.php`，我们马上就会介绍到。

###### 注意

如果您使用 AMPPS（或类似的 WAMP、LAMP 或 MAMP）设置开发服务器在第二章中下载示例文件从[GitHub](https://github.com/RobinNixon/lpmj6)并将它们保存在 web 服务器的文档根目录中（如该章节中所述），则第十八章文件夹将在正确的位置使此代码能够正常工作。如果您的设置有任何不同部分，或者在使用您选择的域名的开发服务器上运行此代码，则必须相应地更改此代码中的这些值。

指定要加载的资源后，调用 `XHR.setRequestHeader`，传递要发送到资源服务器的必需头部，并在调用 `XHR.send` 中发送要发布的值。在这种情况下，是主页面在 *news.com*。

### `readyState` 属性

现在我们进入异步调用的核心，所有这些都依赖于 `readyState` 属性。这使得浏览器可以继续接受用户输入和更改屏幕，而我们的程序设置 `onreadystatechange` 属性，每次 `readyState` 改变时调用我们选择的函数。在本例中，使用了一个无名（或匿名）内联函数，而不是一个单独命名的函数。这种类型的函数被称为*回调*函数，因为每次 `readyState` 改变时都会调用它。

使用内联匿名函数设置回调函数的语法如下：

```php
XHR.onreadystatechange = function()
{
  if (this.readyState == 4 && this.status == 200)
  {
    // do something
  }
}
```

如果您希望使用单独命名的函数，语法略有不同：

```php
XHR.onreadystatechange = asyncCallback

function asyncCallback()
{
  if (this.readyState == 4 && this.status == 200)
  {
    // do something
  }
}
```

事实上，`readyState` 可以有五个值，但我们只关心一个，即值为 `4`，表示调用已完成。因此，每次调用新函数时，它会在 `readyState` 的值为 `4` 之前不做任何操作。当我们的函数检测到该值时，接下来会检查调用的 `status`，确保其值为 `200`，这表示调用成功。

###### 注意

您会注意到，所有这些对象属性都是使用 `this.readyState`、`this.status` 等引用的，而不是对象的当前名称 `XHR`，如 `XHR.readyState` 或 `XHR.status`。这是为了您可以轻松复制粘贴代码，并且它能在任何对象名称上工作，因为 `this` 关键字始终引用当前对象。

因此，确认 `readyState` 为 `4` 和 `status` 为 `200` 后，我们将 `responseText` 中的值放入 `<div>` 的内部 HTML 中，该 `<div>` 的 `id` 为 `info`：

```php
document.getElementById("info").innerHTML = this.responseText
```

在这一行中，通过`getElementById`方法引用了`info`元素，然后将其`innerHTML`属性赋值为调用返回的值。效果是网页的这个元素发生了变化，而其他一切保持不变。

### 异步过程的服务器端

现在我们来看看等式的 PHP 部分，你可以在例子 18-2 中看到它。输入这段代码并将其保存为*urlpost.php*。

##### 例子 18-2\. urlpost.php

```php
<?php // urlpost.php
  if (isset($_POST['url']))
  {
    echo file_get_contents('http://' . SanitizeString($_POST['url']));
  }

  function SanitizeString($var)
  {
    $var = strip_tags($var);
    $var = htmlentities($var);
    return stripslashes($var);
  }
?>
```

正如你所看到的，这很简短且简洁，并且还使用了非常重要的`SanitizeString`函数，应该对所有发布的数据进行这样的操作。在这种情况下，未经过清理的数据可能会导致用户插入 JavaScript 并对你的代码获得优势。

这个程序使用`file_get_contents` PHP 函数加载了通过变量`$_POST['url']`提供的 URL 的网页内容。`file_get_contents`函数非常灵活，可以从本地或远程服务器加载文件或网页的整个内容；它甚至考虑了移动页面和其他重定向。

当你输入完程序后，可以在浏览器中调用*urlpost.html*，几秒钟后，你应该会看到*news.com*首页的内容加载到我们为此目的创建的`<div>`中。

###### 注意

跨域安全性使得使用 Ajax 比以前更加困难，因为你必须对加载文件的方式非常准确和清晰。例如，在本地开发服务器 localhost 上的这个例子中，你需要使用其 IP 地址来引用文件。因此，例如，如果你将示例文件保存在 AMPPS 服务器的文档根目录中，如第二章中所述，则所有文件将位于名为*18*的子文件夹中。

要测试程序，请在浏览器中输入以下内容：

```php
http://127.0.0.1/18/urlpost.html
```

它不会像直接加载网页那样快，因为它转移两次——一次到服务器，再从服务器到你的浏览器——但结果应该看起来像图 18-2。

![](img/pmj6_1802.png)

###### 图 18-2\. news.com 的首页

我们不仅成功地进行了异步调用，并在 JavaScript 中返回了响应，而且还利用了 PHP 的能力来合并一个完全不相关的网络对象。顺便说一句，如果我们试图找到一种不借助 PHP 服务器端模块直接异步获取这个网页的方法，我们是不会成功的，因为有其他安全阻止跨域异步通信。因此，这个例子还展示了一个实际问题的方便解决方案。

## 使用 GET 而不是 POST

就像当你从表单提交任何数据时，你可以选择以 GET 请求的形式提交你的数据，如果这样做，你将节省几行代码。然而，有一个缺点：一些浏览器可能会缓存 GET 请求，而 POST 请求永远不会被缓存。你不希望缓存请求，因为浏览器会直接重新显示上次获取的内容，而不是从服务器获取新的输入。解决此问题的方法是使用一个方法来添加一个随机参数到每个请求中，确保每个请求的 URL 都是唯一的。

示例 18-3 展示了如何通过 GET 请求实现与示例 18-1 相同的结果。

##### 示例 18-3\. urlget.html

```php
<!DOCTYPE html>
<html> <!-- urlget.html -->
  <head>
    <title>Asynchronous Communication Example</title>
  </head>
  <body style='text-align:center'>
    <h1>Loading a web page into a DIV</h1>
    <div id='info'>This sentence will be replaced</div>

    <script>
      `let` `nocache` `=` `"&nocache="` `+` `Math``.``random``(``)` `*` `1000000`
      let XHR     = new XMLHttpRequest()

      `XHR``.``open``(``"GET"``,` `"http://127.0.0.1/18/urlget.php?url=news.com"` `+` `nocache``,` `true``)`
      `XHR``.``send``(``)`

      XHR.onreadystatechange = function()
      {
        if (this.readyState == 4 && this.status == 200)
        {
          document.getElementById("info").innerHTML = this.responseText
        }
      }      
    </script>
  </body>
</html>

```

需要注意两个文档之间的差异已用粗体突出，并描述如下：

+   对于 GET 请求，不需要发送头部信息。

+   我们使用 GET 请求调用`open`方法，提供一个字符串构成的 URL，该字符串由`?`符号后跟参数/值对`url=news.com`组成。

+   我们使用`&`符号提供第二个参数/值对，然后将参数`nocache`的值设置为 0 到 100 万之间的随机值。这样做是为了确保每个请求的 URL 都是不同的，因此不会被缓存。

+   调用`send`现在没有参数，因为没有通过`POST`请求传递任何内容需要它。

为了配合这个新文档，PHP 程序必须修改以响应 GET 请求，就像示例 18-4 中的*urlget.php*一样。

##### 示例 18-4\. urlget.php

```php
<?php
  if (isset(`$_GET`['url']))
  {
    echo file_get_contents("http://".sanitizeString(`$_GET`['url']));
  }

  function sanitizeString($var)
  {
    $var = strip_tags($var);
    $var = htmlentities($var);
    return stripslashes($var);
  }
?>
```

这与示例 18-2 唯一的不同之处在于，将对`$_POST`的引用替换为`$_GET`。在浏览器中加载*urlget.html*的最终结果与加载*urlpost.html*完全相同。

要测试程序的这个修订版本，请在浏览器中输入以下内容，你应该看到与之前完全相同的结果，只是通过 GET 而不是 POST 请求加载：

```php
http://127.0.0.1/18/urlget.html

```

## 发送 XML 请求

虽然我们创建的对象称为`XMLHttpRequest`对象，但我们迄今为止并未使用 XML。正如你所见，我们已经能够异步请求整个 HTML 文档，但我们同样可以请求文本页面、字符串或数字，甚至是电子表格数据。

因此，让我们修改前面的示例文档和 PHP 程序，以获取一些 XML 数据。为此，请先查看 PHP 程序*xmlget.php*，如示例 18-5 所示。

##### 示例 18-5\. xmlget.php

```php
<?php
  if (isset($_GET['url']))
  {
    `header``(``'Content-Type: text/xml'``);`
    echo file_get_contents("http://".sanitizeString($_GET['url']));
  }

  function sanitizeString($var)
  {
    $var = strip_tags($var);
    $var = htmlentities($var);
    return stripslashes($var);
  }
?>
```

这个程序已经略微修改（以粗体突出显示），在返回获取的文档之前输出正确的 XML 头。这里没有进行任何检查，因为假定调用代码将请求一个实际的 XML 文档。

现在转到 HTML 文档*xmlget.html*，如示例 18-6 所示。

##### 示例 18-6\. xmlget.html

```php
<!DOCTYPE html>
<html> <!-- xmlget.html -->
  <head>
    <title>Asynchronous Communication Example</title>
  </head>
  <body>
    <h1>Loading XML data into a DIV</h1>
    <div id='info'>This sentence will be replaced</div>

    <script>
      `let out     = ''` let nocache = "&nocache=" + Math.random() * 1000000 `let url     = "rss.news.yahoo.com/rss/topstories"` let XHR     = new XMLHttpRequest()

      XHR.open("POST", "http://127.0.0.1/18/xmlget.php?url=" + url + nocache, true)
      XHR.setRequestHeader("Content-type", "application/x-www-form-urlencoded")
      XHR.send()

      XHR.onreadystatechange = function()
      {
        if (this.readyState == 4 && this.status == 200)
        {`let titles = this.responseXML.getElementsByTagName('title')             for (let j = 0 ; j` `< titles.length` `;` `+``+``j``)`
           `{`
             `o``u``t` `+``=` `t``i``t``l``e``s``[``j``]``.``c``h``i``l``d``N``o``d``e``s``[``0``]``.``n``o``d``e``V``a``l``u``e` `+` `'``<``b``r``>``'            }            document.getElementById('info').innerHTML = out `}
      } </script>
  </body>
</html>

```

再次，差异已经用粗体标出。正如你所见，这段代码与以前的版本基本相似，唯一的不同是现在请求的 URL，*rss.news.yahoo.com/rss/topstories*，包含一个 XML 文档，*Yahoo! News Top Stories* 订阅。

另一个重要变化是使用 `responseXML` 属性替代了 `responseText` 属性。每当服务器返回 XML 数据时，`responseXML` 将包含返回的 XML。

然而，`responseXML`并非简单地包含一个 XML 文本字符串：实际上它是一个完整的 XML 文档对象，我们可以使用 DOM 树的方法和属性来检查和解析它。这意味着它是可访问的，例如，可以通过 JavaScript 的 `getElementsByTagName` 方法访问它。

### 关于 XML

一个 XML 文档通常采用如 Example 18-7 中所示的 RSS 订阅的形式。然而，XML 的美妙之处在于我们可以将这种类型的结构存储在 DOM 树中（参见 Figure 18-3）中，以便快速搜索：

```php
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0">
    <channel>
        <title>RSS Feed</title>
        <link>http://website.com</link>
        <description>website.com's RSS Feed</description>
        <pubDate>Mon, 10 May 2027 00:00:00 GMT</pubDate>
        <item>
            <title>Headline</title>
            <guid>http://website.com/headline</guid>
            <description>This is a headline</description>
        </item>
        <item>
            <title>Headline 2</title>
            <guid>http://website.com/headline2</guid>
            <description>The 2nd headline</description>
        </item>
    </channel>
</rss>

```

![Example 17-8 的 DOM 树](img/pmj6_1803.png)

###### 图 18-3\. Example 18-7 的 DOM 树

然后，使用 `getElementsByTagName` 方法，我们可以快速提取与各种标签相关联的值，而无需进行大量的字符串搜索。这正是我们在 Example 18-6 中所做的，其中发出了以下命令：

```php
let titles = this.responseXML.getElementsByTagName('title')
```

这个单一命令的效果是将所有 `<title>` 元素的值放入数组 `titles` 中。然后，可以通过以下表达式轻松提取它们（其中 `j` 已经被赋值为表示要访问的标题的整数）：

```php
titles[j].childNodes[0].nodeValue
```

所有标题然后都附加到字符串变量 `out` 中，一旦它们全部被处理，结果将被插入到文档开头的空 `<div>` 中。

总结一下，每个实体如 `title` 都是一个节点，因此例如标题文本被视为标题内的一个节点。但即使获取了子节点，你还必须要求它作为文本，这就是 `.nodeValue` 的目的。此外，与所有表单数据一样，请记住，在请求 XML 数据时可以使用 POST 或 GET 方法；选择不会对结果造成太大差异。

要测试这个 XML 程序，输入以下内容到你的浏览器中，你应该看到类似 Figure 18-4 的东西：

```php
http://127.0.0.1/18/xmlget.html
```

![](img/pmj6_1804.png)

###### 图 18-4\. 异步获取 Yahoo! XML 新闻订阅

### 为什么使用 XML？

你可能会问为什么要使用 XML，除了获取如 RSS 订阅之类的 XML 文档。嗯，简单的答案是你不必使用，但如果你希望将结构化数据传递回应用程序，传递一个简单的、无组织的文本混乱可能需要在 JavaScript 中进行复杂的处理。

相反，您可以创建一个 XML 文档，并将其传递回调用函数，该函数将自动将其放入 DOM 树中，就像您现在熟悉的 HTML DOM 对象一样轻松访问。

现在，程序员更倾向于使用 [JavaScript 对象表示法（JSON）作为其首选的数据交换格式](http://json.org)，因为它是 JavaScript 的一个简单子集。

## 使用框架进行异步通信

现在您知道如何编写自己的异步程序，您可能想调查一些免费框架，这些框架可以使其更加轻松，并提供许多更高级的功能。特别是，我建议您查看非常流行的 [jQuery](http://jquery.com) 或者可能是增长最快的框架 [React](http://reactjs.org)。不过，在接下来的章节中，我们将讨论如何使用 CSS 为您的网站应用样式。

# 问题

1.  在 Web 服务器和 JavaScript 客户端之间进行异步通信时必须创建哪个对象？

1.  如何判断异步调用何时完成？

1.  如何知道异步调用是否成功完成？

1.  哪个 `XMLHttpRequest` 对象属性返回异步调用的文本响应？

1.  哪个 `XMLHttpRequest` 对象属性返回异步调用的 XML 响应？

1.  如何指定回调函数以处理异步响应？

1.  哪个 `XMLHttpRequest` 方法用于启动异步请求？

1.  异步 GET 和 POST 请求的主要区别是什么？

请参阅 “第十八章答案”，以获取这些问题的答案。
