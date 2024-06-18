# 第十一章\. 流

*PHP*中的*流*代表了可以按线性、连续方式写入或读取的数据资源的常见接口。在内部，每个流都由称为*桶*的对象集合表示。每个桶代表来自底层流的一块数据，这类似于数字重现了传统的水桶链，就像图 11-1 中所示的那样。

![水桶链将数据从一个传递到另一个](img/phpc_1101.png)

###### 图 11-1\. 水桶链将数据从一个传递到另一个

水桶链经常用于将水从河流、溪流、湖泊或井中运输到火源。当无法使用软管移动水时，人们排成队，依次传递水桶以灭火。一个人在水源处装满水桶，然后将水桶传递给队列中的下一个人。排队的人不动，但水桶逐个传递，直到最后一个人能将水泼在火上。这个过程将继续，直到火被扑灭或水源耗尽。

尽管你没有使用 PHP 来灭火，但流的内部结构与水桶链有些相似，因为数据被逐块（桶）地通过处理它的代码组件传递。

生成器也类似于这种模式。¹ 与一次性将整个数据集加载到内存中不同，生成器提供了一种将其缩减为较小块并逐个数据块操作的方式。这使得 PHP 应用程序能够处理连续数据，而不是离散数据点的集合或数组。

## 包装器和协议

在 PHP 中，流是使用*包装器*实现的，这些包装器在系统中注册以操作特定协议。你可能会与最常见的包装器互动，例如文件访问或 HTTP URL，分别注册为`file://`和`http://`。每个包装器针对不同类型的数据操作，但它们都支持相同的基本功能。表 11-1 列出了 PHP 本地公开的包装器和协议。

表 11-1\. PHP 本地流包装器和协议

| 协议 | 描述 |
| --- | --- |
| `file://` | 访问本地文件系统 |
| `http://` | 通过 HTTP(S)访问远程 URL |
| `ftp://` | 通过 FTP(S)访问远程文件系统 |
| `php://` | 访问各种本地 I/O 流（内存、`stdin`、`stdout`等） |
| `zlib://` | 压缩 |
| `data://` | 原始数据（根据[RFC 2397](https://oreil.ly/EBJv6)） |
| `glob://` | 查找匹配模式的路径名 |
| `phar://` | 操作 PHP 归档 |
| `ssh2://` | 通过安全外壳连接 |
| `rar://` | RAR 压缩 |
| `ogg://` | 音频流 |

每个包装器产生一个 `stream` 资源，使你能够以线性方式读取或写入数据，并额外具有“寻找”到流内任意位置的能力。例如，`file://` 流允许对磁盘上的字节进行任意访问。类似地，`php://` 协议提供了对本地系统内存中各种字节流的读取/写入访问权限。

## 过滤器

PHP 的流过滤器提供了一种构造，允许在读取或写入期间动态操作流中的字节。一个简单的例子是自动将字符串中的每个字符转换为大写或小写。这可以通过创建一个自定义类，该类扩展了 `php_user_filter` 类，并将该类注册为编译器要使用的过滤器来实现，如 示例 11-1 所示。

##### 示例 11-1\. 用户定义的过滤器

```php
class StringFilter extends php_user_filter
{
    private string $mode;

    public function filter($in, $out, &$consumed, bool $closing): int
    {
        while ($bucket = stream_bucket_make_writeable($in)) { ![1](img/1.png)
            switch($this->mode) {
                case 'lower':
                    $bucket->data = strtolower($bucket->data);
                    break;
                case 'upper':
                    $bucket->data = strtoupper($bucket->data);
                    break;
            }

            $consumed += $bucket->datalen; ![2](img/2.png)
            stream_bucket_append($out, $bucket); ![3](img/3.png)
        }

        return PSFS_PASS_ON; ![4](img/4.png)
    }

    public function onCreate(): bool
    {
        switch($this->filtername) { ![5](img/5.png)
            case 'str.tolower':
                $this->mode = 'lower';
                return true;
            case 'str.toupper':
                $this->mode = 'upper';
                return true;
            default:
                return false;
        }
    }
}

stream_filter_register('str.*', 'StringFilter'); ![6](img/6.png)

$fp = fopen('document.txt', 'w');
stream_filter_append($fp, 'str.toupper'); ![7](img/7.png)

fwrite($fp, 'Hello' . PHP_EOL); ![8](img/8.png)
fwrite($fp, 'World' . PHP_EOL);

fclose($fp);

echo file_get_contents('document.txt'); ![9](img/9.png)
```

![1](img/#co_streams_CO1-1)

传递给过滤器的 `$in` 资源必须首先被设置为可写状态，然后才能进行任何操作。

![2](img/#co_streams_CO1-2)

在使用数据时，请务必更新 `$consumed` 输出变量，以便 PHP 能够跟踪你操作了多少字节。

![3](img/#co_streams_CO1-3)

`$out` 资源最初为空，你需要向其中写入数据块，以便其他过滤器（或仅 PHP 本身）继续在流上操作。

![4](img/#co_streams_CO1-4)

`PSFS_PASS_ON` 标志告诉 PHP 过滤器成功，并且数据在 `$out` 定义的资源中可用。

![5](img/#co_streams_CO1-5)

这个特定过滤器可以作用于任何 `str.` 标志，但故意只读取两个过滤器名称，用于将文本转换为大写或小写。通过打开定义的过滤器名称开关，你可以拦截和过滤你想要的操作，同时允许其他过滤器定义它们自己的 `str.` 函数。

![6](img/#co_streams_CO1-6)

定义过滤器是不够的；你必须显式注册过滤器，这样 PHP 才知道在过滤流时实例化哪个类。

![7](img/#co_streams_CO1-7)

一旦定义并注册了过滤器，你必须将自定义过滤器附加（或前置）到当前流资源的过滤器列表中。

![8](img/#co_streams_CO1-8)

带有附加过滤器后，写入流的任何数据都将通过过滤器。

![9](img/#co_streams_CO1-9)

再次打开文件表明你的输入数据确实被转换为大写。请注意，`file_get_contents()` 会将整个文件读入内存，而不是作为流进行操作。

在内部，任何自定义过滤器的 `filter()` 方法必须返回三个标志中的一个：

`PSFS_PASS_ON`

表明处理成功完成，并且输出桶列（`$out`）包含准备传递给下一个过滤器的数据

`PSFS_FEED_ME`

演示过滤器成功完成，但输出旅行队中没有可用数据。您必须从基础流或堆栈中的前一个过滤器提供更多数据才能获得任何输出。

`PSFS_ERR_FATAL`

指示过滤器遇到错误

`onCreate()` 方法公开了底层 `php_user_filter` 类的三个内部变量，就像它们是子类自身的属性一样：

`::filtername`

如在 `stream_filter_append()` 或 `stream_​fil⁠ter_​pre⁠pend()` 中指定的过滤器名称

`::params`

在将其附加或预置到过滤器堆栈时，传递给过滤器的额外参数

`::stream`

正在被过滤的实际流资源

流过滤器是操作数据在系统中流入或流出的强大方式。以下示例涵盖了 PHP 中流的各种用法，包括流封装器和过滤器。

# 11.1 流数据到/从临时文件

## 问题

要在程序中的其他地方使用临时文件来存储数据。

## 解决方案

要存储数据，请像操作文件一样使用 `php://temp` 流：

```php
$fp = fopen('php://temp', 'rw');

while (true) {
    // Get data from some source

    fputs($fp, $data);

    if ($endOfData) {
        break;
    }
}
```

要再次检索数据，请将流倒回到开始位置，然后按以下方式再次读取数据：

```php
rewind($fp);

while (true) {
    $data = fgets($fp);

    if ($data === false) {
        break;
    }

    echo $data;
}

fclose($fp);
```

## 讨论

通常情况下，PHP 支持两种不同的临时数据流。解决方案示例利用了 `php://temp` 流，但同样可以使用 `php://memory` 来达到相同效果。对于完全适合内存的数据流，这两个包装器是可互换的。默认情况下，两者都将使用系统内存来存储流数据。然而，一旦流的数据超过应用程序可用内存的量，`php://temp` 将把数据路由到磁盘上的临时文件中。

在这两种情况下，写入流的数据被认为是短暂的。一旦关闭流，这些数据将不再可用。同样地，你不能创建指向相同数据的*新*流资源。示例 11-2 说明了 PHP 在使用相同流封装器时，如何为流使用*不同*的临时文件。

##### 示例 11-2\. 临时流是唯一的

```php
$fp = fopen('php://temp', 'rw');

fputs($fp, 'Hello world!'); ![1](img/1.png)

rewind($fp); ![2](img/2.png)
echo fgets($fp) . PHP_EOL; ![3](img/3.png)

$fp2 = fopen('php://temp', 'rw'); ![4](img/4.png)
fputs($fp2, 'Goodnight moon.'); ![5](img/5.png)

rewind($fp); ![6](img/6.png)
rewind($fp2);

echo fgets($fp2) . PHP_EOL; ![7](img/7.png)
echo fgets($fp) . PHP_EOL; ![8](img/8.png)
```

![1](img/#co_streams_CO2-1)

向临时流写入一行数据。

![2](img/#co_streams_CO2-2)

将流句柄倒回，以便可以从中重新读取数据。

![3](img/#co_streams_CO2-3)

从流中读取数据会在控制台上打印`Hello world!`。

![4](img/#co_streams_CO2-4)

创建新的流句柄会创建一个完全新的流，尽管协议包装器相同。

![5](img/#co_streams_CO2-5)

向这个新流写入一些唯一的数据。

![6](img/#co_streams_CO2-6)

为了保险起见，将两个流都倒回。

![7](img/#co_streams_CO2-7)

首先打印第二个流来证明它是唯一的。这会在控制台上打印`Goodnight moon`。

![8](img/#co_streams_CO2-8)

将`Hello world!`重新打印到控制台以证明原始流仍按预期工作。

在任一情况下，临时流在您运行应用程序并且不明确希望将其持久存储到磁盘时非常有用。

## 参见

[`fopen()`函数](https://oreil.ly/LR8pa)和[PHP I/O 流包装器](https://oreil.ly/6De6p)的文档。

# 11.2 从 PHP 输入流中读取

## 问题

您希望从 PHP 中读取原始输入。

## 解决方案

利用`php://stdin`流来读取[标准输入流（`stdin`）](https://oreil.ly/-_Bxl)，如下所示：

```php
$stdin = fopen('php://stdin', 'r');
```

## 讨论

与任何其他应用程序一样，PHP 可以直接访问由命令和其他上游应用程序传递给它的输入。在控制台环境中，这可能是另一个命令，终端中的文字输入，或者是来自其他应用程序的数据管道。在 Web 上下文中，您可以使用`php://input`+来访问通过 Web 服务器传递的文字内容，并通过 PHP 应用程序前面的任何 Web 服务器访问。

###### 注意

在命令行应用程序中，您还可以直接使用预定义的[`STDIN`常量](https://oreil.ly/wgDpd)。 PHP 本地为您打开一个流，这意味着您根本不需要创建新的资源变量。

简单的命令行应用程序可以从输入中获取数据，处理该数据，然后将其存储到文件中。在食谱 9.5 中，您学习了如何使用 Libsodium 和对称密钥来加密和解密文件。假设您有一个加密密钥（以十六进制编码），该密钥作为环境变量公开，示例 11-3 中的程序将使用该密钥加密传入的任何数据并将其存储在输出文件中。

##### 示例 11-3\. 使用 Libsodium 加密`stdin`

```php
if (empty($key = getenv('ENCRYPTION_KEY'))) { ![1](img/1.png)
    throw new Exception('No encryption key provided!');
}

$key = hex2bin($key);
if (strlen($key) !== SODIUM_CRYPTO_STREAM_XCHACHA20_KEYBYTES) { ![2](img/2.png)
    throw new Exception('Invalid encryption key provided!');
}

$in = fopen('php://stdin', 'r'); ![3](img/3.png)
$filename = sprintf('encrypted-%s.bin', uniqid()); ![4](img/4.png)
$out = fopen($filename, 'w'); ![5](img/5.png)

[$state, $header] = sodium_crypto_secretstream_xchacha20poly1305_init_push($key); ![6](img/6.png)

fwrite($out, $header);

while (!feof($in)) {
    $text = fread($in, 8175);

    if (strlen($text) > 0) {
        $cipher = sodium_crypto_secretstream_xchacha20poly1305_push($state, $text);

        fwrite($out, $cipher);
    }
}

sodium_memzero($state);

fclose($in);
fclose($out);

echo sprintf('Wrote %s' . PHP_EOL, $filename);
```

![1](img/#co_streams_CO3-1)

由于您希望使用环境变量存储加密密钥，请首先检查该变量是否存在。

![2](img/#co_streams_CO3-2)

在使用加密前，还应对密钥的大小进行合理性检查。

![3](img/#co_streams_CO3-3)

在此示例中，直接从`stdin`读取字节。

![4](img/#co_streams_CO3-4)

使用动态命名的文件存储加密数据。请注意，在实践中，`uniqid()`使用时间戳，并且在高度使用的系统上可能会出现竞态条件和名称冲突。在真实世界的环境中，您将希望使用更可靠的随机源生成文件名。

![5](img/#co_streams_CO3-5)

输出可以返回到控制台，但由于此加密生成原始字节，将输出流到文件更安全。在这种情况下，文件名将根据系统时钟动态生成。

![6](img/#co_streams_CO3-6)

其余的加密步骤与食谱 9.5 相同。

前面的示例让您可以通过使用标准输入缓冲区将数据从文件直接传递给 PHP。这样的管道操作可能看起来像`cat plaintext-file.txt | php encrypt.php`。

鉴于加密操作将生成一个文件，您可以通过类似的脚本反向操作，并类似地利用`cat`将原始二进制数据管道传回 PHP，如示例 11-4 所示。

##### 示例 11-4\. 使用 Libsodium 解密`stdin`

```php
if (empty($key = getenv('ENCRYPTION_KEY'))) {
    throw new Exception('No encryption key provided!');
}

$key = hex2bin($key);
if (strlen($key) !== SODIUM_CRYPTO_STREAM_XCHACHA20_KEYBYTES) {
    throw new Exception('Invalid encryption key provided!');
}

$in = fopen('php://stdin', 'r');
$filename = sprintf('decrypted-%s.txt', uniqid());
$out = fopen($filename, 'w');

$header = fread($in, SODIUM_CRYPTO_SECRETSTREAM_XCHACHA20POLY1305_HEADERBYTES);
$state = sodium_crypto_secretstream_xchacha20poly1305_init_pull($header, $key);

try {
    while (!feof($in)) {
        $cipher = fread($in, 8192);

        [$plain, ] = sodium_crypto_secretstream_xchacha20poly1305_pull(
            $state,
            $cipher
        );

        if ($plain === false) {
            throw new Exception('Error decrypting file!');
        }

        fwrite($out, $plain);
    }
} finally {
    sodium_memzero($state);

    fclose($in);
    fclose($out);

    echo sprintf('Wrote %s' . PHP_EOL, $filename);
}
```

由于 PHP 的 I/O 流包装器，任意输入流与本地磁盘上的原生文件一样易于操作。

## 参见

PHP I/O 流包装器的文档请参考[PHP I/O 流包装器](https://oreil.ly/wKjj9)。

# 11.3 写入 PHP 输出流

## 问题

您希望直接输出数据。

## 解决方案

使用`php://output`将数据直接推送到[标准输出（`stdout`）流](https://oreil.ly/coZ8n)如下：

```php
$stdout = fopen('php://stdout', 'w');
fputs($stdout, 'Hello, world!');
```

## 讨论

PHP 向用户空间代码公开了三个标准 I/O 流——`stdin`、`stdout`和`stderr`。默认情况下，您在应用程序中打印的任何内容都会发送到标准输出流（`stdout`），这使得以下两行代码在功能上是等效的：

```php
fputs($stdout, 'Hello, world!');
echo 'Hello, world!';
```

许多开发人员学会使用`echo`和`print`语句作为调试应用程序的简单方法；在您的代码中添加指示器可以方便地确定编译器失败的确切位置或输出一个隐藏变量的值。然而，这并不是管理输出的*唯一*方法。`stdout`流是许多应用程序共有的，直接向其写入数据（而不是隐式的`print`语句）可以使您的应用程序集中在需要完成的任务上。

类似地，一旦您开始直接利用`php://stdout`向客户端打印输出，您就可以开始利用`php://stderr`流来发出关于*错误*的消息。这两个流在操作系统中处理方式不同，您可以用它们来区分有用消息和错误状态之间的消息。

###### 注意

在命令行应用程序中，您还可以直接使用预定义的[`STDOUT`和`STDERR`常量](https://oreil.ly/HArEs)。PHP 本地为您打开这些流，这意味着您根本不需要创建新的资源变量。

示例 11-4 允许您从`php://stdin`读取加密数据，解密后将解密内容存储到文件中。一个更有用的示例将显示解密的数据直接发送到`php://stdout`（以及任何错误到`php://stderr`），如示例 11-5 所示。

##### 示例 11-5\. 将`stdin`解密为`stdout`

```php
if (empty($key = getenv('ENCRYPTION_KEY'))) {
    throw new Exception('No encryption key provided!');
}

$key = hex2bin($key);
if (strlen($key) !== SODIUM_CRYPTO_STREAM_XCHACHA20_KEYBYTES) {
    throw new Exception('Invalid encryption key provided!');
}

$in = fopen('php://stdin', 'r');
$out = fopen('php://stdout', 'w'); ![1](img/1.png)
$err = fopen('php://stderr', 'w'); ![2](img/2.png)

$header = fread($in, SODIUM_CRYPTO_SECRETSTREAM_XCHACHA20POLY1305_HEADERBYTES);
$state = sodium_crypto_secretstream_xchacha20poly1305_init_pull($header, $key);

while (!feof($in)) {
    $cipher = fread($in, 8192);

    [$plain, ] = sodium_crypto_secretstream_xchacha20poly1305_pull(
        $state,
        $cipher
    );

    if ($plain === false) {
        fwrite($err, 'Error decrypting file!'); ![3](img/3.png)
        exit(1);
    }

    fwrite($out, $plain);
}

sodium_memzero($state);

fclose($in);
fclose($out);
fclose($err);
```

![1](img/#co_streams_CO4-1)

您可以直接写入标准输出流，而不是创建一个中间文件。

![2](img/#co_streams_CO4-2)

在这期间，您也应该掌握标准错误流。

![3](img/#co_streams_CO4-3)

而不是触发异常，您可以直接写入错误流。

## 参见

关于 [PHP I/O 流包装器](https://oreil.ly/PmXdc) 的文档。

# 11.4 从一个流读取并写入另一个流

## 问题

想要连接两个流，将一个流的字节传递给另一个流。

## 解决方案

使用`stream_copy_to_stream()`从一个流复制数据到另一个流，操作如下：

```php
$source = fopen('document1.txt', 'r');
$dest = fopen('destination.txt', 'w');

stream_copy_to_stream($source, $destination);
```

## 讨论

PHP 中的流机制提供了处理相当大数据块的高效方式。通常，您可能会在 PHP 应用程序中使用太大而无法放入可用内存的文件。大多数文件可能会通过 Apache 或 NGINX 直接向用户公开并发送。另外，例如，您可能希望使用 PHP 编写的脚本保护大文件下载（如 zip 文件或视频），以验证用户的身份。

在 PHP 中可以实现这种情况，因为系统不需要将整个流保存在内存中，而是可以从一个流读取字节时立即将其写入另一个流。 示例 11-6 假设您的 PHP 应用程序直接验证用户并验证他们对特定文件的权限后，流式传输其内容。

##### 示例 11-6\. 通过链接流将大文件复制到`stdout`

```php
if ($user->isAuthenticated()) {
    $in = fopen('largeZipFile.zip', 'r'); ![1](img/1.png)
    $out = fopen('php://stdout', 'w');

    stream_copy_to_stream($in, $out); ![2](img/2.png)
    exit; ![3](img/3.png)
}
```

![1](img/#co_streams_CO5-1)

打开流的操作仅仅是获取底层数据的句柄。系统尚未读取任何字节。

![2](img/#co_streams_CO5-2)

将一个流复制到另一个流将直接复制字节，而无需在内存中保留任一流的全部内容。请记住，流类似于一个桶链，因此在任何给定时间内只会在内存中保存必要字节的子集。

![3](img/#co_streams_CO5-3)

在复制流后始终使用`exit`；否则，您可能会错误地附加杂项字节。

类似地，当需要时，可以编程方式*构建*一个大流并将其复制到另一个流中。例如，某些 Web 应用程序可能需要在需要时编程方式构建大数据块（例如非常大的单页 Web 应用程序）。可以将这些大数据元素写入 PHP 的临时内存流，然后在需要时复制字节。 示例 11-7 具体说明了这样做的方式。

##### 示例 11-7\. 将临时流复制到`stdout`

```php
$buffer = fopen('php://temp', 'w+'); ![1](img/1.png)
fwrite($buffer, '<html><head>');

// ... Several hundred fwrite()s later ... 
fwrite($buffer, '</body></html>');
rewind($buffer); ![2](img/2.png)

$output = fopen('php://stdout', 'w');
stream_copy_to_stream($buffer, $output); ![3](img/3.png)
exit; ![4](img/4.png)
```

![1](img/#co_streams_CO6-1)

临时流利用磁盘上的临时文件。您受限于操作系统分配给临时文件的可用空间，而不是 PHP 可用的内存。

![2](img/#co_streams_CO6-2)

将整个 HTML 文档写入临时文件后，请将流倒回到开头，以便将所有这些字节复制到`stdout`。

![3](img/#co_streams_CO6-3)

即使这两个流都不指向特定的磁盘文件，复制一个流到另一个的机制仍然保持不变。

![4](img/#co_streams_CO6-4)

总是在所有字节都复制到客户端后才退出，以避免意外错误。

## 参见

[`stream_copy_to_stream()`](https://oreil.ly/Us_Yj) 的文档。

# 11.5 组合不同的流处理程序在一起

## 问题

您希望在一个代码片段中结合多个流概念，例如包装器和过滤器。

## 解决方案

根据需要追加过滤器并使用适当的包装器协议。示例 11-8 使用`file://`协议来访问本地文件系统，并使用两个额外的过滤器来处理 Base64 编码和文件解压缩。

##### 示例 11-8\. 将多个过滤器应用到流

```php
$fp = fopen('compressed.txt', 'r'); ![1](img/1.png)
stream_filter_append($fp, 'convert.base64-decode'); ![2](img/2.png)
stream_filter_append($fp, 'zlib.inflate'); ![3](img/3.png)

echo fread($fp, 1024) . PHP_EOL; ![4](img/4.png)
```

![1](img/#co_streams_CO7-1)

假设此文件存在于磁盘上并包含字面内容`80jNycnXUS​jPL8pJUQQA`。

![2](img/#co_streams_CO7-2)

添加到堆栈的第一个流过滤器将从 Base64 编码的 ASCII 文本转换为原始字节。

![3](img/#co_streams_CO7-3)

第二个过滤器将利用 Zlib 压缩来解压（或解压缩）原始字节。

![4](img/#co_streams_CO7-4)

如果您从步骤 1 中的字面内容开始，这很可能会在控制台打印`Hello, world!`。

## 讨论

当涉及流时，思考层次是很有帮助的。基础始终是用于实例化流的协议处理程序。在解决方案示例中没有显式协议，这意味着 PHP 将默认使用`file://`协议。在这个基础之上是任意数量的流过滤器层。

解决方案示例利用 Zlib 压缩和 Base64 编码来压缩文本并对原始（压缩的）字节进行编码。要创建这样的压缩/编码文件，您需要执行以下操作：

```php
$fp = fopen('compressed.txt', 'w');

stream_filter_append($fp, 'zlib.deflate');
stream_filter_append($fp, 'convert.base64-encode');

fwrite($fp, 'Goodnight, moon!');
```

前面的示例利用与解决方案示例相同的协议包装器和过滤器。但请注意它们添加的顺序是相反的。这是因为流过滤器的工作方式类似于豆腐花上的层次，就像图 11-2 中的插图一样。协议包装器位于核心位置，数据从该核心流向外部世界，通过每个后续层次以特定顺序传递。

![数据进出 PHP 流过滤器](img/phpc_1102.png)

###### 图 11-2\. 数据进出 PHP 流过滤器

PHP 中已经内置了几种可以应用于流的过滤器。但是，您也可以定义*自己的*过滤器。将原始字节编码为 Base64 很有用，但有时将字节编码/解码为十六进制也很有用。PHP 中没有这样的过滤器，但您可以通过类似于本章介绍中示例 11-1 中所做的方式扩展`php_​user_​filter`类来定义它。考虑示例 11-9 中的类。

##### 示例 11-9\. 使用过滤器进行十六进制编码/解码

```php
class HexFilter extends php_user_filter
{
    private string $mode;

    public function filter($in, $out, &$consumed, bool $closing): int
    {
        while ($bucket = stream_bucket_make_writeable($in)) {
            switch ($this->mode) {
                case 'encode':
                    $bucket->data = bin2hex($bucket->data);
                    break;
                case 'decode':
                    $bucket->data = hex2bin($bucket->data);
                    break;
                default:
                    throw new Exception('Invalid encoding mode!');
            }

            $consumed += $bucket->datalen;
            stream_bucket_append($out, $bucket);
        }

        return PSFS_PASS_ON;
    }

    public function onCreate(): bool
    {
        switch($this->filtername) {
            case 'hex.decode':
                $this->mode = 'decode';
                return true;
            case 'hex.encode':
                $this->mode = 'encode';
                return true;
            default:
                return false;
        }
    }
}
```

在任意流应用为过滤器后，可以使用示例 11-9 中定义的类来任意编码和解码十六进制。只需像注册其他过滤器一样注册它，然后将其应用于需要转换的流。

在解决方案示例中使用的 Base64 编码可以完全替换为十六进制，如示例 11-10 所示。

##### 示例 11-10\. 结合十六进制流过滤器和 Zlib 压缩

```php
stream_filter_register('hex.*', 'HexFilter'); ![1](img/1.png)

// Writing data $fp = fopen('compressed.txt', 'w');

stream_filter_append($fp, 'zlib.deflate');
stream_filter_append($fp, 'hex.encode');

fwrite($fp, 'Hello, world!' . PHP_EOL);
fwrite($fp, 'Goodnight, moon!');

fclose($fp); ![2](img/2.png)

$fp2 = fopen('compressed.txt', 'r');
stream_filter_append($fp2, 'hex.decode');
stream_filter_append($fp2, 'zlib.inflate');

echo fread($fp2, 1024); ![3](img/3.png)
```

![1](img/#co_streams_CO8-1)

一旦过滤器存在，必须将其注册，以便 PHP 知道如何使用它。在注册过程中利用`*`通配符允许同时注册编码和解码。

![2](img/#co_streams_CO8-2)

此时*compressed.txt*的内容将是`f348cdc9c9d75128cf2fca4951e472cfcf 4fc9cb4ccf28d151c8cdcfcf530400`。

![3](img/#co_streams_CO8-3)

解码和解压后，`Hello world! Goodnight, moon!` 将被打印到控制台（两个语句之间有换行）。

## 参见

支持的[协议和包装器](https://oreil.ly/HxKpb)以及[可用过滤器的列表](https://oreil.ly/IE5UR)。另请参考用户定义的流过滤器的示例 11-1。

# 11.6 编写自定义流包装器

## 问题

您想定义自己的自定义流协议。

## 解决方案

创建一个遵循`streamWrapper`原型的自定义类，并将其注册到 PHP 中。例如，`VariableStream`类可以提供一种类似流的接口，用于读取或写入特定全局变量，如下所示：²

```php
class VariableStream
{
    private int $position;
    private string $name;
    public $context;

    function stream_open($path, $mode, $options, &$opened_path)
    {
        $url = parse_url($path);
        $this->name = $url['host'];
        $this->position = 0;

        return true;
    }

    function stream_write($data)
    {
        $left = substr($GLOBALS[$this->name], 0, $this->position);
        $right = substr($GLOBALS[$this->name], $this->position + strlen($data));
        $GLOBALS[$this->name] = $left . $data . $right;
        $this->position += strlen($data);
        return strlen($data);
    }
}
```

在 PHP 中，可以如下注册和使用上述类：

```php
if (!in_array('var', stream_get_wrappers())) {
    stream_wrapper_register('var', 'VariableStream');
}

$varContainer = '';

$fp = fopen('var://varContainer', 'w');

fwrite($fp, 'Hello' . PHP_EOL);
fwrite($fp, 'World' . PHP_EOL);
fclose($fp);

echo $varContainer;
```

## 讨论

PHP 中的`streamWrapper`构造函数是一个类的原型。不幸的是，它既不是可以扩展的类，也不是可以具体实现的接口。相反，它是任何用户定义的流协议必须遵循的文档格式。

虽然可以将类注册为不同接口的协议处理程序，但强烈建议任何潜在的协议类实现`streamWrapper`接口中定义的所有方法（从 PHP 文档中复制为示例 11-11 伪接口定义），以满足 PHP 对流行为的预期要求。

##### 示例 11-11。`streamWrapper`接口定义

```php
 class streamWrapper {
    public $context;

    public __construct()

    public dir_closedir(): bool

    public dir_opendir(string $path, int $options): bool

    public dir_readdir(): string

    public dir_rewinddir(): bool

    public mkdir(string $path, int $mode, int $options): bool

    public rename(string $path_from, string $path_to): bool

    public rmdir(string $path, int $options): bool

    public stream_cast(int $cast_as): resource

    public stream_close(): void

    public stream_eof(): bool

    public stream_flush(): bool

    public stream_lock(int $operation): bool

    public stream_metadata(string $path, int $option, mixed $value): bool

    public stream_open(
        string $path,
        string $mode,
        int $options,
        ?string &$opened_path
    ): bool

    public stream_read(int $count): string|false

    public stream_seek(int $offset, int $whence = SEEK_SET): bool

    public stream_set_option(int $option, int $arg1, int $arg2): bool

    public stream_stat(): array|false

    public stream_tell(): int

    public stream_truncate(int $new_size): bool

    public stream_write(string $data): int

    public unlink(string $path): bool

    public url_stat(string $path, int $flags): array|false

    public __destruct()
}
```

某些特定功能，例如`mkdir`、`rename`、`rmdir`或`unlink`，如果协议没有特定的使用场景，应完全**不**实现。否则，系统将无法为您（或构建在您库之上的开发人员）提供有用的错误消息，并且表现会出乎意料。

虽然大多数您日常使用的协议都与 PHP 原生集成，但可以编写新的协议处理程序或利用其他开发人员构建的处理程序。

在引用使用专有协议的云存储时很常见（例如 Amazon Web Services 的`s3://`），而不是其他地方常见的更通用的`https://`或`file://`前缀。AWS 实际上发布了一个[公共 SDK](https://oreil.ly/RVXlw)，该 SDK 在内部使用`stream_​wrap⁠per_​register()`为其他应用程序代码提供`s3://`协议，使您能够像处理本地文件一样轻松地处理托管在云中的数据。

## 另请参阅

[`streamWrapper`](https://oreil.ly/SyhD8)文档。

¹ 欲了解更多有关生成器的信息，请参阅 Recipe 7.15。

² PHP 手册提供了一个名字类似的类，其功能比本解决方案示例中演示的更广泛更完整，[点击此处查看](https://oreil.ly/b0PLM)。
