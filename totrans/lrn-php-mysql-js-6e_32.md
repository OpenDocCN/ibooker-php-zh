# 第二十九章 统筹全局

现在你已经读到了本书的末尾，在动态网页编程的各种方法、原理和应用的道路上迈出了第一个里程碑，我想给你留下一个真实的例子，让你能够深入理解。事实上，它是一个例子的集合，因为我组建了一个简单的社交网络项目，包括所有你期望在这样一个站点上看到的主要功能，更重要的是，这样一个 Web 应用程序。

在各种文件中，有 MySQL 表的创建和数据库访问、CSS、文件包含、会话控制、DOM 访问、异步调用、事件和错误处理、文件上传、图像处理、HTML5 画布等示例。

每个示例文件都是完整的、独立的，但与其他文件一起工作，构建一个完全可用的社交网络站点，甚至包括一个可以修改以完全改变项目外观和感觉的样式表。由于体积小巧，最终产品特别适用于智能手机或平板电脑等移动平台，但同样可以在全尺寸桌面计算机上运行良好。

并且你会发现，通过充分利用 jQuery 和 jQuery Mobile 的强大功能，代码运行速度快，易于使用，适应所有环境，并且外观优美。作为练习，你可能希望进一步调整代码，也许包括某种方式使用 React。

话虽如此，我已尽可能简化这段代码，以便易于理解。因此，它有很大的改进空间，例如通过存储哈希（不可逆单向函数的固定长度输出）来增强安全性，而不是未加密的密码，并且更顺畅地处理登录和注销之间的过渡——但让我们将这些留给读者作为传说中的练习，尤其是本章末尾没有问题。（嗯，就一个！）

我将这段代码的任何部分留给你，你认为可以使用并扩展为自己的目的。也许你甚至希望在这些文件的基础上创建一个属于自己的社交网络站点。

# 设计社交网络应用程序

在编写任何代码之前，我坐下来并想出了几个我认为对这样一个应用程序至关重要的事物。这些包括以下内容：

+   注册流程

+   登录表单

+   注销设施

+   会话控制

+   用户配置文件及其上传的缩略图

+   成员目录

+   添加成员作为好友

+   成员之间的公共和私人消息传递

+   项目样式化

我决定将项目命名为*Robin's Nest*；如果你使用这段代码，你需要在*index.php*和*header.php*文件中修改名称和标志。

# 在网站上

本章中的所有示例都可以在[我的 GitHub 存储库](https://github.com/RobinNixon/lpmj6)中找到，您可以下载一个存档文件，应将其解压到计算机上适当的位置。

特别值得注意的是，对于本章而言，在*ZIP*文件中，您将找到一个名为*robinsnest*的文件夹，其中包含所有以下示例所需的正确文件名。这意味着您可以轻松地将它们全部复制到您的 Web 开发文件夹中以进行尝试。

# functions.php

让我们直接进入项目，从示例 29-1，*functions.php*开始，这是主要函数的包含文件。这个文件不仅仅包含函数，因为我已经在这里添加了数据库登录详细信息，而不是使用另一个单独的文件。代码的前四行定义了要使用的数据库的主机和名称，以及用户名和密码。

默认情况下，在这个文件中，MySQL 用户名设置为*robinsnest*，程序使用的数据库也叫做*robinsnest*。第八章 提供了关于如何创建新用户和/或数据库的详细说明，但是简要回顾一下，首先通过输入 MySQL 命令提示符并键入以下命令来创建一个名为*robinsnest*的新数据库：

```php
CREATE DATABASE robinsnest;
```

然后，您可以像这样创建一个名为*robinsnest*的用户，该用户可以访问此数据库：

```php
CREATE USER 'robinsnest'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON robinsnest.* TO 'robinsnest'@'localhost';
```

显然，您应该为此用户使用比*password*更安全的密码，但为简单起见，这些示例中使用的是该密码——如果您在生产站点上使用此代码，请确保更改密码。

## 函数

该项目使用了五个主要函数：

<dfn class="keep-together">`createTable`</dfn>

检查表是否已存在，如果不存在则创建

<dfn class="keep-together">`queryMysql`</dfn>

向 MySQL 发出查询，如果失败则输出错误消息

<dfn class="keep-together">`destroySession`</dfn>

销毁 PHP 会话并清除其数据以注销用户

<dfn class="keep-together">`sanitizeString`</dfn>

从用户输入中删除潜在恶意代码或标签

<dfn class="keep-together">`showProfile`</dfn>

如果用户有图像和“关于我”消息，则显示它们

到目前为止，所有这些行为对您来说应该是显而易见的，除了`showProfile`可能是个例外，它查找名称为*<user.jpg>*（其中*<user>*是当前用户的用户名）的图像，如果找到，则显示它。它还显示用户保存的任何“关于我”文本。

我已确保所有需要的函数都有错误处理，以便捕获您可能引入的任何排版或其他错误并生成错误消息。但是，如果您在生产服务器上使用任何此代码，您将需要提供自己的错误处理程序以使代码更加用户友好。

所以，输入示例 29-1，将其保存为*functions.php*（或从伴随网站下载），然后您将准备好进入下一节。

##### 示例 29-1\. functions.php

```php
<?php // Example 01: functions.php
  $host = 'localhost';    // Change as necessary
  $data = 'robinsnest';   // Change as necessary
  $user = 'robinsnest';   // Change as necessary
  $pass = 'password';     // Change as necessary
  $chrs = 'utf8mb4';
  $attr = "mysql:host=$host;dbname=$data;charset=$chrs";
  $opts =
  [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES   => false,
  ];

  try
  {
    $pdo = new PDO($attr, $user, $pass, $opts);
  }
  catch (\PDOException $e)
  {
    throw new \PDOException($e->getMessage(), (int)$e->getCode());
  }

  function createTable($name, $query)
  {
    queryMysql("CREATE TABLE IF NOT EXISTS $name($query)");
    echo "Table '$name' created or already exists.<br>";
  }

  function queryMysql($query)
  {
    global $pdo;
    return $pdo->query($query);
  }

  function destroySession()
  {
    $_SESSION=array();

    if (session_id() != "" || isset($_COOKIE[session_name()]))
      setcookie(session_name(), '', time()-2592000, '/');

    session_destroy();
  }

  function sanitizeString($var)
  {
    global $pdo;
    $var = strip_tags($var);
    $var = htmlentities($var);
    if (get_magic_quotes_gpc())
      $var = stripslashes($var);
    $result = $pdo->quote($var);          // This adds single quotes
    return str_replace("'", "", $result); // So now remove them
  }

  function showProfile($user)
  {
    if (file_exists("$user.jpg"))
      echo "<img src='$user.jpg' style='float:left;'>";

    $result = $pdo->query("SELECT * FROM profiles WHERE user='$user'");

    while ($row = $result->fetch())
    {
      die(stripslashes($row['text']) . "<br style='clear:left;'><br>");
    }

    echo "<p>Nothing to see here, yet</p><br>";
  }
?>

```

###### 注意

如果您曾阅读本书的早期版本，在这些示例中使用了旧的`mysql`扩展名，后来改为`mysqli`，您现在将看到我再次转向到目前为止最好的解决方案，即`PDO`。

要使用`PDO`引用 MySQL 数据库，必须在`queryMysql`和`sanitizeString`函数中应用`global`关键字，以允许它们使用`$PDO`的值。

# 头文件

为了保持项目的统一性，项目的每个页面都需要访问相同的功能集。因此，我将这些内容放在了*header.php*（示例 29-2）中。这个文件实际上是其他文件所包含的文件。它包含了*functions.php*。这意味着每个文件只需要一次`require_once`。

*header.php*从调用`session_start`函数开始。正如您在第十三章中回忆的那样，这将设置一个会话，它将记住我们希望跨不同 PHP 文件存储的某些值。换句话说，它代表用户对站点的访问，并且如果用户在一段时间内忽略站点，则会超时。

会话开始后，程序输出所需的 HTML 来设置每个网页，包括加载样式表和各种所需的 JavaScript 库。在此之后，包含函数文件（*functions.php*）并将默认字符串“Welcome Guest”分配给`$userstr`。

在此之后，随机字符串值被分配给变量`$randstr`，在整个应用程序中用于追加到 URL 上，以便每个加载的页面看起来对 jQuery 滑动界面都是唯一的。如果没有这样做，看起来不应该改变到 jQuery 的页面将从其缓存中获取，这将导致尽可能好的性能。在具有静态信息的一组页面上，这是可以接受的，但这是一个动态应用程序，页面信息随时会变化，因此我们必须确保每个新的页面请求来自服务器而不是缓存。

接下来，代码检查会话变量`user`当前是否被赋值。如果是，表示用户已经登录，所以变量`$loggedin`被设置为`TRUE`，并且用户名从会话变量`user`中检索到 PHP 变量`$user`中，并相应地更新了`$userstr`。如果用户尚未登录，则`$loggedin`被设置为`FALSE`。

接下来，一些 HTML 输出欢迎用户（如果尚未登录则为访客），并输出 jQuery Mobile 页面头部和内容部分所需的`<div>`元素。

在此之后，根据`$loggedin`的值，`if`块将显示两套菜单中的一套。非登录集合仅提供主页、注册和登录选项，而登录版本则提供对应用程序功能的完全访问权限。按钮使用 jQuery Mobile 标记进行样式设置，例如`data-role='button'`用于将元素显示为按钮，`data-inline='true'`用于内联显示元素（类似于`<span>`元素），`data-transition="slide"`用于在单击时使新页面滑入视图中，详见第二十三章。

在这些 URL 中，您将注意到`r=$randstr`的使用，如前所述，以确保从服务器获取每个页面，而不是从 jQuery 的缓存中获取。

此文件应用的附加样式位于文件*styles.css*（详见示例 29-13，本章末详述）中。

##### 示例 29-2\. header.php

```php
<?php // Example 02: header.php
  session_start();

echo <<<_INIT
<!DOCTYPE html>
<html>
 <head>
 <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1'> 
    <link rel='stylesheet' href='jquery.mobile-1.4.5.min.css'>
    <link rel='stylesheet' href='styles.css'>
    <script src='javascript.js'></script>
    <script src='jquery-2.2.4.min.js'></script>
    <script src='jquery.mobile-1.4.5.min.js'></script>

_INIT;

  require_once 'functions.php';

  $userstr = 'Welcome Guest';
  $randstr = substr(md5(rand()), 0, 7);

  if (isset($_SESSION['user']))
  {
    $user     = $_SESSION['user'];
    $loggedin = TRUE;
    $userstr  = "Logged in as: $user";
  }
  else $loggedin = FALSE;

echo <<<_MAIN
    <title>Robin's Nest: $userstr</title>
  </head>
  <body>
    <div data-role='page'>
      <div data-role='header'>
        <div id='logo'
          class='center'>R<img id='robin' src='robin.gif'>bin's Nest</div>
        <div class='username'>$userstr</div>
      </div>
      <div data-role='content'>

_MAIN;

  if ($loggedin)
  {
echo <<<_LOGGEDIN
        <div class='center'>
          <a data-role='button' data-inline='true' data-icon='home'
            data-transition="slide" href='members.php?view=$user&r=$randstr'>Home</a>
          <a data-role='button' data-inline='true' data-icon='user'
            data-transition="slide" href='members.php?r=$randstr'>Members</a>
          <a data-role='button' data-inline='true' data-icon='heart'
            data-transition="slide" href='friends.php?r=$randstr'>Friends</a><br>
          <a data-role='button' data-inline='true' data-icon='mail'
            data-transition="slide" href='messages.php?r=$randstr'>Messages</a>
          <a data-role='button' data-inline='true' data-icon='edit'
            data-transition="slide" href='profile.php?r=$randstr'>Edit Profile</a>
          <a data-role='button' data-inline='true' data-icon='action'
            data-transition="slide" href='logout.php?r=$randstr'>Log out</a>
        </div>

_LOGGEDIN;
  }
  else
  {
echo <<<_GUEST
        <div class='center'>
          <a data-role='button' data-inline='true' data-icon='home'
            data-transition='slide' href='index.php?r=$randstr''>Home</a>
          <a data-role='button' data-inline='true' data-icon='plus'
            data-transition="slide" href='signup.php?r=$randstr''>Sign Up</a>
          <a data-role='button' data-inline='true' data-icon='check'
            data-transition="slide" href='login.php?r=$randstr''>Log In</a>
        </div>
        <p class='info'>(You must be logged in to use this app)</p>

_GUEST;
  }
?>

```

# setup.php

随着编写的这对包含文件，现在是时候设置它们将使用的 MySQL 表格了。我们可以使用示例 29-3，*setup.php*来实现这一点，您应该在调用任何其他文件之前在浏览器中输入并加载它，否则将会出现大量的 MySQL 错误。

创建的表格简洁明了，具有以下名称和列：

*members*

用户名 *user*（已索引）、密码 *pass*

*messages*

ID *id*（已索引）、作者 *auth*（已索引）、接收者 *recip*、消息类型 *pm*、消息 *message*

*friends*

用户名 *user*（已索引）、朋友的用户名 *friend*

*profiles*

用户名 *user*（已索引）、“关于我” *text*

由于`createTable`函数首先检查表是否已经存在，因此可以安全地多次调用此程序而不会生成任何错误。

如果您选择扩展此项目，则很可能需要向这些表格添加更多列。如果是这样，请记住在重新创建表格之前可能需要发出 MySQL 的`DROP TABLE`命令。

##### 示例 29-3\. setup.php

```php
<!DOCTYPE html> <!-- Example 03: setup.php -->
<html>
  <head>
    <title>Setting up database</title>
  </head>
  <body>
    <h3>Setting up...</h3>

<?php
 require_once 'functions.php';

 createTable('members',
 'user VARCHAR(16),
 pass VARCHAR(16),
 INDEX(user(6))');

 createTable('messages',
 'id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
 auth VARCHAR(16),
 recip VARCHAR(16),
 pm CHAR(1),
 time INT UNSIGNED,
 message VARCHAR(4096),
 INDEX(auth(6)),
 INDEX(recip(6))');

 createTable('friends',
 'user VARCHAR(16),
 friend VARCHAR(16),
 INDEX(user(6)),
 INDEX(friend(6))');

 createTable('profiles',
 'user VARCHAR(16),
 text VARCHAR(4096),
 INDEX(user(6))');
?>

    <br>...done.
  </body>
</html>
```

###### 警告

要使此示例有效，您必须首先确保已创建变量`$data`中指定的数据库，详情请参见示例 29-1，并且已授予给定名称的用户（在`$user`中）及其密码（在`$pass`中）访问权限。

# index.php

此文件虽然微不足道，但却是必不可少的，以便为项目提供一个首页。它所做的只是显示一个简单的欢迎消息。在完成的应用程序中，这将是您向用户推销站点优点以鼓励注册的地方。

顺便说一句，由于我们已经设置好了所有 MySQL 表格并创建了包含的文件，您现在可以加载示例 29-4，*index.php*，到您的浏览器中，以便首次查看新应用程序。它应该看起来像图 29-1。

##### 示例 29-4\. index.php

```php
<?php // Example 04: index.php
  session_start();
  require_once 'header.php';

  echo "<div class='center'>Welcome to Robin's Nest,";

  if ($loggedin) echo " $user, you are logged in";
  else           echo ' please sign up or log in';

  echo <<<_END
      </div><br>
    </div>
    <div data-role="footer">
      <h4>Web App from <i><a href='https://github.com/RobinNixon/lpmj6'
      target='_blank'>Learning PHP MySQL & JavaScript</a></i></h4>
    </div>
  </body>
</html>
_END;
?>
```

![](img/pmj6_2901.png)

###### 图 29-1\. 应用程序的主页

# signup.php

现在我们需要一个模块来让用户加入我们的新社交网络，那就是示例 29-5，*signup.php*。这是一个略长一些的程序，但你之前已经看过其所有部分。

让我们从 HTML 的末尾块开始。这是一个简单的表单，允许输入用户名和密码。但请注意使用了带有`id`为`used`的空`<div>`。这将是程序中异步调用的目的地，用于检查所需用户名是否可用。完整的工作原理请参见第十八章。

## 检查用户名是否可用

现在回到程序的起点，你会看到一段以函数`checkUser`开头的 JavaScript 代码块。当焦点从表单的`username`字段移除时，这段代码会被 JavaScript 的`onBlur`事件调用。首先，它会将我提到的带有`id`为`used`的`<div>`的内容设置为非空格`&nbsp;`，以清除它（如果之前有值的话）。

接下来会向*checkuser.php*发送请求，该程序报告`user`中的用户名是否可用。异步调用的返回结果（使用 jQuery 执行），一个友好的消息，然后放置在`used`的`<div>`中。

在 JavaScript 部分之后是一些 PHP 代码，你应该能从第十七章关于表单验证的讨论中识别出来。本节还使用`sanitizeString`函数，在查找数据库中的用户名之前移除潜在恶意字符，如果用户名尚未使用，则插入新的用户名`$user`和密码`$pass`。

## 登录

成功注册后，用户将被提示登录。此时更流畅的响应可能是自动登录新创建的用户，但因为我不想过度复杂化代码，所以保持了注册和登录模块的分离。不过，如果你想要的话，很容易实现这一点。

此文件使用 CSS 类`fieldname`来排列表单字段，使它们在列下面整齐对齐。加载到浏览器中时（并与稍后显示的*checkuser.php*配合使用），该程序将看起来像图 29-2，你可以看到异步调用已经确认用户名*Robin*可用。如果希望密码字段只显示星号，请将其类型从`text`更改为`password`。

记住，在运行任何其他 PHP 程序文件之前，你必须先运行*setup.php*。

###### 警告

在生产服务器上，我不建议像我这样将用户密码明文存储，出于空间和简易性的考虑才这样做。相反，你应该对其进行盐值处理并存储为单向哈希字符串。有关如何执行此操作的详细信息，请参阅第十三章。

![](img/pmj6_2902.png)

###### 图 29-2\. 注册页面

##### 示例 29-5\. signup.php

```php
<?php // Example 05: signup.php
  require_once 'header.php';

echo <<<_END
  <script>
    function checkUser(user)
    {
      if (user.value == '')
      {
        $('#used').html('&nbsp;')
        return
      }

      $.post
      (
        'checkuser.php',
        { user : user.value },
        function(data)
        {
          $('#used').html(data)
        }
      )
    }
  </script>  
_END;

  $error = $user = $pass = "";
  if (isset($_SESSION['user'])) destroySession();

  if (isset($_POST['user']))
  {
    $user = sanitizeString($_POST['user']);
    $pass = sanitizeString($_POST['pass']);

    if ($user == "" || $pass == "")
      $error = 'Not all fields were entered<br><br>';
    else
    {
      $result = queryMysql("SELECT * FROM members WHERE user='$user'");

      if ($result->rowCount())
        $error = 'That username already exists<br><br>';
      else
      {
        queryMysql("INSERT INTO members VALUES('$user', '$pass')");
        die('<h4>Account created</h4>Please Log in.</div></body></html>');
      }
    }
  }

echo <<<_END
      <form method='post' action='signup.php?r=$randstr'>$error
      <div data-role='fieldcontain'>
        <label></label>
        Please enter your details to sign up
      </div>
      <div data-role='fieldcontain'>
        <label>Username</label>
        <input type='text' maxlength='16' name='user' value='$user'
          onBlur='checkUser(this)'>
        <label></label><div id='used'>&nbsp;</div>
      </div>
      <div data-role='fieldcontain'>
        <label>Password</label>
        <input type='text' maxlength='16' name='pass' value='$pass'>
      </div>
      <div data-role='fieldcontain'>
        <label></label>
        <input data-transition='slide' type='submit' value='Sign Up'>
      </div>
    </div>
  </body>
</html>
_END;
?>

```

# checkuser.php

配合*signup.php*，这里是示例 29-6，*checkuser.php*，它在数据库中查找用户名并返回指示它是否已经被使用的字符串。因为它依赖于`sanitizeString`和`queryMysql`函数，程序首先包含文件*functions.php*。

然后，如果`$_POST`变量`user`有值，函数将在数据库中查找它，并根据它是否作为用户名存在输出“对不起，用户名*`user`*已被使用”或“用户名*`user`*可用”。只需检查函数调用返回的值`$result->rowCount`即可，如果未找到名称，则返回`0`，如果找到，则返回`1`。

HTML 实体`&#x2718;`和`&#x2714;`也用于在字符串前面加上红叉或绿勾，并且根据*styles.css*中定义的`taken`类显示红色或`available`类显示绿色，本章后面将显示。

##### 示例 29-6\. checkuser.php

```php
<?php // Example 06: checkuser.php
  require_once 'functions.php';

  if (isset($_POST['user']))
  {
    $user   = sanitizeString($_POST['user']);
    $result = queryMysql("SELECT * FROM members WHERE user='$user'");

    if ($result->rowCount())
      echo  "<span class='taken'>&nbsp;&#x2718; " .
            "The username '$user' is taken</span>";
    else
      echo "<span class='available'>&nbsp;&#x2714; " .
           "The username '$user' is available</span>";
  }
?>

```

# login.php

现在用户可以在网站上注册了，示例 29-7，*login.php*提供了让他们登录所需的代码。像注册页面一样，它具有简单的 HTML 表单和一些基本的错误检查，还使用`sanitizeString`在查询 MySQL 数据库之前。

在这里需要注意的主要是，一旦验证用户名和密码成功，会话变量`user`和`pass`将分别被赋予用户名和密码的值。只要当前会话保持活动状态，这些变量将被项目中的所有程序访问，从而允许它们自动为已登录用户提供访问权限。

如果成功登录，您可能会对`die`函数的使用感兴趣。这是因为它将`echo`和`exit`命令合并在一起，从而节省了一行代码。为了样式化，这个文件（像大多数文件一样）将类`main`应用于内容，以使其从左侧边缘缩进。

当您在浏览器中调用此程序时，它应该看起来像图 29-3。注意在此处使用了`password`的输入类型，以用星号掩盖密码，防止任何人看到用户的密码。

##### 示例 29-7\. login.php

```php
 <?php // Example 07: login.php
  require_once 'header.php';
  $error = $user = $pass = "";

  if (isset($_POST['user']))
  {
    $user = sanitizeString($_POST['user']);
    $pass = sanitizeString($_POST['pass']);

    if ($user == "" || $pass == "")
      $error = 'Not all fields were entered';
    else
    {
      $result = queryMySQL("SELECT user,pass FROM members
        WHERE user='$user' AND pass='$pass'");

      if ($result->rowCount() == 0)
      {
        $error = "Invalid login attempt";
      }
      else
      {
        $_SESSION['user'] = $user;
        $_SESSION['pass'] = $pass;
        die("<div class='center'>You are now logged in. Please
             <a data-transition='slide'
               href='members.php?view=$user&r=$randstr'>click here</a>
               to continue.</div></div></body></html>");
      }
    }
  }

echo <<<_END
      <form method='post' action='login.php?r=$randstr'>
        <div data-role='fieldcontain'>
          <label></label>
          <span class='error'>$error</span>
        </div>
        <div data-role='fieldcontain'>
          <label></label>
          Please enter your details to log in
        </div>
        <div data-role='fieldcontain'>
          <label>Username</label>
          <input type='text' maxlength='16' name='user' value='$user'>
        </div>
        <div data-role='fieldcontain'>
          <label>Password</label>
          <input type='password' maxlength='16' name='pass' value='$pass'>
        </div>
        <div data-role='fieldcontain'>
          <label></label>
          <input data-transition='slide' type='submit' value='Login'>
        </div>
      </form>
    </div>
  </body>
</html>
_END;
?>

```

![](img/pmj6_2903.png)

###### 图 29-3\. 登录页面

# profile.php

在注册并登录后，新用户可能希望做的第一件事之一是创建个人资料，这可以通过示例 29-8，*profile.php*完成。我认为您会在这里找到一些有趣的代码，比如用于上传、调整大小和锐化图像的例程。

让我们从代码末尾的主要 HTML 开始。这与您刚刚看到的表单类似，但这次有一个参数`enctype='multipart/form-data'`。这使我们能够同时发送多种类型的数据，允许上传图片以及一些文本。还有一个`file`类型的输入，它创建一个“浏览”按钮，用户可以单击以选择要上传的文件。

当表单提交时，程序开始时执行的代码。它首先确保用户已登录，然后才允许程序执行继续。只有在这样做后才显示页面标题。

###### 注意

如第二十三章所述，由于 jQuery Mobile 使用异步通信的方式，使用 HTML 上传文件是不可能的，除非您通过向`<form>`元素添加属性`data-ajax='false'`来禁用该功能。这将允许 HTML 文件上传正常进行，但您将失去执行页面更改动画的能力。

## 添加“关于我”的文本

接下来，检查`$_POST`变量`text`，看看是否有文本发布到程序中。如果有，将对其进行过滤，并将所有长的空白序列（包括换行符和回车符）替换为单个空格。此函数包含了双重安全检查，确保用户实际存在于数据库中，并且在将此文本插入数据库中成为用户的“关于我”详情之前，没有任何黑客尝试能够成功。

如果没有发布文本，则查询数据库以查看是否已存在任何文本，以便预填充用户编辑的`<textarea>`。

## 添加个人资料图片

接下来，我们转到检查`$_FILES`系统变量是否已上传图像的部分。如果是，则创建一个名为`$saveto`的字符串变量，基于用户的用户名后跟扩展名*.jpg*。例如，名为*Jill*的用户将导致`$saveto`具有值*Jill.jpg*。这是保存上传图像以供用户个人资料使用的文件。

接下来，检查上传的图像类型，并仅在它是*.jpeg*、*.png*或*.gif*图像时接受。成功后，使用一个`imagecreatefrom`函数之一将上传的图像填充到变量`$src`中，根据上传的图像类型。现在，该图像以 PHP 可处理的原始格式存在。如果图像不是允许的类型，则将标志`$typeok`设置为`FALSE`，防止处理图像上传代码的最后部分。

## 处理图像

首先，我们使用以下语句将图像的尺寸存储在`$w`和`$h`中，这是将数组中的值快速分配给单独变量的一种方法：

```php
list($w, $h) = getimagesize($saveto);
```

然后，使用变量 `$max` 的值（设置为 `100`），计算新的尺寸，以确保新图像的宽高比与原图相同，但各维度均不超过 100 像素。这将为变量 `$tw` 和 `$th` 赋予所需的新值。如果需要更小或更大的缩略图，只需相应地更改变量 `$max` 的值即可。

接下来，调用 `imagecreatetruecolor` 函数创建一个新的空画布 `$tw` 宽、`$th` 高的 `$tmp`。然后使用 `imagecopyresampled` 函数将图像从 `$src` 重新取样到新的 `$tmp`。有时重新取样图像会导致略微模糊的副本，因此下一段代码使用 `imageconvolution` 函数稍微清晰化图像。

最后，将图像保存为 *.jpeg* 文件，保存位置由变量 `$saveto` 定义，然后使用 `imagedestroy` 函数从内存中移除原始图像和调整大小后的图像画布，释放所使用的内存。

## 显示当前个人资料

最后但并非最不重要的是，为了在编辑之前让用户查看当前的个人资料，将在输出表单 HTML 之前调用 *functions.php* 中的 `showProfile` 函数。如果尚未存在个人资料，则不会显示任何内容。

当显示个人资料图像时，会应用 CSS 样式，为其添加边框、阴影，并在其右侧添加边距，以将个人资料文本与图像分隔开。将 示例 29-8 加载到浏览器中的结果显示在 图 29-4 中，您可以看到 `<textarea>` 已经预填充了“关于我”的文本。

##### 示例 29-8\. profile.php

```php
<?php // Example 08: profile.php
  require_once 'header.php';

  if (!$loggedin) die("</div></body></html>");

  echo "<h3>Your Profile</h3>";

  $result = queryMysql("SELECT * FROM profiles WHERE user='$user'");

  if (isset($_POST['text']))
  {
    $text = sanitizeString($_POST['text']);
    $text = preg_replace('/\s\s+/', ' ', $text);

    if ($result->rowCount())
         queryMysql("UPDATE profiles SET text='$text' where user='$user'");
    else queryMysql("INSERT INTO profiles VALUES('$user', '$text')");
  }
  else
  {
    if ($result->rowCount())
    {
      $row  = $result->fetch();
      $text = stripslashes($row['text']);
    }
    else $text = "";
  }

  $text = stripslashes(preg_replace('/\s\s+/', ' ', $text));

  if (isset($_FILES['image']['name']))
  {
    $saveto = "$user.jpg";
    move_uploaded_file($_FILES['image']['tmp_name'], $saveto);
    $typeok = TRUE;

    switch($_FILES['image']['type'])
    {
      case "image/gif":   $src = imagecreatefromgif($saveto); break;
      case "image/jpeg":  // Both regular and progressive jpegs
      case "image/pjpeg": $src = imagecreatefromjpeg($saveto); break;
      case "image/png":   $src = imagecreatefrompng($saveto); break;
      default:            $typeok = FALSE; break;
    }

    if ($typeok)
    {
      list($w, $h) = getimagesize($saveto);

      $max = 100;
      $tw  = $w;
      $th  = $h;

      if ($w > $h && $max < $w)
      {
        $th = $max / $w * $h;
        $tw = $max;
      }
      elseif ($h > $w && $max < $h)
      {
        $tw = $max / $h * $w;
        $th = $max;
      }
      elseif ($max < $w)
      {
        $tw = $th = $max;
      }

      $tmp = imagecreatetruecolor($tw, $th);
      imagecopyresampled($tmp, $src, 0, 0, 0, 0, $tw, $th, $w, $h);
      imageconvolution($tmp, array(array(-1, -1, -1),
        array(-1, 16, -1), array(-1, -1, -1)), 8, 0);
      imagejpeg($tmp, $saveto);
      imagedestroy($tmp);
      imagedestroy($src);
    }
  }

  showProfile($user);

echo <<<_END
      <form data-ajax='false' method='post'
        action='profile.php?r=$randstr' enctype='multipart/form-data'>
      <h3>Enter or edit your details and/or upload an image</h3>
      <textarea name='text'>$text</textarea><br>
      Image: <input type='file' name='image' size='14'>
      <input type='submit' value='Save Profile'>
      </form>
    </div><br>
  </body>
</html>
_END;
?>

```

![](img/pmj6_2904.png)

###### 图 29-4\. 编辑用户配置文件

# members.php

使用 示例 29-9，*members.php*，您的用户将能够找到其他成员，并选择将其添加为好友（如果他们已经是好友，则删除）。此程序有两种模式。第一种列出所有成员及其与您的关系，第二种显示用户的个人资料。

## 查看用户个人资料

后续代码是为了处理后一种模式，首先检测从 `$_GET` 数组中获取的变量 `view` 是否存在。如果存在，则用户想要查看某人的个人资料，因此程序将使用 `showProfile` 函数执行该操作，并提供一些链接到用户的朋友和消息。

## 添加和删除好友

在此之后，将测试两个 `$_GET` 变量 `add` 和 `remove`。如果其中一个变量有值，它将是要添加或删除的用户的用户名。我们通过在 MySQL 的 *friends* 表中查找用户来实现这一点，并插入或从表中删除该用户名。

当然，每个提交的变量都首先通过 `sanitizeString` 函数进行处理，以确保在与 MySQL 一起使用时安全。

## 列出所有成员

代码的最后部分发出 SQL 查询来列出所有用户名。代码在输出页面标题之前将返回的数字放入变量`$num`中。

然后，`for`循环遍历每个成员，获取其详细信息，然后在*friends*表中查找他们，看他们是被用户关注还是正在关注用户。如果某人既是粉丝又是关注者，则被归类为共同朋友。

当用户关注另一个成员时，变量`$t1`为非零值；当另一个成员关注用户时，变量`$t2`为非零值。根据这些值，在每个用户名后显示文本，显示与当前用户的关系（如果有的话）。

还显示图标以展示这些关系。双向箭头表示用户是共同朋友，左箭头表示用户正在关注另一个成员，右箭头表示另一个成员正在关注用户。

最后，根据用户是否在关注另一个成员，提供链接以添加或取消该成员作为朋友。

当在浏览器中调用示例 29-9 时，显示效果类似于图 29-5。请注意用户被邀请“关注”一个未关注的成员，但如果成员已经在关注用户，则会提供一个“回礼”链接以回报友谊。如果用户已经在关注另一个成员，则用户可以选择“取消”来结束关注。

![](img/pmj6_2905.png)

###### 图 29-5\. 使用成员模块

###### 注意

在生产服务器上，可能会有数千甚至数十万的用户，因此您将大幅修改此程序，以支持搜索“关于我”文本、分页输出等。

##### 示例 29-9\. members.php

```php
<?php // Example 09: members.php
  require_once 'header.php';

  if (!$loggedin) die("</div></body></html>");

  if (isset($_GET['view']))
  {
    $view = sanitizeString($_GET['view']);

    if ($view == $user) $name = "Your";
    else                $name = "$view's";

    echo "<h3>$name Profile</h3>";
    showProfile($view);
    echo "<a data-role='button' data-transition='slide'
          href='messages.php?view=$view&r=$randstr'>View $name messages</a>";
    die("</div></body></html>");
  }

  if (isset($_GET['add']))
  {
    $add = sanitizeString($_GET['add']);

    $result = queryMysql("SELECT * FROM friends
      WHERE user='$add' AND friend='$user'");
    if (!$result->rowCount)
      queryMysql("INSERT INTO friends VALUES ('$add', '$user')");
  }
  elseif (isset($_GET['remove']))
  {
    $remove = sanitizeString($_GET['remove']);
    queryMysql("DELETE FROM friends
      WHERE user='$remove' AND friend='$user'");
  }

  $result = queryMysql("SELECT user FROM members ORDER BY user");
  $num    = $result->rowCount();

  while ($row = $result->fetch())
  {
    if ($row['user'] == $user) continue;

    echo "<li><a data-transition='slide' href='members.php?view=" .
      $row['user'] . "&$randstr'>" . $row['user'] . "</a>";
    $follow = "follow";

    $result1 = queryMysql("SELECT * FROM friends WHERE
      user='" . $row['user'] . "' AND friend='$user'");
    $t1      = $result1->rowCount();

    $result1 = queryMysql("SELECT * FROM friends WHERE
      user='$user' AND friend='" . $row['user'] . "'");
    $t2      = $result1->rowCount();

    if (($t1 + $t2) > 1) echo " &harr; is a mutual friend";
    elseif ($t1)         echo " &larr; you are following";
    elseif ($t2)       { echo " &rarr; is following you";
                         $follow = "recip"; }

    if (!$t1) echo " [<a data-transition='slide'
      href='members.php?add=" . $row['user'] . "&r=$randstr'>$follow</a>]";
    else      echo " [<a data-transition='slide'
      href='members.php?remove=" . $row['user'] . "&r=$randstr'>drop</a>]";
  }

?>
    </ul></div>
  </body>
</html>

```

# friends.php

显示用户的朋友和粉丝的模块是示例 29-10，*friends.php*。这个程序像*members.php*一样查询*friends*表，但只针对单个用户。然后显示所有该用户的共同朋友和粉丝，以及他们正在关注的人。

所有粉丝都保存在名为`$followers`的数组中，所有正在关注的人都放在名为`$following`的数组中。然后使用一段整洁的代码来提取所有既是粉丝又被用户关注的人，如下所示：

```php
$mutual = array_intersect($followers, $following);
```

函数`array_intersect`提取两个数组中共同的所有成员，并返回一个新数组，其中仅包含这些人。然后可以使用`array_diff`函数来分别处理`$followers`和`$following`数组，只保留*不是*共同朋友的人，如下所示：

```php
$followers = array_diff($followers, $mutual);
$following = array_diff($following, $mutual);
```

这导致数组`$mutual`仅包含共同的朋友，`$followers`仅包含粉丝（没有共同朋友），`$following`仅包含正在关注的人（没有共同朋友）。

现在我们掌握了这些数组，分别显示每个类别的成员就变得非常简单，如 Figure 29-6 所示。PHP 的`sizeof`函数返回数组中元素的数量；在这里，我仅在大小非零时触发代码（即当该类型的朋友存在时）。请注意，通过在相关位置使用变量`$name1`、`$name2`和`$name3`，代码可以知道您正在查看自己的朋友列表，并使用*Your*和*You are*而不仅仅显示用户名。如果希望在此屏幕上显示用户的个人资料信息，则可以取消注释该行。

##### Example 29-10\. friends.php

```php
<?php // Example 10: friends.php
  require_once 'header.php';

  if (!$loggedin) die("</div></body></html>");

  if (isset($_GET['view'])) $view = sanitizeString($_GET['view']);
  else                      $view = $user;

  if ($view == $user)
  {
    $name1 = $name2 = "Your";
    $name3 =          "You are";
  }
  else
  {
    $name1 = "<a data-transition='slide'
              href='members.php?view=$view&r=$randstr'>$view</a>'s";
    $name2 = "$view's";
    $name3 = "$view is";
  }

  // Uncomment this line if you wish the user’s profile to show here
  // showProfile($view);

  $followers = array();
  $following = array();

  $result = queryMysql("SELECT * FROM friends WHERE user='$view'");

  while ($row = $result->fetch())
  {
    $followers[$j] = $row['friend'];
  }

  $result = queryMysql("SELECT * FROM friends WHERE friend='$view'");

  while ($row = $result->fetch())
  {
    $following[$j] = $row['user'];
  }

  $mutual    = array_intersect($followers, $following);
  $followers = array_diff($followers, $mutual);
  $following = array_diff($following, $mutual);
  $friends   = FALSE;

  echo "<br>";

  if (sizeof($mutual))
  {
    echo "<span class='subhead'>$name2 mutual friends</span><ul>";
    foreach($mutual as $friend)
      echo "<li><a data-transition='slide'
            href='members.php?view=$friend&r=$randstr'>$friend</a>";
    echo "</ul>";
    $friends = TRUE;
  }

  if (sizeof($followers))
  {
    echo "<span class='subhead'>$name2 followers</span><ul>";
    foreach($followers as $friend)
      echo "<li><a data-transition='slide'
            href='members.php?view=$friend&r=$randstr'>$friend</a>";
    echo "</ul>";
    $friends = TRUE;
  }

  if (sizeof($following))
  {
    echo "<span class='subhead'>$name3 following</span><ul>";
    foreach($following as $friend)
      echo "<li><a data-transition='slide'
            href='members.php?view=$friend&r=$randstr'>$friend</a>";
    echo "</ul>";
    $friends = TRUE;
  }

  if (!$friends) echo "<br>You don't have any friends yet.";
?>
    </div><br>
  </body>
</html>

```

![](img/pmj6_2906.png)

###### Figure 29-6\. 显示用户的朋友和追随者

# messages.php

主要模块的最后一个是 Example 29-11，*messages.php*。程序首先检查变量`text`中是否有消息发布。如果有，它将被插入到*messages*表中。同时，`pm`的值也被存储。这表示消息是私有还是公共的。`0`代表公共消息，`1`代表私有消息。

接下来，显示用户的个人资料以及输入消息的表单，还有用于选择私有或公共消息的单选按钮。之后，根据它们是私有还是公共消息，显示所有的消息。如果是公共消息，所有用户都可以看到，但私有消息只有发件人和收件人可以看到。所有这些都通过对 MySQL 数据库的一些查询来处理。另外，当消息是私有时，它会以*whispered*开头并显示为斜体。

最后，程序显示几个链接，用于刷新消息（以防其他用户同时发布消息）和查看用户的朋友。再次使用变量`$name1`和`$name2`的技巧，使得在查看自己的个人资料时，显示为*Your*而不是用户名。

##### Example 29-11\. messages.php

```php
<?php // Example 11: messages.php
  require_once 'header.php';

  if (!$loggedin) die("</div></body></html>");

  if (isset($_GET['view'])) $view = sanitizeString($_GET['view']);
  else                      $view = $user;

  if (isset($_POST['text']))
  {
    $text = sanitizeString($_POST['text']);

    if ($text != "")
    {
      $pm   = substr(sanitizeString($_POST['pm']),0,1);
      $time = time();
      queryMysql("INSERT INTO messages VALUES(NULL, '$user',
        '$view', '$pm', $time, '$text')");
    }
  }

  if ($view != "")
  {
    if ($view == $user) $name1 = $name2 = "Your";
    else
    {
      $name1 = "<a href='members.php?view=$view&r=$randstr'>$view</a>'s";
      $name2 = "$view's";
    }

    echo "<h3>$name1 Messages</h3>";
    showProfile($view);

    echo <<<_END
      <form method='post' action='messages.php?view=$view&r=$randstr'>
        <fieldset data-role="controlgroup" data-type="horizontal">
          <legend>Type here to leave a message</legend>
          <input type='radio' name='pm' id='public' value='0' checked='checked'>
          <label for="public">Public</label>
          <input type='radio' name='pm' id='private' value='1'>
          <label for="private">Private</label>
        </fieldset>
      <textarea name='text'></textarea>
      <input data-transition='slide' type='submit' value='Post Message'>
    </form><br>
_END;

    date_default_timezone_set('UTC');

    if (isset($_GET['erase']))
    {
      $erase = sanitizeString($_GET['erase']);
      queryMysql("DELETE FROM messages WHERE id='$erase' AND recip='$user'");
    }

    $query  = "SELECT * FROM messages WHERE recip='$view' ORDER BY time DESC";
    $result = queryMysql($query);

    while ($row = $result->fetch())
    {
      if ($row['pm'] == 0 || $row['auth'] == $user || $row['recip'] == $user)
      {
        echo date('M jS \'y g:ia:', $row['time']);
        echo " <a href='messages.php?view=" . $row['auth'] .
             "&r=$randstr'>" . $row['auth']. "</a> ";

        if ($row['pm'] == 0)
          echo "wrote: &quot;" . $row['message'] . "&quot; ";
        else
          echo "whispered: <span class='whisper'>&quot;" .
            $row['message']. "&quot;</span> ";

        if ($row['recip'] == $user)
          echo "[<a href='messages.php?view=$view" .
               "&erase=" . $row['id'] . "&r=$randstr'>erase</a>]";

        echo "<br>";
      }
    }
  }

  if (!$num)
    echo "<br><span class='info'>No messages yet</span><br><br>";

  echo "<br><a data-role='button'
        href='messages.php?view=$view&r=$randstr'>Refresh messages</a>";
?>

    </div><br>
  </body>
</html>

```

您可以在浏览器中查看此程序的结果，如 Figure 29-7 所示。请注意，用户查看自己的消息时，提供了链接以删除不想保留的任何消息。还要注意，jQuery Mobile 如何实现对选择发送私有或公共消息的单选按钮的样式化。这在 Chapter 23 中有详细说明。

![](img/pmj6_2907.png)

###### Figure 29-7\. The messaging module

# logout.php

我们社交网络食谱的最后一步是示例 29-12 的*logout.php*，这是一个登出页面，用于关闭会话并删除任何相关数据和 cookie。调用此程序的结果显示在图 29-8 中，用户现在被要求点击一个链接，该链接将带他们到未登录的主页，并从屏幕顶部删除登录链接。当然，您也可以编写 JavaScript 或 PHP 重定向来执行此操作（如果您希望保持登出的整洁状态，则这可能是个好主意）。

![](img/pmj6_2908.png)

###### 图 29-8\. 登出页面

##### 示例 29-12\. logout.php

```php
<?php // Example 12: logout.php
  require_once 'header.php';

  if (isset($_SESSION['user']))
  {
    destroySession();
    echo "<br><div class='center'>You have been logged out. Please
         <a data-transition='slide'
           href='index.php?r=$randstr'>click here</a>
           to refresh the screen.</div>";
  }
  else echo "<div class='center'>You cannot log out because
             you are not logged in</div>";
?>
    </div>
  </body>
</html>

```

# styles.css

此项目使用的样式表显示在示例 29-13 中。以下是几组声明：

<dfn class="keep-together">`*`</dfn>

使用通用选择器为项目设置默认字体系列和大小。

<dfn class="keep-together">`body`</dfn>

设置项目窗口的宽度，水平居中，指定背景颜色，并添加边框。

<dfn class="keep-together">`html`</dfn>

设置 HTML 部分的背景颜色。

<dfn class="keep-together">`img`</dfn>

给所有图像添加边框、阴影和右侧边距。

<dfn class="keep-together">`.username`</dfn>

居中用户名，并选择字体系列、大小、颜色、背景和填充以显示它。

<dfn class="keep-together">`.info`</dfn>

此类用于显示重要信息。它设置了背景和前景文本颜色，应用了边框和填充，并缩进了使用它的元素。

<dfn class="keep-together">`.center`</dfn>

此类用于居中`<div>`元素的内容。

<dfn class="keep-together">`.subhead`</dfn>

此类强调文本的各个部分。

<dfn class="keep-together">`.taken`、`.available`、`.error`和`.whisper`</dfn>

这些声明设置用于显示不同类型信息的颜色和字体样式。

<dfn class="keep-together">`#logo`</dfn>

将 logo 文本样式设为 HTML5 以外的浏览器中使用时的备用，以及当画布 logo 无法创建时。

<dfn class="keep-together">`#robin`</dfn>

对页面标题中的 robin 图像进行对齐。

<dfn class="keep-together">`#used`</dfn>

确保由*checkuser.php*异步调用填充的元素，如果用户名已被使用，则不会与上面的字段太接近。

##### 示例 29-13\. styles.css

```php
* {
  font-family:verdana,sans-serif;
  font-size  :14pt;
}

body {
  width     :700px;
  margin    :20px auto;
  background:#f8f8f8;
  border    :1px solid #888;
}

html {
  background:#fff
}

img {
  border            :1px solid black;
  margin-right      :15px;
  -moz-box-shadow   :2px 2px 2px #888;
  -webkit-box-shadow:2px 2px 2px #888;
  box-shadow        :2px 2px 2px #888;
}

.username {
  text-align :center;
  background :#eb8;
  color      :#40d;
  font-family:helvetica;
  font-size  :20pt;
  padding    :4px;
}

.info {
  font-style :italic;
  margin     :40px 0px;
  text-align :center;
}

.center {
  text-align:center;
}

.subhead {
  font-weight:bold;
}

.taken, .error {
  color:red;
}

.available {
  color:green;
}

.whisper {
  font-style:italic;
  color     :#006600;
}

#logo {
  font-family:Georgia;
  font-weight:bold;
  font-style :italic;
  font-size  :97px;
  color      :red;
  }

#robin {
  position          :relative;
  border            :0px;
  margin-left       :-6px;
  margin-right      :0px;
  top               :17px;
  -moz-box-shadow   :0px 0px 0px;
  -webkit-box-shadow:0px 0px 0px;
  box-shadow        :0px 0px 0px;
}

#used {
  margin-top:50px;
}

```

# javascript.js

最后，还有 JavaScript 文件（见示例 29-14），其中包含本书中始终使用的`O`、`S`和`C`函数。

##### 示例 29-14\. javascript.js

```php
function O(i)
{
  return typeof i == 'object' ? i : document.getElementById(i)
}

function S(i)
{
  return O(i).style
}

function C(i)
{
  return document.getElementsByClassName(i)
}
```

如众所周知，这就是全部。如果您基于此代码或本书中的其他示例编写了任何内容，或者从中获得了其他收获，我很高兴能够提供帮助，并感谢您阅读本书。

# 问题

1.  你喜欢从这本书中学到了什么？

查看“第二十九章答案”，在附录 A 中找到这个问题的答案。
