# 第十五章 电子邮件与通知

通过电子邮件、Slack、SMS 或其他通知系统向应用的用户发送通知是一个常见但令人惊讶地复杂的需求。Laravel 的邮件和通知功能提供了一致的 API，抽象了不必太关注任何特定提供者的需要。就像在第十四章中一样，你只需编写一次代码，并在配置级别选择要用于发送电子邮件或通知的提供程序。

# 电子邮件

Laravel 的邮件功能是建立在[Symfony Mailer](https://oreil.ly/ceZ3K)之上的便捷层。默认情况下，Laravel 提供了 SMTP、Mailgun、Postmark、Amazon SES 和 Sendmail 驱动程序。

对于所有的云服务，你将在 *config**/services.php* 中设置你的身份验证信息。然而，如果你查看一下，你会看到已经有了一些键——在 *config/mail.php* 中——允许你使用像 `MAIL_MAILER` 和 `MAILGUN_SECRET` 这样的变量，在 *.env* 中自定义应用程序的邮件功能。

## 基本的“Mailable”邮件使用

在现代 Laravel 应用中，你发送的每一封邮件都是一个特定的 PHP 类的实例，用来表示每封邮件，称为*mailable*。

要创建一个 mailable，可以使用 `make:mail` Artisan 命令：

```php
php artisan make:mail AssignmentCreated
```

示例 15-1 展示了该类的样子。

##### 示例 15-1 自动生成的 mailable PHP 类

```php
<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;

class AssignmentCreated extends Mailable
{
    use Queueable, SerializesModels;

    /**
 * Create a new message instance.
 */
    public function __construct()
    {
        //
    }

    /**
 * Get the message envelope.
 */
    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Assignment Created',
        );
    }

    /**
 * Get the message content definition.
 */
    public function content(): Content
    {
        return new Content(
            view: 'view.name',
        );
    }

    /**
 * Get the attachments for the message.
 *
 * @return array<int, \Illuminate\Mail\Mailables\Attachment>
 */
    public function attachments(): array
    {
        return [];
    }
}
```

你可能会注意到 mailable 和 jobs 之间有一些相似之处；这个类甚至导入了 `Queueable` trait 用于排队你的邮件和 `SerializesModels` trait，因此你传递给构造函数的任何 Eloquent 模型都将被正确序列化。

那么，这是如何工作的呢？类的构造函数是你传递任何数据的地方，你在 mailable 类上设置为公共的任何属性都将在模板中可用。

在 `envelope()` 方法中，你将设置关于邮件的配置详情——发件人、主题、元数据。

在 `content()` 方法中，你将定义内容——包括你使用的视图以及任何 Markdown 内容和文本参数。

如果要附加文件到邮件中，你将使用 `attachments()` 方法。

查看 示例 15-2 以了解我们如何更新为我们的任务示例自动生成的 mailable。

##### 示例 15-2 一个示例 mailable

```php
<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Address;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;

class AssignmentCreated extends Mailable
{
    use Queueable, SerializesModels;

    public function __construct(public $trainer, public $trainee) {}

    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'New assignment from ' . $this->trainer->name,
            from: new Address($this->trainer->email, $this->trainer->name),
        );
    }

    public function content(): Content
    {
        return new Content(
            view: 'emails.assignment-created'
        );
    }

    public function attachments(): array
    {
        return [];
    }
}
```

一旦创建了 mailable 类，就可以开始发送邮件了。首先，创建 mailable 类的一个实例，并传入适当的数据；然后，通过链式调用 `Mail::to($user)->send($mailable)` 来发送邮件。你还可以在内联调用链的一部分中自定义邮件的一些其他细节，例如 CC 和 BCC。查看 示例 15-3 以查看一些示例。

##### 示例 15-3 如何发送 mailables

```php
$mail = new AssignmentCreated($trainer, $trainee);

// Simple
Mail::to($user)->send($mail);

// With CC/BCC/etc.
Mail::to($user1))
    ->cc($user2)
    ->bcc($user3)
    ->send($mail);

// With string email address and collections
Mail::to('me@app.com')
    ->bcc(User::all())
    ->send($mail)
```

## 电子邮件模板

邮件模板与任何其他模板都一样。它们可以扩展其他模板，使用部分，解析变量，包含条件或循环指令，以及执行任何您可以在普通 Blade 视图中执行的操作。

查看示例 15-4，查看示例 15-2 的可能的 `emails.assignment-created` 模板。

##### 示例 15-4\. 示例 `assignment-created` 电子邮件模板

```php
<!-- resources/views/emails/assignment-created.blade.php -->
<p>Hey {{ $trainee->name }}!</p>

<p>You have received a new training assignment from <b>{{ $trainer->name }}</b>.
Check out your <a href="{{ route('training-dashboard') }}">training
dashboard</a> now!</p>
```

在示例 15-2 中，`$trainer` 和 `$trainee` 都是您的可邮件化对象上的公共属性，这使它们可供模板使用。如果其中一个是私有的，它将不可用。

如果您想明确定义传递给模板的变量，可以在您的可邮件化内容的 `with` 参数中使用，如示例 15-5。

##### 示例 15-5\. 自定义模板变量

```php
use Illuminate\Mail\Mailables\Content;

public function content(): Content
{
    return new Content(
        view: 'emails.assignment-created',
        with: ['assignment' => $this->event->name],
    );
}
```

# HTML 与纯文本邮件

到目前为止，我们已经使用了 `new Content()` 实例上的 `view` 参数。这需要我们引用的模板返回 HTML。如果您想传递纯文本版本，请使用 `text` 参数来定义您的纯文本视图：

```php
public function content(): Content
{
    return new Content(
        html: 'emails.assignment-created',
        text: 'emails.assignment-created-text',
    );
}
```

## `envelope()` 方法中可用的方法

我们已经看过如何使用 `envelope()` 方法自定义主题和“发件人”地址。请注意，我们自定义它们的方式是通过向 `Envelope` 类的构造函数传递不同的命名参数：

```php
public function envelope(): Envelope
{
    return new Envelope(
        subject: 'New assignment from ' . $this->trainer->name,
        from: new Address($this->trainer->email, $this->trainer->name),
    );
}
```

这不是一个详尽的列表，但这里是一些可以通过 `envelope()` 方法传递到 `Envelope` 类以自定义电子邮件的参数的简短列表。任何可以接受 `Address` 的参数也可以接受字符串电子邮件地址或包含 `Address` 对象和/或字符串混合的数组。

`from: *地址*`

设置“发件人”姓名和地址 — 代表作者

`subject: *字符串*`

设置电子邮件主题

`cc: *地址*`

设置抄送

`bcc: *地址*`

设置密送

`replyTo: *地址*`

设置“回复地址”

`tags: *数组*`

设置标签，如果适用于您的电子邮件发件人

`metadata: *数组*`

设置元数据，如果适用于您的电子邮件发件人

最后，如果您想要对底层 Symfony 消息进行任何手动修改，可以在 `using` 参数中执行，如示例 15-6 所示。

##### 示例 15-6\. 修改底层 `SymfonyMessage` 对象

```php
public function envelope(): Envelope
{
    return new Envelope(
        subject: 'Howdy!',
        view: 'emails.howdy',
        using: [
            function (Email $message) {
                $message->setReplyTo('noreply@email.com');
            },
        ],
    );
}
```

## 附加文件和内联图片

要将文件附加到您的邮件中，请从 `attachments()` 方法返回一个数组（其中每个条目都是 `Attachment`），如示例 15-7 所示。

##### 示例 15-7\. 附加文件或数据到可邮件化对象

```php
use Illuminate\Mail\Mailables\Attachment;

// Attach a file using the local filename
public function attachments(): array
{
    return [
         Attachment::fromPath('/absolute/path/to/file'),
    ];
}

// Attach a file using storage disks
public function attachments(): array
{
    return [
        // Attach from default disk
        Attachment::fromStorage('/path/to/file'),
        // Attach from custom disk
        Attachment::fromStorageDisk('s3', '/path/to/file'),
    ];
}

// Attach a file passing the raw data
public function attachments(): array
{
    return [
        Attachment::fromData(fn () => file_get_contents($this->pdf), 'whitepaper.pdf')
            ->withMime('application/pdf'),
    ];
}
```

### 可附加的邮件对象

如果您有一个可以作为电子邮件附件表示的 PHP 类，或者如果您想要构建一个围绕附加到电子邮件的对象的逻辑的 PHP 类，您可以尝试 Laravel 的可附加对象。

这些对象中的每一个只需要是一个实现`Illuminate\Contracts\Mail\Attachable`接口的 PHP 类，该接口需要一个`toMailAttachment()`方法，该方法返回一个`Illuminate\Mail\Attachment`实例。

一个常见的例子是，如果你想要使你的 Eloquent 模型中的一个可附加的，我们的例子中，我们一直在给我们的客户发送来自他们的教练的新作业的电子邮件，所以让我们尝试使`Assignment`可附加。查看示例 15-8。

##### 示例 15-8\. 使 Eloquent 模型可附加

```php
<?php

namespace App\Models;

use Illuminate\Contracts\Mail\Attachable;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Mail\Attachment;

class Assignment extends Model implements Attachable
{
    /**
 * Get the attachable representation of the model.
 */
    public function toMailAttachment(): Attachment
    {
        return Attachment::fromPath($this->pdf_path);
    }
}
```

如果一个类实现了`Attachable`接口，你可以将该类的任何实例用作从`attachments()`方法返回的数组中的条目：

```php
public function attachments(): array
{
    return [$this->assignment];
}
```

### 内联图像

如果你想要将图像内联附加到你的电子邮件中，Laravel 也提供了相应的功能，正如你在示例 15-9 中所见。

##### 示例 15-9\. 在电子邮件中内联图像

```php
<!-- emails/image.blade.php -->
Here is an image:

<img src="{{ $message->embed(storage_path('embed.jpg')) }}">

Or, the same image embedding the data:

<img src="{{ $message->embedData(
 file_get_contents(storage_path('embed.jpg')), 'embed.jpg'
) }}">
```

## Markdown 可发送邮件

Markdown 可发送邮件允许你在 Markdown 中编写电子邮件内容，之后它将被转换为具有 Laravel 内置响应式 HTML 模板的完整 HTML（和纯文本）电子邮件。你还可以调整这些模板，以创建一个简单的、适合开发人员和非开发人员创建内容的自定义电子邮件模板。

首先，使用`markdown`标志运行`make:mail` Artisan 命令：

```php
php artisan make:mail AssignmentCreated --markdown=emails.assignment-created
```

你可以在示例 15-10 中看到生成的邮件文件的示例。

##### 示例 15-10\. 生成的 Markdown 可发送邮件

```php
class AssignmentCreated extends Mailable
{
    // ...

    public function content(): Content
    {
        return new Content(
            markdown: 'emails.assignment-created',
        );
    }
}
```

如你所见，这几乎与 Laravel 中的普通可发送邮件文件完全相同。主要区别在于，你将你的模板传递给`markdown`参数而不是`view`参数。还要注意，你引用的模板应该代表一个 Markdown 模板，而不是普通的 Blade 模板。

什么是*Markdown 模板*？与普通的 Blade 电子邮件模板不同，Blade 模板预期—​通过包含和继承等方式—​生成完整的 HTML 电子邮件，而 Markdown 模板只需将 Markdown 内容传递给几个预定义的组件。

这些组件看起来像`<x-mail::component-name-here>`，因此，你的 Markdown 邮件的主体应该传递给名为`<x-mail::message>`的组件。查看示例 15-11 来查看一个简单 Markdown 邮件模板的示例。

##### 示例 15-11\. 简单分配的 Markdown 邮件

```php
{{-- resources/views/emails/assignment-created.blade.php --}}
<x-mail::message>
# Hey {{ $trainee->name }}!

You have received a new training assignment from **{{ $trainer->name }}**

<x-mail::button :url="route('training-dashboard')">
View Your Assignment
</x-mail::button>

Thanks,<br>
{{ config('app.name') }}
</x-mail::message>
```

正如你在示例 15-11 中所见，有一个父级`mail::message`组件，你可以向其传递电子邮件的正文，但你还可以添加其他更小的组件到你的电子邮件中。我们在这里使用了`mail::button`组件，它需要传递内容（“查看您的作业”），同时需要传递`url`属性。

有三种可用的组件类型：

按钮

生成一个居中的按钮链接。按钮组件需要一个`url`属性，并允许一个可选的`color`属性，你可以传递`primary`、`success`或`error`。

面板

使用稍亮于消息其余部分的背景来渲染提供的文本。

表格

将通过 Markdown 表格语法传递的内容转换为表格。

# 自定义组件

这些 Markdown 组件内置在 Laravel 框架的核心中，但如果您需要自定义它们的工作方式，可以发布它们的文件并进行编辑：

```php
php artisan vendor:publish --tag=laravel-mail
```

您可以在[Laravel 文档](https://oreil.ly/R4Gr9)中了解有关自定义这些文件及其主题的更多信息。

## 将可邮件渲染到浏览器

在应用程序中开发电子邮件时，能够预览它们的渲染效果是很有帮助的。您可以依赖像 Mailtrap 这样的工具来进行预览，这是一个很有用的工具，但直接在浏览器中渲染邮件并立即看到您所做的更改也是很有帮助的。

查看示例 15-12 以查看您可以添加到应用程序中以渲染给定可邮件的示例路由。

##### 示例 15-12. 将可邮件渲染到路由

```php
Route::get('preview-assignment-created-mailable', function () {
    $trainer = Trainer::first();
    $trainee = Trainee::first();

    return new \App\Mail\AssignmentCreated($trainer, $trainee);
});
```

Laravel 也提供了一种快速在浏览器中预览通知的方法：

```php
Route::get('preview-notification', function () {
    $trainer = Trainer::first();
    $trainee = Trainee::first();

    return (new App\Notifications\AssignmentCreated($trainer, $trainee))
        ->toMail($trainee);
});
```

## 队列

发送电子邮件是一项耗时的任务，可能导致应用程序变慢，因此通常将其移到后台队列中是很常见的。事实上，Laravel 提供了一组内置工具，使得可以更轻松地将消息加入队列，而不必为每封电子邮件编写队列作业：

`queue()`

要将邮件对象加入队列而不是立即发送，只需将可邮件对象传递给`Mail::queue()`而不是`Mail::send()`：

```php
 Mail::to($user)->queue(new AssignmentCreated($trainer, $trainee));
```

`later()`

`Mail::later()`与`Mail::queue()`相同，但允许您添加延迟—​可以是几分钟，也可以通过传递`DateTime`或`Carbon`的实例来指定具体时间—​指定何时从队列中提取并发送电子邮件：

```php
 $when = now()->addMinutes(30);
 Mail::to($user)->later($when, new AssignmentCreated($trainer,  $trainee));
```

# 配置队列

这些方法的工作需要正确配置您的队列。查看第十六章了解有关队列工作原理及如何在应用程序中运行它们的更多信息。

对于`queue()`和`later()`，如果您希望指定邮件添加到哪个队列或队列连接，请在可邮件对象上使用`onConnection()`和`onQueue()`方法：

```php
$message = (new AssignmentCreated($trainer, $trainee))
    ->onConnection('sqs')
    ->onQueue('emails');

Mail::to($user)->queue($message);
```

如果您希望指定某个可邮件始终应该加入队列，可以让该可邮件实现`Illuminate\Contracts\Queue\ShouldQueue`接口。

## 本地开发

这对于在生产环境中发送邮件是很好的。但是如何进行测试呢？有两个主要工具值得考虑：Laravel 的`log`驱动程序和用于测试的虚拟收件箱，比如 Mailtrap。

### log 驱动程序

Laravel 提供了一个`log`驱动程序，会将您尝试发送的每封电子邮件记录到本地的*laravel.log*文件中（默认位于*storage/logs*中）。

要使用此功能，请编辑*.env*文件，并将`MAIL_MAILER`设置为`log`。现在打开或尾随*storage/logs/laravel.log*并从您的应用程序发送电子邮件。您将看到类似于以下内容：

```php
Message-ID: <04ee2e97289c68f0c9191f4b04fc0de1@localhost>
Date: Tue, 17 May 2016 02:52:46 +0000
Subject: Welcome to our app!
From: Matt Stauffer <matt@mattstauffer.com>
To: freja@jensen.no
MIME-Version: 1.0
Content-Type: text/html; charset=utf-8
Content-Transfer-Encoding: quoted-printable

Welcome to our app!
```

您可以选择指定将记录的邮件发送到与其余日志不同的日志通道。要么修改*config/mail.php*，要么在您的*.env*文件中将`MAIL_LOG_CHANNEL`变量设置为任何现有日志通道的名称。

### 虚拟收件箱

如果您想看看您的测试电子邮件在真实收件箱中的外观，您可以使用几种服务之一，这些服务允许您将您的电子邮件发送到它们，并在一个完整的虚假收件箱中显示您的电子邮件。

最常见的两种此类服务是 Mailtrap，这是一个无需设置的付费 SaaS，允许您与同事和客户分享收件箱，以及 Mailpit，这是一个可以通过 Docker 在本地运行的服务。

#### Mailtrap

[Mailtrap](https://mailtrap.io)是一个在开发环境中捕获和检查电子邮件的服务。您通过 SMTP 将您的邮件发送到 Mailtrap 服务器，但 Mailtrap 不会将这些邮件发送给预期的接收者，而是捕获它们并为您提供一个基于 Web 的电子邮件客户端以进行检查，无论目标电子邮件地址是什么。

要设置 Mailtrap，请注册免费帐户并访问您的演示基础仪表板。从 SMTP 列复制您的用户名和密码。

然后编辑您的应用程序的*.env*文件，并在`mail`部分设置以下值：

```php
MAIL_MAILER=smtp
MAIL_HOST=mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=your_username_from_mailtrap_here
MAIL_PASSWORD=your_password_from_mailtrap_here
MAIL_ENCRYPTION=null
```

现在，您从应用程序发送的任何电子邮件都将显示在 Mailtrap 收件箱中。

#### Mailpit

如果您喜欢 Mailtrap 的想法，但希望在本地（免费）运行应用程序，您可以使用[Mailpit](https://oreil.ly/dPgRK)，这是 Mailtrap 的一种替代方案，您可以在本地 Docker 容器中运行。

# 通知

大多数从 Web 应用程序发送的邮件的目的是通知用户特定操作已发生或需要发生。随着用户的沟通偏好变得越来越多样化，我们通过 Slack、短信和其他方式收集越来越多——以及更为不同的——通信包。

为支持这些偏好，Laravel 引入了一个名为*notifications*的概念。就像可邮寄物一样，通知是一个表示您可能希望发送给用户的单个通信的 PHP 类。现在，让我们假设我们正在通知我们的身体训练应用程序用户，他们有新的训练可用。

每个类代表发送通知给您的用户所需的所有信息，*可以使用一个或多个通知渠道*。单个通知可以通过电子邮件发送，通过 Vonage 发送短信，发送 WebSocket ping，向数据库添加记录，向 Slack 频道发送消息等等。

所以，让我们创建我们的通知：

```php
php artisan make:notification WorkoutAvailable
```

示例 15-13 展示了这给我们带来的东西。

##### 示例 15-13\. 自动生成的通知类

```php
<?php

namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Notification;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;

class WorkoutAvailable extends Notification
{
    use Queueable;

    /**
 * Create a new notification instance.
 */
    public function __construct()
    {
        //
    }

    /**
 * Get the notification's delivery channels.
 *
 * @return array<int, string>
 */
    public function via(object $notifiable): array
    {
        return ['mail'];
    }

    /**
 * Get the mail representation of the notification.
 */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->line('The introduction to the notification.')
                    ->action('Notification Action', url('/'))
                    ->line('Thank you for using our application!');
    }

    /**
 * Get the array representation of the notification.
 *
 * @return array<string, mixed>
 */
    public function toArray(object $notifiable): array
    {
        return [
            //
        ];
    }
}
```

我们可以从这里学到一些东西。首先，我们将向构造函数传递相关数据。其次，有一个`via()`方法，允许我们为给定用户定义要使用的通知渠道（`$notifiable`代表您系统中希望通知的任何实体；对于大多数应用程序，它将是一个用户，但并非总是如此）。第三，每个通知渠道都有单独的方法，允许我们明确定义如何通过该渠道发送其中一个通知。

# 当`$notifiable`不是一个用户时会发生什么？

尽管最常见的通知目标将是用户，但您可能希望通知其他内容。这可能仅仅是因为您的应用程序具有多个用户类型，因此您可能希望通知培训师和学员。但您也可能希望通知一个群组、一个公司或一个服务器。

所以，让我们修改这个类，适用于我们的`WorkoutAvailable`示例。看一下示例 15-14。

##### 示例 15-14\. 我们的`WorkoutAvailable`通知类

```php
...
class WorkoutAvailable extends Notification
{
    use Queueable;

    public function __construct(public $workout) {}

    public function via(object $notifiable): array
    {
        // This method doesn't exist on the User... we're going to make it up
        return $notifiable->preferredNotificationChannels();
    }

    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
            ->line('You have a new workout available!')
            ->action('Check it out now', route('workout.show', [$this->workout]))
            ->line('Thank you for training with us!');
    }

    public function toArray(object $notifiable): array
    {
        return [];
    }
}
```

## 为您的通知定义`via()`方法

正如您在示例 15-14 中所看到的，我们需要负责决定每个通知和每个通知对象要使用哪些通知渠道。

您可以将所有内容都发送为邮件或只发送为短信（示例 15-15）。

##### 示例 15-15\. 最简单的`via()`方法

```php
public function via(object $notifiable): array
{
    return 'vonage';
}
```

您还可以让每个用户选择一种首选方法，并将其保存在用户本身上（示例 15-16）。

##### 示例 15-16\. 根据用户自定义`via()`方法

```php
public function via(object $notifiable): array
{
    return $notifiable->preferred_notification_channel;
}
```

或者，正如我们在示例 15-14 中设想的那样，您可以在每个通知对象上创建一个方法，以进行一些复杂的通知逻辑。例如，您可以在工作时间通过某些渠道通知用户，而在晚上通过其他渠道通知用户。重要的是`via()`是一个 PHP 类方法，因此您可以在那里执行任何复杂的逻辑。

## 发送通知

有两种方法可以发送通知：使用`Notification`门面，或将`Notifiable`特性添加到一个 Eloquent 类（通常是您的`User`类）。

### 使用通知门面发送通知

`Notification`门面是两种方法中较为笨拙的一种，因为您必须同时传递通知对象和通知内容。然而，它非常有用，因为您可以选择同时传递多个通知对象，就像示例 15-17 中所示。

##### 示例 15-17\. 使用`Notification`门面发送通知

```php
use App\Notifications\WorkoutAvailable;
...
Notification::send($users, new WorkoutAvailable($workout));
```

### 使用通知特性发送通知

任何导入`Laravel\Notifications\Notifiable`特性的模型（默认情况下是`App\User`类）都有一个`notify()`方法，可以传递一个通知，看起来像示例 15-18。

##### 示例 15-18\. 使用`Notifiable`特性发送通知

```php
use App\Notifications\WorkoutAvailable;
...
$user->notify(new WorkoutAvailable($workout));
```

## 将通知排队

大多数通知驱动程序需要使用 HTTP 请求发送通知，这可能会减慢用户体验。为了处理这个问题，您可能希望将通知加入队列。所有通知默认导入 `Queueable` 特性，因此您只需在通知中添加 `implements ShouldQueue`，Laravel 将立即将其移到队列中。

与任何其他队列功能一样，您需要确保正确配置队列设置并运行队列工作程序。

如果您想延迟发送通知，可以在通知上运行 `delay()` 方法：

```php
$delayUntil = now()->addMinutes(15);

$user->notify((new WorkoutAvailable($workout))->delay($delayUntil));
```

## 开箱即用的通知类型

Laravel 默认提供了用于电子邮件、数据库、广播、Vonage SMS 和 Slack 的通知驱动程序。我会简要介绍每一个，但建议参考 [通知文档](https://oreil.ly/VzICd) 以获取更详细的介绍。

创建自己的通知驱动程序也很容易，已经有数十个人这样做了；您可以在 [Laravel 通知通道网站](https://oreil.ly/6d4_L) 找到它们。

### 电子邮件通知

让我们看看我们先前示例中的电子邮件，示例 15-14，是如何构建的：

```php
public function toMail(object $notifiable): MailMessage
{
    return (new MailMessage)
        ->line('You have a new workout available!')
        ->action('Check it out now', route('workouts.show', [$this->workout]))
        ->line('Thank you for training with us!');
}
```

结果显示在 图 15-1 中。电子邮件通知系统将您应用程序的名称放在电子邮件的标题中；您可以在 *config/app.php* 的 `name` 键中自定义该应用程序名称。

此电子邮件会自动发送到可通知对象的 `email` 属性，但您可以通过向可通知类添加名为 `routeNotificationForMail()` 的方法并返回您希望发送电子邮件通知的电子邮件地址来自定义此行为。

电子邮件的主题是通过解析通知类名称并将其转换为单词来设置的。因此，我们的 `WorkoutAvailable` 通知将具有默认主题“Workout Available”。您还可以通过在 `toMail()` 方法中的 `MailMessage` 上链接 `subject()` 方法来自定义此主题。

如果您想修改模板，请发布它们并随心所欲地编辑：

```php
php artisan vendor:publish --tag=laravel-notifications
```

![使用默认通知模板发送的电子邮件](img/lur3_1501.png)

###### 图 15-1\. 使用默认通知模板发送的电子邮件

### 数据库通知

您可以使用 `database` 通知渠道将通知发送到数据库表。首先，使用 `php artisan notifications:table` 创建您的表。接下来，在通知上创建一个 `toDatabase()` 方法并在其中返回一个数据数组。这些数据将被编码为 JSON 并存储在数据库表的 `data` 列中。

`Notifiable` 特性将 `notifications` 关系添加到其导入的任何模型中，使您可以轻松访问通知表中的记录。因此，如果您使用数据库通知，您可以执行类似 示例 15-20 的操作：

##### 示例 15-20\. 遍历用户的数据库通知

```php
User::first()->notifications->each(function ($notification) {
    // Do something
});
```

`database`通知渠道还有一个通知是否“已读”的概念。您可以仅作用于“未读”通知，如示例 15-21 所示：

##### 示例 15-21\. 遍历用户的未读数据库通知

```php
User::first()->unreadNotifications->each(function ($notification) {
    // Do something
});
```

您可以将一个或所有通知标记为已读，正如示例 15-22 所示。

##### 示例 15-22\. 将数据库通知标记为已读

```php
// Individual
User::first()->unreadNotifications->each(function ($notification) {
    if ($condition) {
        $notification->markAsRead();
    }
});

// All
User::first()->unreadNotifications->markAsRead();
```

### 广播通知

`broadcast`通道使用 Laravel 的事件广播功能发送通知，该功能由 WebSockets 提供支持（我们将在“通过 WebSockets 广播事件和 Laravel Echo”中了解更多信息）。

在您的通知上创建一个`toBroadcast()`方法并返回一个数据数组。如果您的应用程序已正确配置为事件广播，则该数据将在名为`*notifiable.id*`的私有通道上广播。`*id*`将是可通知对象的 ID，而`*notifiable*`将是可通知对象的完全限定类名，斜杠替换为句点—例如，ID 为`1`的`App\User`的私有通道将是`App.User.1`。

### 短信通知

短信通知通过[Vonage](https://www.vonage.com)发送，因此如果您想发送短信通知，请注册 Vonage 帐户，并按照[通知文档](https://oreil.ly/VzICd)中的说明操作。与其他渠道一样，您将设置`toVonage()`方法并在那里自定义短信内容。

# 短信通知包安装

在 Laravel 中，短信通知渠道是一个官方包。如果您想使用 Vonage 短信通知，只需使用 Composer 要求此包：

```php
composer require laravel/vonage-notification-channel \
    guzzlehttp/guzzle
```

### Slack 通知

`slack`通知渠道允许您自定义通知的外观，甚至附加文件。与其他渠道一样，您将设置`toSlack()`方法并在那里自定义消息。

# Slack 通知包安装

Laravel 的 Slack 通知渠道是一个官方包。如果您想使用 Slack 通知，只需使用 Composer 要求此包：

```php
composer require laravel/slack-notification-channel
```

### 其他通知

想要通过其他渠道发送通知，而不是默认提供的渠道？有一个强大的社区努力提供各种通知渠道的选择；请查看[Laravel 通知渠道网站](https://oreil.ly/6d4_L)上提供的内容。

# 测试

让我们看看如何测试邮件和通知。

## 邮件

我们可以针对我们的邮件编写两个方面的断言：邮件的内容和属性，以及实际触发发送的事实。让我们从针对邮件内容的断言开始。

### 针对邮件进行断言

首先，我们可以对`envelope()`类型的数据运行断言，就像您在示例 15-23 中所看到的那样。

##### 示例 15-23\. 针对可邮寄信封数据的断言

```php
$mailable = new AssignmentCreated($trainer, trainee);

$mailable->assertFrom('noreply@mytrainingapp.com');
$mailable->assertTo('user@gmail.com');
$mailable->assertHasCc('trainer@mytrainingapp.com');
$mailable->assertHasBcc('records@mytrainingapp.com');
$mailable->assertHasReplyTo('trainer@mytrainingap.com');
$mailable->assertHasSubject('New assignment from Faith Elizabeth');
$mailable->assertHasTag('assignments');
$mailable->assertHasMetadata('clientId', 4);
```

接下来，我们可以对消息内容进行断言，正如您在 示例 15-24 中所看到的。

##### 示例 15-24\. 对可邮寄对象的内容进行断言

```php
$mailable->assertSeeInHtml($trainee->name);
$mailable->assertSeeInHtml('You have received a new training assignment');
$mailable->assertSeeInOrderInHtml(['Hey', 'You have received']);

$mailable->assertSeeInText($trainee->name);
$mailable->assertSeeInOrderInText(['Hey', 'You have received']);
```

我们可以对附件进行断言，正如您在 示例 15-25 中所看到的。

##### 示例 15-25\. 对可邮寄对象的附件进行断言

```php
$mailable->assertHasAttachment('/pdfs/assignment-24.pdf');
$mailable->assertHasAttachment(Attachment::fromPath('/pdfs/assignment-24.pdf'));
$mailable->assertHasAttachedData($pdfData, 'assignment-24.pdf', [
    'mime' => 'application/pdf',
]);
$mailable->assertHasAttachmentFromStorage(
    '/pdfs/assignment-24.pdf',
    'assignment-24.pdf',
    ['mime' => 'application/pdf']
);
$mailable->assertHasAttachmentFromStorageDisk(
    's3',
    '/pdfs/assignment-24.pdf',
    'assignment-24.pdf',
    ['mime' => 'application/pdf']
);
```

### 断言邮件是否已发送

要测试邮件是否发送（或未发送），我们首先会运行 `Mail::fake()` 来捕获邮件动作以便检查。然后，我们可以运行各种断言，正如您在 示例 15-26 中所看到的。

##### 示例 15-26\. 对邮件是否已发送进行断言

```php
Mail::fake();

// Call the code that sends the email

// Assert that no mailables were sent
Mail::assertNothingSent();

// Assert that a mailable was sent
Mail::assertSent(AssignmentCreated::class);

// Assert a mailable was sent a certain number of times
Mail::assertSent(AssignmentCreated::class, 4);

// Assert a mailable was not sent
Mail::assertNotSent(AssignmentCreated::class);

// Assertions for queued emails
Mail::assertQueued(AssignmentCreated::class);
Mail::assertNotQueued(AssignmentCreated::class);
Mail::assertNothingQueued();
```

Laravel 还允许我们将闭包作为这些断言的第二个参数，检查电子邮件以确保它们符合我们的预期。看一看 示例 15-27。

##### 示例 15-27\. 在断言中检查邮件的属性

```php
Mail::assertSent(
    AssignmentCreated::class,
    function (AssignmentCreated $mail) use ($trainer, $trainee) {
        return $mail->hasTo($trainee->email) &&
            $mail->hasSubject('New assignment from ' . $trainer->name);
    }
);
```

您还可以使用 `hasCc()`、`hasBcc()`、`hasReplyTo()` 和 `hasFrom()`。

## 通知

Laravel 提供了一组内置的断言用于测试您的通知。示例 15-28 示范了这一点。

##### 示例 15-28\. 断言通知已发送

```php
public function test_new_signups_triggers_admin_notification()
{
    Notification::fake();

    Notification::assertSentTo($user, NewUsersSignedup::class,
        function ($notification, $channels) {
            return $notification->user->email == 'user-who-signed-up@gmail.com'
            && $channels == ['mail'];
    });

    // Assert that the email was sent to a given user
    Notification::assertSentTo(
        [$user],
        NewUsersSignedup::class
    );

    // You can also use assertNotSentTo()
    Notification::assertNotSentTo(
        [$userDidntSignUp], NewUsersSignedup::class
    );
}
```

# TL;DR

Laravel 的邮件和通知功能提供了简单一致的接口，可以连接各种消息系统。Laravel 的邮件系统使用可邮寄对象（mailables），这些是代表电子邮件的 PHP 类，提供了对不同邮件驱动程序一致的语法。通知系统使得构建一个可以通过多种不同媒介传送的单一通知变得简单，从电子邮件到短信再到实体明信片。
