# 前言

几乎每一个构建现代 Web 应用程序的开发者对 PHP 都有自己的看法。有些人喜欢这门语言，有些人则厌恶它。大多数人对这门语言及其应用程序的影响都很熟悉。这是因为 PHP 支持超过 75%的已知编写网站的语言。鉴于互联网的庞大规模，这是相当多的 PHP 代码在广泛使用中。¹

诚然，并非所有的 PHP 代码都是优秀的代码。任何写过 PHP 代码的人都见过语言所呈现的好与坏，以及不堪入目的一面。这是一门非常易于操作的语言，这也是其在市场上占主导地位背后强大力量的原因，也是许多工程师在写出质疑代码时犯下的错误。

与完全编译语言不同，这些语言强制执行严格的类型和内存管理，PHP 是一种解释语言，对编程错误宽容度极高。在许多情况下，即使出现严重的编程错误，PHP 也会发出警告，但仍然可以继续执行程序。对于学习新语言的开发者来说，这非常有利，因为无辜的错误不会必然导致应用程序崩溃。但这种宽容的本质在某种程度上是一把双刃剑。因为即使是“糟糕代码”也会运行，许多开发者发布了这些代码，然后被毫无戒心的初学者轻易地复用了。

这本书的目标是帮助你理解如何避免前人犯过的错误，从而防止糟糕代码的重复使用。它还旨在建立任何开发者都可以遵循以解决 PHP 常见问题的模式和示例。本书中的实例将帮助你快速识别和解决复杂问题，无需重新发明轮子，也不会被诱惑复制粘贴通过额外研究发现的“糟糕代码”。

# 本书适合对象

本书适合任何曾经构建或维护过使用 PHP 构建的 Web 应用程序或网站的工程师。它旨在温和地介绍 PHP 开发中的特定概念。它并非是语言中所有功能的全面概述。理想情况下，您已经尝试过 PHP，构建了一个简单的应用程序，或者至少按照互联网上无数的“Hello, world!”示例之一进行了尝试。

如果你对 PHP 不熟悉，但对其他编程语言很了解，这本书将帮助你顺利将技能转移到新的技术栈上。*PHP Cookbook*详细说明了如何在 PHP 中完成特定任务。请将每个示例代码块与您最熟悉的语言解决相同问题的方式进行比较；这将有助于深入了解该语言与 PHP 之间的差异。

# 导读本书

我不指望任何人能一口气读完这本书。相反，这些内容旨在在您构建或设计新应用程序时作为经常参考的资源。无论您选择一次阅读一个章节来掌握一个概念，还是查看一个或多个特定的代码示例来解决特定问题，都完全取决于您。

每个示例都是独立的，并包含您可以在日常工作中利用的完整实现的代码解决方案，以解决类似的问题。每章最后都有一个具体的示例程序，演示了整章讨论的概念，并在您已经阅读的示例基础上进行扩展。

本书首先介绍了任何语言的基本构建模块：变量、运算符和函数。第一章介绍了变量和基本数据处理。第二章通过详细讲解 PHP 本地支持的各种运算符和操作来扩展这一基础。第三章通过建立更高级的函数和创建基本程序将这两个概念结合起来。

接下来的五章介绍了 PHP 的类型系统。第四章涵盖了您想了解的关于 PHP 中字符串处理的一切内容，以及一些您不知道自己不知道的东西。第五章解释了整数和浮点（小数）算术，并介绍了复杂功能所需的进一步构建模块。第六章介绍了 PHP 处理日期、时间和日期时间的方式。第七章介绍了开发人员可能希望将数据分组到列表中的每一种方式。最后，第八章解释了开发人员如何通过引入自己的类和更高级对象来扩展 PHP 的原始类型。

在这些基本构建模块之后，第九章讨论了 PHP 的加密和安全功能，帮助构建真正安全、现代化的应用程序。第十章介绍了 PHP 的文件处理和操作功能。由于文件基本上是基于流构建的，这些知识将通过第十一章更加丰富，该章介绍了 PHP 中更高级的流接口。

接下来的三章涵盖了 Web 开发中的关键概念。第十二章介绍了 PHP 的错误处理和异常接口。第十三章直接将错误与交互式调试和单元测试联系起来。最后，第十四章说明了如何为 PHP 应用程序进行适当的调优，以提高速度、可伸缩性和稳定性。

PHP 本身是开源的，核心语言的大部分起源于社区的*扩展*系统。接下来的第十五章涵盖了 PHP 的本地扩展（用 C 语言编写并编译以与语言本身并行运行）以及第三方 PHP 包，这些包可以扩展您自己应用的功能。接着，第十六章介绍了数据库及其管理所使用的扩展。

我将第十七章致力于介绍 PHP 8.1 引入的新线程模型以及一般的异步编码。最后，第十八章通过介绍命令行的强大功能及针对命令作为接口编写的应用程序来总结 PHP 的调查。

# 本书使用的约定

本书使用以下约定：

## 编程约定

本书中的所有编程示例都是为至少 PHP 8.0.11 编写并测试的（除非另有说明，一些较新的功能需要 8.2 或更新版本）。示例代码在容器化的 Linux 环境中进行了测试，但在裸机 Linux，Microsoft Windows 或 Apple macOS 上同样能很好运行。

## 排版约定

本书使用以下排版约定：

*斜体*

表示新术语，URL，电子邮件地址，文件名和文件扩展名。

`固定宽度`

用于程序列表以及段落内部引用程序元素，例如变量或函数名，数据库，数据类型，环境变量，语句和关键字。

**`固定宽度加粗`**

显示用户应直接输入的命令或其他文本。

*`固定宽度斜体`*

显示应由用户提供值或根据上下文确定值的文本。

###### 提示

这个元素表示一个提示或建议。

###### 注释

这个元素表示一般注释。

###### 警告

这个元素指示警告或注意事项。

# 奥莱利在线学习

###### 注释

40 多年来，[*O’Reilly Media*](https://oreilly.com)提供技术和商业培训，知识和见解，帮助企业成功。

我们独特的专家和创新者网络通过书籍，文章和我们的在线学习平台分享他们的知识和专业知识。奥莱利的在线学习平台为您提供按需访问实时培训课程，深入学习路径，交互式编码环境以及奥莱利和其他 200 多家出版商的大量文本和视频。有关更多信息，请访问[*https://oreilly.com*](https://oreilly.com)。

# 如何联系我们

请将有关本书的评论和问题发送给出版商：

+   O’Reilly Media, Inc.

+   1005 Gravenstein Highway North

+   CA 95472 Sebastopol

+   800-889-8969（美国或加拿大）

+   707-829-7019（国际或本地）

+   707-829-0104（传真）

+   *support@oreilly.com*

+   [*https://www.oreilly.com/about/contact.html*](https://www.oreilly.com/about/contact.html)

我们为这本书设有一个网页，列出勘误、示例和任何额外信息。您可以访问[*https://oreil.ly/phpCookbook*](https://oreil.ly/phpCookbook)。

欲了解我们的书籍和课程的新闻和信息，请访问[*https://oreilly.com*](https://oreilly.com)。

在 LinkedIn 找到我们：[*https://linkedin.com/company/oreilly-media*](https://linkedin.com/company/oreilly-media)

关注我们的 Twitter 账号：[*https://twitter.com/oreillymedia*](https://twitter.com/oreillymedia)

观看我们的 YouTube 频道：[*https://www.youtube.com/oreillymedia*](https://www.youtube.com/oreillymedia)

# 致谢

首先要感谢我美妙的妻子，鼓励我踏上撰写又一本书的旅程。老实说，如果没有你的持续爱、支持和鼓励，无论在职业上还是个人生活中，我都不可能达到现在的位置。

还要感谢我的了不起的孩子们，他们忍受了撰写这本手稿所需的长时间工作。我欠你们整个世界！

还要特别感谢 Chris Ling、Michal Špaček、Matthew Turland 和 Kendra Ash，在撰写本书过程中，他们出色的技术评审帮助我保持诚实，并且完善了一些你将阅读到的最关键的配方和主题的覆盖范围。

¹ 截至 2023 年 3 月，[W3Techs 使用统计](https://oreil.ly/sb24e)显示 PHP 被 77.5%的所有网站使用。