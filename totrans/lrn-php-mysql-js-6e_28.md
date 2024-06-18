# 第二十五章。HTML5 简介

HTML5 代表了网页设计、布局和可用性的重大进步。它提供了一种简单的方法，在网页浏览器中操作图形，而无需依赖 Flash 等插件，提供了在网页中插入音频和视频的方法（同样无需插件），并消除了 HTML 演变过程中出现的几个令人讨厌的不一致之处。

此外，HTML5 还包括许多其他增强功能，如处理地理位置信息、Web Workers 管理后台任务、改进的表单处理以及访问本地存储捆绑（远远超出了 Cookie 的有限功能）。

不过，HTML5 的有趣之处在于它是一个持续发展的过程，各个浏览器在不同时间采纳了不同的特性。幸运的是，现在所有主流浏览器（市场份额超过 1%的浏览器，如 Chrome、Internet Explorer、Edge、Firefox、Safari、Opera 以及 Android 和 iOS 的浏览器）都已支持所有最重要和最受欢迎的 HTML5 新增功能。

# 画布

最初由苹果为其 Safari 浏览器的 WebKit 渲染引擎引入（该引擎本身起源于 KDE 的 HTML 布局引擎），*canvas*元素使我们能够在网页中绘制图形，而无需依赖 Java 或 Flash 等插件。在标准化后，所有其他浏览器都采纳了 canvas，并且它现在是现代 Web 开发的重要组成部分。

与其他 HTML 元素一样，canvas 只是网页中的一个元素，具有定义的尺寸，在其中可以使用 JavaScript 插入内容——在本例中，用于绘制图形。您通过使用`<canvas>`标签创建 canvas，必须为其分配一个 ID，以便 JavaScript 知道它正在访问哪个 canvas（因为页面上可以有多个 canvas）。

在示例 25-1 中，我创建了一个带有 ID `mycanvas` 的 canvas 元素，在不支持 canvas 的浏览器中仅显示一些文本。在下面的 JavaScript 部分中，绘制了日本国旗的代码（如图 25-1 所示）。

##### 示例 25-1。使用 HTML5 canvas 元素

```php
<!DOCTYPE html>
<html>
  <head>
    <title>The HTML5 Canvas</title>
    <script src='OSC.js'></script>
  </head>
  <body>
    <canvas id='mycanvas' width='320' height='240'>
      This is a canvas element given the ID <i>mycanvas</i>
      This text is visible only in non-HTML5 browsers
    </canvas>

    <script>
      canvas            = O('mycanvas')
      context           = canvas.getContext('2d')
      context.fillStyle = 'red'
      S(canvas).border  = '1px solid black'

      context.beginPath()
      context.moveTo(160, 120)
      context.arc(160, 120, 70, 0, Math.PI * 2, false)
      context.closePath()
      context.fill()
    </script>
  </body>
</html>
```

到这一点，不必详细说明正在发生的事情——我在第二十六章中会解释——但你应该已经看到使用画布并不难，尽管它确实需要学习一些新的 JavaScript 函数。请注意，此示例利用了*OSC.js*函数集来自第二十一章，以帮助保持代码整洁和紧凑。

![使用 HTML5 canvas 绘制日本国旗](img/pmj6_2501.png)

###### 图 25-1。使用 HTML5 canvas 绘制日本国旗

# 地理位置

使用 *地理位置*，你的浏览器可以向服务器返回关于你位置的信息。此信息可以来自你使用的计算机或移动设备中的 GPS 芯片，也可以来自你的 IP 地址，或通过分析附近的 WiFi 热点获得。为了安全起见，用户始终控制这些信息的提供，可以拒绝一次性提供这些信息，或者启用设置以永久性地阻止或允许所有网站或某个网站访问这些数据。

此技术有许多用途，包括提供逐步导航；提供本地地图；通知附近的餐馆、WiFi 热点或其他地方；告知你附近的朋友；指引你去最近的加油站；等等。

示例 25-2 将显示用户位置的谷歌地图，只要浏览器支持地理位置和用户授予位置数据访问权限（如 图 25-2 所示）。否则，它将显示一个错误。

##### 示例 25-2\. 显示用户位置的地图

```php
<!DOCTYPE html>
<html>
  <head>
    <title>Geolocation Example</title>
  </head>
  <body>
    <script>
      if (typeof navigator.geolocation == 'undefined')
         alert("Geolocation not supported.")
      else
        navigator.geolocation.getCurrentPosition(granted, denied)

      function granted(position)
      {
        var lat = position.coords.latitude
        var lon = position.coords.longitude

        alert("Permission Granted. You are at location:\n\n"
          + lat + ", " + lon +
          "\n\nClick 'OK' to load Google Maps with your location")

        window.location.replace("https://www.google.com/maps/@"
          + lat + "," + lon + ",14z")
      }

      function denied(error)
      {
        var message

        switch(error.code)
        {
          case 1: message = 'Permission Denied'; break;
          case 2: message = 'Position Unavailable'; break;
          case 3: message = 'Operation Timed Out'; break;
          case 4: message = 'Unknown Error'; break;
        }

        alert("Geolocation Error: " + message)
      }
    </script>
  </body>
</html>

```

再次强调，这里不是描述所有工作原理的地方；我会在 第二十八章 中详细说明。目前，这个示例展示了管理地理位置信息的简易性。

![](img/pmj6_2502.png)

###### 图 25-2\. 使用用户位置显示地图

# 音频和视频

HTML5 的另一个重要新增是对浏览器内音频和视频的支持。尽管由于编码类型和许可证的多样性，播放这些媒体类型可能有些复杂，但 `<audio>` 和 `<video>` 元素提供了你展示可用媒体类型所需的灵活性。

在 示例 25-3 中，同一个视频文件已经被编码成不同格式，以确保所有主流浏览器都能播放。浏览器将简单地选择它们认识的第一个类型并播放，如 图 25-3 所示。

##### 示例 25-3\. 使用 HTML5 播放视频

```php
<!DOCTYPE html>
<html>
  <head>
    <title>HTML5 Video</title>
  </head>
  <body>
    <video width='560' height='320' controls>
      <source src='movie.mp4'  type='video/mp4'>
      <source src='movie.webm' type='video/webm'>
      <source src='movie.ogv'  type='video/ogg'>
    </video>
  </body>
</html>
```

![](img/pmj6_2503.png)

###### 图 25-3\. 使用 HTML5 显示视频

将音频插入到网页中同样简单，你将在 第二十七章 中了解到。

# 表单

正如你在 第十二章 中已经看到的，HTML5 表单正在被增强，但跨所有浏览器的支持仍然不完整。今天可以安全使用的部分已经在 第十二章 中详细说明了。

# 本地存储

使用本地存储，你可以在本地设备上保存的数据量和复杂性大大增加，远远超过 cookies 提供的微小空间。这打开了离线使用 Web 应用处理文档并在联网时与 Web 服务器同步的可能性。它还提升了本地存储小型数据库的前景，比如使用 WebSQL 保存音乐集合的细节或作为饮食或减肥计划的一部分保存所有个人统计数据。在第二十八章中，我向你展示如何在你的 Web 项目中充分利用这一新功能。

# Web Workers

多年来，使用 JavaScript 在后台运行中断驱动应用程序是可能的，但只能通过笨拙且效率低下的过程。更明智的做法是让底层浏览器技术代表你运行后台任务，它可以比你连续中断浏览器检查进展做得更快。

相反，通过*Web Workers*，你设置好一切并将代码传递给网页浏览器，然后由浏览器运行。当发生重要事件时，你的代码只需通知浏览器，然后浏览器回报给主代码。与此同时，你的网页可以什么也不做或者进行多个其他任务，直到后台任务需要它时才知晓。

在第二十八章中，我展示了如何使用 Web Workers 创建一个简单的时钟并计算质数。

# 问题

1.  哪个 HTML5 元素允许在网页中绘制图形？

1.  访问许多高级 HTML5 功能需要哪种编程语言？

1.  在网页中添加音频和视频，你会使用哪些 HTML5 标签？

1.  HTML5 中哪个特性比 cookies 提供更强大的功能？

1.  哪项 HTML5 技术支持运行后台 JavaScript 任务？

查看“第二十五章答案”，在附录 A 中有这些问题的答案。
