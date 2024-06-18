# 第十章 图形

显而易见，网络比文字更具视觉效果。图像以标志、按钮、照片、图表、广告和图标的形式出现。这些图像中的许多是静态的，从未改变，是使用诸如 Photoshop 之类的工具构建的。但是许多图像是动态创建的——从包含您姓名的亚马逊推荐计划广告到股票表现图。

PHP 支持使用内置的 GD 扩展库进行图形创建。在本章中，我们将向您展示如何在 PHP 中动态生成图像。

# 将图像嵌入页面

一个常见的误解是在单个 HTTP 请求中流动的文本和图形的混合。毕竟，当您查看页面时，您会看到一个包含这种混合的页面。重要的是要理解，通过 Web 浏览器的一系列 HTTP 请求创建包含文本和图形的标准网页；每个请求都由 Web 服务器的响应回答。每个响应只能包含一种类型的数据，每个图像需要一个单独的 HTTP 请求和 Web 服务器响应。因此，如果您看到一个包含一些文本和两个图像的页面，您知道它已经进行了三个 HTTP 请求和相应的响应来构建此页面。

以这个 HTML 页面为例：

```php
<html>
 <head>
 <title>Example Page</title>
 </head>

 <body>
 This page contains two images.
 <img src="image1.png" alt="Image 1" />
 <img src="image2.png" alt="Image 2" />
 </body>
</html>
```

Web 浏览器发送到此页面的请求序列如下所示：

```php
GET /page.html HTTP/1.0
GET /image1.png HTTP/1.0
GET /image2.png HTTP/1.0
```

Web 服务器对这些请求的每个响应都会返回一个响应。这些响应中的`Content-Type`头部如下所示：

```php
Content-Type: text/html
Content-Type: image/png
Content-Type: image/png
```

要在 HTML 页面中嵌入由 PHP 生成的图像，请假设生成图像的 PHP 脚本实际上是图像本身。因此，如果我们有*image1.php*和*image2.php*脚本来创建图像，我们可以修改先前的 HTML 代码如下（现在的图像名称都是 PHP 扩展名）：

```php
<html>
 <head>
 <title>Example Page</title>
 </head>

 <body>
 This page contains two images.
 <img src="image1.php" alt="Image 1" />
 <img src="image2.php" alt="Image 2" />
 </body>
</html>
```

现在，您不再引用 Web 服务器上的真实图像，而是将`<img>`标签指向生成并返回图像数据的 PHP 脚本。

此外，您可以向这些脚本传递变量，因此，您可以像这样编写您的`<img>`标签，而不是使用单独的脚本来生成每个图像：

```php
<img src="image.php?num=1" alt="Image 1" />
<img src="image.php?num=2" alt="Image 2" />
```

然后，在调用的 PHP 文件*image.php*中，您可以访问请求参数`$_GET['num']`以生成适当的图像。

# 基本图形概念

*图像*是各种颜色的像素矩形。颜色通过它们在*调色板*中的位置进行标识，调色板是一个颜色数组。调色板中的每个条目都有三个单独的颜色值——分别为红色、绿色和蓝色。每个值的范围从`0`（颜色不存在）到`255`（完全强度的颜色）。这被称为其*RGB 值*。还有十六进制或“hex”值——用于 HTML 中的颜色的字母数字表示。一些图像工具，如[ColorPic](https://oreil.ly/F-Z3e)，将为您转换 RGB 值为十六进制。

图像文件很少是像素和调色板的直接转储。相反，创建了各种*文件格式*（GIF、JPEG、PNG 等），试图对数据进行压缩以使文件更小。

不同的文件格式以不同方式处理图像的*透明度*，它控制背景是否以及如何显示在图像之后。例如，PNG 支持*alpha 通道*，每个像素都有一个额外值反映该点的透明度。其他格式，如 GIF，简单地指定调色板中的一个条目表示透明度。还有一些格式，如 JPEG，根本不支持透明度。

粗糙和锯齿状的边缘，即*锯齿效应*，会导致图像不够吸引人。*反锯齿*涉及移动或重新着色形状边缘的像素，以便更渐变地过渡到其背景。一些绘制图像的函数实现了反锯齿功能。

每个像素有 256 种可能的红、绿、蓝值，因此每个像素有 16,777,216 种可能的颜色。某些文件格式限制调色板中的颜色数量（例如，GIF 最多支持 256 种颜色）；其他文件格式则允许使用所需数量的颜色。后者被称为*真彩色*格式，因为 24 位色（每种颜色分别有 8 位）提供了比人眼能分辨的更多色调。

# 创建和绘制图像

现在，让我们从最简单的可能的 GD 示例开始。示例 10-1 是一个生成黑色填充方块的脚本。这段代码适用于任何支持 PNG 图像格式的 GD 版本。

##### 示例 10-1\. 白底黑色方块（black.php）

```php
<?php
$image = imagecreate(200, 200);

$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
$black = imagecolorallocate($image, 0x00, 0x00, 0x00);
imagefilledrectangle($image, 50, 50, 150, 150, $black);

header("Content-Type: image/png");
imagepng($image);
```

示例 10-1 展示了生成任何图像的基本步骤：创建图像、分配颜色、绘制图像，然后保存或发送图像。图 10-1 显示了 示例 10-1 的输出。

![白底黑色方块](img/php4_1001.png)

###### 图 10-1\. 白底黑色方块

要查看结果，只需将浏览器指向 *black.php* 页面。要在网页中嵌入此图像，请使用：

```php
<img src="black.php" />
```

## 图形程序的结构

大多数动态图像生成程序都遵循 示例 10-1 中概述的相同基本步骤。

您可以使用 `imagecreate()` 函数创建一个 256 色图像，该函数返回一个图像句柄：

```php
$image = imagecreate(*`width`*, *`height`*);
```

图像中使用的所有颜色都必须使用 `imagecolorallocate()` 函数分配。第一个分配的颜色成为图像的背景颜色：¹

```php
$color = imagecolorallocate(*`image`*, *`red`*, *`green`*, *`blue`*);
```

参数是颜色的数字 RGB（红色、绿色、蓝色）组件。在 示例 10-1 中，我们用十六进制写入颜色值，以便函数调用更接近 HTML 颜色表示 `#FFFFFF` 和 `#000000`。

GD 中有许多绘图原语。示例 10-1 使用了`imagefilledrectangle()`，其中通过传递左上角和右下角的坐标来指定矩形的尺寸：

```php
imagefilledrectangle(*`image`*, *`tlx`*, *`tly`*, *`brx`*, *`bry`*, *`color`*);
```

下一步是向浏览器发送`Content-Type`头部，使用适当的内容类型来创建所需类型的图像。完成此操作后，我们调用相应的输出函数。`imagejpeg()`、`imagegif()`、`imagepng()` 和 `imagewbmp()` 函数分别用于创建 GIF、JPEG、PNG 和 WBMP 文件：

```php
imagegif(*`image`* [, *`filename`* ]);
imagejpeg(*`image`* [, *`filename`* [, *`quality`* ]]);
imagepng(*`image`* [, *`filename`* ]);
imagewbmp(*`image`* [, *`filename`* ]);
```

如果没有给出*filename*，则图像将输出到浏览器；否则，它将创建（或覆盖）给定路径的图像。对于 JPEG，*quality*参数的取值范围为`0`（最差）到`100`（最佳）。质量越低，JPEG 文件越小。默认设置为`75`。

在示例 10-1 中，在调用输出生成函数`imagepng()`之前立即设置 HTTP 头部。如果在脚本的最开始设置了`Content-Type`，则生成的任何错误都会被视为图像数据，并导致浏览器显示损坏的图像图标。表 10-1 列出了各种图像格式及其`Content-Type`值。

表 10-1\. 图像格式的 Content-Type 值

| 格式 | `Content-Type` |
| --- | --- |
| GIF | `image/gif` |
| JPEG | `image/jpeg` |
| PNG | `image/png` |
| WBMP | `image/vnd.wap.wbmp` |

## 更改输出格式

如你所推测的那样，生成不同类型的图像流只需对脚本进行两处更改：发送不同的`Content-Type`并使用不同的图像生成函数。示例 10-2 展示了修改后生成 JPEG 而非 PNG 图像的示例 10-1。

##### 示例 10-2\. 黑色正方形的 JPEG 版本

```php
<?php
$image = imagecreate(200, 200);
$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
$black = imagecolorallocate($image, 0x00, 0x00, 0x00);

imagefilledrectangle($image, 50, 50, 150, 150, $black);

header("Content-Type: image/jpeg");
imagejpeg($image);
```

## 测试支持的图像格式

如果编写的代码必须在可能支持不同图像格式的系统上移植，请使用`imagetypes()`函数检查支持的图像类型。此函数返回一个位字段；您可以使用位与运算符（`&`）来检查给定位是否设置。常量`IMG_GIF`、`IMG_JPG`、`IMG_PNG` 和 `IMG_WBMP` 对应于这些图像格式的位。

如果支持 PNG，则示例 10-3 生成 PNG 文件；如果不支持 PNG，则生成 JPEG 文件；如果既不支持 PNG 也不支持 JPEG，则生成 GIF 文件。

##### 示例 10-3\. 检查图像格式支持情况

```php
<?php
$image = imagecreate(200, 200);
$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
$black = imagecolorallocate($image, 0x00, 0x00, 0x00);

imagefilledrectangle($image, 50, 50, 150, 150, $black);

if (imagetypes() & IMG_PNG) {
 header("Content-Type: image/png");
 imagepng($image);
}
else if (imagetypes() & IMG_JPG) {
 header("Content-Type: image/jpeg");
 imagejpeg($image);
}
else if (imagetypes() & IMG_GIF) {
 header("Content-Type: image/gif");
 imagegif($image);
}
```

## 读取现有文件

如果想要从现有图像开始并进行修改，可以使用`imagecreatefromgif()`、`imagecreatefromjpeg()`或`imagecreatefrompng()`：

```php
$image = imagecreatefromgif(*`filename`*);
$image = imagecreatefromjpeg(*`filename`*);
$image = imagecreatefrompng(*`filename`*);
```

## 基本绘图函数

GD 提供了绘制基本点、线条、弧线、矩形和多边形的函数。本节描述了 GD 2.x 支持的基本函数。

最基本的函数是`imagesetpixel()`，它设置指定像素的颜色：

```php
imagesetpixel(*`image`*, *`x`*, *`y`*, *`color`*);
```

有两个用于绘制线条的函数，`imageline()`和`imagedashedline()`：

```php
imageline(*`image`*, *`start_x`*, *`start_` `y`*, *`end_x`*, *`end_` `y`*, *`color`*);
imagedashedline(*`image`*, *`start_x`*, *`start_` `y`*, *`end_x`*, *`end_` `y`*, *`color`*);
```

有两个用于绘制矩形的函数，一个仅绘制轮廓，另一个使用指定的颜色填充矩形：

```php
imagerectangle(*`image`*, *`tlx`*, *`tly`*, *`brx`*, *`bry`*, *`color`*);
imagefilledrectangle(*`image`*, *`tlx`*, *`tly`*, *`brx`*, *`bry`*, *`color`*);
```

指定矩形的位置和大小，通过传递左上角和右下角的坐标。

您可以使用`imagepolygon()`和`imagefilled`​`polygon()`函数绘制任意多边形：

```php
imagepolygon(*`image`*, *`points`*, *`number`*, *`color`*);
imagefilledpolygon(*`image`*, *`points`*, *`number`*, *`color`*);
```

这两个函数接受一个点的数组。该数组对于多边形的每个顶点都有两个整数（*x*和*y*坐标）。*number*参数是数组中顶点的数量（通常为`count($points)/2`）。

`imagearc()`函数绘制弧（椭圆的一部分）：

```php
imagearc(*`image`*, *`center_x`*, *`center_y`*, *`width`*, *`height`*, *`start`*, *`end`*, *`color`*);
```

椭圆由其中心、宽度和高度定义（对于圆来说，高度和宽度相同）。弧的起始和结束点以度数给出，逆时针从 3 点开始计数。使用*start*为`0`和*end*为`360`绘制完整的椭圆。

有两种方法可以填充已绘制的形状。`imagefill()`函数执行洪水填充，从给定位置开始更改像素的颜色。像素颜色的任何变化标记了填充的限制。`imagefilltoborder()`函数允许您传递填充限制的特定颜色：

```php
imagefill(*`image`*, *`x`*, *`y`*, *`color`*);
imagefilltoborder(*`image`*, *`x`*, *`y`*, *`border_color`*, *`color`*);
```

另一件可能需要对图像做的事情是旋转它们。例如，如果您试图创建 Web 风格的宣传册，这可能会有所帮助。`image``rotate()`函数允许您以任意角度旋转图像：

```php
imagerotate(*`image`*, *`angle`*, *`background_color`*);
```

示例 10-4 中的代码显示了之前的黑盒子图像，旋转了 45 度。*background_color*选项用于指定图像旋转后未覆盖区域的颜色，已设置为`1`以显示黑白颜色的对比。图 10-2 显示了此代码的结果。

##### 示例 10-4\. 图像旋转示例

```php
<?php
$image = imagecreate(200, 200);
$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
$black = imagecolorallocate($image, 0x00, 0x00, 0x00);
imagefilledrectangle($image, 50, 50, 150, 150, $black);

$rotated = imagerotate($image, 45, 1);

header("Content-Type: image/png");
imagepng($rotated);
```

![黑盒子图像旋转 45 度](img/php4_1002.png)

###### 图 10-2\. 黑盒子图像旋转 45 度

# 带有文本的图像

经常需要向图像添加文本。GD 提供了内置字体以供此用途。示例 10-5 向我们的黑方形图像添加了一些文本。

##### 示例 10-5\. 向图像添加文本

```php
<?php
$image = imagecreate(200, 200);
$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
$black = imagecolorallocate($image, 0x00, 0x00, 0x00);

imagefilledrectangle($image, 50, 50, 150, 150, $black);
imagestring($image, 5, 50, 160, "A Black Box", $black);

header("Content-Type: image/png");
imagepng($image);
```

图 10-3 显示了示例 10-5 的输出。

![添加文本后的黑盒子图像](img/php4_1003.png)

###### 图 10-3\. 添加文本后的黑盒子图像

`imagestring()`函数向图像添加文本。指定文本的左上角点、颜色和要使用的 GD 字体（通过标识符）：

```php
imagestring(*`image`*, *`font_id`*, *`x`*, *`y`*, *`text`*, *`color`*);
```

## 字体

GD 通过 ID 识别字体。内置五种字体，并可以通过`imageloadfont()`函数加载额外的字体。这五种内置字体显示在图 10-4 中。

![本地 GD 字体](img/php4_1004.png)

###### 图 10-4\. 本地 GD 字体

这里是用于显示这些字体的代码：

```php
<?php
$image = imagecreate(200, 200);
$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
$black = imagecolorallocate($image, 0x00, 0x00, 0x00);

imagestring($image, 1, 10, 10, "Font 1: ABCDEfghij", $black);
imagestring($image, 2, 10, 30, "Font 2: ABCDEfghij", $black);
imagestring($image, 3, 10, 50, "Font 3: ABCDEfghij", $black);
imagestring($image, 4, 10, 70, "Font 4: ABCDEfghij", $black);
imagestring($image, 5, 10, 90, "Font 5: ABCDEfghij", $black);

header("Content-Type: image/png");
imagepng($image);
```

您可以创建自己的位图字体，并使用`imageloadfont()`函数将它们加载到 GD 中。但是，这些字体是二进制的，且依赖于架构，使它们无法从一台机器移植到另一台机器。在 GD 中使用 TrueType 函数与 TrueType 字体提供了更大的灵活性。

## TrueType 字体

TrueType 是一种轮廓字体标准；它提供了对字符渲染更精确的控制。要向图像中添加 TrueType 字体的文本，请使用`imagettftext()`：

```php
imagettftext(*`image`*, *`size`*, *`angle`*, *`x`*, *`y`*, *`color`*, *`font`*, *`text`*);
```

*大小*以像素为单位。*角度*以从 3 点钟开始的度数表示（`0`表示水平文本，`90`表示向上的垂直文本等）。*x* 和 *y* 坐标指定文本基线的左下角。文本可能包含 UTF-8²形式的`&#234;`序列，以打印高位 ASCII 字符。

字体参数是用于渲染字符串的 TrueType 字体的位置。如果字体不以斜杠开头，则添加*.ttf*扩展名，并在*/usr/share/fonts/truetype*中查找字体。

默认情况下，TrueType 字体的文本是反锯齿的。这使大多数字体更易阅读，尽管略微模糊。但是，反锯齿可能使非常小的文本更难读——小字符具有更少的像素，因此反锯齿的调整更为显著。

您可以通过使用负颜色索引（例如，`−4`表示使用颜色索引 4 但不反锯齿文本）关闭反锯齿。

示例 10-6 使用 TrueType 字体向图像添加文本，搜索字体位置与脚本相同，但仍需提供字体文件位置的完整路径（包含在本书的代码示例中）。

##### 示例 10-6\. 使用 TrueType 字体

```php
<?php
$image = imagecreate(350, 70);
$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
$black = imagecolorallocate($image, 0x00, 0x00, 0x00);

$fontname = "c:/wamp64/www/bookcode/chapter_10/IndieFlower.ttf";

*`imagettftext`*($image, 20, 0, 10, 40, $black, $fontname, "The Quick Brown Fox");

header("Content-Type: image/png");
imagepng($image);
```

图 10-5 显示了示例 10-6 的输出。

![Indie Flower TrueType 字体](img/php4_1005.png)

###### 图 10-5\. Indie Flower TrueType 字体

示例 10-7 使用`imagettftext()`向图像添加垂直文本。

##### 示例 10-7\. 显示垂直 TrueType 文本

```php
<?php
$image = *`imagecreate`*(70, 350);
$white = *`imagecolorallocate`*($image, 255, 255, 255);
$black = *`imagecolorallocate`*($image, 0, 0, 0);

$fontname = "c:/wamp64/www/bookcode/chapter_10/IndieFlower.ttf";

*`imagettftext`*($image, 20, 270, 28, 10, $black, $fontname, "The Quick Brown Fox");

*`header`*("Content-Type: image/png");
*`imagepng`*($image);
```

图 10-6 显示了示例 10-7 的输出。

![垂直 TrueType 文本](img/php4_1006.png)

###### 图 10-6\. 垂直 TrueType 文本

# 动态生成的按钮

动态生成按钮图像是生成图像的一种流行用途之一（本主题在第一章中介绍）。通常情况下，这涉及将文本合成到预先存在的背景图像上，如示例 10-8 所示。

##### 示例 10-8\. 创建动态按钮

```php
<?php
$font = "c:/wamp64/www/bookcode/chapter_10/IndieFlower.ttf" ;
$size = *`isset`*(`$_GET`['size']) ? `$_GET`['size'] : 12;
$text = *`isset`*(`$_GET`['text']) ? `$_GET`['text'] : 'some text';

$image = *`imagecreatefrompng`*("button.png");
$black = *`imagecolorallocate`*($image, 0, 0, 0);

`if` ($text) {
 // calculate position of text
 $tsize = *`imagettfbbox`*($size, 0, $font, $text);
 $dx = *`abs`*($tsize[2] - $tsize[0]);
 $dy = *`abs`*($tsize[5] - $tsize[3]);
 $x = (*`imagesx`*($image) - $dx ) / 2;
 $y = (*`imagesy`*($image) - $dy ) / 2 + $dy;

 // draw text
 *`imagettftext`*($image, $size, 0, $x, $y, $black, $font, $text);
}

*`header`*("Content-Type: image/png");
*`imagepng`*($image);
```

在这种情况下，空白按钮（*button.png*）被默认文本覆盖，如图 10-7 所示。

![默认文本的动态按钮](img/php4_1007.png)

###### 图 10-7\. 默认文本的动态按钮

可以像这样从页面调用示例 10-8 中的脚本：

```php
<img src="button.php?text=PHP+Button" />
```

此 HTML 生成了图 10-8 中显示的按钮。

![带有生成文本标签的按钮](img/php4_1008.png)

###### 图 10-8\. 带有生成文本标签的按钮

URL 中的+字符是空格的编码形式。空格在 URL 中是非法的，必须进行编码。使用 PHP 的`urlencode()`函数对按钮字符串进行编码。例如：

```php
<img src="button.php?text=<?= urlencode("PHP Button"); ?>" />
```

## 缓存动态生成的按钮

生成图像比发送静态图像稍慢一些。对于每次以相同文本参数调用时始终看起来相同的按钮，您可以实现一个简单的缓存机制。

示例 10-9 仅在找不到该按钮的缓存文件时生成该按钮。`$path`变量保存一个目录，由 Web 服务器用户可写，其中按钮可以被缓存；确保它可以从运行此代码的位置访问。`filesize()`函数返回文件的大小，`readfile()`函数将文件内容发送到浏览器。因为此脚本使用文本形式参数作为文件名，所以非常不安全。(第十四章，讨论安全问题，解释了如何修复它。)

##### 示例 10-9\. 缓存动态按钮

```php
<?php

$font = "c:/wamp64/www/bookcode/chapter_10/IndieFlower.ttf";
$size = isset($_GET['size']) ? $_GET['size'] : 12;
$text = isset($_GET['text']) ? $_GET['text'] : 'some text';

$path = "/tmp/buttons"; // button cache directory

// send cached version

if ($bytes = @filesize("{$path}/button.png")) {
 header("Content-Type: image/png");
 header("Content-Length: {$bytes}");
 readfile("{$path}/button.png");

 exit;
}

// otherwise, we have to build it, cache it, and return it
$image = imagecreatefrompng("button.png");
$black = imagecolorallocate($image, 0, 0, 0);

if ($text) {
 // calculate position of text
 $tsize = imagettfbbox($size, 0, $font, $text);
 $dx = abs($tsize[2] - $tsize[0]);
 $dy = abs($tsize[5] - $tsize[3]);
 $x = (imagesx($image) - $dx ) / 2;
 $y = (imagesy($image) - $dy ) / 2 + $dy;

 // draw text
 imagettftext($image, $size, 0, $x, $y, $black, $font, $text);

 // save image to file
 imagepng($image, "{$path}/{$text}.png");
}

header("Content-Type: image/png");
imagepng($image);
```

## 更快的缓存

示例 10-9 仍不如可能的快速。使用 Apache 指令，您可以完全绕过 PHP 脚本，并在创建后直接加载缓存的图像。

首先，在您的 Web 服务器的`DocumentRoot`下的某个地方创建一个*buttons*目录，并确保您的 Web 服务器用户有权限写入此目录。例如，如果`DocumentRoot`目录是*/var/www/html*，则创建*/var/www/html/buttons*。

其次，编辑您的 Apache *httpd.conf*文件，并添加以下代码块：

```php
<Location /buttons/>
 ErrorDocument 404 /button.php
</Location>
```

这告诉 Apache，对于*buttons*目录中不存在的文件请求，应将其发送到您的*button.php*脚本。

第三步，将示例 10-10 保存为*button.php*。该脚本创建新的按钮，将它们保存到缓存并发送到浏览器。与示例 10-9 不同，这里没有`$_GET`中的表单参数，因为 Apache 将错误页面处理为重定向。相反，我们必须从`$_SERVER`中分析数值以确定正在生成的按钮。顺便说一句，我们删除文件名中的`'..'`以修复来自示例 10-9 的安全漏洞。

安装*button.php*后，当像*http://your.site/buttons/php.png*这样的请求到来时，Web 服务器会检查是否存在*buttons/php.png*文件。如果不存在，请求将重定向到*button.php*脚本，该脚本创建带有“php”文本的图像并保存到*buttons/php.png*中。任何对此文件的后续请求都将直接提供，而无需运行 PHP 代码。

##### 示例 10-10\. 更高效的动态按钮缓存

```php
<?php
// bring in redirected URL parameters, if any
parse_str($_SERVER['REDIRECT_QUERY_STRING']);

$cacheDir = "/buttons/";
$url = $_SERVER['REDIRECT_URL'];

// pick out the extension
$extension = substr($url, strrpos($url, '.'));

// remove directory and extension from $url string
$file = substr($url, strlen($cacheDir), -strlen($extension));

// security - don't allow '..' in filename
$file = str_replace('..', '', $file);

// text to display in button
$text = urldecode($file);

$font = "c:/wamp64/www/bookcode/chapter_10/IndieFlower.ttf" ;

// build it, cache it, and return it
$image = imagecreatefrompng("button.png");
$black = imagecolorallocate($image, 0, 0, 0);

if ($text) {
 // calculate position of text
 $tsize = imagettfbbox($size, 0, $font, $text);
 $dx = abs($tsize[2] - $tsize[0]);
 $dy = abs($tsize[5] - $tsize[3]);
 $x = (imagesx($image) - $dx ) / 2;
 $y = (imagesy($image) - $dy ) / 2 + $dy;

 // draw text
 imagettftext($image, $size, 0, $x, $y, $black, $font, $text);

 // save image to file
 imagepng($image, "{$_SERVER['DOCUMENT_ROOT']}{$cacheDir}{$file}.png");
}

header("Content-Type: image/png");
imagepng($image);
```

示例 10-10 机制的一个显著缺点是按钮文本不能包含文件名中非法字符。尽管如此，这仍然是缓存动态生成图像的最有效方式。如果更改按钮外观并需要重新生成缓存图像，只需删除*buttons*目录中的所有图像，它们将在请求时重新创建。

还可以进一步操作，让您的*button.php*脚本支持多种图像类型。只需检查`$extension`并在脚本末尾调用适当的`imagepng()`、`imagejpeg()`或`imagegif()`函数。还可以解析文件名并添加修改器，如颜色、大小和字体，或直接在 URL 中传递它们。由于示例中的`parse_str()`调用，例如*http://your.site/buttons/php.png?size=16*的 URL 将以 16 号字体大小显示“php”。

# 缩放图像

图像大小可以通过两种方式改变。`imagecopyresized()`函数速度快但粗糙，在新图像中可能会产生锯齿边缘。`imagecopyresampled()`函数速度较慢，但使用像素插值生成平滑边缘，并提供调整大小后图像的清晰度。这两个函数接受相同的参数：

```php
imagecopyresized(*`dest`*, *`src`*, *`dx`*, *`dy`*, *`sx`*, *`sy`*, *`dw`*, *`dh`*, *`sw`*, *`sh`*);
imagecopyresampled(*`dest`*, *`src`*, *`dx`*, *`dy`*, *`sx`*, *`sy`*, *`dw`*, *`dh`*, *`sw`*, *`sh`*);
```

*dest*和*src*参数是图像句柄。点`(`*dx*`,` *dy*`)`是目标图像中将复制区域的点。点`(`*sx*`,` *sy*`)`是源图像的左上角。*sw*、*sh*、*dw*和*dh*参数给出了源和目标中复制区域的宽度和高度。

示例 10-11 对 *php.jpg* 图像进行缩放，平滑地缩小为原尺寸的四分之一，得到 图 10-10 中的图像。

##### 示例 10-11\. 使用 `imagecopyresampled()` 进行调整大小

```php
<?php
$source = imagecreatefromjpeg("php_logo_big.jpg");

$width = imagesx($source);
$height = imagesy($source);
$x = $width / 2;
$y = $height / 2;

$destination = imagecreatetruecolor($x, $y);
imagecopyresampled($destination, $source, 0, 0, 0, 0, $x, $y, $width, $height);

header("Content-Type: image/png");
imagepng($destination);
```

![原始 php.jpg 图像](img/php4_1009.png)

###### 图 10-9\. 原始 php.jpg 图像

![结果为 1/4 大小的图像](img/php4_1010.png)

###### 图 10-10\. 结果为 1/4 大小的图像

将高度和宽度除以 4 而不是 2，会产生如 图 10-11 所示的输出。

![结果为 1/16 大小的图像](img/php4_1011.png)

###### 图 10-11\. 结果为 1/16 大小的图像

# 颜色处理

GD 库支持既有 8 位调色板（256 色）图像，也支持带有 alpha 通道透明度的真彩色图像。

要创建一个 8 位调色板图像，使用 `imagecreate()` 函数。随后，使用 `imagecolorallocate()` 分配的第一个颜色填充图像的背景：

```php
$width = 128;
$height = 256;

$image = imagecreate($width, $height);
$white = imagecolorallocate($image, 0xFF, 0xFF, 0xFF);
```

要创建带有 7 位 alpha 通道的真彩色图像，请使用 `imagecreatetruecolor()` 函数：

```php
$image = imagecreatetruecolor(*`width`*, *`height`*);
```

使用 `imagecolorallocatealpha()` 创建包含透明度的颜色索引：

```php
$color = imagecolorallocatealpha(*`image`*, *`red`*, *`green`*, *`blue`*, *`alpha`*);
```

*Alpha* 值介于 0（不透明）和 127（透明）之间。

尽管大多数人习惯于 8 位（0–255）的 alpha 通道，GD 实际上使用的是很方便的 7 位（0–127）alpha 通道。每个像素由一个 32 位有符号整数表示，四个 8 位字节排列如下：

```php
High Byte Low Byte
{Alpha Channel} {Red} {Green} {Blue}
```

对于有符号整数，最左边的位，或者最高位，用于指示值是否为负数，因此只留下了 31 位的实际信息。PHP 的默认整数值是有符号长整型，可以存储单个 GD 调色板条目。该整数是正数还是负数告诉我们该调色板条目是否启用了抗锯齿。

与调色板图像不同，真彩色图像中的第一个颜色分配并不自动成为背景色。相反，图像最初填充了完全透明的像素。调用 `imagefilledrectangle()` 可以用任何背景颜色填充图像。

示例 10-12 创建了一个真彩色图像，并在白色背景上绘制了半透明的橙色椭圆。

##### 示例 10-12\. 白色背景上的简单橙色椭圆

```php
<?php
$image = imagecreatetruecolor(150, 150);
$white = imagecolorallocate($image, 255, 255, 255);

imagealphablending($image, false);
imagefilledrectangle($image, 0, 0, 150, 150, $white);

$red = imagecolorallocatealpha($image, 255, 50, 0, 50);
imagefilledellipse($image, 75, 75, 80, 63, $red);

header("Content-Type: image/png");
imagepng($image);
```

图 10-12 展示了 示例 10-12 的输出。

![白色背景上的橙色椭圆](img/php4_1012.png)

###### 图 10-12\. 白色背景上的橙色椭圆

您可以使用 `imagetruecolortopalette()` 函数将真彩色图像转换为带有颜色索引的图像（也称为*调色板*图像）。

## 使用 Alpha 通道

在示例 10-12 中，我们在绘制背景和椭圆之前关闭了*alpha 混合*。Alpha 混合是一个开关，用于确定绘制图像时是否应用 alpha 通道（如果存在）。如果 alpha 混合关闭，新像素将替换旧像素。如果新像素具有 alpha 通道，则将其维持，但将覆盖的原始像素的所有像素信息都将丢失。

示例 10-13 通过在橙色椭圆上绘制一个 50% alpha 通道的灰色矩形来说明 alpha 混合。

##### 示例 10-13\. 50% alpha 通道的灰色矩形叠加

```php
<?php
$image = imagecreatetruecolor(150, 150);
imagealphablending($image, false);

$white = imagecolorallocate($image, 255, 255, 255);
imagefilledrectangle($image, 0, 0, 150, 150, $white);

$red = imagecolorallocatealpha($image, 255, 50, 0, 63);
imagefilledellipse($image, 75, 75, 80, 50, $red);

imagealphablending($image, false);

$gray = imagecolorallocatealpha($image, 70, 70, 70, 63);
imagefilledrectangle($image, 60, 60, 120, 120, $gray);

header("Content-Type: image/png");
imagepng($image);
```

图 10-13 展示了示例 10-13 的输出（alpha 混合仍然关闭）。

![橙色椭圆上的灰色矩形](img/php4_1013.png)

###### 图 10-13\. 橙色椭圆上的灰色矩形

如果我们在调用`image`​`filledrectangle()`之前将示例 10-13 修改为启用 alpha 混合，我们将得到图像显示在图 10-14 中。

![启用了 alpha 混合的图像](img/php4_1014.png)

###### 图 10-14\. 启用了 alpha 混合的图像

## 颜色识别

要检查图像中特定像素的颜色索引，可以使用`imagecolorat()`：

```php
$color = imagecolorat(*`image`*, *`x`*, *`y`*);
```

对于使用 8 位色板的图像，该函数返回一个颜色索引，然后将其传递给`imagecolorsforindex()`以获取实际的 RGB 值：

```php
$values = imagecolorsforindex(*`image`*, *`index`*);
```

`imagecolorsforindex()`返回的数组具有键`'red'`、`'green'`和`'blue'`。如果在真彩图像的颜色上调用`imagecolorsforindex()`，返回的数组还具有键`'alpha'`的值。这些键的值对应于调用`image`​`colorallocate()`和`imagecolorallocatealpha()`时使用的 0 到 255 的颜色值和 0 到 127 的 alpha 值。

## 真彩索引

`imagecolorallocatealpha()`返回的颜色索引实际上是一个 32 位有符号长整数，其中前三个字节分别表示红、绿、蓝的值。接下来的一个位表示此颜色是否启用了抗锯齿，剩余的七位表示透明度值。

例如：

```php
$green = imagecolorallocatealpha($image, 0, 0, 255, 127);
```

此代码将`$green`设置为`2130771712`，在十六进制中为`0x7F00FF00`，在二进制中为`01111111000000001111111100000000`。

这等同于以下的`imagecolorresolvealpha()`调用：

```php
$green = (127 << 24) | (0 << 16) | (255 << 8) | 0;
```

在这个例子中，你也可以去掉两个`0`条目，简化成：

```php
$green = (127 << 24) | (255 << 8);
```

要解构这个值，可以使用如下方法：

```php
$a = ($col & 0x7F000000) >> 24;
$r = ($col & 0x00FF0000) >> 16;
$g = ($col & 0x0000FF00) >> 8;
$b = ($col & 0x000000FF);
```

很少需要像这样直接操作颜色值。一个应用场景是生成一个颜色测试图像，显示纯红、绿和蓝的纯色。例如：

```php
$image = imagecreatetruecolor(256, 60);

for ($x = 0; $x < 256; $x++) {
 imageline($image, $x, 0, $x, 19, $x);
 imageline($image, 255 - $x, 20, 255 - $x, 39, $x << 8);
 imageline($image, $x, 40, $x, 59, $x<<16);
}

header("Content-Type: image/png");
imagepng($image);
```

图 10-15 展示了颜色测试程序的输出。

![颜色测试](img/php4_1015.png)

###### 图 10-15\. 颜色测试

显然，它比我们在黑白打印中展示的要更加丰富多彩，因此请自行尝试此示例。在这个特定示例中，仅计算像素颜色要比为每种颜色调用`imagecolorallocatealpha()`更容易。

## 图像的文本表示

`imagecolorat()` 函数的一个有趣用途是循环遍历图像中的每个像素，并使用该颜色数据执行某些操作。示例 10-14 在*php-tiny.jpg*图像中的每个像素处打印`#`，表示该像素的颜色。

##### 示例 10-14\. 将图像转换为文本

```php
<html><body bgcolor="#000000">

<tt><?php
$image = imagecreatefromjpeg("php_logo_tiny.jpg");

$dx = imagesx($image);
$dy = imagesy($image);

for ($y = 0; $y < $dy; $y++) {
 for ($x = 0; $x < $dx; $x++) {
 $colorIndex = imagecolorat($image, $x, $y);
 $rgb = imagecolorsforindex($image, $colorIndex);

 printf('<font color=#%02x%02x%02x>#</font>',
 $rgb['red'], $rgb['green'], $rgb['blue']);
 }

 echo "<br>\n";
} ?></tt>

</body></html>
```

其结果是图像的 ASCII 表示，如图 10-16 所示。

![图像的 ASCII 表示](img/php4_1016.png)

###### 图 10-16\. 图像的 ASCII 表示

# 接下来是什么

有许多不同的方式可以利用 PHP 在运行时处理图像。这确实打破了 PHP 仅用于生成 Web HTML 内容的神话。如果你有时间和兴趣更深入地探索可能性，请随意尝试这里的代码示例。在下一章中，我们将探讨生成动态 PDF 文档中的另一个神话破除者。敬请关注！

¹ 这仅适用于具有调色板的图像。使用`ImageCreateTrueColor()` 创建的真彩色图像不遵循此规则。

² UTF-8 是一个 8 位 Unicode 编码方案（[*http://www.unicode.org*](http://www.unicode.org)）。
