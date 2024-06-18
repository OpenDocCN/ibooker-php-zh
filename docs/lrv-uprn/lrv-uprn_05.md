# 第五章：数据库和 Eloquent

Laravel 提供了一套工具来与你的应用程序数据库进行交互，其中最显著的是 Eloquent，Laravel 的 ActiveRecord ORM。

Eloquent 是 Laravel 最受欢迎和有影响力的功能之一。它是 Laravel 与大多数 PHP 框架不同之处的一个很好的例子；在强大但复杂的 DataMapper ORM 的世界中，Eloquent 因其简洁而脱颖而出。每个表格对应一个类，负责在该表格中检索、表示和持久化数据。

无论你选择使用 Eloquent 与否，你仍然会从 Laravel 提供的其他数据库工具中获得很多好处。因此，在深入研究 Eloquent 之前，我们将首先介绍 Laravel 数据库功能的基础：迁移、填充器和查询构建器。

然后我们将涵盖 Eloquent：定义你的模型；插入、更新和删除；使用访问器、修改器和属性转换自定义你的响应；最后是关系。这里涉及很多内容，很容易感到不知所措，但如果我们一步一个脚印地进行，我们一定能够成功。

# 配置

在我们深入了解 Laravel 的数据库工具如何使用之前，让我们暂停片刻，回顾一下如何配置你的数据库凭据和连接。

数据库访问的配置位于 *config/database.php* 和 *.env* 中。像 Laravel 的许多其他配置区域一样，你可以定义多个“连接”，然后决定代码默认使用哪一个。

## 数据库连接

默认情况下，每个驱动程序都有一个连接，如你可以在 示例 5-1 中看到的。

##### 示例 5-1\. 默认的数据库连接列表

```php
'connections' => [

    'sqlite' => [
        'driver' => 'sqlite',
        'url' => env('DATABASE_URL'),
        'database' => env('DB_DATABASE', database_path('database.sqlite')),
        'prefix' => '',
        'foreign_key_constraints' => env('DB_FOREIGN_KEYS', true),
    ],

    'mysql' => [
        'driver' => 'mysql',
        'url' => env('DATABASE_URL'),
        'host' => env('DB_HOST', '127.0.0.1'),
        'port' => env('DB_PORT', '3306'),
        'database' => env('DB_DATABASE', 'forge'),
        'username' => env('DB_USERNAME', 'forge'),
        'password' => env('DB_PASSWORD', ''),
        'unix_socket' => env('DB_SOCKET', ''),
        'charset' => 'utf8mb4',
        'collation' => 'utf8mb4_unicode_ci',
        'prefix' => '',
        'prefix_indexes' => true,
        'strict' => true,
        'engine' => null,
        'options' => extension_loaded('pdo_mysql') ? array_filter([
            PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
        ]) : [],
    ],

    'pgsql' => [
        'driver' => 'pgsql',
        'url' => env('DATABASE_URL'),
        'host' => env('DB_HOST', '127.0.0.1'),
        'port' => env('DB_PORT', '5432'),
        'database' => env('DB_DATABASE', 'forge'),
        'username' => env('DB_USERNAME', 'forge'),
        'password' => env('DB_PASSWORD', ''),
        'charset' => 'utf8',
        'prefix' => '',
        'prefix_indexes' => true,
        'search_path' => 'public',
        'sslmode' => 'prefer',
    ],

    'sqlsrv' => [
        'driver' => 'sqlsrv',
        'url' => env('DATABASE_URL'),
        'host' => env('DB_HOST', 'localhost'),
        'port' => env('DB_PORT', '1433'),
        'database' => env('DB_DATABASE', 'forge'),
        'username' => env('DB_USERNAME', 'forge'),
        'password' => env('DB_PASSWORD', ''),
        'charset' => 'utf8',
        'prefix' => '',
        'prefix_indexes' => true,
        // 'encrypt' => env('DB_ENCRYPT', 'yes'),
        // 'trust_server_certificate' => env('DB_TRUST_SERVER_CERTIFICATE', 'false'),
    ],

]
```

没有什么能阻止你删除或修改这些命名连接或创建自己的连接。你可以创建新的命名连接，并且可以在其中设置驱动程序（MySQL、PostgreSQL 等）。因此，尽管默认情况下每个驱动程序只有一个连接，但这并不是限制；如果你愿意，你可以拥有五个不同的连接，都使用 `mysql` 驱动程序。

每个连接都允许你定义连接到和定制每种连接类型所需的属性。

对于多驱动程序的想法有几个原因。首先，“连接”部分是一个简单的模板，使得启动使用任何支持的数据库连接类型的应用程序变得简单。在许多应用程序中，你可以选择你将使用的数据库连接，填写它的信息，甚至如果愿意，删除其他的连接。我通常会把它们都保留在那里，以防以后可能会用到它们。

但也有一些情况下，你可能需要在同一个应用程序中使用多个连接。例如，你可能会为两种不同类型的数据使用不同的数据库连接，或者你可能会从一个数据库读取并向另一个数据库写入。支持多连接使这成为可能。

## URL 配置

像 Heroku 这样的服务通常会提供一个环境变量，其中包含连接到数据库所需的所有信息。它看起来像这样：

```php
mysql://root:password@127.0.0.1/forge?charset=UTF-8
```

您无需编写代码来解析此 URL；相反，将其作为 `DATABASE_URL` 环境变量传递给 Laravel，它将理解它。

## 其他数据库配置选项

*config/database.php* 配置部分有许多其他配置设置。您可以配置 Redis 访问、自定义迁移表名称、确定默认连接，并切换非 Eloquent 调用返回 `stdClass` 或数组实例。

在 Laravel 中，任何允许来自多个源的连接的服务 —— 会话可以由数据库或文件存储支持，缓存可以使用 Redis 或 Memcached，数据库可以使用 MySQL 或 PostgreSQL —— 您可以定义多个连接，并选择特定连接将成为“默认”连接，这意味着任何时候不显式请求特定连接时将使用该连接。以下是如何请求特定连接的示例：

```php
$users = DB::connection('secondary')->select('select * from users');
```

# 迁移

现代框架如 Laravel 使得使用代码驱动的迁移轻松定义数据库结构。每个新表、列、索引和键都可以在代码中定义，任何新环境都可以在几秒钟内从空白数据库带到应用程序的完美架构。

## 定义迁移

迁移是一个单独的文件，定义了两件事情：运行此迁移时所需的修改（*向上*），以及可选的运行此迁移时所需的修改（*向下*）。

示例 5-2 展示了 Laravel 默认的“创建用户表”迁移的样子。

##### 示例 5-2\. Laravel 默认的“创建用户表”迁移

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
 * Run the migrations.
 *
 * @return void
 */
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }

    /**
 * Reverse the migrations.
 *
 * @return void
 */
    public function down(): void
    {
        Schema::dropIfExists('users');
    }
};
```

# 电子邮件验证

`email_verified_at` 列存储了用户验证其电子邮件地址的时间戳。

如您所见，我们有一个 `up()` 方法和一个 `down()` 方法。`up()` 告诉迁移创建一个名为 `users` 的新表，并带有几个字段，而 `down()` 则告诉它删除 `users` 表。

### 创建迁移

正如您将在第 8 章中看到的，Laravel 提供了一系列命令行工具，您可以使用它们与应用程序交互并生成样板文件。其中一个命令允许您创建迁移文件。您可以使用 `php artisan make:migration` 运行它，并且它有一个参数，即迁移的名称。例如，要创建我们刚刚讨论过的表，您可以运行 `php artisan make:migration create_users_table`。

可以选择向此命令传递两个标志。`--create=*table_name*` 参数将填充迁移，用于创建名为 `*table_name*` 的表，而 `--table=*table_name*` 则仅填充迁移以修改现有表。

```php
php artisan make:migration create_users_table
php artisan make:migration add_votes_to_users_table --table=users
php artisan make:migration create_users_table --create=users
```

### 创建表

我们已经在默认的`create_users_table`迁移中看到，我们的迁移依赖于`Schema`门面及其方法。我们可以在这些迁移中做的一切都依赖于`Schema`的方法。

要在迁移中创建新表格，请使用`create()`方法—第一个参数是表名，第二个是定义其列的闭包：

```php
Schema::create('users', function (Blueprint $table) {
    // Create columns here
});
```

### 创建列

要在表格中创建新列，无论是在创建表格调用还是修改表格调用中，都可以使用传递到闭包中的`Blueprint`实例：

```php
Schema::create('users', function (Blueprint $table) {
    $table->string('name');
});
```

让我们看看`Blueprint`实例上可用的各种方法来创建列。我将描述它们在 MySQL 中的工作方式，但如果您使用的是其他数据库，Laravel 将使用最接近的等效方法。

以下是简单字段`Blueprint`方法：

`id()`

`$table->bigIncrements('id')`的别名

`integer(*colName*)`, `tinyInteger(*colName*)`, `smallInteger(*colName*)`, `mediumInteger(*colName*)`, `bigInteger(*colName*)`, `unsignedTinyInteger(*colName*)`, `unsignedSmallInteger(*colName*)`, `unsignedMediumInteger(*colName*)`,

`unsignedBigInteger(*colName*)`

添加一个`INTEGER`类型列，或其许多变体之一

`string(*colName*, *length*)`

添加一个带有可选长度的`VARCHAR`类型列

`binary(*colName*)`

添加一个`BLOB`类型列

`boolean(*colName*)`

添加一个`BOOLEAN`类型列（在 MySQL 中为`TINYINT(1)`）

`char(*colName*, *length*)`

添加一个带有可选长度的`CHAR`类型列

`date(*colName*)`, `datetime(*colName*)`, `dateTimeTz(*colName*)`

添加一个`DATE`或`DATETIME`类型列；如果需要时区感知，请使用`dateTimeTz()`方法来创建带有时区的`DATETIME`列

`decimal(*colName*, *precision*, *scale*)`,

`unsignedDecimal(*colName*, *precision*, *scale*)`

添加一个带有精度和比例的`DECIMAL`类型列—例如，`decimal('*amount*', *5*, *2*)`指定了精度为 5，比例为 2；对于无符号列，请使用`unsignedDecimal`方法

`double(*colName*, *total digits*, *digits after decimal*)`

添加一个`DOUBLE`类型的列—例如，`double('*tolerance*', *12*, *8*)`指定了总长度为 12 位，其中小数点后有 8 位，比如`7204.05691739`

`enum(*colName*, [*choiceOne*, *choiceTwo*])`

添加一个带有提供的选择的`ENUM`类型列

`float(*colName*, *precision*, *scale*)`

添加一个`FLOAT`类型列（与 MySQL 中的`double`相同）

`foreignId(*colName*)`, `foreignUuid(*colName*)`

添加一个`UNSIGNED BIGINT`类型或`UUID`列，提供了选择

`foreignIdFor(*colName*)`

使用名称为*`colName`*的`UNSIGNED BIG INT`类型列

`geometry(*colName*)`, `geometryCollection(*colName*)`

添加一个`GEOMETRY`或`GEOMETRYCOLLECTION`类型列

`ipAddress(*colName*)`

添加一个`VARCHAR`类型列

`json(*colName*)`, `jsonb(*colName*)`

添加一个`JSON`或`JSONB`类型列

`lineString(*colName*)`, `multiLineString(*colName*)`

使用给定的*`colName`*添加一个`LINESTRING`或`MULTILINESTRING`类型列

`text(*colName*)`、`tinyText(*colName*)`、`mediumText(*colName*)`、`longText(*colName*)`

添加一个`TEXT`列（或其不同大小的变体）

`macAddress(*colName*)`

在支持它的数据库（如 PostgreSQL）中添加一个`MACADDRESS`列；在其他数据库系统中，它创建一个字符串等效

`multiPoint(*colName*)`、`multiPolygon(*colName*)`、`polygon(*colName*)`

`point(*colName*)`

分别添加`MULTIPOINT`、`MULTIPOLYGON`、`POLYGON`和`POINT`类型的列

`set(*colName*, *membersArray*)`

创建一个名为*`colName`*的`SET`列，*`membersArray`*作为成员

`time(*colName*, *precision*), timeTz(*colName*, *precision*)`

添加一个带有*`colName`*名称的`TIME`列；要进行时区感知，请使用`timeTz()`方法

`timestamp(*colName*, *precision*)`

`timestampTz(*colName*, *precision*)`

添加一个`TIMESTAMP`列；要进行时区感知，请使用`timestampTz()`方法

`uuid(*colName*)`

添加一个`UUID`列（在 MySQL 中为`CHAR(36)`）

`year()`

添加一个`YEAR`列

这些是特殊（联合）的`Blueprint`方法：

`increments(*colName*)`、`tinyIncrements(*colName*)`、`smallIncrements(*colName*)`、`mediumIncrements(*colName*)`、`bigIncrements(*colName*)`

添加一个无符号增量的`INTEGER`主键 ID，或其多种变体之一

`timestamps(*precision*)`、`nullableTimestamps(*precision*)`

`timestampsTz(*precision*)`

添加具有可选精度、可空和时区感知变体的`created_at`和`updated_at`时间戳列

`rememberToken()`

为用户“记住我”令牌添加一个`remember_token`列（`VARCHAR(100)`）

`softDeletes(*colName*, *precision*)`、`softDeletesTz(*colName*, *precision*)`

添加一个`deleted_at`时间戳以用于软删除，具有可选的精度和时区感知变体

`morphs(*colName*)`, `nullableMorphs(*colName*)`, `uuidMorphs(*relationshipName*)`,

`nullableUuidMorphs(*relationshipName*)`

对于提供的*`colName`*，添加一个整数`colName_id`和一个字符串`colName_type`（例如，`morphs(tag)`添加整数`tag_id`和字符串`tag_type`）；用于多态关系中使用 ID 或 UUID，并可以根据方法名称设置为可空

### 流畅地构建额外的属性

字段定义的大多数属性——例如其长度——作为字段创建方法的第二个参数设置，就像我们在前一节中看到的那样。但是还有一些其他属性，我们将在创建列后通过更多方法调用链设置。例如，此`email`字段可空，并将（在 MySQL 中）放在`last_name`字段后面：

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('email')->nullable()->after('last_name');
});
```

以下方法是用于设置字段的附加属性的一些方法；请参阅[migrations docs](https://oreil.ly/4Z-gC)获取详尽列表。

`nullable()`

允许将`NULL`值插入此列

`default('*default content*')`

指定此列的默认内容（如果未提供值）

`unsigned()`

将整数列标记为无符号（不是负数或正数，而是整数）

`first()`（仅适用于 MySQL）

将列名放在列顺序的最前面

`after(*colName*)`（仅适用于 MySQL）

将列放在列顺序的另一列之后

`charset(*charset*)`（仅适用于 MySQL）

为列设置字符集

`collation(*collation*)`

为列设置排序规则

`invisible()`（仅适用于 MySQL）

将列对`SELECT`查询不可见

`useCurrent()` 

用于`TIMESTAMP`列，使用`CURRENT_TIMESTAMP`作为默认值

`isGeometry()`（仅适用于 PostgreSQL）

将列类型设置为`GEOMETRY`（默认为`GEOGRAPHY`）

`unique()`

添加`UNIQUE`索引

`primary()`

添加主键索引

`index()`

添加基本索引

请注意，`unique()`、`primary()`和`index()`也可以在流畅的列构建上下文之外使用，我们稍后会讨论这一点。

### 删除表

如果要删除表，请在`Schema`上使用`dropIfExists()`方法，该方法接受一个参数，即表名：

```php
Schema::dropIfExists('contacts');
```

### 修改列

要修改列，只需编写代码，就像创建新列一样，然后在其后追加一个`change()`方法的调用。

# 修改列之前的必需依赖项

如果您没有使用原生支持重命名和删除列的数据库（大多数常见数据库的最新版本支持这些操作），在修改任何列之前，您需要运行`composer require doctrine/dbal`。

所以，如果我们有一个名为`name`的字符串列，长度为`255`，我们想把它的长度改为`100`，写法如下：

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('name', 100)->change();
});
```

如果我们要调整未在方法名中定义的任何字段属性，使其可为空，我们可以这样做：

```php
Schema::table('contacts', function (Blueprint $table) {
    $table->string('deleted_at')->nullable()->change();
});
```

这是如何重命名列的方法：

```php
Schema::table('contacts', function (Blueprint $table)
{
    $table->renameColumn('promoted', 'is_promoted');
});
```

这是如何删除列的方法：

```php
Schema::table('contacts', function (Blueprint $table)
{
    $table->dropColumn('votes');
});
```

### 压缩迁移

如果迁移过多而无法理解，可以将它们全部合并到一个单独的 SQL 文件中，Laravel 将在运行任何未来迁移之前运行该文件。这称为“压缩”您的迁移。

```php
// Squash the schema but keep your existing migrations
php artisan schema:dump

// Dump the current database schema and delete all existing migrations
php artisan schema:dump --prune
```

Laravel 仅在检测到到目前为止未运行迁移时才运行这些转储。这意味着您可以压缩您的迁移，而不会破坏已部署的应用程序。

###### 警告

如果您使用模式转储，则无法在内存中使用 SQLite；它仅适用于 MySQL、PostgreSQL 和本地文件 SQLite。

### 索引和外键

我们已经讨论了如何创建、修改和删除列。现在让我们继续索引和关联它们。

如果您对索引不熟悉，您的数据库可以在不使用它们的情况下运行，但它们对性能优化非常重要，并且对于一些关于相关表的数据完整性控制也很重要。我建议您详细了解一下它们，但如果您确实需要，您可以暂时跳过此部分。

#### 添加索引

查看示例 5-3 了解如何向列添加索引的示例。

##### 示例 5-3。在迁移中添加列索引

```php
// After columns are created...
$table->primary('primary_id'); // Primary key; unnecessary if used increments()
$table->primary(['first_name', 'last_name']); // Composite keys
$table->unique('email'); // Unique index
$table->unique('email', 'optional_custom_index_name'); // Unique index
$table->index('amount'); // Basic index
$table->index('amount', 'optional_custom_index_name'); // Basic index
```

请注意，如果你使用`increments()`或`bigIncrements()`方法创建索引，那么`primary()`的第一个示例是不必要的；这会自动为你添加一个主键索引。

#### 移除索引

我们可以如示例 5-4 中所示移除索引（#rm-col-ix）。

##### 示例 5-4. 在迁移中移除列索引

```php
$table->dropPrimary('contacts_id_primary');
$table->dropUnique('contacts_email_unique');
$table->dropIndex('optional_custom_index_name');

// If you pass an array of column names to dropIndex, it will
// guess the index names for you based on the generation rules
$table->dropIndex(['email', 'amount']);
```

#### 添加和移除外键

要添加一个外键，定义一个特定列引用另一张表上的列，Laravel 的语法简单清晰：

```php
$table->foreign('user_id')->references('id')->on('users');
```

在这里，我们在`user_id`列上添加一个`foreign`索引，显示它引用了`users`表上的`id`列。再简单不过了。

如果我们想指定外键约束，也可以，比如`cascadeOnUpdate()`，`restrictOnUpdate()`，`cascadeOnDelete()`，`restrictOnDelete()`和`nullOnDelete()`。例如：

```php
$table->foreign('user_id')
    ->references('id')
    ->on('users')
    ->cascadeOnDelete();
```

还有一个别名用于创建外键约束。使用它，上面的示例可以这样写：

```php
$table->foreignId('user_id')->constrained()->cascadeOnDelete();
```

要删除外键，我们可以通过引用其索引名称来删除它（该名称是通过组合引用的列和表的名称自动生成的）：

```php
$table->dropForeign('contacts_user_id_foreign');
```

或者通过传递一个数组，其中包含它在本地表中引用的字段：

```php
$table->dropForeign(['user_id']);
```

## 运行迁移

定义了迁移之后，如何运行它们？有一个 Artisan 命令可以做到：

```php
php artisan migrate
```

此命令运行所有“未完成”的迁移（通过在每个上运行`up()`方法）。Laravel 会跟踪你已经运行和未运行的迁移。每次运行此命令时，它会检查是否已运行所有可用的迁移，如果没有，则运行任何未完成的迁移。

在这个命名空间中有几个选项可以使用。首先，你可以运行你的迁移*和*你的种子（我们将在下文中介绍）：

```php
php artisan migrate --seed
```

你也可以运行以下任何命令：

`migrate:install`

创建一个数据库表，用来跟踪你已经和未运行的迁移；当你运行迁移时，这会自动运行，所以基本上你可以忽略它。

`migrate:reset`

回滚你在此实例上运行的每个数据库迁移。

`migrate:refresh`

回滚你在此实例上运行的每个数据库迁移，然后运行所有可用的迁移。这与运行`migrate:reset`然后`migrate`相同。

`migrate:fresh`

删除所有表并再次运行每个迁移。这与`refresh`相同，但不涉及“下”迁移，它只是删除表然后再次运行“上”迁移。

`migrate:rollback`

仅回滚上次运行`migrate`的迁移，或者使用添加的选项`--step=*n*`，回滚指定数量的迁移。

`migrate:status`

显示一个表格，列出每个迁移，每个旁边都有一个`Y`或`N`，表示此环境中是否已经运行。

# 使用 Homestead/Vagrant 进行迁移

如果您在本地计算机上运行迁移，并且您的 *.env* 文件指向 Vagrant 盒子中的数据库，则迁移将失败。您需要通过 SSH 进入您的 Vagrant 盒子，然后从那里运行迁移。对于种子和任何其他会影响或从数据库读取的 Artisan 命令也是如此。

# 检查您的数据库

如果您想深入了解数据库的状态或定义，包括其表和模型，那么有一些专门的 Artisan 命令可以帮到您：

`db:show`

显示整个数据库的表概览，包括连接详细信息、表、大小和开放连接数

`db:table {*tableName*}`

给定表名，显示大小并列出列

`db:monitor`

列出数据库的开放连接数目

# 填充

使用 Laravel 进行填充是如此简单，以至于它作为正常开发工作流程的一部分得到了广泛采用，这在以前的 PHP 框架中并不常见。有一个 *database/seeders* 文件夹，其中包含一个 `DatabaseSeeder` 类，该类在调用填充器时会调用其 `run()` 方法。

运行填充器有两种主要方式：与迁移一起或单独运行。

要与迁移一起运行填充器，只需在任何迁移调用中添加 `--seed`：

```php
php artisan migrate --seed
php artisan migrate:refresh --seed
```

要独立运行填充器：

```php
php artisan db:seed
php artisan db:seed VotesTableSeeder
```

这将默认调用 `DatabaseSeeder` 的 `run()` 方法，或者在传递类名时指定的填充器类。

## 创建一个填充器

要创建一个填充器，请使用 `make:seeder` Artisan 命令：

```php
php artisan make:seeder ContactsTableSeeder
```

您现在将在 *database/seeders* 目录中看到一个 `ContactsTableSeeder` 类。在我们编辑它之前，让我们将它添加到 `DatabaseSeeder` 类中，就像 示例 5-5 中所示的那样，这样当我们运行填充器时它就会运行。

##### 示例 5-5\. 从 DatabaseSeeder.php 调用自定义填充器

```php
// database/seeders/DatabaseSeeder.php
...
public function run(): void
{
    $this->call(ContactsTableSeeder::class);
}
```

现在让我们编辑填充器本身。在那里，我们可以做的最简单的事情是使用`DB`门面手动插入记录，就像 示例 5-6 中所示的那样。

##### 示例 5-6\. 在自定义填充器中插入数据库记录

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class ContactsTableSeeder extends Seeder
{
    public function run(): void
    {
        DB::table('contacts')->insert([
            'name' => 'Lupita Smith',
            'email' => 'lupita@gmail.com',
        ]);
    }
}
```

这将为我们获取一条记录，这是一个很好的开始。但是对于真正功能齐全的填充器，您可能希望循环使用某种随机生成器并多次运行`insert()`，对吧？Laravel 就有这样的功能。

## 模型工厂

模型工厂定义了创建数据库表中虚假条目的模式之一（或更多）。默认情况下，每个工厂的命名都是根据 Eloquent 类命名的。

理论上，您可以随意命名这些工厂，但将工厂命名为您的 Eloquent 类的方式是最习惯的方法。如果您遵循不同的约定来命名您的工厂，您可以在相关模型中设置工厂类名。

### 创建一个模型工厂

模型工厂位于 *database/factories*。每个工厂都在自己的类中定义，具有一个定义方法。在此方法中，您定义要在工厂创建模型时使用的属性及其值。

要生成一个新的工厂类，使用 Artisan 的`make:factory`命令；通常将工厂类命名为它们要生成实例的 Eloquent 模型：

```php
php artisan make:factory ContactFactory
```

这将在*database/factories*目录中生成一个名为*ContactFactory.php*的新文件。我们为联系人定义的最简单的工厂可能看起来像 Example 5-7：

##### Example 5-7\. 最简单的工厂定义

```php
<?php

namespace Database\Factories;

use App\Models\Contact;
use Illuminate\Database\Eloquent\Factories\Factory;

class ContactFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => 'Lupita Smith',
            'email' => 'lupita@gmail.com',
        ];
    }
}
```

现在，你需要在你的模型中使用`Illuminate\Database\Eloquent\Factories\HasFactory`特性。

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    use HasFactory;
}
```

`HasFactory`特性提供了一个静态的`factory()`方法，它使用 Laravel 的约定来确定模型的适当工厂。它将在`Database\Factories`命名空间中寻找一个类名与模型名匹配且以`Factory`结尾的工厂。如果你不遵循这些约定，你可以在你的模型中覆盖`newFactory()`方法来指定应该使用的工厂类：

```php
// app/Models/Contact.php
...
 * Create a new factory instance for the model.
 *
 * @return \Illuminate\Database\Eloquent\Factories\Factory
 */
protected static function newFactory()
{
    return \Database\Factories\Base\ContactFactory::new();
}
```

现在我们可以在模型上调用静态的`factory()`方法，在我们的种子和测试中创建一个`Contact`的实例：

```php
// Create one
$contact = Contact::factory()->create();

// Create many
Contact::factory()->count(20)->create();
```

然而，如果我们使用该工厂创建了 20 个联系人，那么所有 20 个联系人将具有相同的信息。这就没那么有用了。

当我们利用[Faker](https://oreil.ly/gxnrI)全球可用于 Laravel 的`fake()`助手时，我们将从模型工厂中获得更多好处；Faker 使得随机生成结构化虚假数据变得轻而易举。之前的例子现在变成了 Example 5-8。

##### Example 5-8\. 一个简单的工厂，修改以使用 Faker

```php
<?php

namespace Database\Factories;

use App\Models\Contact;
use Illuminate\Database\Eloquent\Factories\Factory;

class ContactFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->email(),
        ];
    }
}
```

现在，每次我们使用这个模型工厂创建一个虚假联系人时，所有属性都将被随机生成。

模型工厂至少需要返回此表所需的数据库字段。

# 确保随机生成的数据的唯一性

如果你希望保证任何给定条目的随机生成值在 PHP 进程中与其他随机生成值不同，你可以使用 Faker 的`unique()`方法：

```php
return ['email' => fake()->unique()->email()];
```

### 使用模型工厂

有两种主要情境下我们会使用模型工厂：测试，我们将在 Chapter 12 中介绍；以及种子，我们在这里介绍。让我们使用模型工厂编写一个种子程序；看一看 Example 5-9。

##### Example 5-9\. 使用模型工厂

```php
$post = Post::factory()->create([
    'title' => 'My greatest post ever',
]);

// Pro-level factory; but don't get overwhelmed!
User::factory()->count(20)->has(Address::factory()->count(2))->create()
```

要创建一个对象，我们在模型上使用`factory()`方法。然后我们可以在它上面运行两种方法之一：`make()`或`create()`。

这两种方法都会使用工厂类中的定义生成指定模型的实例。区别在于，`make()`创建实例但不立即保存到数据库，而`create()`则立即保存到数据库。

#### 在调用模型工厂时覆盖属性

如果你将数组传递给`make()`或`create()`，你可以像我们在 Example 5-9 中所做的那样，手动设置帖子上的`title`来覆盖特定的键。

#### 使用模型工厂生成多个实例

如果在调用`factory()`方法后调用`count()`方法，可以指定要创建多个实例。它不会返回单个实例，而是返回一组实例。这意味着您可以像处理数组一样处理结果，迭代它们或将它们传递给接受多个对象的任何方法：

```php
$posts = Post::factory()->count(6);
```

您还可以选择定义每个覆盖的“序列”：

```php
$posts = Post::factory()
    ->count(6)
    ->state(new Sequence(
        ['is_published' => true],
        ['is_published' => false],
    ))
    ->create();
```

### 专业级模型工厂

现在我们已经涵盖了模型工厂的最常见用途和排列方式，让我们深入一些更复杂的使用方法。

#### 在定义模型工厂时附加关系

有时，您需要创建一个与您正在创建的项目相关的项目。您可以调用相关模型上的工厂方法以获取其 ID，如示例 5-10 所示。

##### 示例 5-10\. 在工厂中创建相关项

```php
<?php

namespace Database\Factories;

use App\Models\Contact;
use Illuminate\Database\Eloquent\Factories\Factory;

class ContactFactory extends Factory
{
    protected $model = Contact::class;

    public function definition(): array
    {
        return [
            'name' => 'Lupita Smith',
            'email' => 'lupita@gmail.com',
            'company_id' => \App\Models\Company::factory(),
        ];
    }
}
```

您还可以传递一个闭包，闭包将传递一个参数，其中包含到目前为止生成项目的数组形式。这可以在其他地方使用，如示例 5-11 所示。

##### 示例 5-11\. 在工厂中使用其他参数的值

```php
// ContactFactory.php
public function definition(): array
{
    return [
        'name' => 'Lupita Smith',
        'email' => 'lupita@gmail.com',
        'company_id' => Company::factory(),
        'company_size' => function (array $attributes) {
            // Uses the "company_id" property generated above
            return Company::find($attributes['company_id'])->size;
        },
    ];
}
```

#### 在生成模型工厂实例时附加相关项

虽然我们已经讨论了如何在工厂定义中定义关系，但更常见的情况是我们在创建实例时定义实例的相关项。

在这方面，我们将使用两种主要方法：`has()`和`for()`。`has()`允许我们定义我们正在创建的实例“有”子项或其他项目的“hasMany”类型关系，而`for()`允许我们定义我们正在创建的实例“belongsTo”另一个项目。让我们看几个示例，以更好地了解它们的工作方式。

在示例 5-12 中，让我们假设`Contact`有许多`Addresses`。

##### 示例 5-12\. 在生成相关模型时使用`has()`

```php
// Attach 3 addresses
Contact::factory()
    ->has(Address::factory()->count(3))
    ->create()

// Accessing information about each user in the child factory
$contact = Contact::factory()
    ->has(
        Address::factory()
            ->count(3)
            ->state(function (array $attributes, User $user) {
                return ['label' => $user->name . ' address'];
            })
    )
    ->create();
```

现在让我们假设我们正在创建子实例而不是父实例。让我们生成一个地址。

在这些情况下，您通常可以假设子工厂定义将负责生成父实例。那么，`for()`的用途是什么？如果您想要特别定义父实例的某些内容，通常是其一个或多个属性，或者传递特定的模型实例，这将非常有帮助。看看示例 5-13，看看它是如何最常用的。

##### 示例 5-13\. 在生成相关模型时使用`for()`

```php
// Specify details about the created parent
Address::factory()
    ->count(3)
    ->for(Contact::factory()->state([
        'name' => 'Imani Carette',
    ]))
    ->create();

// Use an existing parent model (assuming we already have it as $contact)
Address::factory()
    ->count(3)
    ->for($contact)
    ->create();
```

#### 定义和访问多个模型工厂状态

让我们回顾一下*ContactFactory.php*（来自示例 5-7 和 5-8）。我们定义了一个基础的`Contact`工厂：

```php
class ContactFactory extends Factory
{
    protected $model = Contact::class;

    public function definition(): array
    {
        return [
            'name' => 'Lupita Smith',
            'email' => 'lupita@gmail.com',
        ];
    }
}
```

有时候，你需要为某个类的对象创建多个工厂。如果我们需要添加一些非常重要的联系人（VIP），我们可以使用`state()`方法为此定义第二个工厂状态，正如在[示例 5-14](https://example.org/EX804)中所见。`state()`方法接收一个属性数组，你可以为这个状态专门设置任何属性。

##### 示例 5-14\. 为同一模型定义多个工厂状态

```php
class ContactFactory extends Factory
{
    protected $model = Contact::class;

    public function definition(): array
    {
        return [
            'name' => 'Lupita Smith',
            'email' => 'lupita@gmail.com',
        ];
    }

    public function vip()
    {
        return $this->state(function (array $attributes) {
            return [
                'vip' => true,
                // Uses the "company_id" property from the $attributes
                'company_size' => function () use ($attributes) {
                    return Company::find($attributes['company_id'])->size;
                },
            ];
        });
    }
}
```

现在，让我们创建一个特定状态的实例：

```php
$vip = Contact::factory()->vip()->create();

$vips = Contact::factory()->count(3)->vip()->create();
```

#### 在复杂工厂设置中使用相同模型作为关系

有时候你有一个工厂通过它们的工厂创建相关项，其中两个或更多有相同的关系。也许通过你的工厂生成`Trip`会自动创建`Reservation`和`Receipt`，并且所有三者都应该附属于同一个`User`。当你去创建`Trip`时，除非告诉它们要做其他事情，否则工厂将每个手动创建它们自己的用户。

使用`recycle()`方法，你可以指示每个调用链中的工厂使用给定对象的同一个实例。正如你在[示例 5-15](https://example.org/EX5i)中看到的，这为确保整个工厂链中使用同一模型提供了简单的语法。

##### 示例 5-15\. 使用`recycle()`在工厂链中的每个关系中使用相同的实例

```php
$user = User::factory()->create();

$trip = Trip::factory()
    ->recycle($user)
    ->create();
```

哇，这真是太多了。如果你觉得理解起来有些困难，不要担心——最后一部分确实是更高级的内容。让我们回到基础，谈谈 Laravel 数据库工具的核心：查询构建器。

# 查询构建器

现在你已经连接并且迁移和填充了你的表，让我们开始学习如何使用数据库工具。在每一个 Laravel 数据库功能的核心是*查询构建器*，它是一个流畅的接口，用于与多种不同类型的数据库进行交互，具有清晰的单一 API。

Laravel 的数据库架构可以通过单一接口连接到 MySQL、PostgreSQL、SQLite 和 SQL Server，只需改变几个配置设置。

如果你曾经使用过 PHP 框架，你可能使用过一个工具，允许你运行带有基本转义的“原始”SQL 查询，以确保安全性。查询构建器就是这样一个工具，在其上还有许多便利层和帮助程序。因此，让我们从一些简单的调用开始。

## `DB`门面的基本用法

在我们开始使用流畅方法链构建复杂查询之前，让我们看一些查询构建器命令的示例。`DB`门面用于查询构建器链式调用和更简单的原始查询，如[示例 5-16](https://example.org/EX806)所示。

##### 示例 5-16\. 示例原始 SQL 和查询构建器的使用

```php
// Basic statement
DB::statement('drop table users');

// Raw select, and parameter binding
DB::select('select * from contacts where validated = ?', [true]);

// Select using the fluent builder
$users = DB::table('users')->get();

// Joins and other complex calls
DB::table('users')
    ->join('contacts', function ($join) {
        $join->on('users.id', '=', 'contacts.user_id')
             ->where('contacts.type', 'donor');
    })
    ->get();
```

## 原始 SQL

正如你在[示例 5-16](https://example.org/EX806)中看到的，可以使用`DB`门面和`statement()`方法对数据库进行任何原始调用：`DB::statement('*SQL statement here*')`。

但也有特定于各种常见操作的方法：`select()`、`insert()`、`update()`和`delete()`。这些仍然是原始调用，但有所不同。首先，使用`update()`和`delete()`将返回受影响的行数，而`statement()`则不会；其次，使用这些方法可以让未来的开发人员更清楚地了解您正在进行的语句类型。

### 原始选择

具体的`DB`方法中最简单的是`select()`。您可以在不添加任何额外参数的情况下运行它：

```php
$users = DB::select('select * from users');
```

这将返回一个`stdClass`对象的数组。

### 参数绑定和命名绑定

Laravel 的数据库架构允许使用 PDO（PHP 数据对象，PHP 的本地数据库访问层）参数绑定，这可以保护您的查询免受潜在的 SQL 攻击。将参数传递给语句就像在语句中用`?`替换值，然后将值添加到调用的第二个参数中：

```php
$usersOfType = DB::select(
    'select * from users where type = ?',
    [$type]
);
```

您还可以为了清晰起见为这些参数命名：

```php
$usersOfType = DB::select(
    'select * from users where type = :type',
    ['type' => $userType]
);
```

### 原始插入

从这里开始，原始命令基本上都是相同的。原始插入如下所示：

```php
DB::insert(
    'insert into contacts (name, email) values (?, ?)',
    ['sally', 'sally@me.com']
);
```

### 原始更新

更新操作如下：

```php
$countUpdated = DB::update(
    'update contacts set status = ? where id = ?',
    ['donor', $id]
);
```

### 原始删除

删除操作如下所示：

```php
$countDeleted = DB::delete(
    'delete from contacts where archived = ?',
    [true]
);
```

## 使用查询构建器进行链式调用

到目前为止，我们实际上还没有使用查询构建器。我们只是在`DB`门面上使用了简单的方法调用。让我们实际构建一些查询。

查询构建器使得可以将方法链接在一起，以构建查询。在链的末尾，您将使用某些方法——很可能是`get()`——触发刚刚构建的查询的实际执行。

让我们来看一个快速的示例：

```php
$usersOfType = DB::table('users')
    ->where('type', $type)
    ->get();
```

在这里，我们构建了我们的查询——`users`表，`$type`类型——然后执行了查询并得到了结果。请注意，与之前的调用不同，这将返回*collection*类型的`stdClass`对象，而不是数组。

让我们看看查询构建器允许您链式调用哪些方法。这些方法可以分为我称之为约束方法、修改方法、条件方法和结束/返回方法。

### 约束方法

这些方法以当前查询为基础，将其约束为返回可能数据的更小子集：

`select()`

允许您选择要选择的列：

```php
$emails = DB::table('contacts')
    ->select('email', 'email2 as second_email')
    ->get();
// Or
$emails = DB::table('contacts')
    ->select('email')
    ->addSelect('email2 as second_email')
    ->get();
```

`where()`

使用`WHERE`可以限制返回内容的范围。默认情况下，`where()`方法的签名需要三个参数——列名、比较运算符和数值：

```php
$newContacts = DB::table('contact')
    ->where('created_at', '>', now()->subDay())
    ->get();
```

然而，如果您的比较是`=`, 这是最常见的比较，您可以省略第二个操作符：

```php
$vipContacts = DB::table('contacts')->where('vip',true)->get();
```

如果要组合`where()`语句，可以将它们依次链接在一起，或者传递一个数组的数组：

```php
$newVips = DB::table('contacts')
    ->where('vip', true)
    ->where('created_at', '>', now()->subDay());
// Or
$newVips = DB::table('contacts')->where([
    ['vip', true],
    ['created_at', '>', now()->subDay()],
]);
```

`orWhere()`

创建简单的`OR WHERE`语句：

```php
$priorityContacts = DB::table('contacts')
    ->where('vip', true)
    ->orWhere('created_at', '>', now()->subDay())
    ->get();
```

要创建具有多个条件的更复杂的`OR WHERE`语句，请将`orWhere()`传递给闭包：

```php
$contacts = DB::table('contacts')
    ->where('vip', true)
    ->orWhere(function ($query) {
        $query->where('created_at', '>', now()->subDay())
            ->where('trial', false);
    })
    ->get();
```

`whereBetween(*colName*, [*low*, *high*])`

允许您将查询范围限定为只返回某一列在两个值之间的行（包括两个值）：

```php
$mediumDrinks = DB::table('drinks')
    ->whereBetween('size', [6, 12])
    ->get();
```

与 `whereNotBetween()` 同样有效，但会选择其反向操作。

`whereIn(*colName*, [*1*, *2*, *3*])`

允许你将查询范围限定为仅返回列值在显式提供的选项列表中的行：

```php
$closeBy = DB::table('contacts')
    ->whereIn('state', ['FL', 'GA', 'AL'])
    ->get();
```

与 `whereNotIn()` 同样有效，但会选择其反向操作。

`whereNull(*colName*)`, `whereNotNull(*colName*)`

允许你仅选择给定列为 `NULL` 或 `NOT NULL` 的行。

`whereRaw()`

允许你传递一个原始、未转义的字符串，添加到 `WHERE` 语句之后：

```php
$goofs = DB::table('contacts')->whereRaw('id = 12345')->get();
```

# 警惕 SQL 注入！

传递给 `whereRaw()` 的任何 SQL 查询都不会被转义。请谨慎使用这个方法，并且使用频率不高；这是你的应用中 SQL 注入攻击的一个主要机会。

`whereExists()`

允许你仅选择那些在提供的子查询中至少返回一行的行。想象一下，你只想获取那些至少留下一条评论的用户：

```php
$commenters = DB::table('users')
    ->whereExists(function ($query) {
        $query->select('id')
            ->from('comments')
            ->whereRaw('comments.user_id = users.id');
    })
    ->get();
```

`distinct()`

仅选择与返回数据中其他行相比具有唯一选择数据的行。通常与 `select()` 配对使用，因为如果使用主键，就不会有重复行：

```php
$lastNames = DB::table('contacts')->select('city')->distinct()->get();
```

### 修改方法

这些方法改变了查询结果输出的方式，而不仅仅限制其结果：

`orderBy(*colName*, *direction*)`

对结果进行排序。第二个参数可以是 `asc`（默认，升序）或 `desc`（降序）：

```php
$contacts = DB::table('contacts')
    ->orderBy('last_name', 'asc')
    ->get();
```

`groupBy()`, `having()`, `havingRaw()`

将你的结果按列分组。可选地，`having()` 和 `havingRaw()` 允许你根据组的属性过滤结果。例如，你可以只查找至少有 30 个人口的城市：

```php
$populousCities = DB::table('contacts')
    ->groupBy('city')
    ->havingRaw('count(contact_id) > 30')
    ->get();
```

`skip()`, `take()`

最常用于分页，允许你定义要返回的行数以及在开始返回之前要跳过的行数 —— 就像分页系统中的页码和页面大小：

```php
// returns rows 31-40
$page4 = DB::table('contacts')->skip(30)->take(10)->get();
```

`latest(*colName*)`, `oldest(*colName*)`

按传递的列排序（如果没有传递列名，则按 `created_at`），支持降序（`latest()`）或升序（`oldest()`）：

`inRandomOrder()`

随机排序结果。

### 条件方法

有两种方法允许你根据传入值的布尔状态有条件地应用它们的“内容”（你传递给它们的闭包）：

`when()`

给定一个真值的第一个参数，应用包含在闭包中的查询修改；给定一个假值的第一个参数，它什么也不做。注意，第一个参数可以是布尔值（例如，设置为 `true` 或 `false` 的 `$ignoreDrafts`）、可选值（从用户输入中提取的 `$status`，默认为 `null`）或返回布尔值的闭包；重要的是它评估为真或假。例如：

```php
$status = request('status'); // Defaults to null if not set

$posts = DB::table('posts')
    ->when($status, function ($query) use ($status) {
        return $query->where('status', $status);
    })
    ->get();

// Or
$posts = DB::table('posts')
    ->when($ignoreDrafts, function ($query) {
        return $query->where('draft', false);
    })
    ->get();
```

你还可以传递第三个参数，另一个闭包，只有在第一个参数为假时才会应用。

`unless()`

`when()` 的确切反向操作。如果第一个参数为假，它将运行第二个闭包。

### 结束/返回方法

这些方法停止查询链并触发 SQL 查询的执行。如果在查询链的末尾没有这些方法之一，则返回值始终只是查询构建器的实例；将其中一个链到查询构建器上，你将获得一个实际的结果：

`get()`

获取构建查询的所有结果：

```php
$contacts = DB::table('contacts')->get();
$vipContacts = DB::table('contacts')->where('vip', true)->get();
```

`first()`，`firstOrFail()`

仅获取第一个结果——类似于 `get()`，但添加了 `LIMIT 1`：

```php
$newestContact = DB::table('contacts')
    ->orderBy('created_at', 'desc')
    ->first();
```

如果没有结果，`first()` 会静默失败，而 `firstOrFail()` 将抛出异常。

如果你向任一方法传递一个列名数组，它将返回仅这些列的数据，而不是所有列。

`find(*id*)`，`findOrFail(*id*)`

类似于 `first()`，但你传递一个与主键对应的 ID 值来查找。如果不存在具有该 ID 的行，则 `find()` 会静默失败，而 `findOrFail()` 将抛出异常：

```php
$contactFive = DB::table('contacts')->find(5);
```

`value()`

从第一行中的单个字段中提取值。类似于 `first()`，但如果你只想要单个列：

```php
$newestContactEmail = DB::table('contacts')
    ->orderBy('created_at', 'desc')
    ->value('email');
```

`count()`

返回所有匹配结果的整数计数：

```php
$countVips = DB::table('contacts')
    ->where('vip', true)
    ->count();
```

`min()`，`max()`

返回特定列中的最小或最大值：

```php
$highestCost = DB::table('orders')->max('amount');
```

`sum()`，`avg()`

返回特定列中所有值的总和或平均值：

```php
$averageCost = DB::table('orders')
    ->where('status', 'completed')
    ->avg('amount');
```

`dd()`，`dump()`

显示底层的 SQL 查询和绑定，并且如果使用 `dd()`，则结束脚本。

```php
DB::table('users')->where('name', 'Wilbur Powery')->dd();

// "select * from `users` where `name` = ?"
// array:1 [ 0 => "Wilbur Powery"]
```

# 解释方法

`explain()` 方法返回 SQL 将如何执行查询的解释。你可以将其与 `dd()` 或 `dump()` 方法一起使用来调试查询：

```php
User::where('name', 'Wilbur Powery')->explain()->dd();

/*
array:1 [
 0 => {#5111
 +"id": 1
 +"select_type": "SIMPLE"
 +"table": "users"
 +"type": "ALL"
 +"possible_keys": null
 +"key": null
 +"key_len": null
 +"ref": null
 +"rows": "209"
 +"Extra": "Using where"
 }
]
*/
```

### 使用 DB::raw 在查询构建器方法中编写原始查询

你已经看到了一些用于原始语句的自定义方法，例如 `select()` 有一个 `selectRaw()` 对应方法，允许你传递一个字符串给查询构建器，放置在 `WHERE` 语句之后。

你也可以将 `DB::raw()` 调用的结果传递给查询构建器中的几乎任何方法来实现相同的结果：

```php
$contacts = DB::table('contacts')
    ->select(DB::raw('*, (score * 100) AS integer_score'))
    ->get();
```

### 连接

连接有时可能很难定义，框架可以尽力简化它们，但查询构建器会尽最大努力。让我们看一个示例：

```php
$users = DB::table('users')
    ->join('contacts', 'users.id', '=', 'contacts.user_id')
    ->select('users.*', 'contacts.name', 'contacts.status')
    ->get();
```

`join()` 方法创建内连接。你还可以依次链接多个连接，或使用 `leftJoin()` 获取左连接。

最后，通过将闭包传递给 `join()` 方法，可以创建更复杂的连接：

```php
DB::table('users')
    ->join('contacts', function ($join) {
        $join
            ->on('users.id', '=', 'contacts.user_id')
            ->orOn('users.id', '=', 'contacts.proxy_user_id');
    })
    ->get();
```

### 联合

你可以通过首先创建它们，然后使用 `union()` 或 `unionAll()` 方法将两个查询联合起来（将它们的结果合并为一个结果集）：

```php
$first = DB::table('contacts')
    ->whereNull('first_name');

$contacts = DB::table('contacts')
    ->whereNull('last_name')
    ->union($first)
    ->get();
```

### 插入

`insert()` 方法非常简单。将其作为数组传递以插入单行，或者作为数组的数组插入多行，并使用 `insertGetId()` 而不是 `insert()` 来获取自增的主键 ID 作为返回：

```php
$id = DB::table('contacts')->insertGetId([
    'name' => 'Abe Thomas',
    'email' => 'athomas1987@gmail.com',
]);

DB::table('contacts')->insert([
    ['name' => 'Tamika Johnson', 'email' => 'tamikaj@gmail.com'],
    ['name' => 'Jim Patterson', 'email' => 'james.patterson@hotmail.com'],
]);
```

### 更新

更新也很简单。创建更新查询，而不是使用 `get()` 或 `first()`，只需使用 `update()` 并传递参数数组：

```php
DB::table('contacts')
    ->where('points', '>', 100)
    ->update(['status' => 'vip']);
```

你还可以使用 `increment()` 和 `decrement()` 方法快速增加和减少列。每个方法的第一个参数是列名，第二个（可选）参数是增加/减少的数字：

```php
DB::table('contacts')->increment('tokens', 5);
DB::table('contacts')->decrement('tokens');
```

### 删除操作

删除操作更加简单。构建你的查询，然后以 `delete()` 结束：

```php
DB::table('users')
    ->where('last_login', '<', now()->subYear())
    ->delete();
```

你还可以截断表，这会删除每一行，并且重置自增 ID：

```php
DB::table('contacts')->truncate();
```

### JSON 操作

如果你有 JSON 列，可以使用箭头语法遍历子元素的方面来更新或选择行：

```php
// Select all records where the "isAdmin" property of the "options"
// JSON column is set to true
DB::table('users')->where('options->isAdmin', true)->get();

// Update all records, setting the "verified" property
// of the "options" JSON column to true
DB::table('users')->update(['options->isVerified', true]);
```

## 事务

*数据库事务* 是一种工具，允许你将一系列数据库查询封装成批处理，你可以选择回滚，撤销整个查询系列。事务通常用于确保一系列相关查询的*全部*或*无*操作完成——如果其中一个失败，ORM 将回滚整个查询系列。

使用 Laravel 查询构建器的事务功能，如果事务闭包内的任何异常被抛出，事务中的所有查询将会回滚。如果事务闭包成功完成，所有查询将会提交而不会回滚。

让我们看一下 示例 5-17 中的样本事务。

##### 示例 5-17\. 一个简单的数据库事务

```php
DB::transaction(function () use ($userId, $numVotes) {
    // Possibly failing DB query
    DB::table('users')
        ->where('id', $userId)
        ->update(['votes' => $numVotes]);

    // Caching query that we don't want to run if the above query fails
    DB::table('votes')
        ->where('user_id', $userId)
        ->delete();
});
```

在这个例子中，我们可以假设之前的某个过程总结了 `votes` 表中给定用户的投票数。我们想把这个数字缓存到 `users` 表中，然后从 `votes` 表中清除这些投票。但是，当然，在成功运行到 `users` 表的更新之前，我们不想清除投票。如果 `votes` 表的删除操作失败，我们也不想在 `users` 表中保留更新后的投票数。

如果任何一个查询出现问题，另一个都不会被应用。这就是数据库事务的魔力。

注意，你也可以手动开始和结束事务——这对于查询构建器和 Eloquent 查询都适用。以 `DB::beginTransaction()` 开始，以 `DB::commit()` 结束，以 `DB::rollBack()` 中止：

```php
DB::beginTransaction();

// Take database actions

if ($badThingsHappened) {
    DB::rollBack();
}

// Take other database actions

DB::commit();
```

# Eloquent 简介

现在我们已经介绍了查询构建器，让我们来谈谈 Eloquent，Laravel 的旗舰数据库工具，它是建立在查询构建器基础上的。

Eloquent 是一个 *ActiveRecord ORM*，这意味着它是一个数据库抽象层，提供了与多种数据库类型交互的单一接口。“ActiveRecord”意味着单个 Eloquent 类不仅负责提供与整个表交互的能力（例如 `User::all()` 获取所有用户），还代表一个单独的表行（例如 `$sharon = new User`）。此外，每个实例都能够管理自己的持久性；你可以调用 `$sharon->save()` 或 `$sharon->delete()`。

Eloquent 主要关注简单性，并且像框架的其余部分一样，它依赖于“约定优于配置”，以允许您用最少的代码构建强大的模型。

例如，你可以使用在示例 5-18 中定义的模型执行示例 5-19 中的所有操作。

##### 示例 5-18。最简单的 Eloquent 模型

```php
<?php

use Illuminate\Database\Eloquent\Model;

class Contact extends Model {}
```

##### 示例 5-19。使用最简单的 Eloquent 模型可以实现的操作

```php
// In a controller
public function save(Request $request)
{
    // Create and save a new contact from user input
    $contact = new Contact();
    $contact->first_name = $request->input('first_name');
    $contact->last_name = $request->input('last_name');
    $contact->email = $request->input('email');
    $contact->save();

    return redirect('contacts');
}

public function show($contactId)
{
    // Return a JSON representation of a contact based on a URL segment;
    // if the contact doesn't exist, throw an exception
    return Contact::findOrFail($contactId);
}

public function vips()
{
    // Unnecessarily complex example, but still possible with basic Eloquent
    // class; adds a "formalName" property to every VIP entry
    return Contact::where('vip', true)->get()->map(function ($contact) {
        $contact->formalName = "The exalted {$contact->first_name} of the
         {$contact->last_name}s";

        return $contact;
    });
}
```

怎么做？约定。Eloquent 假设表名（`Contact`变成`contacts`），然后你就有了一个完全功能的 Eloquent 模型。

让我们介绍如何使用 Eloquent 模型。

## 创建和定义 Eloquent 模型

首先，让我们创建一个模型。有一个 Artisan 命令可以做到这一点：

```php
php artisan make:model Contact
```

这是我们将得到的，在*app/Models/Contact.php*中：

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    //
}
```

# 创建迁移及其模型

如果你想在创建模型时自动创建一个迁移，请传递`-m`或`--migration`标志：

```php
php artisan make:model Contact --migration
```

### 表名

表名的默认行为是，Laravel 会将你的类名“蛇形命名”并将其复数化，因此`SecondaryContact`将访问名为`secondary_contacts`的表。如果你想自定义名称，请在模型上显式设置`$table`属性：

```php
    protected $table = 'contacts_secondary';
```

### 主键

默认情况下，Laravel 假定每个表都将具有自动递增的整数主键，并将其命名为`id`。

如果你想更改主键的名称，请更改`$primaryKey`属性：

```php
    protected $primaryKey = 'contact_id';
```

如果你想将其设置为非自增，使用：

```php
    public $incrementing = false;
```

### 时间戳

Eloquent 期望每个表都有`created_at`和`updated_at`时间戳列。如果你的表不需要它们，可以禁用`$timestamps`功能：

```php
    public $timestamps = false;
```

您可以通过将`$dateFormat`类属性设置为自定义字符串来自定义 Eloquent 用于将时间戳存储到数据库中的格式。该字符串将使用 PHP 的`date()`语法进行解析，因此以下示例将日期存储为自 Unix 纪元以来的秒数：

```php
    protected $dateFormat = 'U';
```

## 使用 Eloquent 检索数据

当你使用 Eloquent 从数据库中提取数据时，大多数时候会在你的 Eloquent 模型上使用静态调用。

让我们从获取所有内容开始：

```php
$allContacts = Contact::all();
```

那很容易。让我们稍微过滤一下：

```php
$vipContacts = Contact::where('vip', true)->get();
```

我们可以看到，`Eloquent`Facade 使我们能够链式约束，从而使约束变得非常熟悉：

```php
$newestContacts = Contact::orderBy('created_at', 'desc')
    ->take(10)
    ->get();
```

结果表明，一旦你超越了最初的 Facade 名称，你只是在使用 Laravel 的查询构建器。你可以做更多的事情——我们很快会涵盖这一点——但是你可以在`DB`Facade 上使用查询构建器的一切操作，同样也可以在你的 Eloquent 对象上使用。

### 获取一个

正如我们在本章早些时候介绍的，你可以使用 `first()` 只返回查询的第一条记录，或者使用 `find()` 仅检索提供的 ID 对应的记录。对于任何一个方法，如果在方法名称后添加“OrFail”，则如果没有匹配结果，它将抛出异常。这使得 `findOrFail()` 成为通过 URL 段查找实体（或者如果找不到匹配的实体则抛出异常）的常用工具，正如你可以在 示例 5-20 中看到的。

##### 示例 5-20\. 在控制器方法中使用 Eloquent 的 `OrFail()` 方法

```php
// ContactController
public function show($contactId)
{
    return view('contacts.show')
        ->with('contact', Contact::findOrFail($contactId));
}
```

任何意图返回单个记录的方法（`first()`、`firstOrFail()`、`find()` 或 `findOrFail()`）将返回 Eloquent 类的实例。因此，`Contact::first()` 将返回填充了表中第一行数据的 `Contact` 类的实例。

你还可以使用 `firstWhere()` 方法，它是 `where()` 和 `first()` 结合的快捷方式：

```php
// With where() and first()
Contact::where('name', 'Wilbur Powery')->first();

// With firstWhere()
Contact::firstWhere('name', 'Wilbur Powery');
```

# 异常

正如你可以在 示例 5-20 中看到的，我们不需要在我们的控制器中捕获 Eloquent 的模型未找到异常（`Illuminate\Database\Eloquent\ModelNotFoundException`）；Laravel 的路由系统将捕获它并为我们抛出 404。

当然，你可以捕获特定的异常并处理它，如果你愿意的话。

### 获取多个

`get()` 在 Eloquent 中的工作方式与普通查询构建器调用中的工作方式相同——构建一个查询，并在末尾调用 `get()` 来获取结果：

```php
$vipContacts = Contact::where('vip', true)->get();
```

然而，还有一个仅限于 Eloquent 的方法 `all()`，当人们希望获取表中所有数据的未过滤列表时，经常会看到它：

```php
$contacts = Contact::all();
```

# 使用 `get()` 替代 `all()`

任何时候你可以使用 `all()`，你也可以使用 `get()`。`Contact::get()` 的响应与 `Contact::all()` 相同。然而，一旦你开始修改你的查询——例如添加一个 `where()` 过滤器，`all()` 将不再工作，但 `get()` 仍将继续工作。

所以，尽管 `all()` 非常常见，我建议一切都使用 `get()`，并忽略 `all()` 的存在。

### 使用 `chunk()` 分块响应

如果你曾经需要一次处理大量（数千或更多）的记录，可能会遇到内存或锁定问题。Laravel 允许你将请求分成较小的片段（块）并批量处理它们，从而减少大请求的内存负载。示例 5-21 演示了如何使用 `chunk()` 将查询分成每个包含 100 条记录的“块”。

##### 示例 5-21\. 将 Eloquent 查询分块以限制内存使用

```php
Contact::chunk(100, function ($contacts) {
    foreach ($contacts as $contact)  {
        // Do something with $contact
    }
});
```

### 聚合

查询构建器上可用的聚合函数也可以在 Eloquent 查询上使用。例如：

```php
$countVips = Contact::where('vip', true)->count();
$sumVotes = Contact::sum('votes');
$averageSkill = User::avg('skill_level');
```

## 使用 Eloquent 进行插入和更新

插入和更新值是 Eloquent 开始与普通查询构建器语法分离的地方之一。

### 插入

使用 Eloquent 插入新记录有两种主要方式。

首先，你可以创建你的 Eloquent 类的新实例，手动设置属性，然后在该实例上调用`save()`，就像在示例 5-22 中一样。

##### 示例 5-22\. 通过创建新实例插入 Eloquent 记录

```php
$contact = new Contact;
$contact->name = 'Ken Hirata';
$contact->email = 'ken@hirata.com';
$contact->save();

// or

$contact = new Contact([
    'name' => 'Ken Hirata',
    'email' => 'ken@hirata.com',
]);
$contact->save();

// or

$contact = Contact::make([
    'name' => 'Ken Hirata',
    'email' => 'ken@hirata.com',
]);
$contact->save();
```

直到你调用`save()`之前，这个`Contact`实例完全代表了这个联系人，但它尚未保存到数据库中。这意味着它没有`id`，如果应用程序退出，它将不会持久存在，并且它的`created_at`和`updated_at`值也没有设置。

你也可以像在示例 5-23 中展示的那样，将数组传递给`Model::create()`。与`make()`不同，`create()`在调用时会将实例保存到数据库中。

##### 示例 5-23\. 通过将数组传递给`create()`插入 Eloquent 记录

```php
$contact = Contact::create([
    'name' => 'Keahi Hale',
    'email' => 'halek481@yahoo.com',
]);
```

请注意，在任何传递数组的上下文中（例如`new Model()`，`Model::make()`，`Model::create()`或`Model::update()`），你通过`Model::create()`设置的每个属性都必须经过“批量赋值”批准，我们稍后会介绍。在示例 5-22 中的第一个示例中，这是不必要的，因为你可以单独分配每个属性。

注意，如果你使用`Model::create()`，你不需要调用`save()`方法—​这是作为模型的`create()`方法的一部分处理的。

### 更新

更新记录看起来与插入非常相似。你可以获取一个特定实例，更改其属性，然后保存，或者你可以进行一次调用并传递一组更新后的属性。示例 5-24 说明了第一种方法。

##### 示例 5-24\. 通过更新实例并保存来更新 Eloquent 记录

```php
$contact = Contact::find(1);
$contact->email = 'natalie@parkfamily.com';
$contact->save();
```

由于此记录已经存在，它已经具有`created_at`时间戳和`id`，这些将保持不变，但`updated_at`字段将更改为当前日期和时间。示例 5-25 说明了第二种方法。

##### 示例 5-25\. 通过将数组传递给`update()`方法更新一个或多个 Eloquent 记录

```php
Contact::where('created_at', '<', now()->subYear())
    ->update(['longevity' => 'ancient']);

// or

$contact = Contact::find(1);
$contact->update(['longevity' => 'ancient']);
```

此方法期望一个数组，其中每个键是列名，每个值是列值。

### 批量赋值

我们已经看过几个例子，演示了如何将值数组传递给 Eloquent 类方法。然而，在你定义模型上哪些字段是“fillable”之前，这些例子都不会真正起作用。

这样做的目的是保护你免受（可能是恶意的）用户输入意外设置你不想更改的字段的新值。考虑在示例 5-26 中的常见场景。

##### 示例 5-26\. 使用请求输入的全部内容更新 Eloquent 模型

```php
// ContactController
public function update(Contact $contact, Request $request)
{
    $contact->update($request->all());
}
```

Illuminate 的`Request`对象在示例 5-26 中将用户输入的每一部分传递给`update()`方法。该`all()`方法包括诸如 URL 参数和表单输入之类的内容，因此恶意用户可以轻松添加一些东西，例如`id`和`owner_id`，这些你可能不希望更新。

幸运的是，在定义模型的可填充字段之前，这实际上不起作用。您可以定义允许的“可填充字段或不允许的 *受保护* 字段，以确定哪些字段可以或不可以通过批量 *赋值*（即通过将值数组传递到 `create()` 或 `update()`）进行编辑。请注意，非可填充属性仍然可以通过直接赋值进行更改（例如，`$contact->password = '*abc*';`）。示例 5-27 展示了两种方法。

##### 示例 5-27\. 使用 Eloquent 的可填充或受保护属性来定义可批量赋值的字段

```php
class Contact extends Model
{
    protected $fillable = ['name', 'email'];

    // or

    protected $guarded = ['id', 'created_at', 'updated_at', 'owner_id'];
}
```

# 使用 Request::only() 与 Eloquent 批量赋值

在 示例 5-26 中，我们需要使用 Eloquent 的批量赋值保护，因为我们正在使用 `Request` 对象上的 `all()` 方法来传递用户输入的 *全部内容*。

Eloquent 的批量赋值保护在这里是一个很好的工具，但还有一个有用的技巧可以防止您接受用户输入的任何旧数据。

`Request` 类具有 `only()` 方法，允许您从用户输入中仅提取几个键。现在您可以这样做：

```php
Contact::create($request->only('name', 'email'));
```

### firstOrCreate() 和 firstOrNew()

有时您希望告诉应用程序，“使用这些属性获取一个实例，如果不存在，则创建它。”这就是 `firstOr*()` 方法发挥作用的地方。

`firstOrCreate()` 和 `firstOrNew()` 方法将一个键值对数组作为它们的第一个参数：

```php
$contact = Contact::firstOrCreate(['email' => 'luis.ramos@myacme.com']);
```

它们都会查找并检索与这些参数匹配的第一条记录，如果没有匹配的记录，则会创建一个具有这些属性的实例；`firstOrCreate()` 将将该实例持久化到数据库中然后返回它，而 `firstOrNew()` 则会返回它而不保存它。

如果您将值数组作为第二个参数传递，这些值将被添加到创建的条目中（如果已创建），但 *不* 用于查找条目是否存在。

## 使用 Eloquent 进行删除

使用 Eloquent 进行删除与使用 Eloquent 进行更新非常相似，但是通过（可选的）软删除，您可以将已删除的项目归档以供以后检查甚至恢复。

### 普通删除

删除模型记录的最简单方法是在实例本身上调用 `delete()` 方法：

```php
$contact = Contact::find(5);
$contact->delete();
```

但是，如果您只有 ID，则没有理由查找实例只是为了删除它；您可以将 ID 或 ID 数组传递给模型的 `destroy()` 方法，直接删除它们：

```php
Contact::destroy(1);
// or
Contact::destroy([1, 5, 7]);
```

最后，您可以删除查询的所有结果：

```php
Contact::where('updated_at', '<', now()->subYear())->delete();
```

### 软删除

*软删除* 将数据库行标记为已删除，但实际上并未从数据库中删除它们。这使您可以以后检查它们，以便在显示历史信息时显示比“无信息，已删除”更多的记录，并允许用户（或管理员）恢复一些或所有数据。

手动编写启用软删除的应用程序的难点在于，*每个查询*你所写的都需要排除软删除的数据。幸运的是，如果你使用 Eloquent 的软删除，除非显式要求将它们重新引入，否则你所做的每个查询都会默认排除软删除的数据。

Eloquent 的软删除功能要求在表中添加`deleted_at`列。一旦在 Eloquent 模型上启用了软删除，除非显式包含软删除的记录，否则你编写的每个查询都将被作用域限制以忽略软删除的行。

#### 启用软删除

要启用软删除，需要做两件事情：在迁移中添加`deleted_at`列，并在模型中导入`SoftDeletes`特性。在模式生成器上有一个`softDeletes()`方法可以用来向表中添加`deleted_at`列，就像你在示例 5-28 中看到的那样。示例 5-29 展示了启用软删除的 Eloquent 模型。

##### 示例 5-28\. 添加软删除列到表的迁移

```php
Schema::table('contacts', function (Blueprint $table) {
    $table->softDeletes();
});
```

##### 示例 5-29\. 启用软删除的 Eloquent 模型

```php
<?php

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Contact extends Model
{
    use SoftDeletes; // use the trait
}
```

一旦你进行这些更改，每次调用`delete()`和`destroy()`将会将你的行的`deleted_at`列设置为当前日期和时间，而不是删除该行。随后的所有查询将会因此排除该行。

#### 使用软删除进行查询

那么，我们如何获取软删除的条目呢？

首先，你可以将软删除的条目添加到查询中：

```php
$allHistoricContacts = Contact::withTrashed()->get();
```

接下来，你可以使用`trashed()`方法来查看特定实例是否已被软删除：

```php
if ($contact->trashed()) {
    // do something
}
```

最后，你可以*仅仅*获取软删除的条目：

```php
$deletedContacts = Contact::onlyTrashed()->get();
```

#### 恢复软删除的实体

如果你想要恢复一个软删除的条目，可以在实例或查询上运行`restore()`：

```php
$contact->restore();

// or

Contact::onlyTrashed()->where('vip', true)->restore();
```

#### 强制删除软删除的实体

你可以通过在实体或查询上调用`forceDelete()`来删除软删除的实体：

```php
$contact->forceDelete();

// or

Contact::onlyTrashed()->forceDelete();
```

## 作用域

我们已经涵盖了“过滤”查询，意味着任何不仅仅返回表中每个结果的查询。但到目前为止，在本章中编写它们时，使用的都是手动的查询构建器过程。

Eloquent 中的局部和全局作用域允许你定义预定义的*作用域*（过滤器），你可以在每次查询模型时使用（全局），或者在使用特定方法链查询时使用（局部）。

### 局部作用域

局部作用域是最容易理解的。让我们来看这个例子：

```php
$activeVips = Contact::where('vip', true)->where('trial', false)->get();
```

首先，如果我们一遍又一遍地编写这些查询方法的组合，将会变得很烦琐。而且，定义“活跃 VIP”的知识现在散布在我们的整个应用程序中。我们希望将这些知识集中化。如果我们可以只写这样一段代码呢？

```php
$activeVips = Contact::activeVips()->get();
```

我们可以这样做——称之为局部作用域。并且在`Contact`类上定义起来很容易，就像你在示例 5-30 中看到的那样。

##### 示例 5-30\. 在模型上定义局部作用域

```php
class Contact extends Model
{
    public function scopeActiveVips($query)
    {
        return $query->where('vip', true)->where('trial', false);
    }
```

要定义一个本地作用域，我们在 Eloquent 类中添加一个以“scope”开头并包含作用域名称的 Pascal case 版本的方法。该方法传递一个查询构建器，并需要返回一个查询构建器，当然你可以在返回之前修改查询——这就是整个意义所在。

你也可以定义接受参数的作用域，示例见示例 5-31。

##### 示例 5-31\. 向作用域传递参数

```php
class Contact extends Model
{
    public function scopeStatus($query, $status)
    {
        return $query->where('status', $status);
    }
```

你可以以相同的方式使用它们，只需将参数传递给作用域即可：

```php
$friends = Contact::status('friend')->get();
```

你也可以在两个本地作用域之间链式使用`orWhere()`。

```php
$activeOrVips = Contact::active()->orWhere()->vip()->get();
```

### 全局作用域

记住我们之前谈到的软删除只在将每个模型上的每个查询范围限制为忽略软删除项时才起作用？这就是一个全局作用域。我们可以定义自己的全局作用域，将其应用于来自给定模型的每个查询。

定义全局作用域有两种方法：使用闭包或使用整个类。在每种情况下，你都将在模型的`booted()`方法中注册定义的作用域。让我们从闭包方法开始，示例见示例 5-32。

##### 示例 5-32\. 使用闭包添加全局作用域

```php
...
class Contact extends Model
{
    protected static function booted()
    {
        static::addGlobalScope('active', function (Builder $builder) {
            $builder->where('active', true);
        });
    }
```

就这样。我们刚刚添加了一个名为`active`的全局作用域，现在这个模型上的每个查询都将仅限于`active`设置为`true`的行。

接下来，让我们尝试更长的方法，如示例 5-33 所示。运行以下命令来创建一个名为 ActiveScope 的类。

```php
php artisan make:scope ActiveScope
```

它将具有一个`apply()`方法，该方法接受一个查询构建器的实例和模型的实例。

##### 示例 5-33\. 创建一个全局作用域类

```php
<?php

namespace App\Models\Scopes;

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;

class ActiveScope implements Scope
{
    public function apply(Builder $builder, Model $model): void
    {
        $builder->where('active', true);
    }
}
```

要将此作用域应用于模型，再次重写父类的`booted()`方法，并在类上使用`static`调用`addGlobalScope()`，示例见示例 5-34。

##### 示例 5-34\. 应用基于类的全局作用域

```php
<?php

use App\Models\Scopes;
use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    protected static function booted()
    {
        static::addGlobalScope(new ActiveScope);
    }
}
```

# 没有命名空间的联系人

你可能已经注意到，几个示例中都使用了没有命名空间的`Contact`类。这是不正常的，我之所以这样做是为了节省书中的空间。通常，即使是你顶层的模型也应该位于像`App\Models\Contact`这样的命名空间下。

#### 移除全局作用域

有三种方法可以移除全局作用域，所有方法都使用`withoutGlobalScope()`或`withoutGlobalScopes()`。如果你要移除基于闭包的作用域，那么该作用域的`addGlobalScope()`注册的第一个参数将是你用来启用它的键：

```php
$allContacts = Contact::withoutGlobalScope('active')->get();
```

如果你要移除单个基于类的全局作用域，你可以将类名传递给`withoutGlobalScope()`或`withoutGlobalScopes()`：

```php
Contact::withoutGlobalScope(ActiveScope::class)->get();

Contact::withoutGlobalScopes([ActiveScope::class, VipScope::class])->get();
```

或者，你可以仅在查询中禁用所有全局作用域：

```php
Contact::withoutGlobalScopes()->get();
```

## 使用访问器、修改器和属性类型转换自定义字段交互

现在我们已经讨论了如何使用 Eloquent 将记录存入和从数据库中取出，接下来让我们来讨论如何装饰和操作你的 Eloquent 模型的各个属性。

访问器、变动器和属性转换都允许你自定义 Eloquent 实例的各个属性的输入或输出方式。如果不使用这些功能，你的每个 Eloquent 实例的属性都将被视为字符串，且不能在模型上有任何数据库中不存在的属性。但我们可以改变这一点。

### 访问器

*访问器* 允许你为从模型实例 *读取* 数据时在你的 Eloquent 模型上定义自定义属性。这可能是因为你想改变特定列的输出方式，或者因为你想创建一个数据库表中根本不存在的自定义属性。

通过在模型上创建一个方法来定义访问器，方法名与属性名相同，但采用驼峰命名。因此，如果你的属性名是 `first_name`，访问器方法将命名为 `firstName`。然后，该方法需要有返回类型，表明它返回 `Illuminate\Database\Eloquent\Casts\Attribute` 的实例。

让我们试一试。首先，我们将装饰一个预先存在的列（[Example 5-35](https://example.org/#EX823)）。

##### Example 5-35\. 使用 Eloquent 访问器装饰预先存在的列

```php
// Model definition:
use Illuminate\Database\Eloquent\Casts\Attribute;

class Contact extends Model
{
    protected function name(): Attribute
    {
        return Attribute::make(
            get: fn (string $value) => $value ?: '(No name provided)',
        );
    }
}

// Accessor usage:
$name = $contact->name;
```

但我们也可以使用访问器定义从未在数据库中存在的属性，正如在 [Example 5-36](https://example.org/#EX824) 中所见。

##### Example 5-36\. 使用 Eloquent 访问器定义没有后备列的属性

```php
// Model definition:
class Contact extends Model
{
    protected function fullName(): Attribute
    {
        return Attribute::make(
            get: fn () => $this->first_name . ' ' . $this->last_name,
        );
    }
}

// Accessor usage:
$fullName = $contact->full_name;
```

### 变动器

*变动器* 与访问器的工作方式相同，不同之处在于它们用于确定如何处理 *设置* 数据，而不是 *获取* 数据。与访问器一样，你可以使用它们修改写入现有列数据的过程，或者允许设置数据库中不存在的列。

变动器的定义方式与访问器相同，但不使用 `get` 参数，而是使用 `set` 参数。

让我们试一试。首先，我们将添加对更新预先存在列的约束（[Example 5-37](https://example.org/#EX825)）。

##### Example 5-37\. 修改使用 Eloquent 变动器设置属性的值

```php
// Defining the mutator
class Order extends Model
{
    protected function amount(): Attribute
    {
        return Attribute::make(
            set: fn (string $value) => $value > 0 ? $value : 0,
        );
    }
}

// Using the mutator
$order->amount = '15';
```

现在让我们添加一个代理列用于设置，如 [Example 5-38](https://example.org/#EX826) 所示。如果我们一次设置多个列的值，或者我们自定义要设置的列的名称，我们可以从 `set()` 方法返回一个数组。

##### Example 5-38\. 使用 Eloquent 变动器允许设置不存在属性的值

```php
// Defining the mutator
class Order extends Model
{
    protected function workgroupName(): Attribute
    {
        return Attribute::make(
            set: fn (string $value) => [
                'email' => "{$value}@ourcompany.com",
            ],
        );
    }
}

// Using the mutator
$order->workgroup_name = 'jstott';
```

如你所料，为不存在的列创建变动器相对较少见，因为设置一个属性并改变另一列可能会令人困惑，但这是可能的。

### 属性转换

你可以想象编写访问器以将所有整数类型字段强制转换为整数，将 JSON 编码和解码以存储在 `TEXT` 列中，或者将 `TINYINT` `0` 和 `1` 转换为布尔值。

幸运的是，Eloquent 已经为此设计了一个系统。它被称为 *属性转换*，它允许您定义任何列始终被视为特定数据类型，无论读取还是写入时都如此。选项列在 表 5-1 中。

表 5-1\. 可能的属性转换列类型

| 类型 | 描述 |
| --- | --- |
| `int&#124;integer` | 使用 PHP (`int`) 进行转换 |
| `real&#124;float&#124;double` | 使用 PHP (`float`) 进行转换 |
| `decimal:<digits>` | 使用 PHP 的 `number_format()`，并指定小数位数进行转换。 |
| `string` | 使用 PHP (`string`) 进行转换 |
| `bool&#124;boolean` | 使用 PHP (`bool`) 进行转换 |
| `object&#124;json` | 解析为 JSON，并作为 `stdClass` 对象 |
| `array` | 解析为 JSON，并作为数组 |
| `collection` | 解析为 JSON，并作为集合 |
| `date&#124;datetime` | 在数据库 `DATETIME` 和 Carbon 之间进行解析 |
| `timestamp` | 在数据库 `TIMESTAMP` 和 Carbon 之间进行解析 |
| `encrypted` | 处理字符串的加密和解密 |
| `enum` | 转换为枚举类型 |
| `hashed` | 处理字符串的哈希值 |

示例 5-39 展示了如何在您的模型中使用属性转换。

##### 示例 5-39\. 在 Eloquent 模型上使用属性转换

```php
use App\Enums\SubscriptionStatus;

class Contact extends Model
{
    protected $casts = [
        'vip' => 'boolean',
        'children_names' => 'array',
        'birthday' => 'date',
        'subscription' => SubscriptionStatus::class
    ];
}
```

### 自定义属性转换

如果内置的属性类型不够用，我们可以构建自定义转换类型，并在 `$casts` 数组中使用它们。

可以将 *自定义转换类型* 定义为一个常规的 PHP 类，具有 `get` 和 `set` 方法。当从 Eloquent 模型检索给定属性时，将调用 `get` 方法。在将属性保存到数据库之前，将调用 `set` 方法，如您可以在 示例 5-40 中看到的。

##### 示例 5-40\. 一个样例自定义转换类型

```php
<?php

namespace App\Casts;

use Carbon\Carbon;
use Illuminate\Support\Facades\Crypt;
use Illuminate\Contracts\Database\Eloquent\CastsAttributes;
use Illuminate\Database\Eloquent\Model;

class Encrypted implements CastsAttributes
{

    /**
 * Cast the given value.
 *
 * @param  array<string, mixed>  $attributes
 */
    public function get(Model $model, string $key, mixed $value, array $attributes)
    {
        return Crypt::decrypt($value);
    }

    /**
 * Prepare the given value for storage.
 *
 * @param  array<string, mixed>  $attributes
 */
    public function set(Model $model, string $key, mixed $value, array $attributes)
    {
        return Crypt::encrypt($value);
    }
}
```

您可以在您的 Eloquent 模型的 `$casts` 属性中使用自定义转换：

```php
protected $casts = [
    'ssn' => \App\Casts\Encrypted::class,
];
```

## Eloquent 集合

当您在 Eloquent 中进行任何可能返回多行结果的查询调用时，它们将以 Eloquent 集合的形式返回，这是一种专门的集合类型。让我们来看看集合和 Eloquent 集合，以及它们比纯数组更好的地方。

### 引入基础集合

Laravel 的 `Collection` 对象 (`Illuminate\Support\Collection`) 类似于增强型数组。它们在类似数组的对象上公开的方法非常有用，使用一段时间后，您可能会希望将它们引入非 Laravel 项目中 —— 您可以通过 [Illuminate/Collections package](https://oreil.ly/YWnbl) 来实现这一点。

创建集合的最简单方法是使用 `collect()` 辅助函数。可以传入一个数组，也可以不带参数使用它，后续再将项目推送到其中。让我们试试：

```php
$collection = collect([1, 2, 3]);
```

现在假设我们想要过滤掉任何偶数：

```php
$odds = $collection->reject(function ($item) {
    return $item % 2 === 0;
});
```

或者，如果我们想要得到每个项目乘以 10 的版本，可以这样做：

```php
$multiplied = $collection->map(function ($item) {
    return $item * 10;
});
```

我们甚至可以仅获取偶数，将它们全部乘以`10`，并通过`sum()`将它们缩减为一个单一的数字：

```php
$sum = $collection
    ->filter(function ($item) {
        return $item % 2 == 0;
    })->map(function ($item) {
        return $item * 10;
    })->sum();
```

如你所见，集合提供了一系列方法，可以选择链式调用以在数组上执行功能操作。它们提供了与本地 PHP 方法（如 `array_map()` 和 `array_reduce()`）相同的功能，但你无需记忆 PHP 不可预测的参数顺序，而且方法链语法更加可读。

`Collection` 类中提供了超过 `60` 个方法，包括 `max()`、`whereIn()`、`flatten()` 和 `flip()` 等方法——这里没有足够的空间来覆盖它们所有。我们将在第十七章讨论更多方法，或者你可以查看[Laravel 集合文档](https://oreil.ly/i83f4)以查看所有方法。

# 集合取代数组

集合还可以在任何你可以使用数组的地方（除了类型提示）使用。它们允许迭代，所以你可以将它们传递给 `foreach`；它们还允许数组访问，所以如果它们有键，你可以尝试 `$a = $collection['a']`。

### 延迟集合

[延迟集合](https://oreil.ly/uyoGf)利用 PHP 生成器的强大功能来处理非常大的数据集，同时保持你的应用程序内存使用量非常低。

想象一下需要遍历数据库中的 100,000 个联系人。如果你使用 Laravel 的普通 `Collections`，很快就会遇到内存问题；所有 100,000 条记录将加载到内存中，这对你的机器来说是一个很大的负担：

```php
$verifiedContacts = App\Contact::all()->filter(function ($contact) {
    return $contact->isVerified();
});
```

Eloquent 可以轻松地使用延迟集合来处理你的 Eloquent 模型。如果你使用 `cursor` 方法，Eloquent 模型将返回一个 `LazyCollection` 实例，而不是默认的 `Collection` 类。通过使用延迟集合，你的应用程序每次只会将一条记录加载到内存中：

```php
$verifiedContacts = App\Contact::cursor()->filter(function ($contact) {
    return $contact->isVerified();
});
```

### Eloquent 集合增加了什么

每个 Eloquent 集合都是一个普通集合，但扩展了对 Eloquent 结果集合特定需求的支持。

再次说明，这里没有足够的空间来覆盖所有新增功能，但它们的核心是与集合交互的独特方面，不仅仅是一般对象的集合，而是用于表示数据库行的对象。

例如，每个 Eloquent 集合都有一个名为 `modelKeys()` 的方法，返回集合中每个实例的主键数组。`find($id)` 查找具有主键 `$id` 的实例。

这里还有一个额外的功能，就是定义任何给定模型应该返回其结果的特定集合类。因此，如果你想向你的 `Order` 模型的任何对象集合中添加特定方法——可能与汇总订单财务详情相关——你可以创建一个自定义的 `OrderCollection`，扩展 `Illuminate\Database\Eloquent\Collection`，然后在你的模型中注册它，如示例 5-41 所示。

##### 示例 5-41\. 用于 Eloquent 模型的自定义`Collection`类

```php
...
class OrderCollection extends Collection
{
    public function sumBillableAmount()
    {
        return $this->reduce(function ($carry, $order) {
            return $carry + ($order->billable ? $order->amount : 0);
        }, 0);
    }
}

...
class Order extends Model
{
    public function newCollection(array $models = [])
    {
        return new OrderCollection($models);
    }
```

现在，每当你获取`Order`集合（例如，从`Order::all()`获取）时，它实际上是`OrderCollection`类的一个实例：

```php
$orders = Order::all();
$billableAmount = $orders->sumBillableAmount();
```

## Eloquent 序列化

*序列化*是指当你拿到一个复杂的东西——比如一个数组或对象——然后将其转换成字符串的过程。在基于 Web 的情境中，这个字符串通常是 JSON，但也可能采用其他形式。

序列化复杂的数据库记录可能会很复杂，这是许多 ORM 在某些地方不足的地方之一。幸运的是，你可以免费使用 Eloquent 提供的两种强大方法：`toArray()`和`toJson()`。集合也有`toArray()`和`toJson()`，因此所有这些都是有效的：

```php
$contactArray = Contact::first()->toArray();
$contactJson = Contact::first()->toJson();
$contactsArray = Contact::all()->toArray();
$contactsJson = Contact::all()->toJson();
```

你还可以将一个 Eloquent 实例或集合转换为字符串（`$string = (string) $contact;`），但是模型和集合都只会运行`toJson()`并返回结果。

### 从路由方法直接返回模型

Laravel 的路由最终将路由方法返回的所有内容转换为字符串，因此你可以使用一个巧妙的技巧。如果在控制器中返回 Eloquent 调用的结果，它将自动转换为字符串，因此以 JSON 返回的路由可以简单地像示例 5-42 中的任意一个那样简单。

##### 示例 5-42\. 直接从路由返回 JSON

```php
// routes/web.php
Route::get('api/contacts', function () {
    return Contact::all();
});

Route::get('api/contacts/{id}', function ($id) {
    return Contact::findOrFail($id);
});
```

### 从 JSON 中隐藏属性

在 API 中使用 JSON 返回是非常常见的，而在这些上下文中隐藏某些属性也很常见，因此 Eloquent 使得每次转换为 JSON 时隐藏任何属性变得非常容易。

你可以禁止特定的属性，隐藏你列出的那些：

```php
class Contact extends Model
{
    public $hidden = ['password', 'remember_token'];
```

或者允许指定的属性，仅显示你列出的那些：

```php
class Contact extends Model
{
    public $visible = ['name', 'email', 'status'];
```

这也适用于关系：

```php
class User extends Model
{
    public $hidden = ['contacts'];

    public function contacts()
    {
        return $this->hasMany(Contact::class);
    }
```

# 加载关系的内容

默认情况下，当你获取数据库记录时，关系的内容是不会被加载的，因此无论你是否隐藏它们都无所谓。但是，就像你很快就会了解到的那样，可以获取带有其相关项的记录，并且在这种情况下，如果选择隐藏该关系，则这些项目将不会包含在记录的序列化副本中。

如果你现在好奇的话，你可以获取一个带有所有联系人的`User`——假设你已经正确设置了关系——使用以下调用：

```php
$user = User::with('contacts')->first();
```

可能会有时候，你想要使一个属性仅在单个调用中可见。这是可能的，使用 Eloquent 方法`makeVisible()`：

```php
$array = $user->makeVisible('remember_token')->toArray();
```

# 将生成的列添加到数组和 JSON 输出中

如果你为不存在的列创建了一个访问器——例如，我们的`full_name`列来自示例 5-36——你可以将其添加到模型的`$appends`数组中，这样会将其添加到数组和 JSON 输出中：

```php
class Contact extends Model
{
    protected $appends = ['full_name'];

    public function getFullNameAttribute()
    {
        return "{$this->first_name} {$this->last_name}";
    }
}
```

## Eloquent 关系

在关系数据库模型中，预计会有表彼此*相关*——因此得名。Eloquent 提供了简单而强大的工具，使得关联数据库表的过程比以往更容易。

本章中许多示例都围绕一个 *用户* 拥有多个 *联系人*，这是一个相对常见的情况。

在像 Eloquent 这样的 ORM 中，您会将其称为 *一对多* 关系：一个用户 *拥有多个* 联系人。

如果这是一个客户关系管理系统（CRM），其中一个联系人可以分配给多个用户，那么这将是一个*多对多*关系：多个用户可以关联到一个联系人，每个用户可以与多个联系人相关联。一个用户 *拥有并且属于多个* 联系人。

如果每个联系人可以有多个电话号码，用户想要一个他们 CRM 中每个电话号码的数据库，您可以说用户 *通过* 联系人 *拥有多个* 电话号码——也就是说，一个用户 *拥有多个* 联系人，每个联系人 *拥有多个* 电话号码，因此联系人是一种中间介质。

如果每个联系人都有一个地址，但您只想追踪一个地址怎么办？您可以在`Contact`上拥有所有地址字段，但您也可以创建一个`Address`模型——这意味着联系人 *拥有一个* 地址。

最后，如果您想要能够收藏联系人，还有事件怎么办？这将是一个*多态*关系，其中一个用户 *拥有多个* 星标，但有些可能是联系人，有些可能是事件。

让我们深入了解如何定义和访问这些关系。

### 一对一

让我们从简单的开始：一个`Contact` *拥有一个* `PhoneNumber`。这个关系在示例 5-43 中定义。

##### 示例 5-43\. 定义一对一关系

```php
class Contact extends Model
{
    public function phoneNumber()
    {
        return $this->hasOne(PhoneNumber::class);
    }
```

正如您所看到的，定义关系的方法在 Eloquent 模型本身上（`$this->hasOne()`）并且在这种情况下至少需要将与之相关的类的完全限定类名传递进去。

在数据库中如何定义这一点？由于我们已经定义`Contact`拥有一个`PhoneNumber`，Eloquent 期望支持`PhoneNumber`类的表（可能是`phone_numbers`）有一个`contact_id`列。如果您使用不同的名称（例如`owner_id`），您需要更改您的定义：

```php
return $this->hasOne(PhoneNumber::class, 'owner_id');
```

这是我们如何访问`Contact`的`PhoneNumber`：

```php
$contact = Contact::first();
$contactPhone = $contact->phoneNumber;
```

注意，我们在示例 5-43 中定义了`phoneNumber()`方法，但我们使用`->phoneNumber`访问它。这就是魔力所在。您还可以使用`->phone_number`访问它。这将返回与相关`PhoneNumber`记录的完整 Eloquent 实例。

但如果我们想要从`PhoneNumber`访问`Contact`怎么办？也有一个方法（参见示例 5-44）。

##### 示例 5-44\. 定义一对一关系的逆向

```php
class PhoneNumber extends Model
{
    public function contact()
    {
        return $this->belongsTo(Contact::class);
    }
```

然后我们以同样的方式访问它：

```php
$contact = $phoneNumber->contact;
```

### 一对多

最常见的是一对多关系。让我们看看如何定义我们的`User` *拥有多个* `Contact`s (示例 5-45)。

##### 示例 5-45\. 定义一对多关系

```php
class User extends Model
{
    public function contacts()
    {
        return $this->hasMany(Contact::class);
    }
```

再次，这期望`Contact`模型的后台表（可能是`contacts`）有一个`user_id`列。如果没有，请通过将正确的列名作为`hasMany()`的第二个参数来覆盖它。

我们可以获取`User`的`Contact`如下：

```php
$user = User::first();
$usersContacts = $user->contacts;
```

就像一对一关系一样，我们使用关系方法的名称，并将其调用为属性而不是方法。但是，这个方法返回的是一个集合而不是一个模型实例。这是一个普通的 Eloquent 集合，所以我们可以做各种有趣的事情：

```php
$donors = $user->contacts->filter(function ($contact) {
    return $contact->status == 'donor';
});

$lifetimeValue = $contact->orders->reduce(function ($carry, $order) {
    return $carry + $order->amount;
}, 0);
```

就像一对一关系一样，我们也可以定义反向关系（Example 5-46）。

##### Example 5-46\. 定义一对多关系的反向关系

```php
class Contact extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
```

就像一对一关系一样，我们可以从`Contact`中访问`User`：

```php
$userName = $contact->user->name;
```

# 附加和分离附加项目

大多数情况下，我们通过在父级上运行`save()`并传入相关项来附加项目，例如`$⁠u⁠s⁠e⁠r⁠-⁠>​c⁠o⁠n⁠t⁠a⁠c⁠t⁠s⁠(⁠)​-⁠>⁠s⁠a⁠v⁠e⁠($contact)`。但是，如果你想在已附加的（“子”）项目上执行这些行为，可以在返回`belongsTo`关系的方法上使用`associate()`和`dissociate()`：

```php
$contact = Contact::first();

$contact->user()->associate(User::first());
$contact->save();

// and later

$contact->user()->dissociate();
$contact->save();
```

#### 使用关系作为查询构建器

到目前为止，我们将方法名（例如`contacts()`）作为属性调用（例如`$user->contacts`）。如果我们将其作为方法调用会发生什么？它不会处理关系，而是返回一个预设的查询构建器。

所以，如果你有`User 1`，并调用其`contacts()`方法，你现在将得到一个查询构建器，预设为“所有`user_id`字段值为`1`的联系人”。然后你可以从这里构建出一个功能性的查询：

```php
$donors = $user->contacts()->where('status', 'donor')->get();
```

#### 仅选择具有相关项的记录

你可以选择只选择符合特定条件的与相关项目的记录，使用`has()`：

```php
$postsWithComments = Post::has('comments')->get();
```

你也可以进一步调整条件：

```php
$postsWithManyComments = Post::has('comments', '>=', 5)->get();
```

你可以嵌套条件：

```php
$usersWithPhoneBooks = User::has('contacts.phoneNumbers')->get();
```

最后，你可以在相关项目上编写自定义查询：

```php
// Gets all contacts with a phone number containing the string "867-5309"
$jennyIGotYourNumber = Contact::whereHas('phoneNumbers', function ($query) {
    $query->where('number', 'like', '%867-5309%');
})->get();

// Shortened version of the same code above
$jennyIGotYourNumber = Contact::whereRelation(
    'phoneNumbers',
    'number',
    'like',
'%867-5309')->get();
```

### 一对多关系的 has one of many

从一对多关系中检索记录的常见情况是，你只想从该关系中检索一个项目，通常是最新的或最旧的。Laravel 为这些情况提供了一个方便的工具：has one of many。

一对多关系允许你定义一个给定方法应该检索相关集合中的最新项目，或者最旧项目，或者具有任何特定列的最小或最大值的项目，如 Example 5-47 所示。

##### Example 5-47\. 定义一对多关系的 has-one-of-many 关系

```php
class User extends Model
{
    public function newestContact(): HasOne
    {
        return $this->hasOne(Contact::class)->latestOfMany();
    }

    public function oldestContact(): HasOne
    {
        return $this->hasOne(Contact::class)->oldestOfMany();
    }

    public function emergencyContact(): HasOne
    {
        return $this->hasOne(Contact::class)->ofMany('priority', 'max');
    }
```

### 多对多关系

`hasManyThrough()`实际上是一个方便的方法，用于获取关系的关系。想象一下我之前给出的例子，一个`User`有许多`Contact`，每个`Contact`有许多`PhoneNumber`。如果你想获取用户的联系电话列表怎么办？这就是一个多对多的关系。

这个结构假设你的`contacts`表有一个`user_id`来将联系人与用户关联起来，而`phone_numbers`表有一个`contact_id`将其与联系人关联起来。然后，我们在`User`上定义关系，如 Example 5-48 所示。

##### Example 5-48\. 定义一个多对多关系

```php
class User extends Model
{
    public function phoneNumbers()
    {
        // Newer string-based syntax
        return $this->through('contact')->has('phoneNumber');

        // Traditional syntax
        return $this->hasManyThrough(PhoneNumber::class, Contact::class);
    }
```

您可以使用`$user->phone_numbers`访问此关系。如果需要在中间或远程模型上自定义关系键，请使用传统语法；您可以在中间模型上定义关键字（使用`h⁠a⁠s​M⁠a⁠n⁠y⁠Through()`的第三个参数）和在远程模型上定义关系键（使用第四个参数）。

### 一对一通过

`hasOneThrough()`与`hasManyThrough()`类似，但不是通过中间项目访问许多相关项目，而是仅通过单个中间项目访问单个相关项目。

如果每个用户属于一个公司，而公司有一个单独的电话号码，并且您希望能够通过获取用户公司的电话号码来获取用户的电话号码，那就是一对一通过关系，如 Example 5-49 所示。

##### Example 5-49\. 定义一对一通过关系

```php
class User extends Model
{
    public function phoneNumber()
    {
        // Newer string-based syntax
        return $this->through('company')->has('phoneNumber');

        // Traditional syntax
        return $this->hasOneThrough(PhoneNumber::class, Company::class);
    }
```

### 多对多

这是事情开始变得复杂的地方。让我们以允许`User`拥有多个`Contact`的 CRM 示例为例，每个`Contact`又与多个`User`相关联。

首先，我们像在 Example 5-50 中一样在`User`上定义关系。

##### Example 5-50\. 定义多对多关系

```php
class User extends Model
{
    public function contacts()
    {
        return $this->belongsToMany(Contact::class);
    }
}
```

由于这是多对多关系，逆关系看起来完全相同（Example 5-51）。

##### Example 5-51\. 定义多对多关系的逆

```php
class Contact extends Model
{
    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

由于单个`Contact`不能有`user_id`列，单个`User`也不能有`contact_id`列，多对多关系依赖于连接这两者的中间表。这种表的常规命名是将两个单数表名按字母顺序排列在一起，并用下划线分隔。

因此，由于我们正在连接`users`和`contacts`，我们的中间表应该命名为`contact_user`（如果你想自定义表名，请将其作为第二个参数传递给`belongsToMany()`方法）。它需要两列：`contact_id`和`user_id`。

就像使用`hasMany()`一样，我们可以从两个方向访问相关项的集合，这一次是从双方面（Example 5-52）。

##### Example 5-52\. 访问多对多关系的双方相关项

```php
$user = User::first();

$user->contacts->each(function ($contact) {
    // do something
});

$contact = Contact::first();

$contact->users->each(function ($user) {
    // do something
});

$donors = $user->contacts()->where('status', 'donor')->get();
```

#### 从中间表获取数据

多对多关系的一个独特之处在于它是我们第一个具有中间表的关系。在中间表中数据越少越好，但也有一些情况下在中间表中存储信息很有价值，比如，你可能想要存储一个`created_at`字段，以查看这个关系是何时创建的。

为了存储这些字段，您必须将它们添加到关系定义中，就像在 Example 5-53 中一样。您可以使用`withPivot()`定义特定字段，或者使用`withTimestamps()`添加`created_at`和`updated_at`时间戳。

##### Example 5-53\. 向中间记录添加字段

```php
public function contacts()
{
    return $this->belongsToMany(Contact::class)
        ->withTimestamps()
        ->withPivot('status', 'preferred_greeting');
}
```

当通过关系获取模型实例时，它将在其上有一个 `pivot` 属性，该属性将表示它在刚刚提取的枢纽表中的位置。因此，您可以执行类似于 示例 5-54 的操作。

##### 示例 5-54\. 从相关项目的枢纽条目获取数据

```php
$user = User::first();

$user->contacts->each(function ($contact) {
    echo sprintf(
        'Contact associated with this user at: %s',
        $contact->pivot->created_at
    );
});
```

如果您愿意，您可以使用 `as()` 方法自定义 `pivot` 键的名称，如 示例 5-55 中所示。

##### 示例 5-55\. 自定义 pivot 属性名称

```php
// User model
public function groups()
{
    return $this->belongsToMany(Group::class)
        ->withTimestamps()
        ->as('membership');
}
```

```php
// Using this relationship:
User::first()->groups->each(function ($group) {
    echo sprintf(
        'User joined this group at: %s',
        $group->membership->created_at
    );
});
```

### 多态

请记住，我们的多态关系是指我们有多个对应于相同关系的 Eloquent 类。我们现在要使用的是 `Star`（例如收藏夹）。用户可以同时收藏 `Contact` 和 `Event`，这就是 *多态* 名称的由来：有多种类型的对象使用单一接口。

因此，我们需要三个表（`stars`、`contacts`、`events`）和三个模型（`Star`、`Contact` 和 `Event`）。实际上，您需要四个，因为我们还需要 `users` 和 `User`，但我们马上会解决这个问题。`contacts` 和 `events` 表将保持正常，而 `stars` 表将包含 `id`、`starrable_id` 和 `starrable_type` 字段。对于每个 `Star`，我们将定义它是哪种“类型”（例如 `Contact` 或 `Event`）以及该类型的哪个 ID（例如 `1`）。

让我们创建我们的模型，如 示例 5-56 中所示。

##### 示例 5-56\. 创建多态星标系统的模型

```php
class Star extends Model
{
    public function starrable()
    {
        return $this->morphTo();
    }
}

class Contact extends Model
{
    public function stars()
    {
        return $this->morphMany(Star::class, 'starrable');
    }
}

class Event extends Model
{
    public function stars()
    {
        return $this->morphMany(Star::class, 'starrable');
    }
}
```

那么，我们如何创建一个 `Star` 呢？

```php
$contact = Contact::first();
$contact->stars()->create();
```

就是这么简单。现在 `Contact` 已经被加星了。

要找到给定 `Contact` 上的所有 `Star`，我们像 示例 5-57 中那样调用 `stars()` 方法。

##### 示例 5-57\. 检索多态关系的实例

```php
$contact = Contact::first();

$contact->stars->each(function ($star) {
    // Do stuff
});
```

如果我们有一个 `Star` 实例，我们可以通过调用用于定义其 `morphTo` 关系的方法来获取其目标，在这种情况下是 `starrable()`。请查看 示例 5-58。

##### 示例 5-58\. 检索多态实例的目标

```php
$stars = Star::all();

$stars->each(function ($star) {
    var_dump($star->starrable); // An instance of Contact or Event
});
```

最后，您可能会问：“如果我想知道谁收藏了这个联系人怎么办？”这是一个很好的问题。只需将 `user_id` 添加到您的 `stars` 表中，然后设置 `User` *有多个* `Star` 和 `Star` *属于* 一个 `User`——一个一对多的关系（示例 5-59）。`stars` 表几乎成为您的 `User` 和您的 `Contact` 和 `Event` 之间的枢纽表。

##### 示例 5-59\. 扩展多态系统以区分用户

```php
class Star extends Model
{
    public function starrable()
    {
        return $this->morphsTo;
    }

    public function user()
    {
        return $this->belongsTo(User::class);
    }
}

class User extends Model
{
    public function stars()
    {
        return $this->hasMany(Star::class);
    }
}
```

就这样！您现在可以运行 `$star->user` 或 `$user->stars` 来查找 `User` 的 `Star` 列表，或者从 `Star` 中查找星标的 `User`。此外，当您创建一个新的 `Star` 时，现在您需要传递 `User`：

```php
$user = User::first();
$event = Event::first();
$event->stars()->create(['user_id' => $user->id]);
```

### 多对多多态

关系类型中最复杂且最不常见的多对多多态关系就像多态关系一样，但不是一对多，而是多对多。

这种关系类型的最常见示例是标签，所以我将保持它的安全性并将其用作我们的示例。假设您希望能够为`Contact`和`Event`打标签。多对多多态关系的独特之处在于它是多对多的：每个标签可以应用于多个项目，并且每个标记的项目可能有多个标签。而且，它是多态的：标签可以与多种不同类型的项目相关联。对于数据库，我们将从多态关系的正常结构开始，但还将添加一个透视表。

这意味着我们需要一个`contacts`表，一个`events`表和一个`tags`表，都像正常的表一样具有 ID 和您想要的任何属性，*还有*一个新的`taggables`表，它将具有`tag_id`、`taggable_id`和`taggable_type`字段。`taggables`表中的每个条目将关联一个标签与一个可标记的内容类型。

现在让我们像示例 5-60 中所示，在我们的模型上定义这个关系。

##### 示例 5-60\. 定义多态多对多关系

```php
class Contact extends Model
{
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

class Event extends Model
{
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

class Tag extends Model
{
    public function contacts()
    {
        return $this->morphedByMany(Contact::class, 'taggable');
    }

    public function events()
    {
        return $this->morphedByMany(Event::class, 'taggable');
    }
}
```

这是如何创建您的第一个标签：

```php
$tag = Tag::firstOrCreate(['name' => 'likes-cheese']);
$contact = Contact::first();
$contact->tags()->attach($tag->id);
```

我们像正常情况下获取这个关系的结果，就像示例 5-61 中所示。

##### 示例 5-61\. 从多对多多态关系的双方访问相关项目

```php
$contact = Contact::first();

$contact->tags->each(function ($tag) {
    // Do stuff
});

$tag = Tag::first();
$tag->contacts->each(function ($contact) {
    // Do stuff
});
```

### 子记录更新父记录时间戳

请记住，默认情况下，任何 Eloquent 模型都将具有`created_at`和`updated_at`时间戳。每当对记录进行更改时，Eloquent 都会自动设置`updated_at`时间戳。

当相关项目与另一个项目具有`belongsTo`或`belongsToMany`关系时，每当更新相关项目时标记另一个项目可能非常有价值。例如，如果更新了`PhoneNumber`，也许应该标记其连接的`Contact`已更新。

我们可以通过将该关系的方法名添加到子类的`$touches`数组属性中来实现这一点，就像示例 5-62 中所示。

##### 示例 5-62\. 每当更新子记录时更新父记录

```php
class PhoneNumber extends Model
{
    protected $touches = ['contact'];

    public function contact()
    {
        return $this->belongsTo(Contact::class);
    }
}
```

### 急加载

默认情况下，Eloquent 使用*lazy loading*加载关系。这意味着当您首次加载模型实例时，它的相关模型不会随之加载。相反，只有在您访问它们时才会加载它们；它们是“懒惰的”，直到被调用时才做任何工作。

如果您正在迭代一系列模型实例，并且每个实例都有一个您正在处理的相关项目（或项目），这可能会成为问题。懒加载的问题在于它可能引入显著的数据库负载（通常是*N*+1 问题，如果您熟悉这个术语的话；如果不熟悉，可以忽略这个括号内的备注）。例如，每次运行示例 5-63 中的循环时，它都会执行一个新的数据库查询来查找`Contact`的电话号码。

##### 示例 5-63\. 检索列表中每个项目的一个相关项目（N+1）

```php
$contacts = Contact::all();

foreach ($contacts as $contact) {
    foreach ($contact->phone_numbers as $phone_number) {
        echo $phone_number->number;
    }
}
```

如果你加载一个模型实例并且你知道你将使用它的关系，你可以选择*急加载*一个或多个相关项目集：

```php
$contacts = Contact::with('phoneNumbers')->get();
```

使用`with()`方法和检索一起获取所有与已拉取项目相关的项目；正如你在这个例子中看到的，你传递给它的是关系定义的方法名称。

当我们使用急加载时，而不是在请求时逐个拉取相关项目（例如，每次`foreach`循环运行时选择一个联系人的电话号码），我们只有一个查询来拉取初始项目（选择所有联系人），并且第二个查询来拉取所有他们的相关项目（选择刚刚拉取的联系人拥有的所有电话号码）。

通过将要急加载的多个关系作为数组传递给`with()`调用，你可以急加载多个关系：

```php
$contacts = Contact::with(['phoneNumbers', 'addresses'])->get();
```

你可以嵌套急加载以急加载关系的关系：

```php
$authors = Author::with('posts.comments')->get();
```

#### 限制急加载

如果你想要急加载一个关系，但不是所有项目，你可以传递一个闭包给`with()`来定义确切要急加载的相关项目：

```php
$contacts = Contact::with(['addresses' => function ($query) {
    $query->where('mailable', true);
}])->get();
```

#### 惰性急加载

我知道这听起来很疯狂，因为我们刚刚定义了急加载，就好像是惰加载的反义词，但有时在初始实例被拉取后你才意识到你想要执行急加载查询。在这种情况下，你仍然能够做一次查询来查找所有相关项目，避免*N*+1 的成本。我们称之为*惰性急加载*：

```php
$contacts = Contact::all();

if ($showPhoneNumbers) {
    $contacts->load('phoneNumbers');
}
```

当关系尚未被加载时才加载它，请使用`loadMissing()`方法：

```php
$contacts = Contact::all();

if ($showPhoneNumbers) {
    $contacts->loadMissing('phoneNumbers');
}
```

#### 防止惰性加载

因为惰性加载通常是一种不良模式，你可以一次为整个应用程序禁用惰性加载。建议你在`AppServiceProvider`的`boot()`方法中执行此操作：

```php
use Illuminate\Database\Eloquent\Model;

    public function boot()
    {
        Model::preventLazyLoading(! $this->app->isProduction());
    }
```

#### 仅急加载计数

如果你想要急加载关系，但只是为了访问每个关系中项目的计数，你可以尝试使用`withCount()`：

```php
$authors = Author::withCount('posts')->get();

// Adds a "posts_count" integer to each Author with a count of that
// author's related posts
```

# Eloquent 事件

Eloquent 模型在发生某些操作时向应用程序的虚空中发出事件，无论你是否在监听。如果你熟悉发布/订阅，它是相同的模型（你将在第十六章中学到更多关于 Laravel 整个事件系统的内容）。

这里是绑定在创建新的`Contact`时触发时的监听器的快速概述。我们将在`AppServiceProvider`的`boot()`方法中绑定它，假设我们在每次创建新的`Contact`时通知第三方服务（示例 5-64）。

##### 示例 5-64。将监听器绑定到 Eloquent 事件

```php
class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        $thirdPartyService = new SomeThirdPartyService;

        Contact::creating(function ($contact) use ($thirdPartyService) {
            try {
                $thirdPartyService->addContact($contact);
            } catch (Exception $e) {
                Log::error('Failed adding contact to ThirdPartyService; canceled.');

                return false; // Cancels Eloquent create()
            }
        });
    }
```

我们可以在 Example 5-64 中看到几个东西。首先，我们使用 ``*Modelname*`::`*eventName()*`` 作为方法，并传递一个闭包。闭包可以访问正在操作的模型实例。其次，我们需要在某个服务提供者中定义此监听器。第三，如果返回 `false`，操作将被取消，`save()` 或 `update()` 将被取消。

这里是每个 Eloquent 模型触发的事件：

+   `creating`

+   `created`

+   `updating`

+   `updated`

+   `saving`

+   `saved`

+   `deleting`

+   `deleted`

+   `restoring`

+   `restored`

+   `retrieved`

大多数这些应该很明确，除了可能 `restoring` 和 `restored`，它们在恢复软删除的行时触发。此外，`saving` 会在 `creating` 和 `updating` 时触发，`saved` 在 `created` 和 `updated` 时触发。

`retrieved` 事件在从数据库中检索到现有模型时触发。

# 测试

Laravel 的整个应用测试框架使得测试数据库变得非常简单——不是通过编写针对 Eloquent 的单元测试，而是通过愿意测试整个应用程序。

想象这种情况。您想测试确保特定页面显示一个联系人但不显示另一个联系人。其中一些逻辑涉及 URL、控制器和数据库之间的相互作用，因此最佳的测试方法是应用测试。您可能正在考虑模拟 Eloquent 调用并尝试避免系统访问数据库。*不要这样做。* 请尝试使用 Example 5-65。

##### Example 5-65\. 使用简单的应用测试测试数据库交互

```php
public function test_active_page_shows_active_and_not_inactive_contacts()
{
    $activeContact = Contact::factory()->create();
    $inactiveContact = Contact::factory()->inactive()->create();

    $this->get('active-contacts')
        ->assertSee($activeContact->name)
        ->assertDontSee($inactiveContact->name);
}
```

正如您所见，模型工厂和 Laravel 的应用测试功能非常适合测试数据库调用。

或者，您可以直接在数据库中查找该记录，就像在 Example 5-66 中一样。

##### Example 5-66\. 使用 `assertDatabaseHas()` 来检查数据库中的特定记录

```php
public function test_contact_creation_works()
{
    $this->post('contacts', [
        'email' => 'jim@bo.com'
    ]);

    $this->assertDatabaseHas('contacts', [
        'email' => 'jim@bo.com'
    ]);
}
```

Eloquent 和 Laravel 的数据库框架经过了广泛测试。*您无需测试它们。* 您不需要模拟它们。如果您确实想避免访问数据库，可以使用存储库，然后返回未保存的 Eloquent 模型实例。但最重要的消息是：测试您的应用程序如何使用您的数据库逻辑。

如果您有自定义的访问器、修改器、作用域或其他内容，也可以直接测试它们，就像在 Example 5-67 中一样。

##### Example 5-67\. 测试访问器、修改器和作用域

```php
public function test_full_name_accessor_works()
{
    $contact = Contact::factory()->make([
        'first_name' => 'Alphonse',
        'last_name' => 'Cumberbund'
    ]);

    $this->assertEquals('Alphonse Cumberbund', $contact->fullName);
}

public function test_vip_scope_filters_out_non_vips()
{
    $vip = Contact::factory()->vip()->create();
    $nonVip = Contact::factory()->create();

    $vips = Contact::vips()->get();

    $this->assertTrue($vips->contains('id', $vip->id));
    $this->assertFalse($vips->contains('id', $nonVip->id));
}
```

Just avoid writing tests that leave you creating complex “Demeter chains” to assert that a particular fluent stack was called on some database mock. If your testing starts to get overwhelming and complex around the database layer, it’s because you’re allowing preconceived notions to force you into unnecessarily complex systems. Keep it simple.

# TL;DR

Laravel 自带一套强大的数据库工具，包括迁移（migrations）、填充（seeding）、优雅的查询构建器以及 Eloquent，一个功能强大的 ActiveRecord ORM。Laravel 的数据库工具并不要求您完全使用 Eloquent —— 您可以通过一个薄层的便利操作访问和操作数据库，而无需直接编写 SQL。但是，添加 ORM（无论是 Eloquent 还是 Doctrine 或其他）都很容易，并且可以与 Laravel 的核心数据库工具很好地配合。

Eloquent 遵循 ActiveRecord 模式，这使得定义支持数据库的对象类变得简单，包括它们存储在哪个表中以及列的形状、访问器和修改器。Eloquent 可以处理各种常规的 SQL 操作，也可以处理复杂的关系，包括多态多对多关系。

Laravel 还为数据库测试提供了强大的系统，包括模型工厂。
