# 第一章。PHP 简介

PHP 是一种简单但功能强大的语言，专为创建 HTML 内容而设计。本章介绍了 PHP 语言的基本背景，描述了 PHP 的性质和历史，它可以运行在哪些平台上，以及如何配置它。本章最后展示了 PHP 的实际应用，通过快速演示几个 PHP 程序，展示常见任务的处理，例如处理表单数据、与数据库交互和创建图形等。

# PHP 能做什么？

PHP 可以以两种主要方式使用：

服务器端脚本

PHP 最初是为创建动态 Web 内容而设计的，至今仍然非常适合这项任务。要生成 HTML，您需要 PHP 解析器和一个 Web 服务器来发送编码的文档文件。PHP 也因其通过数据库连接生成动态内容而变得流行，还支持 XML 文档、图形、PDF 文件等更多内容。

命令行脚本

PHP 可以像 Perl、awk 或 Unix shell 一样从命令行运行脚本。您可以使用命令行脚本执行系统管理任务，例如备份和日志解析；甚至某些 CRON 作业类型的脚本也可以用这种方式完成（作为非可视化 PHP 任务）。

然而，在本书中，我们集中讨论第一项内容：使用 PHP 开发动态 Web 内容。

PHP 可在所有主要操作系统上运行，包括 Unix 变种（包括 Linux、FreeBSD、Ubuntu、Debian 和 Solaris）、Windows 和 macOS。它可以与所有主流的 Web 服务器一起使用，包括 Apache、Nginx 和 OpenBSD 等服务器；甚至像 Azure 和 Amazon 这样的云环境也在不断增长。

PHP 语言本身非常灵活。例如，您不仅限于输出 HTML 或其他文本文件 - 任何文档格式都可以生成。PHP 内置支持生成 PDF 文件以及 GIF、JPEG 和 PNG 图像。

PHP 最显著的特性之一是其广泛支持的数据库。PHP 支持所有主要的数据库（包括 MySQL、PostgreSQL、Oracle、Sybase、MS-SQL、DB2 和符合 ODBC 标准的数据库），甚至支持许多不太常见的数据库。甚至像 CouchDB 和 MongoDB 这样的新型 NoSQL 风格的数据库也得到了支持。通过 PHP，从数据库创建具有动态内容的网页非常简单。

最后，PHP 提供了一个 PHP 代码库，用于执行常见任务，如数据库抽象、错误处理等，通过 PHP 扩展和应用程序库（PEAR）。[PEAR](http://pear.php.net) 是一个用于可重用 PHP 组件的框架和分发系统。

# PHP 的简要历史

Rasmus Lerdorf 最初在 1994 年构思了 PHP，但今天人们使用的 PHP 与最初版本大不相同。要理解 PHP 是如何发展到现在的，了解语言的历史演变是很有用的。这里有详细的评论和来自 Rasmus 自己的电子邮件。

## PHP 的演变

这是 PHP 1.0 的公告，发布于 1995 年 6 月的 Usenet 新闻组（*comp.infosystems.www.authoring.cgi*）：

```php
From: rasmus@io.org (Rasmus Lerdorf)
Subject: Announce: Personal Home Page Tools (PHP Tools)
Date: 1995/06/08
Message-ID: <3r7pgp$aa1@ionews.io.org>#1/1
organization: none
newsgroups: comp.infosystems.www.authoring.cgi

Announcing the Personal Home Page Tools (PHP Tools) version 1.0.

These tools are a set of small tight cgi binaries written in C.
They perform a number of functions including:

. Logging accesses to your pages in your own private log files
. Real-time viewing of log information
. Providing a nice interface to this log information
. Displaying last access information right on your pages
. Full daily and total access counters
. Banning access to users based on their domain
. Password protecting pages based on users' domains
. Tracking accesses ** based on users' e-mail addresses **
. Tracking referring URL's - HTTP_REFERER support
. Performing server-side includes without needing server support for it
. Ability to not log accesses from certain domains (ie. your own)
. Easily create and display forms
. Ability to use form information in following documents

Here is what you don't need to use these tools:

. You do not need root access - install in your ~/public_html dir
. You do not need server-side includes enabled in your server
. You do not need access to Perl or Tcl or any other script interpreter
. You do not need access to the httpd log files

The only requirement for these tools to work is that you have
the ability to execute your own cgi programs. Ask your system
administrator if you are not sure what this means.

The tools also allow you to implement a guestbook or any other
form that needs to write information and display it to users
later in about 2 minutes.

The tools are in the public domain distributed under the GNU
Public License. Yes, that means they are free!

For a complete demonstration of these tools, point your browser
at: http://www.io.org/~rasmus

--
Rasmus Lerdorf
rasmus@io.org
http://www.io.org/~rasmus
```

请注意，此消息中显示的 URL 和电子邮件地址早已消失。这份公告的语言反映了当时人们关注的问题，比如密码保护页面、轻松创建表单以及在后续页面访问表单数据。此外，该公告还说明了 PHP 最初作为一系列有用工具的框架的定位。

公告只谈到了随 PHP 一起提供的工具，但在幕后的目标是创建一个框架，使得扩展 PHP 并添加更多工具变得容易。这些插件的业务逻辑是用 C 编写的；一个简单的解析器从 HTML 中提取标签并调用各种 C 函数。真正的计划从来不是创建一个脚本语言。

那么发生了什么呢？

Rasmus 开始为多伦多大学进行一个相当大的项目，需要一个工具来整合来自各处的数据并呈现一个漂亮的基于 Web 的管理界面。当然，他使用了 PHP 来完成这项任务，但出于性能原因，需要更好地将 PHP 1.0 的各种小工具整合到一起，并集成到 Web 服务器中。

最初，对 NCSA Web 服务器进行了一些修改，以补丁形式支持 PHP 核心功能。这种方法的问题在于，作为用户，你必须用这种特殊的、经过修改的版本替换你的 Web 服务器软件。幸运的是，此时 Apache 也开始获得势头，Apache API 使得像 PHP 这样向服务器添加功能变得更加容易。

在接下来的一年左右，完成了大量工作，关注重点也有了相当大的变化。以下是 PHP 2.0（PHP/FI）的公告，发布于 1996 年 4 月：

```php
 From: rasmus@madhaus.utcs.utoronto.ca (Rasmus Lerdorf)
 Subject: ANNOUNCE: PHP/FI Server-side HTML-Embedded Scripting Language
 Date: 1996/04/16
 Newsgroups: comp.infosystems.www.authoring.cgi

 PHP/FI is a server-side HTML embedded scripting language. It has built-in
 access logging and access restriction features and also support for
 embedded SQL queries to mSQL and/or Postgres95 backend databases.

 It is most likely the fastest and simplest tool available for creating
 database-enabled web sites.

 It will work with any UNIX-based web server on every UNIX flavour out
 there. The package is completely free of charge for all uses including
 commercial.

 Feature List:

 . Access Logging
 Log every hit to your pages in either a dbm or an mSQL database.
 Having hit information in a database format makes later analysis easier.
 . Access Restriction
 Password protect your pages, or restrict access based on the refering URL
 plus many other options.
 . mSQL Support
 Embed mSQL queries right in your HTML source files
 . Postgres95 Support
 Embed Postgres95 queries right in your HTML source files
 . DBM Support
 DB, DBM, NDBM and GDBM are all supported
 . RFC-1867 File Upload Support
 Create file upload forms
 . Variables, Arrays, Associative Arrays
 . User-Defined Functions with static variables + recursion
 . Conditionals and While loops
 Writing conditional dynamic web pages could not be easier than with
 the PHP/FI conditionals and looping support
 . Extended Regular Expressions
 Powerful string manipulation support through full regexp support
 . Raw HTTP Header Control
 Lets you send customized HTTP headers to the browser for advanced
 features such as cookies.
 . Dynamic GIF Image Creation
 Thomas Boutell's GD library is supported through an easy-to-use set of
 tags.

 It can be downloaded from the File Archive at: <URL:http://www.vex.net/php>

 --
 Rasmus Lerdorf
 rasmus@vex.net
```

这是第一次使用术语*脚本语言*。PHP 1.0 的简单标签替换代码被一个可以处理更复杂的嵌入式标签语言的解析器替换。按照今天的标准来看，这种标签语言并不特别复杂，但与 PHP 1.0 相比，它确实是。

这种变化的主要原因是，实际上使用 PHP 1.0 的人中很少有人真正有兴趣使用基于 C 的框架来创建插件。大多数用户更感兴趣的是能够直接在他们的网页中嵌入逻辑，以创建条件 HTML、自定义标签和其他类似功能。PHP 1.0 的用户不断要求能够添加点击跟踪页脚或有条件地发送不同的 HTML 块的功能。这导致了 `if` 标签的创建。一旦有了 `if`，你也需要 `else`，从那时起，你不管是否愿意，最终会编写出一个完整的脚本语言。

到 1997 年中期，PHP 2.0 已经有了很大的发展，并吸引了许多用户，但底层解析引擎仍然存在一些稳定性问题。该项目当时仍然主要是一个人的努力，偶尔会有一些贡献。在这一点上，以色列特拉维夫的 Zeev Suraski 和 Andi Gutmans 自愿重写了底层解析引擎，并且我们同意将他们的重写作为 PHP 版本 3.0 的基础。其他人也自愿参与 PHP 的其他部分的工作，这个项目从一个主要由少数贡献者组成的个人努力转变为一个真正的开源项目，在世界各地拥有许多开发人员。

这里是 PHP 3.0 的 1998 年 6 月公告：

```php
 June 6, 1998 -- The PHP Development Team announced the release of PHP 3.0,
 the latest release of the server-side scripting solution already in use on
 over 70,000 World Wide Web sites.

 This all-new version of the popular scripting language includes support
 for all major operating systems (Windows 95/NT, most versions of Unix,
 and Macintosh) and web servers (including Apache, Netscape servers,
 WebSite Pro, and Microsoft Internet Information Server).

 PHP 3.0 also supports a wide range of databases, including Oracle,
 Sybase, Solid, MySQ, mSQL, and PostgreSQL, as well as ODBC data sources.

 New features include persistent database connections, support for the
 SNMP and IMAP protocols, and a revamped C API for extending the language
 with new features.

 "PHP is a very programmer-friendly scripting language suitable for
 people with little or no programming experience as well as the
 seasoned web developer who needs to get things done quickly. The
 best thing about PHP is that you get results quickly," said
 Rasmus Lerdorf, one of the developers of the language.

 "Version 3 provides a much more powerful, reliable, and efficient
 implementation of the language, while maintaining the ease of use and
 rapid development that were the key to PHP's success in the past,"
 added Andi Gutmans, one of the implementors of the new language core.

 "At Circle Net we have found PHP to be the most robust platform for
 rapid web-based application development available today," said Troy
 Cobb, Chief Technology Officer at Circle Net, Inc. "Our use of PHP
 has cut our development time in half, and more than doubled our client
 satisfaction. PHP has enabled us to provide database-driven dynamic
 solutions which perform at phenomenal speeds."

 PHP 3.0 is available for free download in source form and binaries for
 several platforms at http://www.php.net/.

 The PHP Development Team is an international group of programmers who
 lead the open development of PHP and related projects.

 For more information, the PHP Development Team can be contacted at
 core@php.net.
```

在发布 PHP 3.0 后，其使用量真正开始增长。版本 4.0 是由一些开发人员推动的，他们有兴趣对 PHP 的架构进行一些基本的更改。这些变化包括将语言与 Web 服务器之间的层抽象化，添加线程安全机制，以及添加更高级的两阶段解析/执行标记解析系统。这个由 Zeev 和 Andi 主要编写的新解析器被命名为 Zend 引擎。在许多开发人员的共同努力下，PHP 4.0 于 2000 年 5 月 22 日发布。

本书出版时，PHP 版本 7.3 已经发布了一段时间。已经发布了几个小的“点”版本，并且当前版本的稳定性非常高。正如您将在本书中看到的那样，这个 PHP 版本在服务器端的代码处理方面已经取得了一些重大进展。还包括许多其他小的更改、函数添加和功能增强。

## PHP 的广泛使用

图 1-1 显示了根据 W3Techs 编译的 [PHP 使用情况](http://bit.ly/XjyVZM)，截至 2019 年 3 月。这里最有趣的数据是，在所有调查的网站中，79% 使用了它，但仍然最广泛使用的是版本 5.0。如果您查看 [W3Techs 调查](https://bit.ly/36QtdEF) 中使用的方法，您将看到他们选择了全球排名前 1000 万的网站（基于流量；网站流行度）。显然，PHP 的广泛采用确实令人印象深刻！

![2019 年 3 月 PHP 使用情况](img/php4_0101.png)

###### 图 1-1 PHP 使用情况截至 2019 年 3 月

# 安装 PHP

如前所述，PHP 可用于许多操作系统和平台。因此，建议您查阅 [PHP 文档](https://oreil.ly/FzRfm)，以找到最符合您将使用的环境，并遵循相应的设置说明。

有时，您可能还想更改 PHP 配置的方式。要做到这一点，您将不得不更改 PHP 配置文件并重新启动您的 Web（Apache）服务器。每次您更改 PHP 的环境时，都必须重新启动 Web（Apache）服务器，以使这些更改生效。

PHP 的配置设置通常保存在名为*php.ini*的文件中。该文件中的设置控制 PHP 特性的行为，比如会话处理和表单处理。本书后面的章节会提到一些*php.ini*选项，但通常情况下，本书中的代码不需要自定义配置。详细信息请参阅[PHP 文档](https://oreil.ly/hqVvL)了解如何配置*php.ini*。

# PHP 简介

PHP 页面通常是带有嵌入 PHP 命令的 HTML 页面。这与许多其他动态网页解决方案形成对比，后者是生成 HTML 的脚本。Web 服务器处理 PHP 命令，并将其输出（以及来自文件的任何 HTML）发送到浏览器。示例 1-1 展示了一个完整的 PHP 页面。

##### 示例 1-1\. hello_world.php

```php
<html>
 <head>
 <title>Look Out World</title>
 </head>

 <body>
 <?php echo "Hello, world!"; ?>
 </body>
</html>
```

将示例 1-1 的内容保存到文件*hello_world.php*中，并将浏览器指向它。结果显示在图 1-2 中。

![hello_world.php 的输出](img/php4_0102.png)

###### 图 1-2\. hello_world.php 的输出

PHP 的`echo`命令会输出内容（本例中是字符串“Hello, world!”），并插入到 HTML 文件中。在这个例子中，PHP 代码位于`<?php`和`?>`标签之间。还有其他标记 PHP 代码的方法，请参阅第二章获取完整说明。

## 配置页面

PHP 函数`phpinfo()`创建一个 HTML 页面，包含有关 PHP 安装和当前配置的详细信息。您可以使用它查看是否安装了特定扩展，或者*php.ini*文件是否已定制。示例 1-2 是显示`phpinfo()`页面的完整页面。

##### 示例 1-2\. 使用 phpinfo()

```php
<?php phpinfo();?>
```

图 1-3 展示了示例 1-2 输出的第一部分。

![phpinfo()的部分输出](img/php4_0103.png)

###### 图 1-3\. phpinfo()的部分输出

## 表单

示例 1-3 创建并处理表单。用户提交表单后，输入的名称字段信息通过`$_SERVER['PHP_SELF']`表单动作发送回该页面。PHP 代码检查名称字段，并在找到时显示问候语。

##### 示例 1-3\. 处理表单（form.php）

```php
<html>
 <head>
 <title>Personalized Greeting Form</title>
 </head>

 <body>
 <?php if(!empty($_POST['name'])) {
 echo "Greetings, {$_POST['name']}, and welcome.";
 } ?>

 <form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="post">
 Enter your name: <input type="text" name="name" />
 <input type="submit" />
 </form>
 </body>
</html>
```

表单和消息显示在图 1-4 中。

![表单和问候页面](img/php4_0104.png)

###### 图 1-4\. 表单和问候页面

PHP 程序主要通过`$_POST`和`$_GET`数组变量访问表单值。第八章详细讨论了表单和表单处理。

## 数据库

PHP 支持所有流行的数据库系统，包括 MySQL、PostgreSQL、Oracle、Sybase、SQLite 和兼容 ODBC 的数据库。图 1-5 显示了通过 PHP 脚本运行的 MySQL 数据库查询的部分结果，显示了在图书评论站点上进行书籍搜索的结果。它列出了书名、出版年份和书籍的 ISBN 号。

![通过 PHP 脚本运行的 MySQL 书籍列表查询](img/php4_0105.png)

###### 图 1-5\. 通过 PHP 脚本运行的 MySQL 书籍列表查询

示例 1-4 中的代码连接到数据库，发出查询以检索所有可用的书籍（带有`WHERE`子句），并通过`while`循环为所有返回的结果生成表格输出。

###### 注意

此示例数据库的 SQL 代码在提供的文件*library.sql*中。在创建库数据库并准备好样本数据库后，您可以将此代码插入 MySQL 中，以测试以下代码示例及其在第九章中的相关示例。

##### 示例 1-4\. 查询图书数据库（booklist.php）

```php
<?php

$db = new mysqli("localhost", "petermac", "password", "library");

// make sure the above credentials are correct for your environment
if ($db->connect_error) {
 die("Connect Error ({$db->connect_errno}) {$db->connect_error}");
}

$sql = "SELECT * FROM books WHERE available = 1 ORDER BY title";
$result = $db->query($sql);

?>
<html>
<body>

<table cellSpacing="2" cellPadding="6" align="center" border="1">
 <tr>
 <td colspan="4">
 <h3 align="center">These Books are currently available</h3>
 </td>
 </tr>

 <tr>
 <td align="center">Title</td>
 <td align="center">Year Published</td>
 <td align="center">ISBN</td>
 </tr>
 <?php while ($row = $result->fetch_assoc()) { ?>
 <tr>
 <td><?php echo stripslashes($row['title']); ?></td>
 <td align="center"><?php echo $row['pub_year']; ?></td>
 <td><?php echo $row['ISBN']; ?></td>
 </tr>
 <?php } ?>
</table>

</body>
</html>
```

数据库提供的动态内容推动了网络核心的新闻、博客和电子商务网站。关于如何从 PHP 访问数据库的更多详细信息，请参阅第九章。

## 图形

使用 PHP，您可以轻松创建和操作图像，使用 GD 扩展。示例 1-5 提供了一个文本输入字段，允许用户指定按钮的文本。它获取一个空按钮图像文件，并将传递为 GET 参数`'message'`的文本居中放置在上面。然后将结果作为 PNG 图像发送回浏览器。

##### 示例 1-5\. 动态按钮（graphic_example.php）

```php
<?php
if (isset($_GET['message'])) {
 // load font and image, calculate width of text
 $font = dirname(__FILE__) . '/fonts/blazed.ttf';
 $size = 12;
 $image = imagecreatefrompng("button.png");
 $tsize = imagettfbbox($size, 0, $font, $_GET['message']);

 // center
 $dx = abs($tsize[2] - $tsize[0]);
 $dy = abs($tsize[5] - $tsize[3]);
 $x = (imagesx($image) - $dx) / 2;
 $y = (imagesy($image) - $dy) / 2 + $dy;

 // draw text
 $black = imagecolorallocate($im,0,0,0);
 imagettftext($image, $size, 0, $x, $y, $black, $font, $_GET['message']);

 // return image
 header("Content-type: image/png");
 imagepng($image);

 exit;
} ?>
<html>
 <head>
 <title>Button Form</title>
 </head>

 <body>
 <form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="GET">
 Enter message to appear on button:
 <input type="text" name="message" /><br />
 <input type="submit" value="Create Button" />
 </form>
 </body>
</html>
```

由示例 1-5 生成的表单显示在图 1-6 中。创建的按钮显示在图 1-7 中。

![按钮创建表单](img/php4_0106.png)

###### 图 1-6\. 按钮创建表单

![创建的按钮](img/php4_0107.png)

###### 图 1-7\. 创建的按钮

您可以使用 GD 动态调整图像大小、生成图表等。PHP 还有几个扩展可以生成 Adobe 流行的 PDF 文档。在第十章中深入讨论了动态图像生成，而第十一章则介绍了如何创建 Adobe PDF 文件。

# 下一步

现在您已经体验了 PHP 的可能性，可以开始学习如何使用这种语言。我们从其基本结构开始，特别关注用户定义的函数、字符串操作和面向对象编程。然后我们转向特定的应用领域，如网络、数据库、图形、XML 和安全性。最后，我们提供内置函数和扩展的快速参考。掌握这些章节，您将掌握 PHP！
