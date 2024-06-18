# 附录。函数参考

本附录描述了内置 PHP 扩展中可用的函数。这些是 PHP 构建时默认包含的扩展，如果未提供`--with`或`--enable`选项给`configure`，则不能通过配置选项删除它们。

对于每个函数，我们提供了函数签名，显示各种参数的数据类型以及哪些是必需的或可选的，以及副作用、错误和返回的数据结构的简要描述。

# PHP 函数按类别分类

此部分列出了由 PHP 内置扩展提供的函数列表，按扩展类别分组。

## 数组

> `array_change_key_case`
> 
> `array_chunk`
> 
> `array_combine`
> 
> `array_count_values`
> 
> `array_diff`
> 
> `array_diff_assoc`
> 
> `array_diff_key`
> 
> `array_diff_uassoc`
> 
> `array_diff_ukey`
> 
> `array_fill`
> 
> `array_fill_keys`
> 
> `array_filter`
> 
> `array_flip`
> 
> `array_intersect`
> 
> `array_intersect_assoc`
> 
> `array_intersect_key`
> 
> `array_intersect_uassoc`
> 
> `array_intersect_ukey`
> 
> `array_key_exists`
> 
> `array_keys`
> 
> `array_map`
> 
> `array_merge`
> 
> `array_merge_recursive`
> 
> `array_multisort`
> 
> `array_pad`
> 
> `array_pop`
> 
> `array_product`
> 
> `array_push`
> 
> `array_rand`
> 
> `array_reduce`
> 
> `array_replace`
> 
> `array_replace_recursive`
> 
> `array_reverse`
> 
> `array_search`
> 
> `array_shift`
> 
> `array_slice`
> 
> `array_splice`
> 
> `array_sum`
> 
> `array_udiff`
> 
> `array_udiff_assoc`
> 
> `array_udiff_uassoc`
> 
> `array_uintersect`
> 
> `array_uintersect_assoc`
> 
> `array_uintersect_uassoc`
> 
> `array_unique`
> 
> `array_unshift`
> 
> `array_values`
> 
> `array_walk`
> 
> `array_walk_recursive`
> 
> `arsort`
> 
> `asort`
> 
> `compact`
> 
> `count`
> 
> `current`
> 
> `each`
> 
> `end`
> 
> `extract`
> 
> `in_array`
> 
> `is_countable`
> 
> `key`
> 
> `krsort`
> 
> `ksort`
> 
> `list`
> 
> `natcasesort`
> 
> `natsort`
> 
> `next`
> 
> `prev`
> 
> `range`
> 
> `reset`
> 
> `rsort`
> 
> `shuffle`
> 
> `sort`
> 
> `uasort`
> 
> `uksort`
> 
> `usort`

## 类和对象

> `class_alias`
> 
> `class_exists`
> 
> `get_called_class`
> 
> `get_class`
> 
> `get_class_methods`
> 
> `get_class_vars`
> 
> `get_declared_classes`
> 
> `get_declared_interfaces`
> 
> `get_declared_traits`
> 
> `get_object_vars`
> 
> `get_parent_class`
> 
> `interface_exists`
> 
> `is_a`
> 
> `is_subclass_of`
> 
> `method_exists`
> 
> `property_exists`
> 
> `trait_exists`

## 数据过滤

> `filter_has_var`
> 
> `filter_id`
> 
> `filter_input_array`
> 
> `filter_var`
> 
> `filter_input`
> 
> `filter_list`
> 
> `filter_var_array`

## 日期和时间

> `checkdate`
> 
> `date`
> 
> `date_default_timezone_get`
> 
> `date_default_timezone_set`
> 
> `date_parse`
> 
> `date_parse_from_format`
> 
> `date_sun_info`
> 
> `date_sunrise`
> 
> `date_sunset`
> 
> `getdate`
> 
> `gettimeofday`
> 
> `gmdate`
> 
> `gmmktime`
> 
> `gmstrftime`
> 
> `hrtime`
> 
> `idate`
> 
> `localtime`
> 
> `microtime`
> 
> `mktime`
> 
> `strftime`
> 
> `strptime`
> 
> `strtotime`
> 
> `time`
> 
> `timezone_name_from_abbr`
> 
> `timezone_version_get`

## 目录

> `chdir`
> 
> `chroot`
> 
> `closedir`
> 
> `dir`
> 
> `getcwd`
> 
> `opendir`
> 
> `readdir`
> 
> `rewinddir`
> 
> `scandir`

## 错误和日志记录

> `debug_backtrace`
> 
> `debug_print_backtrace`
> 
> `error_clear_last`
> 
> `error_get_last`
> 
> `error_log`
> 
> `error_reporting`
> 
> `restore_error_handler`
> 
> `restore_exception_handler`
> 
> `set_error_handler`
> 
> `set_exception_handler`
> 
> `trigger_error`

## 文件系统

> `basename`
> 
> `chgrp`
> 
> `chmod`
> 
> `chown`
> 
> `clearstatcache`
> 
> `copy`
> 
> `dirname`
> 
> `disk_free_space`
> 
> `disk_total_space`
> 
> `fclose`
> 
> `feof`
> 
> `fflush`
> 
> `fgetc`
> 
> `fgetcsv`
> 
> `fgets`
> 
> `fgetss`
> 
> `file`
> 
> `file_exists`
> 
> `file_get_contents`
> 
> `file_put_contents`
> 
> `fileatime`
> 
> `filectime`
> 
> `filegroup`
> 
> `fileinode`
> 
> `filemtime`
> 
> `fileowner`
> 
> `fileperms`
> 
> `filesize`
> 
> `filetype`
> 
> `flock`
> 
> `fnmatch`
> 
> `fopen`
> 
> `fpassthru`
> 
> `fputcsv`
> 
> `fread`
> 
> `fscanf`
> 
> `fseek`
> 
> `fstat`
> 
> `ftell`
> 
> `ftruncate`
> 
> `fwrite`
> 
> `glob`
> 
> `is_dir`
> 
> `is_executable`
> 
> `is_file`
> 
> `is_link`
> 
> `is_readable`
> 
> `is_uploaded_file`
> 
> `is_writable`
> 
> `lchgrp`
> 
> `lchown`
> 
> `link`
> 
> `linkinfo`
> 
> `lstat`
> 
> `mkdir`
> 
> `move_uploaded_file`
> 
> `parse_ini_file`
> 
> `parse_ini_string`
> 
> `pathinfo`
> 
> `pclose`
> 
> `popen`
> 
> `readfile`
> 
> `readlink`
> 
> `realpath_cache_get`
> 
> `realpath_cache_size`
> 
> `realpath`
> 
> `rename`
> 
> `rewind`
> 
> `rmdir`
> 
> `stat`
> 
> `symlink`
> 
> `tempnam`
> 
> `tmpfile`
> 
> `touch`
> 
> `umask`
> 
> `unlink`

## 函数

> `call_user_func`
> 
> `call_user_func_array`
> 
> `create_function`
> 
> `forward_static_call`
> 
> `forward_static_call_array`
> 
> `func_get_arg`
> 
> `func_get_args`
> 
> `func_num_args`
> 
> `function_exists`
> 
> `get_defined_functions`
> 
> `register_shutdown_function`
> 
> `register_tick_function`
> 
> `unregister_tick_function`

## 邮件

> `mail`

## 数学

> `abs`
> 
> `acos`
> 
> `acosh`
> 
> `asin`
> 
> `asinh`
> 
> `atan2`
> 
> `atan`
> 
> `atanh`
> 
> `base_convert`
> 
> `bindec`
> 
> `ceil`
> 
> `cos`
> 
> `cosh`
> 
> `decbin`
> 
> `dechex`
> 
> `decoct`
> 
> `deg2rad`
> 
> `exp`
> 
> `expm1`
> 
> `floor`
> 
> `fmod`
> 
> `getrandmax`
> 
> `hexdec`
> 
> `hypot`
> 
> `is_finite`
> 
> `is_infinite`
> 
> `is_nan`
> 
> `lcg_value`
> 
> `log10`
> 
> `log1p`
> 
> `log`
> 
> `max`
> 
> `min`
> 
> `mt_getrandmax`
> 
> `mt_rand`
> 
> `mt_srand`
> 
> `octdec`
> 
> `pi`
> 
> `pow`
> 
> `rad2deg`
> 
> `rand`
> 
> `random_int`
> 
> `round`
> 
> `sin`
> 
> `sinh`
> 
> `sqrt`
> 
> `srand`
> 
> `tan`
> 
> `tanh`

## 杂项函数

> `connection_aborted`
> 
> `connection_status`
> 
> `constant`
> 
> `define`
> 
> `defined`
> 
> `get_browser`
> 
> `highlight_file`
> 
> `highlight_string`
> 
> `ignore_user_abort`
> 
> `pack`
> 
> `php_strip_whitespace`
> 
> `sleep`
> 
> `sys_getloadavg`
> 
> `time_nanosleep`
> 
> `time_sleep_until`
> 
> `uniqid`
> 
> `unpack`
> 
> `usleep`

## 网络

> `checkdnsrr`
> 
> `closelog`
> 
> `fsockopen`
> 
> `gethostbyaddr`
> 
> `gethostbyname`
> 
> `gethostbynamel`
> 
> `gethostname`
> 
> `getmxrr`
> 
> `getprotobyname`
> 
> `getprotobynumber`
> 
> `getservbyname`
> 
> `getservbyport`
> 
> `header`
> 
> `header_remove`
> 
> `headers_list`
> 
> `headers_sent`
> 
> `inet_ntop`
> 
> `inet_pton`
> 
> `ip2long`
> 
> `long2ip`
> 
> `openlog`
> 
> `pfsockopen`
> 
> `setcookie`
> 
> `setrawcookie`
> 
> `syslog`

## 输出缓冲

> `flush`
> 
> `ob_clean`
> 
> `ob_end_clean`
> 
> `ob_end_flush`
> 
> `ob_flush`
> 
> `ob_get_clean`
> 
> `ob_get_contents`
> 
> `ob_get_flush`
> 
> `ob_get_length`
> 
> `ob_get_level`
> 
> `ob_get_status`
> 
> `ob_gzhandler`
> 
> 隐式刷新输出缓冲
> 
> `ob_list_handlers`
> 
> `ob_start`
> 
> `output_add_rewrite_var`
> 
> 重置重写变量输出

## PHP 语言分析器

> `token_get_all`
> 
> `token_name`

## PHP 选项/信息

> `assert_options`
> 
> `assert`
> 
> `extension_loaded`
> 
> `gc_collect_cycles`
> 
> `gc_disable`
> 
> `gc_enable`
> 
> `gc_enabled`
> 
> `get_cfg_var`
> 
> `get_current_user`
> 
> `get_defined_constants`
> 
> `get_extension_funcs`
> 
> `get_include_path`
> 
> `get_included_files`
> 
> `get_loaded_extensions`
> 
> `getenv`
> 
> `getlastmod`
> 
> `getmygid`
> 
> `getmyinode`
> 
> `getmypid`
> 
> `getmyuid`
> 
> `getopt`
> 
> `getrusage`
> 
> `ini_get_all`
> 
> `ini_get`
> 
> `ini_restore`
> 
> `ini_set`
> 
> `memory_get_peak_usage`
> 
> `memory_get_usage`
> 
> `php_ini_loaded_file`
> 
> `php_ini_scanned_files`
> 
> `php_logo_guid`
> 
> `php_sapi_name`
> 
> `php_uname`
> 
> `phpcredits`
> 
> `phpinfo`
> 
> `phpversion`
> 
> `putenv`
> 
> `set_include_path`
> 
> `set_time_limit`
> 
> `sys_get_temp_dir`
> 
> `version_compare`
> 
> `zend_logo_guid`
> 
> `zend_thread_id`
> 
> `zend_version`

## Program Execution

> `escapeshellarg`
> 
> `escapeshellcmd`
> 
> `exec`
> 
> `passthru`
> 
> `proc_close`
> 
> `proc_get_status`
> 
> `proc_nice`
> 
> `proc_open`
> 
> `proc_terminate`
> 
> `shell_exec`
> 
> `system`

## Session Handling

> `session_cache_expire`
> 
> `session_cache_limiter`
> 
> `session_decode`
> 
> `session_destroy`
> 
> `session_encode`
> 
> `session_get_cookie_params`
> 
> `session_id`
> 
> `session_module_name`
> 
> `session_name`
> 
> `session_regenerate_id`
> 
> `session_register_shutdown`
> 
> `session_save_path`
> 
> `session_set_cookie_params`
> 
> `session_set_save_handler`
> 
> `session_start`
> 
> `session_status`
> 
> `session_unset`
> 
> `session_write_close`

## Streams

> `stream_bucket_append`
> 
> `stream_bucket_make_writeable`
> 
> `stream_bucket_new`
> 
> `stream_bucket_prepend`
> 
> `stream_context_create`
> 
> `stream_context_get_default`
> 
> `stream_context_get_options`
> 
> `stream_context_get_params`
> 
> `stream_context_set_default`
> 
> `stream_context_set_option`
> 
> `stream_context_set_params`
> 
> `stream_copy_to_stream`
> 
> `stream_encoding`
> 
> `stream_filter_append`
> 
> `stream_filter_prepend`
> 
> `stream_filter_register`
> 
> `stream_filter_remove`
> 
> `stream_get_contents`
> 
> `stream_get_filters`
> 
> `stream_get_line`
> 
> `stream_get_meta_data`
> 
> `stream_get_transports`
> 
> `stream_get_wrappers`
> 
> `stream_is_local`
> 
> `stream_notification_callback`
> 
> `stream_resolve_include_path`
> 
> `stream_select`
> 
> `stream_set_blocking`
> 
> `stream_set_chunk_size`
> 
> `stream_set_read_buffer`
> 
> `stream_set_timeout`
> 
> `stream_set_write_buffer`
> 
> `stream_socket_accept`
> 
> `stream_socket_client`
> 
> `stream_socket_enable_crypto`
> 
> `stream_socket_get_name`
> 
> `stream_socket_pair`
> 
> `stream_socket_recvfrom`
> 
> `stream_socket_sendto`
> 
> `stream_socket_server`
> 
> `stream_socket_shutdown`
> 
> `stream_supports_lock`
> 
> `stream_wrapper_register`
> 
> `stream_wrapper_restore`
> 
> `stream_wrapper_unregister`

## Strings

> `addcslashes`
> 
> `addslashes`
> 
> `bin2hex`
> 
> `chr`
> 
> `chunk_split`
> 
> `convert_cyr_string`
> 
> `convert_uudecode`
> 
> `convert_uuencode`
> 
> `count_chars`
> 
> `crc32`
> 
> `crypt`
> 
> `echo`
> 
> `explode`
> 
> `fprintf`
> 
> `get_html_translation_table`
> 
> `hebrev`
> 
> `hex2bin`
> 
> `html_entity_decode`
> 
> `htmlentities`
> 
> `htmlspecialchars`
> 
> `htmlspecialchars_decode`
> 
> `implode`
> 
> `lcfirst`
> 
> `levenshtein`
> 
> `localeconv`
> 
> `ltrim`
> 
> `md5`
> 
> `md5_file`
> 
> `metaphone`
> 
> `nl_langinfo`
> 
> `nl2br`
> 
> `number_format`
> 
> `ord`
> 
> `parse_str`
> 
> `printf`
> 
> `quoted_printable_decode`
> 
> `quoted_printable_encode`
> 
> `quotemeta`
> 
> `random_bytes`
> 
> `rtrim`
> 
> `setlocale`
> 
> `sha1`
> 
> `sha1_file`
> 
> `similar_text`
> 
> `soundex`
> 
> `sprintf`
> 
> `sscanf`
> 
> `str_getcsv`
> 
> `str_ireplace`
> 
> `str_pad`
> 
> `str_repeat`
> 
> `str_replace`
> 
> `str_rot13`
> 
> `str_shuffle`
> 
> `str_split`
> 
> `str_word_count`
> 
> `strcasecmp`
> 
> `strcmp`
> 
> `strcoll`
> 
> `strcspn`
> 
> `strip_tags`
> 
> `stripcslashes`
> 
> `stripos`
> 
> `stripslashes`
> 
> `stristr`
> 
> `strlen`
> 
> `strnatcasecmp`
> 
> `strnatcmp`
> 
> `strncasecmp`
> 
> `strncmp`
> 
> `strpbrk`
> 
> `strpos`
> 
> `strrchr`
> 
> `strrev`
> 
> `strripos`
> 
> `strrpos`
> 
> `strspn`
> 
> `strstr`
> 
> `strtok`
> 
> `strtolower`
> 
> `strtoupper`
> 
> `strtr`
> 
> `substr`
> 
> `substr_compare`
> 
> `substr_count`
> 
> `substr_replace`
> 
> `trim`
> 
> `ucfirst`
> 
> `ucwords`
> 
> `vfprintf`
> 
> `vprintf`
> 
> `vsprintf`
> 
> `wordwrap`

## URLs

> `base64_decode`
> 
> `base64_encode`
> 
> `get_headers`
> 
> `get_meta_tags`
> 
> `http_build_query`
> 
> `parse_url`
> 
> `rawurldecode`
> 
> `rawurlencode`
> 
> `urldecode`
> 
> `urlencode`

## Variables

> `debug_zval_dump`
> 
> `empty`
> 
> `floatval`
> 
> `get_defined_vars`
> 
> `get_resource_type`
> 
> `gettype`
> 
> `intval`
> 
> `is_array`
> 
> `is_bool`
> 
> `is_callable`
> 
> `is_float`
> 
> `is_int`
> 
> `is_null`
> 
> `is_numeric`
> 
> `is_object`
> 
> `is_resource`
> 
> `is_scalar`
> 
> `is_string`
> 
> `isset`
> 
> `print_r`
> 
> `serialize`
> 
> `settype`
> 
> `strval`
> 
> `unserialize`
> 
> `unset`
> 
> `var_dump`
> 
> `var_export`

## Zlib

> `deflate_add`
> 
> `deflate_init`
> 
> `inflate_add`
> 
> `inflate_init`

# Alphabetical Listing of PHP Functions

#### abs

`int abs(int` *number*`) float abs(float` *number*`)`

返回*number*的绝对值，类型（浮点数或整数）与参数相同。

#### acos

`float acos(float` *value*`)`

返回弧度中*value*的反余弦值。

#### acosh

`float acosh(float` *value*`)`

返回*value*的反双曲余弦值。

#### addcslashes

`string addcslashes(string` *string*`, string` *characters*`)`

返回*string*中*characters*的转义实例，通过在它们前面添加反斜杠来实现。您可以用两个句点分隔它们指定字符的范围，例如，要转义字符`a`到`q`之间的字符，请使用`"a..q"`。可以在*characters*中指定多个字符和范围。`addcslashes()`函数是`stripcslashes()`函数的反函数。

#### addslashes

`string addslashes(string` *string*`)`

返回在 SQL 数据库查询中具有特殊含义的*string*中的转义字符。单引号（`''`）、双引号（`""`）、反斜杠（`\`）和 NUL 字节（`\0`）都被转义。`stripslashes()`函数是该函数的反函数。

#### array_change_key_case

`array array_change_key_case(array` *array*`[, CASE_UPPER|CASE_LOWER])`

返回一个数组，其元素的键被更改为全部大写或全部小写。数字索引不变。如果省略可选的 case 参数，则将键更改为小写。

#### array_chunk

`array array_chunk(array` *array*`, int` *size*`[, int` *preserve_keys*`])`

将*array*分割成一系列包含*size*个元素的数组，并将它们作为数组返回。如果*preserve_keys*为`true`（默认为`false`），则结果数组保留原始键；否则，值按照从 0 开始的数字索引排序。

#### array_combine

`array array_combine(array` *keys*`, array` *values*`)`

返回一个数组，由*keys*数组中的每个元素作为键和*values*数组中的元素作为值创建。如果任一数组没有元素，两个数组的元素数量不同，或者一个数组中存在但在另一个数组中不存在元素，则返回`false`。

#### array_count_values

`array array_count_values(array` *array*`)`

返回一个数组，其元素的键是输入数组的值。每个键的值是该键作为值在输入数组中出现的次数。

#### array_diff

`array array_diff(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个数组，其中包含第一个数组中存在但其他提供的数组中不存在的所有值。保留值的键。

#### array_diff_assoc

`array array_diff_assoc(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个数组，其中包含第一个数组中存在但其他提供的数组中不存在的所有值。不同于`array_diff()`，必须同时匹配键和值才被视为相同。保留值的键。

#### array_diff_key

`array array_diff_key(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个数组，其中包含第一个数组中的键不在其他提供的数组中存在的所有值。保留值的键。

#### array_diff_uassoc

`array array_diff_uassoc(array` *array1*`, array` *array2* `[, ... array` *arrayN*`], callable` *function*`)`

返回一个数组，其中包含第一个数组中存在但其他提供的数组中不存在的所有值。不同于`array_diff()`，必须同时匹配键和值才被视为相同。函数*function*用于比较元素值的相等性。该函数使用两个参数调用——要比较的值。如果第一个参数小于第二个参数，则应返回小于零的整数；如果第一个参数等于第二个参数，则应返回`0`；如果第一个参数大于第二个参数，则应返回大于零的整数。保留值的键。

#### array_diff_ukey

`array array_diff_ukey(array` *array1*`, array` *array2* `[, ... array` *arrayN*`], callable` *function*`)`

返回一个数组，其中包含第一个数组中的键不在其他提供的数组中存在的所有值。函数*function*用于比较元素键的相等性。该函数使用两个参数调用——要比较的键。如果第一个参数小于第二个参数，则应返回小于零的整数；如果第一个和第二个参数相等，则应返回`0`；如果第一个参数大于第二个参数，则应返回大于零的整数。保留值的键。

#### array_fill

`array array_fill(int` *start*`, int` *count*`, mixed` *value*`)`

返回一个由*count*个元素组成的数组，每个元素的值为*value*。使用数值索引，从*start*开始，每个元素递增 1。如果*count*小于等于零，则会产生错误。

#### array_fill_keys

`array array_fill_keys(array` *keys*`, mixed` *value*`)`

返回一个数组，其中每个元素的键使用*keys*中的元素，并且每个元素的值为*value*。

#### array_filter

`array array_filter(array` *array*`, mixed` *callback*`)`

创建一个数组，其中包含原始数组中给定回调函数返回`true`的所有值。如果输入数组是关联数组，则保留键。例如：

```php
function isBig($inValue)
{
 return($inValue > 10);
}

$array = array(7, 8, 9, 10, 11, 12, 13, 14);
$newArray = array_filter($array, "isBig"); // contains (11, 12, 13, 14)
```

#### array_flip

`array array_flip(array` *array*`)`

返回一个数组，其中元素的键是原始数组的值，反之亦然。如果找到多个值，则保留遇到的最后一个。如果原始数组中的任何值除了字符串和整数之外的任何类型，`array_flip()`将发出警告，并且将不包括问题中的键值对在结果中。`array_flip()`在失败时返回`NULL`。

#### array_intersect

`array array_intersect(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个由*array1*中每个元素也存在于每个其他数组中的元素组成的数组。

#### array_intersect_assoc

`array array_intersect_assoc(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个包含所有给定数组中所有值的数组。与`array_intersect()`不同的是，必须同时匹配键和值才能被视为相同。值的键被保留。

#### array_intersect_key

`array array_intersect_key(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个由*array1*中每个键也存在于每个其他数组中的元素组成的数组。

#### array_intersect_uassoc

`array array_intersect_uassoc(array` *array1*`, array` *array2* `[, ... array` *arrayN*`], callable` *function*`)`

返回一个包含所有给定数组中所有值的数组。

函数*function*用于比较元素的键是否相等。该函数使用两个参数调用——要比较的值。如果第一个参数小于第二个参数，则返回小于零的整数，如果第一个和第二个参数相等，则返回`0`，如果第一个参数大于第二个参数，则返回大于零的整数。值的键被保留。

#### array_intersect_ukey

`array array_intersect_ukey(array` *array1*`, array` *array2* `[, ... array` *arrayN*`], callable` *function*`)`

返回一个由*array1*中每个键也存在于每个其他数组中的元素组成的数组。

函数*function*用于比较元素的值是否相等。该函数使用两个参数调用——要比较的键。如果第一个参数小于第二个参数，则返回小于零的整数，如果第一个和第二个参数相等，则返回`0`，如果第一个参数大于第二个参数，则返回大于零的整数。

#### array_key_exists

`bool array_key_exists(mixed` *key*`, array` *array*`)`

如果*array*包含具有值*key*的键，则返回`true`。如果没有这样的键可用，则返回`false`。

#### array_keys

`array array_keys(array` *array*`[, mixed` *value*`[, bool` *strict*`]])`

返回一个包含给定数组中所有键的数组。如果提供了第二个参数，则只返回其值与*value*匹配的键。如果指定了*strict*并且为`true`，则仅当匹配元素与*value*的类型和值相同时才返回。

#### array_map

`array array_map(mixed` *callback*`, array` *array1*`[, ... array` *arrayN*`])`

通过将第一个参数中引用的回调函数应用于剩余参数（提供的数组）来创建数组；回调函数应该接受与传递给 `array_map()` 的数组数目相等的值作为参数。例如：

```php
function multiply($inOne, $inTwo) {
 return $inOne * $inTwo;
}
$first = (1, 2, 3, 4);
$second = (10, 9, 8, 7);
$array = array_map("multiply", $first, $second); // contains (10, 18, 24, 28)
```

#### array_merge

`array array_merge(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回通过将每个提供的数组的元素附加到前一个数组的数组。如果任何数组具有相同的字符串键的值，则返回数组中遇到的键的最后一个值；任何具有相同数值键的元素都插入到结果数组中。

#### array_merge_recursive

`array array_merge_recursive(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

与 `array_merge()` 类似，通过将每个输入数组附加到前一个数组来创建并返回数组。但与 `array_merge()` 不同的是，当多个元素具有相同的字符串键时，将包含每个值的数组插入到结果数组中。

#### array_multisort

`bool array_multisort(array` *array1*`[, SORT_ASC|SORT_DESC [, SORT_REGULAR|SORT_NUMERIC|SORT_STRING]] [, array` *array2*`[, SORT_ASC|SORT_DESC [, SORT_REGULAR|SORT_NUMERIC|SORT_STRING]], ...])`

用于同时对几个数组进行排序，或者对多维数组的一维或多维进行排序。输入数组被视为表中的列，按行排序——第一个数组是主要排序。根据该排序相同的任何值按照下一个输入数组进行排序，依此类推。

第一个参数是一个数组；随后，每个参数可以是一个数组或以下排序标志之一（排序标志用于更改排序的默认顺序）：

| `SORT_ASC`（默认） | 按升序排序 |
| --- | --- |
| `SORT_DESC` | 按降序排序 |

然后，可以指定以下列表中的排序类型：

| `SORT_REGULAR`（默认） | 普通比较项目 |
| --- | --- |
| `SORT_NUMERIC` | 按数值比较项目 |
| `SORT_STRING` | 按字符串比较项目 |

排序标志仅适用于紧接在前面的数组，并在每个新数组参数之前恢复为 `SORT_ASC` 和 `SORT_REGULAR`。

如果操作成功则返回 `true`，否则返回 `false`。

#### array_pad

`array array_pad(array` *input*`, int` *size*`[, mixed` *padding*`])`

返回输入数组的副本，填充到由 *size* 指定的长度。添加到数组的任何新元素都具有可选的第三个值的值。通过指定负数大小，可以将元素添加到数组的开头——在这种情况下，数组的新大小是大小的绝对值。

如果数组已经具有指定数量的元素或更多，则不进行填充，并返回原始数组的精确副本。

#### array_pop

`mixed array_pop(array &`*stack*`)`

从给定数组中移除最后一个值并返回它。如果数组为空（或参数不是数组），则返回 `NULL`。请注意，提供的数组上的数组指针将被重置。

#### array_product

`number array_product(array` *array*`)`

返回 *array* 中每个元素的乘积。如果 *array* 中的每个值都是整数，则结果乘积为整数；否则，结果乘积为浮点数。

#### array_push

`int array_push(array &`*array*`, mixed` *value1*`[, ... mixed` *valueN*`])`

将给定值添加到第一个参数指定的数组末尾，并返回数组的新大小。对于列表中的每个值，执行与调用 `$array[] = $value` 相同的功能。

#### array_rand

`mixed array_rand(array` *array*`[, int` *count*`])`

从给定数组中随机选择一个元素。第二个（可选）参数可以给出以指定要选择和返回的元素数量。如果返回多个元素，则返回键的数组，而不是元素的值。

#### array_reduce

`mixed array_reduce(array` *array*`, mixed` *callback*`[, int` *initial*`])`

通过迭代调用给定的回调函数和数组中的值对，派生出一个值。如果提供了第三个参数，则将其与数组中的第一个元素一起传递给回调函数进行初始调用。

#### array_replace

`array array_replace(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个由替换值中的值替换 *array1* 中的值而创建的数组。 *array1* 中与替换数组中的键匹配的元素将被这些元素的值替换。

如果提供了多个替换数组，则按顺序处理。保留在 *array1* 中的任何键不匹配替换数组中任何键的元素。

#### array_replace_recursive

`array array_replace_recursive(array` *array1*`, array` *array2*`[, ... array` *arrayN*`])`

返回一个由替换值中的值替换 *array1* 中的值而创建的数组。 *array1* 中与替换数组中的键匹配的元素将被这些元素的值替换。

如果 *array1* 和特定键的替换数组中的值都是数组，则使用相同的过程递归合并这些数组中的值。

如果提供了多个替换数组，则按顺序处理。保留在 *array1* 中的任何键不匹配替换数组中任何键的元素。

#### array_reverse

`array array_reverse(array` *array*`[, bool` *preserve_keys*`])`

返回一个包含与输入数组相同元素的数组，但其顺序相反。如果 `preserve_keys` 设置为 `true`，则保留数值键。非数值键不受此参数影响，始终保留。

#### array_search

`mixed array_search(mixed` *value*`, array` *array*`[, bool` *strict*`])`

执行在数组中搜索值的操作，类似于 `in_array()`。如果找到值，则返回匹配元素的键；如果未找到值，则返回 `NULL`。如果指定了 *strict* 并且为 `true`，则仅当匹配的元素与 *value* 的类型和值相同时才返回。

#### array_shift

`mixed array_shift(array` *stack*`)`

类似于 `array_pop()`，但不是移除并返回数组中的最后一个元素，而是移除并返回数组中的第一个元素。如果数组为空，或者参数不是数组，则返回 `NULL`。

#### array_slice

`array array_slice(array` *array*`, int` *offset*`[, int` *length*`][, bool keepkeys])`

返回一个包含从给定数组中提取的一组元素的数组。如果 *offset* 是正数，则使用从该索引开始的元素；如果 *offset* 是负数，则使用从数组末尾开始的那么多元素。如果提供了第三个参数并且是正数，则返回这么多元素；如果是负数，则从数组末尾开始计算停止这么多元素。如果省略了第三个参数，则返回包含从 *offset* 到数组末尾的所有元素的序列。如果 `keepkeys`，即第四个参数为 `true`，则保留数字键的顺序；否则，它们将被重新编号和重新排序。

#### array_splice

`array array_splice(array` *array*`, int` *offset*`[, int` *length*`[, array` *replacement*`]])`

使用与`array_slice()`相同的规则选择一系列元素，但不返回这些元素，而是将它们删除或者如果提供了第四个参数，则替换为该数组。返回一个包含已删除（或替换）元素的数组。

#### array_sum

`number array_sum(array` *array*`)`

返回数组中每个元素的总和。如果所有的值都是整数，则返回整数。如果任何值是浮点数，则返回浮点数。

#### array_udiff

`array array_udiff(array` *array1*`, array` *array2*`[, ... array` *arrayN*`], string` *function*`)`

返回一个包含 *array1* 中所有未出现在任何其他数组中的值的数组。只使用值来检查相等性；即 `"a" => 1` 和 `"b" => 1` 被视为相等。函数 *function* 用于比较元素的值是否相等。该函数使用两个参数——要比较的值。如果第一个参数小于第二个参数，则返回小于零的整数；如果第一个和第二个参数相等，则返回 `0`；如果第一个参数大于第二个参数，则返回大于零的整数。保留值的键。

#### array_udiff_assoc

`array array_udiff_assoc(array` *array1*`, array` *array2* `[, ... array` *arrayN*`], string` *function*`)`

返回一个包含所有在*array1*中不存在于任何其他数组中的值的数组。键和值都用于检查相等性；即`"a" => 1`和`"b" => 1`不被认为是相等的。函数*function*用于比较元素的值是否相等。该函数使用两个参数调用——要比较的值。如果第一个参数小于第二个参数，则应返回小于零的整数，如果第一个和第二个参数相等，则应返回`0`，如果第一个参数大于第二个参数，则应返回大于零的整数。值的键将被保留。

#### array_udiff_uassoc

`array array_udiff_uassoc(array` *array1*`, array` *array2*`[, ... array` *arrayN*`], string` *function1*`, string` *function2*`)`

返回一个包含所有在*array1*中不存在于任何其他数组中的值的数组。键和值都用于检查相等性；即`"a" => 1`和`"b" => 1`不被认为是相等的。函数*function1*用于比较元素的值是否相等。函数*function2*用于比较键的值是否相等。每个函数使用两个参数调用——要比较的值。如果第一个参数小于第二个参数，则应返回小于零的整数，如果第一个和第二个参数相等，则应返回`0`，如果第一个参数大于第二个参数，则应返回大于零的整数。值的键将被保留。

#### array_uintersect

`array array_uintersect(array` *array1*`, array` *array2* `[, ... array` *arrayN*`], string` *function*`)`

返回一个包含所有在*array1*中存在于所有其他数组中的值的数组。仅使用值来检查相等性；即`"a" => 1`和`"b" => 1`被认为是相等的。函数*function*用于比较元素的值是否相等。该函数使用两个参数调用——要比较的值。如果第一个参数小于第二个参数，则应返回小于零的整数，如果第一个和第二个参数相等，则应返回`0`，如果第一个参数大于第二个参数，则应返回大于零的整数。值的键将被保留。

#### array_uintersect_assoc

`array array_uintersect_assoc(array` *array1*`, array` *array2*`[, ... array` *arrayN*`], string` *function*`)`

返回一个包含所有在*array1*中存在于所有其他数组中的值的数组。键和值都用于检查相等性；即`"a" => 1`和`"b" => 1`不被认为是相等的。函数*function*用于比较元素的值是否相等。该函数使用两个参数调用——要比较的值。如果第一个参数小于第二个参数，则应返回小于零的整数，如果第一个和第二个参数相等，则应返回`0`，如果第一个参数大于第二个参数，则应返回大于零的整数。值的键将被保留。

#### array_uintersect_uassoc

`array array_uintersect_uassoc(array` *array1*`, array` *array2*`[, ... array` *arrayN*`], string` *function1*`, string` *function2*`)`

返回一个包含第一个数组中所有其他数组中也存在的值的数组。同时使用键和值来检查相等性；即，`"a" => 1`和`"b" => 1`不被视为相等。使用*function1*函数比较元素的值相等性。使用*function2*函数比较键的值相等性。每个函数都接收两个参数——要比较的值。如果第一个参数小于第二个参数，则返回小于零的整数，如果第一个和第二个参数相等，则返回`0`，如果第一个参数大于第二个参数，则返回大于零的整数。保留值的键。

#### array_unique

`array array_unique(array` *array*`[, int sort_flags])`

创建并返回一个包含给定数组中每个元素的数组。如果有重复的值，则忽略后续值。可使用`sort_flags`可选参数以常量`SORT_REGULAR`、`SORT_NUMERIC`、`SORT_STRING`（默认值）和`SORT_LOCALE_STRING`改变排序方法。保留原始数组的键。

#### array_unshift

`int array_unshift(array` *stack*`, mixed` *value1*`[, ... mixed` *valueN*`])`

返回给定数组的副本，并将额外的参数添加到数组的开头；添加的元素作为一个整体添加，因此数组中的元素与参数列表中的元素按照它们在列表中出现的顺序相同。返回新数组中的元素数目。

#### array_values

`array array_values(array` *array*`)`

返回一个包含输入数组中所有值的数组。不保留这些值的键。

#### array_walk

`bool array_walk(array` *input*`, string` *callback*`[, mixed` *user_data*`])`

对数组中的每个元素调用命名函数。将以元素的值、键和可选用户数据作为参数调用该函数。为确保函数直接作用于数组的值，请通过引用定义函数的第一个参数。成功时返回`true`，失败时返回`false`。

#### array_walk_recursive

`bool array_walk_recursive(array` *input*`, string` *function*`[, mixed` *user_data*`])`

类似于`array_walk()`，对数组中的每个元素调用命名函数。与`array_walk()`不同的是，如果元素的值是数组，则也会为该数组中的每个元素调用该函数。将以元素的值、键和可选用户数据作为参数调用该函数。为确保函数直接作用于数组的值，请通过引用定义函数的第一个参数。成功时返回`true`，失败时返回`false`。

#### arsort

`bool arsort(array` *array*`[, int` *flags*`])`

对数组按相反的顺序进行排序，保持数组值的键。可选的第二个参数包含额外的排序标志。成功时返回`true`，失败时返回`false`。有关使用此函数的更多信息，请参见第五章和`sort`。

#### asin

`float asin(float` *value*`)`

返回以弧度表示的*value*的反正弦。

#### asinh

`float asinh(float` *value*`)`

返回*value*的反双曲正弦。

#### asort

`bool asort(array` *array*`[, int` *flags*`])`

对数组进行排序，保持数组值的键。可选的第二个参数包含额外的排序标志。成功时返回`true`，失败时返回`false`。有关使用此函数的更多信息，请参见第五章和`sort`。

#### assert

`bool assert(string|bool` *assertion*`[, string description])`

如果*assertion*为`true`，则在执行代码时生成警告。如果*assertion*是一个字符串，则`assert()`将该字符串作为 PHP 代码进行评估。可选的第二个参数允许在失败消息中添加额外的文本。查看`assert_options()`函数以查看其相关连接。

#### assert_options

`mixed assert_options(int` *option*`[, mixed` *value*`])`

如果指定了*value*，则将断言控制选项*option*设置为*value*并返回先前的设置。如果未指定*value*，则返回*option*的当前值。*option*的以下值是允许的：

| `ASSERT_ACTIVE` | 启用断言 |
| --- | --- |
| `ASSERT_WARNING` | 生成警告以显示断言 |
| `ASSERT_BAIL` | 在断言上终止脚本的执行 |
| `ASSERT_QUIET_EVAL` | 在评估传递给`assert()`函数的断言代码时禁用错误报告 |
| `ASSERT_CALLBACK` | 调用指定的用户函数处理断言。断言回调以三个参数调用：文件、行和断言失败的表达式 |

#### atan

`float atan(float` *value*`)`

返回以弧度表示的*value*的反正切。

#### atan2

`float atan2(float` *y*`, float` *x*`)`

使用两个参数的符号确定值所在的象限，返回以弧度表示的*x*和*y*的反正切。

#### atanh

`float atanh(float` *value*`)`

返回*value*的反双曲正切。

#### base_convert

`string base_convert(string` *number*`, int` *from*`, int` *to*`)`

将*number*从一种基数转换为另一种基数。当前数字所在的基数是*from*，要转换的基数是*to*。要从和转换的基数必须在 2 到 36 之间。在大于 10 的基数中，使用字母`a`（10）到`z`（35）表示数字。可以转换的最大 32 位数字或 10 进制 2,147,483,647。

#### base64_decode

`string base64_decode(string` *data*`)`

将经过 base-64 编码的数据*data*解码为字符串（可能包含二进制数据）。有关 base-64 编码的更多信息，请参见 RFC 2045。

#### base64_encode

`string base64_encode(string` *data*`)`

返回*data*的 Base64 编码版本。MIME Base64 编码旨在允许二进制或其他 8 位数据在通过可能不安全的 8 位协议（如电子邮件消息）时保持完整性。

#### basename

`string basename(string *path*[, string *suffix*])`

从完整路径*path*中返回文件名组件。如果文件名以*suffix*结尾，则从名称中删除该字符串。例如：

```php
$path = "/usr/local/httpd/index.html";
echo(basename($path)); // index.html
echo(basename($path, '.html')); // index
```

#### bin2hex

`string bin2hex(string *binary*)`

将*binary*转换为十六进制（基数 16）值。可以转换为 32 位数，或者十进制的 2,147,483,647。

#### bindec

`number bindec(string *binary*)`

将*binary*转换为十进制值。可以转换为 32 位数，或者十进制的 2,147,483,647。

#### call_user_func

`mixed call_user_func(string *function*[, mixed *parameter1*[, ... mixed *parameterN*]])`

调用第一个参数中给出的函数。额外的参数在调用函数时使用。检查匹配函数时不区分大小写。返回函数返回的值。

#### call_user_func_array

`mixed call_user_func_array(string *function*, array *parameters*)`

类似于`call_user_func()`，此函数调用名为*function*的函数，并使用数组*parameters*中的参数进行调用。检查匹配函数时不区分大小写。返回函数返回的值。

#### ceil

`float ceil(float *number*)`

返回比*number*大的下一个最高值，如果需要则向上舍入。

#### chdir

`bool chdir(string *path*)`

将当前工作目录设置为*path*；如果操作成功返回`true`，否则返回`false`。

#### checkdate

`bool checkdate(int *month*, int *day*, int *year*)`

如果参数中给出的月份、日期和年份（公历）有效，则返回`true`，否则返回`false`。如果年份在 1 到 32,767 之间（包括边界），月份在 1 到 12 之间（包括边界），并且日期在指定月份的有效日期范围内（包括闰年）。

#### checkdnsrr

`bool checkdnsrr(string *host*[, string *type*])`

搜索具有给定类型的主机的 DNS 记录。如果找到任何记录，则返回`true`，否则返回`false`。主机类型可以采用以下任何值（如果未指定值，则默认为`MX`）：

| `A` | IP 地址 |
| --- | --- |
| `MX` (默认) | 邮件交换器 |
| `NS` | 名称服务器 |
| `SOA` | 权威起始 |
| `PTR` | 指向信息的指针 |
| `CNAME` | 规范名称 |
| `AAAA` | 128 位 IPv6 地址 |
| `A6` | 定义为早期 IPv6 的一部分，但降级为实验性 |
| `SRV` | 通用服务位置记录 |
| `NAPTR` | 基于正则表达式的域名重写 |
| `TXT` | 最初用于人类可读的文本。但是，这个记录也携带机器可读的数据。 |
| `ANY` | 上述任何一种 |

查看更多详情，请访问[Wikipedia 上的 DNS 记录条目](http://en.wikipedia.org/wiki/List_of_DNS_record_types)。

#### chgrp

`bool chgrp(string` *path*`, mixed` *group*`)`

将文件*path*的组更改为*group*；PHP 必须具有适当的权限才能使该函数工作。如果更改成功，则返回`true`，否则返回`false`。

#### `chmod`

`bool chmod(string` *path*`, int` *mode*`)`

尝试将*path*的权限更改为*mode*。*mode*应为八进制数，例如`0755`。像`755`这样的整数值或`"u+x"`这样的字符串值将不能按预期工作。如果操作成功，则返回`true`，否则返回`false`。

#### `chown`

`bool chown(string` *path*`, mixed` *user*`)`

将文件*path*的所有权更改为名为*user*的用户。PHP 必须具有适当的权限（通常为 root）才能执行此功能。如果更改成功，则返回`true`，否则返回`false`。

#### `chr`

`string chr(int` *char*`)`

返回由单个 ASCII 字符*char*组成的字符串。

#### `chroot`

`bool chroot(string` *path*`)`

将当前进程的根目录更改为*path*。在 Web 服务器环境中运行 PHP 时，无法使用`chroot()`将根目录恢复为`/`。如果更改成功，则返回`true`，否则返回`false`。

#### `chunk_split`

`string chunk_split(string` *string*`[, int` *size*`[, string` *postfix*`]])`

将*postfix*插入到*string*中，每隔*size*个字符插入一次，并在字符串末尾插入；返回生成的字符串。如果未指定，则*postfix*默认为`\r\n`，*size*默认为`76`。此函数最适合将数据编码为 RPF 2045 标准。例如：

```php
$data = "...some long data...";
$converted = chunk_split(base64_encode($data));
```

#### `class_alias`

`bool class_alias(string` *name*`, string` *alias*`)`

创建到类*name*的别名。从此之后，您可以使用*name*或*alias*引用类（例如实例化对象）。如果可以创建别名，则返回`true`；否则返回`false`。

#### `class_exists`

`bool class_exists(string` *name*`[, bool` *autoload_class*`])`

如果具有与字符串同名的类已定义，则返回`true`；否则返回`false`。类名比较不区分大小写。如果设置了并且为`true`，则会通过类的`__autoload()`函数加载类，然后获取其实现的接口。

#### `class_implements`

`array class_implements(mixed` *class*`[, bool` *autoload_class*`])`

如果*class*是对象，则返回包含由*class*对象类实现的接口名称的数组。如果*class*是字符串，则返回包含由名为*class*的类实现的接口名称的数组。如果*class*既不是对象也不是字符串，或者*class*是字符串但不存在该名称的对象类，则返回`false`。如果设置了并且为`true`，则会通过类的`__autoload()`函数加载类，然后获取其实现的接口。

#### `class_parents`

`array class_parents(mixed` *class*`[, bool` *autoload_class*`])`

如果*class* 是一个对象，则返回一个包含*class* 对象类的父类名称的数组。如果*class* 是一个字符串，则返回一个包含命名为*class* 的类的父类类名的数组。如果*class* 既不是对象也不是字符串，或者*class* 是一个字符串但没有该名称的对象类存在，则返回`false`。如果设置了*autoload_class* 并且为`true`，则在获取其父类之前通过类的`__autoload()` 函数加载该类。

#### clearstatcache

`void clearstatcache([bool` *clear_realpath_cache*`[, string` *file*`]])`

清除文件状态函数的缓存。下一次调用任何文件状态函数将从磁盘检索信息。*clear_realpath_cache* 参数允许清除*realpath* 缓存。`file` 参数仅允许清除特定文件名的*realpath* 和 stat 缓存，并且仅在*clear_realpath_cache* 为 `true` 时可用。

#### closedir

`void closedir([int` *handle*`])`

关闭由*handle* 引用的目录流。有关目录流的更多信息，请参见`opendir()`。如果未指定*handle*，则关闭最近打开的目录流。

#### closelog

`int closelog()`

在调用`openlog()`后关闭用于写入系统日志的文件描述符。如果更改成功则返回`true`，否则返回`false`。

#### compact

`array compact(mixed` *variable1*`[, ... mixed` *variableN*`])`

通过检索参数中命名的变量的值来创建数组。如果任何参数是数组，则还会检索数组中命名的变量的值。返回的数组是一个关联数组，其键是函数提供的参数，值是命名变量的值。此函数是`extract()`的相反函数。

#### connection_aborted

`int connection_aborted()`

如果客户端在函数调用之前断开连接（例如在浏览器中点击停止），则返回`true`（`1`）。如果客户端仍然连接，则返回`false`（`0`）。

#### connection_status

`int connection_status()`

以三个状态的位域形式返回连接的状态：`NORMAL`（`0`）、`ABORTED`（`1`）和`TIMEOUT`（`2`）。

#### constant

`mixed constant(string` *name*`)`

返回称为*name*的常量的值。

#### convert_cyr_string

`string convert_cyr_string(string` *value*`, string` *from*`, string` *to*`)`

将*value*从一个西里尔语集转换为另一个。*from* 和 *to* 参数是表示集的单字符字符串，并具有以下有效值：

| `k` | koi8-r |
| --- | --- |
| `w` | Windows-1251 |
| `i` | ISO 8859-5 |
| `a` 或 `d` | x-cp866 |
| `m` | x-mac-cyrillic |

#### convert_uudecode

`string convert_uudecode(string` *value*`)`

解码*uuencode*的字符串*value*并返回它。

#### convert_uuencode

`string convert_uuencode(string` *value*`)`

使用`uuencode`对字符串*value*进行编码并返回。

#### copy

`int copy(string` *path*`, string` *destination*`[, resource` *context*`])`

将路径*path*的文件复制到*destination*。如果操作成功，函数返回`true`；否则，返回`false`。如果目标位置的文件已存在，则将其替换。可选的*context*参数可以利用使用`stream_context_create()`函数创建的有效上下文资源。

#### cos

`float cos(float` *value*`)`

返回弧度中*value*的余弦值。

#### cosh

`float cosh(float` *value*`)`

返回*value*的双曲余弦值。

#### count

`int count(mixed` *value*`[, int` *mode*`])`

返回*value*中的元素数；对于数组或对象，这是元素的数量；对于任何其他*value*，这是`1`。如果参数是变量且变量未设置，则返回`0`。如果设置了*mode*并且为`COUNT_RECURSIVE`，则递归计算元素数，计算数组内部数组中的值的数量。

#### count_chars

`mixed count_chars(string` *string*`[, int` *mode*`])`

返回*string*中从 0 到 255 的每个字节值的出现次数；*mode*确定结果的形式。*mode*的可能值为：

| `0` (default) | 返回一个关联数组，其中每个字节值作为键，该字节值的频率作为值 |
| --- | --- |
| `1` | 与上述相同，但仅列出非零频率的字节值 |
| `2` | 与上述相同，但仅列出频率为零的字节值 |
| `3` | 返回一个包含所有非零频率字节值的字符串 |
| `4` | 返回一个包含所有字节值频率为零的字符串 |

#### crc32

`int crc32(string` *value*`)`

计算并返回*value*的循环冗余校验（CRC）。

#### create_function

`string create_function(string` *arguments*`, string` *code*`)`

使用给定的*arguments*和*code*创建一个匿名函数；返回函数的生成名称。这种匿名函数（也称为*lambda 函数*）对于短期回调函数非常有用，例如在使用`usort()`时。

#### crypt

`string crypt(string` *string*`[, string` *salt*`])`

使用带有两个字符盐值*salt*的 DES 加密算法对*string*进行加密。如果未提供*salt*，则在脚本中首次调用`crypt()`时生成一个随机*salt*值；此值用于后续调用`crypt()`。返回加密后的字符串。

#### current

`mixed current(array` *array*`)`

返回内部指针设置的元素的值。第一次调用`current()`时，或在`reset`后调用`current()`时，将指针设置为数组中的第一个元素。

#### date

`string date(string` *format*`[, int` *timestamp*`])`

根据第一个参数中提供的*format*字符串格式化时间和日期。如果未指定第二个参数，则使用当前时间和日期。*format*字符串中识别以下字符：

| `a` | “am”或“pm” |
| --- | --- |
| `A` | “AM”或“PM” |
| `B` | 斯沃奇互联网时间 |
| `d` | 月份中的日期，如果需要则包括前导零（例如，“01”到“31”） |
| `D` | 星期几的三字母缩写（例如，“Mon”） |
| `F` | 月份的全称（例如，“August”） |
| `g` | 12 小时制的小时数（例如，“1”到“12”） |
| `G` | 24 小时制的小时数（例如，“0”到“23”） |
| `h` | 12 小时制的小时数，如果需要则包括前导零（例如，“01”到“12”） |
| `H` | 24 小时制的小时数，如果需要则包括前导零（例如，“00”到“23”） |
| `i` | 分钟数，如果需要则包括前导零（例如，“00”到“59”） |
| `I` | 如果是夏令时则为“1”，否则为“0” |
| `j` | 月份中的日期（例如，“1”到“31”） |
| `l` | 星期几的全称（例如，“Monday”） |
| `L` | 如果年份不是闰年则为“0”，如果是则为“1” |
| `m` | 月份，如果需要则包括前导零（例如，“01”到“12”） |
| `M` | 月份的三字母缩写（例如，“Aug”） |
| `n` | 无前导零的月份（例如，“1”到“12”） |
| `r` | 根据 RFC 822 格式化的日期（例如，“Thu, 21 Jun 2001 21:27:19 +0600”） |
| `s` | 秒数，如果需要则包括前导零（例如，“00”到“59”） |
| `S` | 日期月份的英文序数后缀，“st”、“nd”或“th” |
| `t` | 月份的天数，从“28”到“31” |
| `T` | 运行 PHP 的机器的时区设置（例如，“MST”） |
| `u` | 自 Unix 纪元以来的秒数 |
| `w` | 数字形式的星期几，从星期天开始（例如，“0”代表星期日） |
| `W` | ISO 8601 标准下的年的第几周 |
| `Y` | 四位数年份（例如，“1998”） |
| `y` | 两位数年份（例如，“98”） |
| `z` | 一年中的第几天，从“0”到“365” |
| `Z` | 时区偏移量（从“–43200”（UTC 的远西）到“43200”（UTC 的远东）） |

*format*字符串中不匹配上述任何内容的任何字符将保留在结果字符串中。如果为`timestamp`提供了非数字值，则返回`false`并发出警告。

#### `date_default_timezone_get`

`string date_default_timezone_get()`

返回当前默认时区，之前由`date_default_timezone_set()`函数或在*php.ini*文件中通过`date.timezone`选项设置。如果都没有设置，则返回`"UTC"`。

#### `date_default_timezone_set`

`string date_default_timezone_set(string` *timezone*`)`

设置当前默认时区。

#### `date_parse`

`array date_parse(string` *time*`)`

将时间和日期的英文描述转换为描述该时间和日期的数组。如果值无法转换为有效日期，则返回`false`。返回的数组包含从`date_parse_from_format()`返回的相同值。

#### `date_parse_from_format`

`array date_parse_from_format(string` *format*`, string` *time*`)`

将 *time* 解析为表示日期的关联数组。*time* 字符串的格式由 *format* 指定，使用与 `date()` 中描述的相同字符代码。返回的数组包含以下条目：

| `year` | 年份 |
| --- | --- |
| `month` | 月份 |
| `day` | 月份中的天数 |
| `hour` | 小时 |
| `minute` | 分钟 |
| `second` | 秒 |
| `fraction` | 秒的小数部分 |
| `warning_count` | 解析过程中发生的警告数量 |
| `warnings` | 解析过程中发生的警告数组 |
| `error_count` | 解析过程中发生的错误数量 |
| `errors` | 解析过程中发生的错误数组 |
| `is_localtime` | 如果时间表示当前默认时区的时间，则为 True |
| `zone_type` | `zone` 表示的时区类型 |
| `zone` | 时间所在的时区 |
| `is_dst` | 如果时间表示夏令时，则为 True |

#### date_sun_info

`array date_sun_info(int` *timestamp*`, float` *latitude*`, float` *longitude*`)`

返回给定纬度和经度的日出和日落时间以及黄昏开始和结束时间的关联数组信息。结果数组包含以下键：

| `sunrise` | 日出时间 |
| --- | --- |
| `sunset` | 日落时间 |
| `transit` | 太阳处于天顶的时间 |
| `civil_twilight_begin` | 民用黄昏开始时间 |
| `civil_twilight_end` | 民用黄昏结束时间 |
| `nautical_twilight_begin` | 航海黄昏开始时间 |
| `nautical_twilight_end` | 航海黄昏结束时间 |
| `astronomical_twilight_begin` | 天文黄昏开始时间 |
| `astronomical_twilight_end` | 天文黄昏结束时间 |

#### 日出时间

`mixed date_sunrise(int` *timestamp*`[, int` *format*`[, float` *latitude*`[, float` *longitude* `[, float` *zenith*`[, float` *gmt_offset*`]]]])`

返回给定 *timestamp* 的当天日出时间；失败时返回 `false`。*format* 参数确定时间的返回格式（默认为 `SUNFUNCS_RET_STRING`），而 *latitude*、*longitude*、*zenith* 和 *gmt_offset* 参数提供特定位置的信息。它们默认为 PHP 配置选项中给出的值（*php.ini*）。参数包括：

| `SUNFUNCS_RET_STRING` | 返回字符串值；例如，“06:14” |
| --- | --- |
| `SUNFUNCS_RET_DOUBLE` | 返回浮点数值；例如，6.233 |
| `SUNFUNCS_RET_TIMESTAMP` | 返回 Unix 时间戳 |

#### 日落时间

`mixed date_sunset(int` *timestamp*`[, int` *format*`[, float` *latitude*`[, float` *longitude* `[, float` *zenith*`[, float` *gmt_offset*`]]]])`

返回当天日落的时间戳；失败时返回 `false`。*format* 参数决定返回时间的格式（默认为 `SUNFUNCS_RET_STRING`），而 *latitude*、*longitude*、*zenith* 和 *gmt_offset* 参数提供特定位置的信息。它们默认使用 PHP 配置选项（*php.ini*）中给出的值。参数包括：

| `SUNFUNCS_RET_STRING` | 返回字符串形式的值；例如，“19:02” |
| --- | --- |
| `SUNFUNCS_RET_DOUBLE` | 返回浮点数形式的值；例如，19.033 |
| `SUNFUNCS_RET_TIMESTAMP` | 返回 Unix 时间戳形式的值 |

#### debug_backtrace

`array debug_backtrace([ int` *options* `[, int` *limit*`]])`

返回包含 PHP 当前执行位置的关联数组的数组。每个函数或文件包含都包含一个元素，具有以下元素：

| `function` | 如果在函数中，则为函数的名称作为字符串 |
| --- | --- |
| `line` | 当前函数或文件包含所在文件中的行号 |
| `file` | 元素所在文件的名称 |
| `class` | 如果在对象实例或类方法中，则为元素所在的类的名称 |
| `object` | 如果在对象中，则为该对象的名称 |
| `type` | 当前调用类型：如果是静态方法，则为 `::`；如果是方法，则为 `->`；如果是函数，则为空 |
| `args` | 如果在函数中，则为调用该函数时使用的参数；如果在文件包含中，则为包含文件的名称 |

每次函数调用或文件包含都会在数组中生成一个新元素。最内层的函数调用或文件包含的索引为 0；更深层次的元素为较浅的函数调用或文件包含。

#### debug_print_backtrace

`void debug_print_backtrace()`

打印当前的调试回溯（参见 `debug_backtrace`）到客户端。

#### decbin

`string decbin(int` *decimal*`)`

将提供的 *decimal* 值转换为其二进制表示形式。最多可以转换为 32 位数字，或 2,147,483,647 十进制。

#### dechex

`string dechex(int` *decimal*`)`

将 *decimal* 转换为其十六进制（base-16）表示。最多可以转换为 32 位数字，或 2,147,483,647 十进制（0x7FFFFFFF 十六进制）。

#### decoct

`string decoct(int` *decimal*`)`

将 *decimal* 转换为其八进制（base-8）表示。最多可以转换为 32 位数字，或 2,147,483,647 十进制（017777777777 八进制）。

#### define

`bool define(string` *name*`, mixed` *value*`[, int` *case_insensitive*`])`

定义一个名为 *name* 的常量，并将其值设置为 *value*。如果 *case_insensitive* 设置为 `true`，则如果先前定义了与该名称（不区分大小写比较）相同的常量，则操作失败。否则，将区分大小写地检查现有的常量。如果常量可以创建，则返回 `true`，如果已存在具有给定名称的常量，则返回 `false`。

#### define_syslog_variables

`void define_syslog_variables()`

初始化由`openlog()`、`syslog()`和`closelog()`使用的所有变量和常量。在使用任何 syslog 函数之前应调用此函数。

#### defined

`bool defined(string` *name*`)`

如果存在一个名为*name*的常量则返回`true`，否则返回`false`。

#### deflate_add

`void deflate_init(resource` *context*`, string` *data*`[, int` *flush_mode*`])`

将*data*添加到 deflate 上下文*context*中，并根据*flush_mode*检查上下文是否应该刷新。*flush_mode*可以是`ZLIB_BLOCK`、`ZLIB_NO_FLUSH`、`ZLIB_PARTIAL_FLUSH`、`ZLIB_SYNC_FLUSH`（默认）、`ZLIB_FULL_FLUSH`或`ZLIB_FINISH`之一。在添加大多数数据块时，选择`ZLIB_NO_FLUSH`以最大化压缩尝试。在添加最后一个数据块后，请使用`ZLIB_FINISH`指示上下文已完成。

#### deflate_init

`void deflate_init(int` *encoding*`[, array` *options*`])`

初始化并返回一个增量式的 deflate 上下文。可以使用对该上下文的`deflate_add()`调用来逐步进行数据的 deflate 压缩。

| `level` | 压缩范围从–1 到 9 |
| --- | --- |
| `memory` | 压缩内存级别从 1 到 9 |
| `window` | zlib 窗口大小从 8 到 15 |
| `strategy` | 使用的压缩策略；可以是`ZLIB_FILTERED`、`ZLIB_HUFFMAN_ONLY`、`ZLIB_RLE`、`ZLIB_FIXED`或`ZLIB_DEFAULT_STRATEGY`（默认） |
| `dictionary` | 压缩预设字典的字符串或字符串数组 |

#### deg2rad

`float deg2rad(float` *number*`)`

将*number*从度转换为弧度并返回结果。

#### dir

`directory dir(string` *path*`[, resource` *context*`])`

返回一个初始化为给定路径的`directory`类实例。您可以在对象上使用`read()`、`rewind()`和`close()`方法，这些方法相当于过程函数`readdir()`、`rewinddir()`和`closedir()`。

#### dirname

`string dirname(string` *path*`)`

返回*path*的目录部分。这包括直到文件名部分的所有内容（参见`basename`），不包括尾部路径分隔符。

#### disk_free_space

`float disk_free_space(string` *path*`)`

返回磁盘分区或文件系统上*path*处的空闲空间大小（以字节为单位）。

#### disk_total_space

`float disk_total_space(string` *path*`)`

返回磁盘分区或文件系统上*path*处的总空间大小（包括已使用和空闲的字节）。

#### each

`array each(array &`*array*`)`

创建一个包含当前指向的数组元素的键和值的数组。数组包含四个元素：键为`0`和*key*，包含元素的键；值为`1`和*value*，包含元素的值。

如果数组的内部指针指向数组末尾之外，则`each()`返回`false`。

#### echo

`void echo string` *string*`[, string` *string2*`[, string` *stringN* `...]]`

输出给定的字符串。`echo` 是一种语言结构，可以选择性地用括号括起参数，除非有多个参数，否则不能使用括号。

#### `empty`

`bool empty(mixed` *value*`)`

如果 *value* 是 `0` 或未设置，则返回 `true`，否则返回 `false`。

#### end

`mixed end(array &`*array*`)`

将数组的内部指针推进到最后一个元素并返回该元素的值。

#### `error_clear_last`

`array error_clear_last()`

清除最近的错误；它将不再被 `error_get_last()` 返回。

#### `error_get_last`

`array error_get_last()`

返回关于最近发生的错误的关联数组信息，如果在处理当前脚本时尚未发生错误，则返回 `NULL`。数组中包括以下值：

| `type` | 错误的类型 |
| --- | --- |
| `message` | 错误的可打印版本 |
| `file` | 发生错误的文件的完整路径 |
| `line` | 发生错误的文件中的行号 |

#### `error_log`

`bool error_log(string` *message*`, int` *type*`[, string` *destination*`[, string` *headers*`]])`

记录错误消息到 Web 服务器的错误日志，电子邮件地址或文件中。第一个参数是要记录的消息。类型是以下之一：

| `0` | `message` 发送到 PHP 系统日志；消息被放入由 `error_log` 配置指令指向的文件中。 |
| --- | --- |
| `1` | `message` 发送到电子邮件地址 *destination*。如果指定了 *headers*，则提供用于创建消息的可选头部（有关可选头部的更多信息，请参见 `mail`）。 |
| `3` | 将 *message* 追加到文件 *destination* 中。 |
| `4` | `message` 直接发送到服务器应用程序接口（SAPI）日志处理程序。 |

#### `error_reporting`

`int error_reporting([int` *level*`])`

设置 PHP 报告的错误级别为 *level* 并返回当前级别；如果省略 *level*，则返回当前的错误报告级别。该函数可用以下值：

| `E_ERROR` | 致命的运行时错误（脚本执行停止） |
| --- | --- |
| `E_WARNING` | 运行时警告 |
| `E_PARSE` | 编译时解析错误 |
| `E_NOTICE` | 运行时通知 |
| `E_CORE_ERROR` | PHP 内部生成的错误 |
| `E_CORE_WARNING` | PHP 内部生成的警告 |
| `E_COMPILE_ERROR` | Zend 脚本引擎内部生成的错误 |
| `E_COMPILE_WARNING` | Zend 脚本引擎内部生成的警告 |
| `E_USER_ERROR` | 由调用 `trigger_error()` 生成的运行时错误 |
| `E_USER_WARNING` | 由调用 `trigger_error()` 生成的运行时警告 |
| `E_STRICT` | 指示 PHP 建议代码更改以帮助实现向前兼容性 |
| `E_RECOVERABLE_ERROR` | 如果发生潜在致命错误，并且已被捕获并正确处理，则代码可以继续执行 |
| `E_DEPRECATED` | 如果启用，将发出有关即将无法正常工作的弃用代码的警告 |
| `E_USER_DEPRECATED` | 如果启用，通过 `trigger_error()` 函数可以生成任何由弃用代码触发的警告消息 |
| `E_ALL` | 所有上述选项 |

这些选项可以使用位运算 OR（`|`）组合在一起，以便报告每个级别的错误。例如，以下代码关闭用户错误和警告，执行一些操作，然后恢复原始级别：

```php
<$level = error_reporting();
 error_reporting($level & ~(E_USER_ERROR | E_USER_WARNING));
 // do some stuff
 error_reporting($level);>
```

#### `string escapeshellarg(string` *argument*`)`

`string escapeshellarg(string` *argument*`)`

适当地转义 *argument*，使其可以作为 shell 函数的安全参数使用。当直接将用户输入（例如来自表单）传递给 shell 命令时，应使用此函数来转义数据，以确保参数不构成安全风险。

#### escapeshellcmd

`string escapeshellcmd(string` *command*`)`

对 *command* 中可能导致 shell 运行额外命令的字符进行转义。当直接将用户输入（例如来自表单）传递给 `exec()` 或 `system()` 函数时，应使用此函数来转义数据，以确保参数不构成安全风险。

#### exec

`string exec(string` *command*`[, array` *output*`[, int` *return*`]])`

通过 shell 执行 *command* 并返回命令结果的最后一行输出。如果指定了 *output*，则用命令返回的行填充它。如果指定了 *return*，则设置为命令的返回状态。

如果想要将命令输出的结果嵌入到 PHP 页面中，请使用 `passthru()`。

#### exp

`float exp(float` *number*`)`

返回 *number* 的 *e* 次幂。

#### explode

`array explode(string` *separator*`, string` *string*`[, int` *limit*`])`

返回由在 *separator* 处找到的 *string* 拆分而成的子字符串数组。如果提供了 *limit*，则最多返回 *limit* 个子字符串，最后一个子字符串包含剩余的字符串。如果未找到 *separator*，则返回原始字符串。

#### expm1

`float expm1(float` *number*`)`

返回 `exp(`*number*`) – 1`，计算使得即使 *number* 接近 0，返回值也准确的结果。

#### extension_loaded

`bool extension_loaded(string` *name*`)`

如果加载了 *name* 扩展，返回 `true`，否则返回 `false`。

#### extract

`int extract(array` *array*`[, int` *type*`[, string` *prefix*`]])`

将变量的值设置为来自数组元素的值。对于数组中的每个元素，使用其键来确定要设置的变量名，并将该变量设置为元素的值。

如果数组中的值与本地作用域中已存在的变量同名，给定的第二个参数可以取以下任一值以确定行为：

| `EXTR_OVERWRITE`（默认） | 覆盖现有变量 |
| --- | --- |
| `EXTR_SKIP` | 不覆盖现有变量（忽略数组中提供的值） |
| `EXTR_PREFIX_SAME` | 使用作为第三个参数给出的字符串前缀变量名 |
| `EXTR_PREFIX_ALL` | 使用作为第三个参数给出的字符串前缀所有变量名 |
| `EXTR_PREFIX_INVALID` | 使用作为第三个参数给出的字符串前缀任何无效或数值变量名 |
| `EXTR_IF_EXISTS` | 仅在当前符号表中存在变量时替换变量 |
| `EXTR_PREFIX_IF_EXISTS` | 仅在同一变量的非前缀版本存在时创建带有前缀的变量名 |
| `EXTR_REFS` | 提取变量作为引用 |

函数返回成功设置的变量数。

#### 关闭文件

`bool fclose(int` *handle* `)`

关闭*handle*引用的文件；如果成功，则返回`true`，如果不成功，则返回`false`。

#### feof

`bool feof(int` *handle* `)`

如果*handle*引用的文件的标记位于文件末尾（EOF）或发生错误时，则返回`true`。如果标记不在 EOF，则返回`false`。

#### 刷新

`bool fflush(int` *handle* `)`

将对*handle*引用的文件的任何更改提交到磁盘，确保文件内容在磁盘上而不仅仅在磁盘缓冲区中。如果操作成功，则函数返回`true`；否则，返回`false`。

#### fgetc

`string fgetc(int` *handle* `)`

返回由*handle*引用的文件标记处的字符，并将标记移动到下一个字符。如果标记位于文件末尾，则函数返回`false`。

#### fgetcsv

`array fgetcsv(resource` *handle* `[, int` *length* `[, string` *delimiter* `[, string` *enclosure* `[, string` *escape* `]]]])`

从被*handle*引用的文件中读取下一行，并将该行解析为逗号分隔的值（CSV）行。要读取的最长行由*length*给出。如果提供了*delimiter*，则用其作为行中的值的分隔符，而不是逗号。如果提供了*enclosure*，则用单个字符作为值的封闭符（默认为双引号字符，`"`）。*escape*设置要使用的转义字符；默认为反斜杠`\`；只能指定一个字符。例如，要读取并显示包含制表符分隔值的文件中的所有行，请使用：

```php
$fp = fopen("somefile.tab", "r");

while($line = fgetcsv($fp, 1024, "\t")) {
 print "<p>" . count($line) . "fields:</p>";
 print_r($line);
}
fclose($fp);
```

#### 获取文件行

`string fgets(resource` *handle* `[, int` *length* `])`

从被*handle*引用的文件中读取一个字符串；返回最多*length*个字符的字符串，但读取会在*length* `− 1`（用于换行字符）个字符处、换行字符处或 EOF 处结束。如果发生任何错误，则返回`false`。

#### fgetss

`string fgetss(resource` *handle* `[, int` *length* `[, string` *tags* `]])`

从被*handle*引用的文件中读取一个字符串；返回最多*length*个字符的字符串，但读取会在*length* `− 1`（用于换行字符）个字符处、换行字符处或 EOF 处结束。返回字符串之前，将其中的任何 PHP 和 HTML 标签（除*tags*中列出的标签外）剥离。如果发生任何错误，则返回`false`。

#### 文件

`array file(string` *filename*`[, int` *flags* `[, resource` *context*`]])`

将*文件*读入数组中。*flags*可以是以下常量之一或多个：

| `FILE_USE_INCLUDE_PATH` | 在*php.ini*文件中设置的包含路径中搜索文件 |
| --- | --- |
| `FILE_IGNORE_NEW_LINES` | 不在数组元素末尾添加换行符 |
| `FILE_SKIP_EMPTY_LINES` | 跳过任何空行 |

#### `file_exists`

`bool file_exists(string` *path*`)`

如果*path*处的文件存在，则返回`true`，否则返回`false`。

#### `fileatime`

`int fileatime(string` *path*`)`

返回文件*path*的最后访问时间，作为 Unix 时间戳值。由于从文件系统检索此信息的成本，此信息已缓存；您可以使用`clearstatcache()`清除缓存。

#### `filectime`

`int filectime(string` *path*`)`

返回*path*处文件的 inode 更改时间值。由于从文件系统检索此信息的成本，此信息已缓存；您可以使用`clearstatcache()`清除缓存。

#### `file_get_contents`

`string file_get_contents(string` *path*`[, bool` *include* `[, resource` *context* `[, int` *offset* `[, int` *maxlen*`]]]])`

读取*path*处的文件，并将其内容作为字符串返回，可选从*offset*开始。如果指定了*include*并且为`true`，则在包含路径中搜索文件。还可以使用*maxlen*参数控制返回字符串的长度。

#### `filegroup`

`int filegroup(string` *path*`)`

返回*path*处文件的拥有组 ID。由于从文件系统检索此信息的成本，此信息已缓存；您可以使用`clearstatcache()`清除缓存。

#### `fileinode`

`int fileinode(string` *path*`)`

返回*path*处文件的 inode 编号，如果出现错误则返回`false`。此信息已缓存；参见`clearstatcache`。

#### `filemtime`

`int filemtime(string` *path*`)`

返回文件*path*的最后修改时间，作为 Unix 时间戳值。此信息已缓存；您可以使用`clearstatcache()`清除缓存。

#### `fileowner`

`int fileowner(string` *path*`)`

返回*path*处文件的所有者用户 ID，如果出现错误则返回`false`。此信息已缓存；您可以使用`clearstatcache()`清除缓存。

#### `fileperms`

`int fileperms(string` *path*`)`

返回文件*path*的文件权限，如果出现错误则返回`false`。此信息已缓存；您可以使用`clearstatcache()`清除缓存。

#### `file_put_contents`

`int file_put_contents(string` *path*`, mixed` *string* `[, int` *flags*`[, resource` *context*`]])`

打开由*path*指定的文件，将*string*写入文件，然后关闭文件。返回写入文件的字节数，如果发生错误则返回`−1`。*flags*参数是一个具有两个可能值的位字段：

| `FILE_USE_INCLUDE_PATH` | 如果指定，则在包含路径中搜索文件，并将文件写入其已存在的第一个位置 |
| --- | --- |
| `FILE_APPEND` | 如果指定并且由`path`指示的文件已经存在，则`string`将附加到文件的现有内容中 |
| `LOCK_EX` | 在写入文件之前独占锁定文件 |

#### filesize

`int filesize(string` *path*`)`

返回*path*指定的文件的大小（以字节为单位）。如果文件不存在或出现任何其他错误，则函数返回`false`。此信息被缓存；可以使用`clearstatcache()`清除缓存。

#### filetype

`string filetype(string` *path*`)`

返回给定*path*中的文件类型。可能的类型有：

| `Fifo` | 文件是一个 FIFO 管道 |
| --- | --- |
| `Char` | 文件是一个文本文件 |
| `Dir` | *path*是一个目录 |
| `Block` | 由文件系统保留的块 |
| `Link` | 文件是一个符号链接 |
| `File` | 文件包含二进制数据 |
| `Socket` | 一个套接字接口 |
| `Unknown` | 无法确定文件类型 |

#### filter_has_var

`bool filter_has_var(int` *context*`, string` *name*`)`

如果指定的*context*中存在名为*name*的值，则返回`true`，否则返回`false`。*context*可以是`INPUT_GET`、`INPUT_POST`、`INPUT_COOKIE`、`INPUT_SERVER`或`INPUT_ENV`之一。

#### filter_id

`int filter_id(string` *name*`)`

返回由*name*标识的过滤器的 ID，如果没有这样的过滤器，则返回`false`。

#### filter_input

`mixed filter_input(mixed` *var*`[, int` *filter_id*`[, mixed` *options*`]])`

在给定的*context*中对*var*执行由 ID *filter_id*标识的过滤器，并返回结果。*context*可以是`INPUT_GET`、`INPUT_POST`、`INPUT_COOKIE`、`INPUT_SERVER`或`INPUT_ENV`之一。如果未指定*filter_id*，则使用默认过滤器。*options*参数可以是适用于过滤器的标志位字段或适当的选项的关联数组。有关使用过滤器的更多信息，请参见第四章。

#### filter_input_array

`mixed filter_input_array(array` *variables*`[, mixed` *filters*`])`

对关联数组*variables*中的变量执行一系列过滤器，并将结果作为关联数组返回。*context*可以是`INPUT_GET`、`INPUT_POST`、`INPUT_COOKIE`、`INPUT_SERVER`或`INPUT_ENV`之一。

可选参数是一个关联数组，其中每个元素的键是变量名，关联值定义了要使用的过滤器及其选项以过滤该变量值。定义可以是要使用的过滤器的 ID 或包含以下一个或多个元素的数组：

| `filter` | 要应用的过滤器的 ID |
| --- | --- |
| `flags` | 一个标志位字段 |
| `options` | 一个与过滤器特定选项相关的关联数组 |

#### filter_list

`array filter_list()`

返回每个可用过滤器的名称的数组；这些名称可以传递给`filter_id()`以获取用于其他过滤函数中使用的过滤器 ID。

#### filter_var

`mixed filter_var(mixed` *var*`[, int` *filter_id*`[, mixed` *options*`]])`

对*var*执行由 ID *filter_id*标识的过滤器，并返回结果。如果未指定*filter_id*，则使用默认过滤器。*options*参数可以是标志位组或适用于过滤器的关联数组选项。有关使用过滤器的更多信息，请参见第四章。

#### filter_var_array

`mixed filter_var_array(mixed` *var*`[, mixed` *options*`])`

对指定上下文中的变量执行一系列过滤器，并将结果作为关联数组返回。上下文是`INPUT_GET`、`INPUT_POST`、`INPUT_COOKIE`、`INPUT_SERVER`或`INPUT_ENV`之一。

*options*参数是一个关联数组，其中每个元素的键是变量名，关联的值定义了要用于过滤该变量值的过滤器和选项。定义可以是要使用的过滤器 ID，也可以是一个包含以下一个或多个元素的数组：

| `filter` | 应用的过滤器 ID |
| --- | --- |
| `flags` | 标志位的位域 |
| `options` | 特定于过滤器的选项的关联数组 |

#### floatval

`float floatval(mixed` *value*`)`

返回*value*的浮点数值。如果 value 是非标量（对象或数组），则返回`1`。

#### flock

`bool flock(resource` *handle*`, int` *operation*`[, int` *would_block*`])`

尝试锁定由*handle*指定的文件路径。操作是以下值之一：

| `LOCK_SH` | 共享锁（读者） |
| --- | --- |
| `LOCK_EX` | 独占锁（写者） |
| `LOCK_UN` | 释放锁定（共享或独占） |
| `LOCK_NB` | 将`LOCK_SH`或`LOCK_EX`添加到获取非阻塞锁 |

如果指定，如果操作将导致文件阻塞，则*would_block*设置为`true`。如果无法获取锁定，则函数返回`false`，如果操作成功，则返回`true`。

因为大多数系统在进程级别实现文件锁定，所以`flock()`无法阻止同一 Web 服务器进程中运行的两个 PHP 脚本同时访问文件。

#### floor

`float floor(float` *number*`)`

返回小于或等于*number*的最大整数值。

#### flush

`void flush()`

将当前输出缓冲区发送给客户端并清空输出缓冲区。有关使用输出缓冲区的更多信息，请参见第十五章。

#### fmod

`float fmod(float` *x*`, float` *y*`)`

返回*x*除以*y*的浮点数模数。

#### fnmatch

`bool fnmatch(string` *pattern*`, string` *string*`[, int` *flags*`])`

如果*string*与*pattern*中给出的 shell 通配符模式匹配，则返回`true`。有关匹配规则，请参见`glob`。flags 值是以下任何一个的按位 OR：

| `FNM_NOESCAPE` | 将`pattern`中的反斜杠视为反斜杠，而不是转义序列的开始 |
| --- | --- |
| `FNM_PATHNAME` | `string`中的斜杠字符必须与`pattern`中的斜杠显式匹配 |
| 打开由*path*指定的文件，并返回指向打开文件的文件资源句柄。如果*path*以`http://`开头，则打开一个 HTTP 连接并返回响应起始处的文件指针。如果*path*以`ftp://`开头，则打开 FTP 连接并返回文件起始处的文件指针；远程服务器必须支持被动 FTP。 |
| forward_static_call |

#### | `w` | 仅打开文件进行写入。如果文件存在，则将其截断为零长度；如果文件不存在，则创建该文件。 |

调用*function*指定的静态方法，并传递提供的参数。如果*function*包含类名，则使用延迟静态绑定来找到方法的适当类。返回函数返回的值。

`resource fopen(string` *path*`, string` *mode*`[, bool` *include* `[, resource` *context*`]])`

| `FNM_CASEFOLD` | 在将`string`与`pattern`匹配时忽略大小写。 |

| `a+` | 以读写方式打开文件。如果文件存在，则文件指针将位于文件末尾；如果文件不存在，则创建该文件。 |

| 如果尝试打开文件时发生任何错误，则返回`false`。 |
| --- |
| `a` | 仅打开文件进行写入。如果文件存在，则文件指针将位于文件末尾；如果文件不存在，则创建该文件。 |
| `x` | 创建并仅打开文件进行写入；将文件指针置于文件开头。 |
| 如果指定了*include*并且为`true`，`fopen()`会尝试在当前*include*路径中定位文件。 |
| fopen |
| `r+` | 以读写方式打开文件；文件指针将位于文件开头。 |
| 如果*path*为`php://stdin`、`php://stdout`或`php://stderr`，则返回适当流的文件指针。 |
| 参数*mode*指定打开文件的权限。必须是以下之一： |
| `FNM_PERIOD` | 字符串开头处的句点，或者如果同时指定了`FNM_PATHNAME`，则在任何斜杠之前必须显式匹配`pattern`中的句点。 |
| `mixed forward_static_call_array(callable` *function*`, array` *parameters*`)` |

| `c` | 仅打开文件进行写入。如果文件不存在，则创建该文件。如果文件存在，则不截断（与`w`不同），也不会调用失败（与`x`不同）。文件指针定位在文件开头。 |

| `x+` | 创建并以读写方式打开文件。 |

#### forward_static_call_array

`mixed forward_static_call(callable` *function*`[, mixed` *parameter1*`[, ... mixed` *parameterN*`]])`

调用当前对象上下文中名为*function*的函数，并使用提供的参数。如果*function*包含类名，则使用延迟静态绑定来找到方法的适当类。返回函数返回的值。

#### | `w+` | 以读写方式打开文件。如果文件存在，将截断为零长度；如果文件不存在，则创建该文件。文件指针从文件开头开始。 |

| `r` | 以读取方式打开文件；文件指针将位于文件开头。 |

在当前对象的上下文中调用名为*function*的函数，并使用数组*parameters*中的参数。如果*function*包括类名，则使用延迟静态绑定查找方法的适当类。返回函数返回的值。

#### fpassthru

`int fpassthru(resource` *handle*`)`

输出指向的文件，并关闭文件句柄。文件从当前文件指针位置输出直到文件结束。如果发生任何错误，返回`false`；如果操作成功，返回`true`。

#### fprintf

`int fprintf(resource` *handle*`, string` *format*`[, mixed` *value1*`[, ...` *valueN*`]])`

将使用给定参数填充*format*的字符串写入流资源*handle*。有关如何使用此函数的更多信息，请参见`printf()`。

#### fputcsv

`int fputcsv(resource` *handle*`[, array` *fields*`[, string` *delimiter*`[, string` *enclosure*`]]])`

以逗号分隔值（CSV）格式格式化*fields*中包含的项目，并将结果写入文件句柄*handle*。如果提供*delimiter*，则使用单个字符代替逗号分隔行中的值。如果提供*enclosure*，则使用单个字符将值括起来（默认为双引号字符`"`）。返回写入的字符串长度，如果失败则返回`false`。

#### fread

`string fread(int` *handle*`, int` *length*`)`

从文件句柄*handle*引用的文件中读取*length*字节并将它们作为字符串返回。如果在到达 EOF 之前可用的字节数少于*length*，则返回直到 EOF 的字节。

#### fscanf

`mixed fscanf(resource` *handle*`, string` *format*`[, string` *name1*`[, ... string` *nameN*`]])`

从文件句柄*handle*引用的文件中读取数据并根据*format*返回一个值。有关如何使用此函数的更多信息，请参见`sscanf`。

如果未提供可选参数*name1*到*nameN*，则从文件中扫描的值将作为数组返回；否则，它们将放入由*name1*到*nameN*命名的变量中。

#### fseek

`int fseek(resource` *handle*`, int` *offset*`[, int` *from*`])`

将文件句柄*handle*中的文件指针移动到*offset*字节处。如果指定了*from*，它确定如何移动文件指针。*from*必须是以下值之一：

| `SEEK_SET` | 将文件指针设置为字节*offset*（默认） |
| --- | --- |
| `SEEK_CUR` | 将文件指针设置为当前位置加上*offset*字节 |
| `SEEK_END` | 将文件指针设置为 EOF 减去*offset*字节处 |

如果函数成功，则此函数返回`0`，如果操作失败，则返回`−1`。

#### fsockopen

`resource fsockopen(string` *host*`, int` *port*`[, int` *error*`[, string` *message*`[, float` *timeout*`]]])`

打开到远程*host*在特定*port*上的 TCP 或 UDP 连接。默认使用 TCP；要通过 UDP 连接，*host*必须以协议`udp://`开头。如果指定*timeout*，则指示超时前等待的时间长度（以秒为单位）。

如果连接成功，将返回一个虚拟文件指针，可以与`fgets()`和`fputs()`等函数一起使用。如果连接失败，则返回`false`。如果提供了*error*和*message*，它们将分别设置为错误编号和错误字符串。

#### fstat

`array fstat(resource` *handle*`)`

返回关于*handle*引用的文件的信息的关联数组。数组包含以下值（以其数值和键索引形式给出）：

| `dev` (`0`) | 文件所在设备 |
| --- | --- |
| `ino` (`1`) | 文件的 inode |
| `mode` (`2`) | 文件打开的模式 |
| `nlink` (`3`) | 指向此文件的硬链接数 |
| `uid` (`4`) | 文件所有者的用户 ID |
| `gid` (`5`) | 文件所有者的组 ID |
| `rdev` (`6`) | 设备类型（如果文件在 inode 设备上） |
| `size` (`7`) | 文件大小（字节为单位） |
| `atime` (`8`) | 上次访问时间（Unix 时间戳格式） |
| `mtime` (`9`) | 上次修改时间（Unix 时间戳格式） |
| `ctime` (`10`) | 文件创建时间（Unix 时间戳格式） |
| `blksize` (`11`) | 文件系统的块大小（以字节为单位） |
| `blocks` (`12`) | 分配给文件的块数 |

#### ftell

`int ftell(resource` *handle*`)`

返回*handle*引用的文件的字节偏移量。如果发生错误，则返回`false`。

#### ftruncate

`bool ftruncate(resource` *handle*`, int` *length*`)`

将*handle*引用的文件截断为*length*字节。如果操作成功，则返回`true`，否则返回`false`。

#### func_get_arg

`mixed func_get_arg(int` *index*`)`

返回函数参数数组中的*index*元素。如果在函数外调用，或者*index*大于参数数组中的参数数量，`func_get_arg()`会生成警告并返回`false`。

#### func_get_args

`array func_get_args()`

返回作为索引数组给定给函数的参数数组。如果在函数外调用，`func_get_args()`返回`false`并生成警告。

#### func_num_args

`int func_num_args()`

返回传递给当前用户定义函数的参数数目。如果在函数外调用，`func_num_args()`返回`false`并生成警告。

#### function_exists

`bool function_exists(string` *function*`)`

如果已定义具有*function*名称的函数（检查用户定义和内置函数），则返回`true`，否则返回`false`。检查匹配函数时不区分大小写。

#### fwrite

`int fwrite(resource` *handle*`, string` *string*`[, int` *length*`])`

向*handle*引用的文件写入*string*。文件必须以写权限打开。如果给定了*length*，则只写入字符串的指定字节数。返回写入的字节数，或者在错误时返回`−1`。

#### gc_collect_cycles

`int gc_collect_cycles()`

执行一次垃圾回收循环，并返回释放的引用数。如果当前未启用垃圾回收，则不执行任何操作。

#### gc_disable

`void gc_disable()`

禁用垃圾收集器。如果垃圾收集器已启用，则在禁用之前进行垃圾回收。

#### gc_enable

`void gc_enable()`

启用垃圾收集器；通常只有运行时间非常长的脚本才能从垃圾收集器中受益。

#### gc_enabled

`bool gc_enabled()`

如果当前垃圾收集器启用，则返回 `true`，如果禁用则返回 `false`。

#### get_browser

`mixed get_browser([string` *name*`[, bool` *return_array*`]])`

返回一个包含用户当前浏览器信息的对象，从 `$HTTP_USER_AGENT` 中获取，或从用户代理 *name* 识别的浏览器。信息从 *browscap.ini* 文件获取。浏览器的版本和各种能力（如是否支持框架、cookie 等）以对象形式返回。如果 `return_array` 是 `true`，则返回一个数组而不是对象。

#### get_called_class

`string get_called_class()`

返回通过后期静态绑定调用静态方法的类名，或者如果在类静态方法外部调用，则返回 `false`。

#### get_cfg_var

`string get_cfg_var(string` *name*`)`

返回 PHP 配置变量 *name* 的值。如果 *name* 不存在，`get_cfg_var()` 返回 `false`。只返回配置文件中设置的变量，如`cfg_file_path()`所返回的；编译时设置和 Apache 配置文件变量不会返回。

#### get_class

`string get_class(object` *object*`)`

返回给定对象的类名。类名以小写字符串形式返回。如果 *object* 不是对象，则返回 `false`。

#### get_class_methods

`array get_class_methods(mixed` *class*`)`

如果参数是字符串，则返回包含指定 *class* 的每个方法名的数组。如果参数是对象，则返回该对象所属类中定义的方法。

#### get_class_vars

`array get_class_vars(string` *class*`)`

返回给定 *class* 的默认属性的关联数组。对于每个属性，数组中添加一个键为属性名、值为默认值的元素。不返回没有默认值的属性。

#### get_current_user

`string get_current_user()`

返回当前 PHP 脚本执行的用户特权下的用户名称。

#### get_declared_classes

`array get_declared_classes()`

返回一个包含每个定义类名的数组。包括 PHP 当前加载的扩展中定义的任何类。

#### get_declared_interfaces

`array get_declared_interfaces()`

返回包含每个已声明接口名称的数组。这包括当前加载的 PHP 扩展和内置接口中声明的任何接口。

#### get_declared_traits

`array get_declared_traits()`

返回包含每个已定义特性名称的数组。这包括当前加载的 PHP 扩展中定义的任何特性。

#### get_defined_constants

`array get_defined_constants([bool` *categories*`])`

返回一个关联数组，其中包含由扩展和`define()`函数定义的所有常量及其值。如果设置了*categories*并且为`true`，则关联数组包含子数组，每个子数组对应一个常量类别。

#### get_defined_functions

`array get_defined_functions()`

返回一个包含每个已定义函数名称的数组。返回的数组是一个关联数组，包括两个键，`internal`和`user`。第一个键的值是一个包含所有内部 PHP 函数名称的数组；第二个键的值是一个包含所有用户定义函数名称的数组。

#### get_defined_vars

`array get_defined_vars()`

返回在环境、服务器、全局和局部范围内定义的所有变量的数组。

#### get_extension_funcs

`array get_extension_funcs(string` *name*`)`

返回由*name*指定的扩展提供的函数数组。

#### get_headers

`array get_headers(string` *url*`[, int` *format*`])`

返回由远程服务器发送到给定*url*页面的标头数组。如果*format*为`0`或未设置，则以简单数组形式返回标头，数组中的每个条目对应一个标头。如果设置并且为`1`，则返回一个关联数组，键和值对应标头字段。

#### get_html_translation_table

`array get_html_translation_table([int` *which*`[, int` *style*`[, string` *encoding*`]]])`

返回`htmlspecialchars()`或`htmlentities()`使用的转换表。如果*which*是`HTML_ENTITIES`，则返回`htmlentities()`使用的表；如果*which*是`HTML_SPECIALCHARS`，则返回`htmlspecialchars()`使用的表。可选地，您可以指定希望返回的引号样式；可能的值与转换函数中的值相同：

| `ENT_COMPAT`（默认） | 转换双引号，但不转换单引号 |
| --- | --- |
| `ENT_NOQUOTES` | 不转换单引号和双引号 |
| `ENT_QUOTES` | 转换单引号和双引号 |
| `ENT_HTML401` | HTML 4.01 实体表 |
| `ENT_XML1` | XML 1 实体表 |
| `ENT_XHTML` | XHTML 实体表 |
| `ENT_HTML5` | HTML 5 实体表 |

可选的*encoding*参数具有以下可能的选择：

| `ISO-8859-1` | 西欧，拉丁-1。 |
| --- | --- |
| `ISO-8859-5` | 西里尔字符集（拉丁/西里尔文），很少使用。 |
| `ISO-8859-15` | 西欧，拉丁-9。添加欧元符号，拉丁-1 中缺少的法国和芬兰字母。 |
| `UTF-8` | 兼容 ASCII 的多字节 8 位 Unicode。 |
| `cp866` | DOS 特定的西里尔字符集。 |
| `cp1251` | 用于 Windows 的特定西里尔字符集。 |
| `cp1252` | 用于西欧的 Windows 特定字符集。 |
| `KOI8-R` | 俄语。 |
| `BIG5` | 主要用于台湾的繁体中文。 |
| `GB2312` | 简体中文，国家标准字符集。 |
| `BIG5-HKSCS` | 带香港扩展的 Big5，繁体中文。 |
| `Shift_JIS` | 日语。 |
| `EUC-JP` | 日语。 |
| `MacRoman` | macOS 使用的字符集。 |
| `""` | 空字符串激活脚本编码（Zend 多字节）、`default_charset`和当前语言环境的检测顺序。不建议使用。 |

#### get_included_files

`array get_included_files()`

返回通过`include()`、`include_once()`、`require()`和`require_once()`包含到当前脚本中的文件数组。

#### get_include_path

`string get_include_path()`

返回包含 include 路径配置选项值的数组，提供包含路径位置列表。如果要将返回的值拆分为单独的条目，请确保使用`PATH_SEPARATOR`常量进行分割，该常量为 Unix 和 Windows 编译分别设置：

```php
$paths = split(PATH_SEPARATOR, get_include_path());
```

#### get_loaded_extensions

`array get_loaded_extensions([ bool *zend_extensions* ])`

返回一个包含编译并加载到 PHP 中的每个扩展的名称的数组。如果`zend_extensions`选项为`true`，则仅返回 Zend 扩展；默认为`false`。

#### get_meta_tags

`array get_meta_tags(string *path* [, int *include* ])`

解析文件*path*并提取其定位的 HTML meta 标签。返回一个关联数组，其中键是 meta 标签的`name`属性，值是标签的适当值。无论原始属性的大小写如何，键都是小写的。如果指定了*include*并且为`true`，函数将在包含路径中搜索*path*。

#### getmygid

`int getmygid()`

返回当前脚本执行的 PHP 进程的组 ID。如果无法确定组 ID，则返回`false`。

#### getmyuid

`int getmyuid()`

返回当前脚本执行的 PHP 进程的用户 ID。如果无法确定用户 ID，则返回`false`。

#### get_object_vars

`array get_object_vars(object *object* )`

返回给定*object*的属性的关联数组。对于每个属性，将添加一个具有属性名称键和当前值值的元素到数组中。不返回没有当前值的属性，即使它们在类中定义了。

#### get_parent_class

`string get_parent_class(mixed *object* )`

返回给定*object*的父类名称。如果对象不继承自其他类，则返回空字符串。

#### get_resource_type

`string get_resource_type(resource *handle* )`

返回表示指定资源*handle*类型的字符串。如果*handle*不是有效的资源，则函数生成错误并返回`false`。可用资源类型取决于加载的扩展，但包括`file`，`mysql link`等。

#### `getcwd`

`string getcwd()`

返回 PHP 进程当前工作目录的路径。

#### `getdate`

`array getdate([int` *timestamp*`])`

返回包含给定*timestamp*时间和日期各个组件值的关联数组。如果没有给出*timestamp*，则使用当前日期和时间。这是`date()`函数的变体。数组包含以下键和值：

| `seconds` | 秒 |
| --- | --- |
| `minutes` | 分钟 |
| `hours` | 小时 |
| `mday` | 月份中的日期 |
| `wday` | 数字星期几（星期日为`0`） |
| `mon` | 月份 |
| `year` | 年份 |
| `yday` | 年份中的日期 |
| `weekday` | 星期几的名称（星期日到星期六） |
| `month` | 月份（一月到十二月）的名称 |

#### `getenv`

`string getenv(string` *name*`)`

返回环境变量*name*的值。如果*name*不存在，则`getenv()`返回`false`。

#### `gethostbyaddr`

`string gethostbyaddr(string` *address*`)`

返回具有 IP 地址*address*的机器的主机名。如果找不到这样的地址或*address*不解析为主机名，则返回*address*。

#### `gethostbyname`

`string gethostbyname(string` *host*`)`

返回*host*的 IP 地址。如果没有这样的主机，则返回*host*。

#### `gethostbynamel`

`array gethostbynamel(string` *host*`)`

返回*host*的 IP 地址数组。如果没有这样的主机，则返回`false`。

#### `gethostname`

`string gethostname()`

返回运行当前脚本的机器的主机名。

#### `getlastmod`

`int getlastmod()`

返回包含当前脚本所在文件的最后修改日期的 Unix 时间戳值。如果在检索信息时发生错误，则返回`false`。

#### `getmxrr`

`bool getmxrr(string` *host*`, array &`*hosts*`[, array &`*weights*`])`

搜索所有邮件交换器（MX）记录的 DNS *host*。结果放入数组*hosts*中。如果提供了权重，每个 MX 记录的权重放入*weights*中。如果找到任何记录，则返回`true`，如果没有找到则返回`false`。

#### `getmyinode`

`int getmyinode()`

返回包含当前脚本的文件的 inode 值。如果发生错误，则返回`false`。

#### `getmypid`

`int getmypid()`

返回执行当前脚本的 PHP 进程的进程 ID。当 PHP 作为服务器模块运行时，可能有多个脚本共享相同的进程 ID，因此不一定是唯一的数字。

#### `getopt`

`array getopt(string` *short_options*`[, array` *long_options*`])`

解析用于调用当前脚本的命令行参数列表，并返回可选名称/值对的关联数组。*short_options*和*long_options*参数定义要解析的命令行参数。

参数 *short_options* 是一个字符串，每个字符表示通过单破折号传递到脚本的单个参数。例如，短选项字符串`"ar"`匹配命令行参数`-a -r`。任何后跟一个冒号`:`的字符需要匹配一个值，而后跟两个冒号`::`的字符则可选择包含一个值进行匹配。例如，`"a:r::x"`可以匹配命令行参数`-aTest -r -x`，但不会匹配`-a -r -x`。

参数*long_options*是一个字符串数组，每个元素表示通过双破折号传递到脚本的单个参数。例如，元素`"verbose"`匹配命令行参数`--verbose`。*long_options*参数中指定的所有参数都可以选择与通过等号与选项名称分隔的命令行中的值匹配。例如，`"verbose"`将匹配`--verbose`和`--verbose=1`。

#### 获取由名称指定的协议。

`int getprotobyname(string` *name*`)`

返回*/etc/protocols*中与*name*关联的协议号。

#### 获取由协议号指定的协议。

`string getprotobynumber(int` *protocol*`)`

返回*/etc/protocols*中与*protocol*关联的协议名称。

#### 获取最大可能返回的值。

`int getrandmax()`

返回由`rand()`返回的最大值。

#### 获取资源使用情况。

`array getrusage([int` *who*`])`

返回一个关联数组，描述当前脚本运行的进程正在使用的资源信息。如果指定了 *who*，并且等于`1`，则返回有关进程子级的信息。有关键和值描述的列表可以在 Unix 命令`getrusage(2)`下找到。

#### 获取由名称指定的服务。

`int getservbyname(string` *service*`, string` *protocol*`)`

返回*/etc/services*中与*service*关联的端口。*protocol*必须是 TCP 或 UDP。

#### 获取由端口和协议指定的服务。

`string getservbyport(int` *port*`, string` *protocol*`)`

返回*/etc/services*中与*port*和*protocol*关联的服务名称。*protocol*必须是 TCP 或 UDP。

#### 获取当前时间。

`mixed gettimeofday([ bool` *return_float*`])`

返回一个包含有关当前时间的信息的关联数组，通过`gettimeofday(2)`获取。当设置`return_float`为`true`时，将返回一个浮点数而不是数组。

数组包含以下键和值：

| `sec` | 自 Unix 时代以来的当前秒数 |
| --- | --- |
| `usec` | 添加到秒数的当前微秒数 |
| `minuteswest` | 当前时区比格林威治西的分钟数 |
| `dsttime` | 应用夏令时修正的类型（在适当的时间年份，如果时区遵循夏令时，则为正数） |

#### 获取类型。

`string gettype(mixed` *value*`)`

返回*value*类型的字符串描述。*value*的可能值为`"boolean"`、`"integer"`、`"float"`、`"string"`、`"array"`、`"object"`、`"resource"`、`"NULL"`和`"unknown type"`。

#### 全局模式匹配。

`globarray(string` *pattern*`[, int` *flags*`])`

返回符合给定*pattern*的 shell 通配符模式的文件名列表。以下字符和序列进行匹配：

| `*` | 匹配任意数量的任意字符（等同于正则表达式模式 `.*`） |
| --- | --- |
| `?` | 匹配任意一个字符（等同于正则表达式模式 `.`） |

例如，要处理特定目录中的每个 JPEG 文件，您可以编写：

```php
foreach(glob("/tmp/images/*.jpg") as $filename) {
 // do something with $filename
}
```

*flags*值是以下任意值的按位 OR：

| `GLOB_MARK` | 对返回的每个项添加斜杠 |
| --- | --- |
| `GLOB_NOSORT` | 返回的文件与目录中的顺序相同。如果未指定，则按 ASCII 值排序名称 |
| `GLOB_NOCHECK` | 如果未找到匹配*pattern*的文件，则返回*pattern* |
| `GLOB_NOESCAPE` | 将*pattern*中的反斜杠视为反斜杠，而不是作为转义序列的开始 |
| `GLOB_BRACE` | 除了正常匹配外，形如`{foo, bar, baz}`的字符串也匹配`"foo"`、`"bar"`或`"baz"` |
| `GLOB_ONLYDIR` | 仅返回匹配*pattern*的目录 |
| `GLOB_ERR` | 在读取错误时停止 |

#### gmdate

`string gmdate(string` *format*`[, int` *timestamp*`])`

返回一个时间戳日期和时间的格式化字符串。与`date()`相同，但它始终使用格林威治标准时间（GMT），而不是本地机器上指定的时区。

#### gmmktime

`int gmmktime(int` *hour*`, int` *minutes*`, int` *seconds*`, int` *month*`, int` *day*`, int` *year*`, int` *is_dst*`)`

从提供的值集合返回一个时间戳日期和时间值。与`mktime()`相同，但这些值代表的是 GMT 时间和日期，而不是本地时区的时间。

#### gmstrftime

`string gmstrftime(string` *format*`[, int` *timestamp*`])`

格式化 GMT 时间戳。查看`strftime`获取更多关于如何使用该函数的信息。

#### hash

`string hash(string` *algorithm*`, string` *data* `[, bool` *output*`])`

根据给定的*算法*生成提供的*数据*的哈希值。当*output*设置为`true`时，默认为`false`；返回的哈希值是原始二进制数据。*算法*的值可以是`md5`、`sha1`、`sha256`等。查看`hash_algos`获取更多算法信息。

#### hash_algos

`array hash_algos(void)`

返回所有支持的哈希算法的数字索引数组。

#### hash_file

`string hash_file(string` *algorithm*`, string` *filename* `[, bool` *output*`])`

根据给定的*算法*在*filename*（文件位置的 URL）的内容上生成哈希值字符串。当*output*设置为`true`时，默认为`false`；返回的哈希值是原始二进制数据。*算法*的值可以是`md5`、`sha1`、`sha256`等。

#### 头部

`void header(string` *header*`[, bool` *replace* `[, int` *http_response_code*`]])`

将*header*作为原始 HTTP 头字符串发送；必须在生成任何输出之前调用（包括空行——这是一个常见错误）。如果*header*是`Location`头，则 PHP 还会生成适当的`REDIRECT`状态码。如果指定了*replace*且为`false`，则该头部不会替换同名头部；否则，该头部将替换同名头部。

#### header_remove

`void header_remove([string` *header*`])`

如果指定了*header*，则从当前响应中删除名为*header*的 HTTP 头。如果未指定*header*或为一个空字符串，则从当前响应中删除`header()`函数生成的所有头部。请注意，如果头部已发送到客户端，则无法删除。

#### headers_list

`array headers_list()`

返回已准备发送（或已发送）到客户端的 HTTP 响应头的数组。

#### headers_sent

`bool headers_sent([ string &`*file* `[, int &`*line*`]])`

如果 HTTP 头已发送，则返回`true`。如果尚未发送，则函数返回`false`。如果提供了*file*和*line*选项，则将输出开始的文件名和行号放置在*file*和*line*变量中。

#### hebrev

`string hebrev(string` *string*`[, int` *size*`])`

将逻辑希伯来文本*string*转换为视觉希伯来文本。如果指定第二个参数，每行的字符数不超过*size*个字符；函数尝试避免断开单词。

#### hex2bin

`string hex2bin(string` *hex*`)`

将*十六进制*转换为其二进制值。

#### hexdec

`number hexdec(string` *hex*`)`

将*十六进制*转换为其十进制值。可转换为 32 位数字，即 2,147,483,647 十进制（0x7FFFFFFF 十六进制）。

#### highlight_file

`mixed highlight_file(string` *filename* `[, bool` *return*`])`

使用 PHP 内置的语法高亮器打印*filename*的语法着色版本。如果*filename*存在且为 PHP 源文件，则返回`true`；否则返回`false`。如果*return*为`true`，则高亮代码作为字符串返回，而不是发送到输出设备。

#### highlight_string

`mixed highlight_string(string` *source* `[, bool` *return*`])`

使用 PHP 内置的语法高亮器打印字符串*source*的语法着色版本。如果成功，则返回`true`；否则返回`false`。如果*return*为`true`，则高亮代码作为字符串返回，而不是发送到输出设备。

#### hrtime

`mixed hrtime([bool` *get_as_number*`])`

将系统的高分辨率时间作为数组返回，从任意时间点开始计算。交付的时间戳是单调的，不可调整。*get_as_number*返回数组（`false`）或数字（`true`）；默认为`false`。

#### htmlentities

`string htmlentities(string` *string*`[, int` *style*`[, string` *encoding* `[, bool` *double_encode*`]]])`

转换*string*中所有在 HTML 中具有特殊含义的字符，并返回结果字符串。转换所有 HTML 标准中定义的实体。如果提供了*style*，则确定引号翻译的方式。*style*的可能取值包括：

| `ENT_COMPAT`（默认） | 转换双引号，但不转换单引号 |
| --- | --- |
| `ENT_NOQUOTES` | 不转换双引号或单引号 |
| `ENT_QUOTES` | 转换双引号和单引号 |
| `ENT_SUBSTITUTE` | 用 Unicode 替换字符替换无效的代码单元序列 |
| `ENT_DISALLOWED` | 替换给定文档类型的无效代码点为 Unicode 替换字符 |
| `ENT_HTML401` | 将代码视为 HTML 4.01 处理 |
| `ENT_XML1` | 将代码视为 XML 1 处理 |
| `ENT_XHTML` | 将代码视为 XHTML 处理 |
| `ENT_HTML5` | 将代码视为 HTML 5 处理 |

如果提供了*encoding*，则确定字符的最终编码方式。*encoding*的可能取值包括：

| `ISO-8859-1` | 西欧、拉丁-1 |
| --- | --- |
| `ISO-8859-5` | 西里尔文字符集（拉丁/西里尔文），很少使用 |
| `ISO-8859-15` | 西欧、拉丁-9。增加了欧元符号、法语和芬兰语中拉丁-1 中缺失的字符。 |
| `UTF-8` | 兼容 ASCII 的多字节 8 位 Unicode |
| `cp866` | DOS 特定的西里尔文字符集 |
| `cp1251` | Windows 特定的西里尔文字符集 |
| `cp1252` | 西欧 Windows 特定字符集 |
| `KOI8-R` | 俄语 |
| `BIG5` | 主要用于台湾的繁体中文 |
| `GB2312` | 简体中文，国家标准字符集 |
| `BIG5-HKSCS` | 带有香港扩展的 Big5，繁体中文 |
| `Shift_JIS` | 日语 |
| `EUC-JP` | 日语 |
| `MacRoman` | Mac OS 使用的字符集 |
| `""` | 空字符串会依次从脚本编码（Zend 多字节）、`default_charset`和当前区域设置中进行检测。不推荐使用。 |

#### html_entity_decode

`string html_entity_decode(string` *string*`[, int` *style*`[, string` *encoding*`]])`

将*string*中所有 HTML 实体转换为等效字符。转换所有 HTML 标准中定义的实体。如果提供了*style*，则确定引号翻译的方式。*style*的可能取值与*htmlentities*相同。

如果提供了*encoding*，则确定字符的最终编码方式。*encoding*的可能取值与*htmlentities*相同。

#### htmlspecialchars

`string htmlspecialchars(string` *string*`[, int` *style*`[, string` *encoding*`[, bool` *double_encode*`]]])`

转换*string*中在 HTML 中具有特殊含义的字符，并返回结果字符串。使用了所有 HTML 标准中定义的常见字符实体的子集来执行转换。如果提供了*style*，则确定引号翻译的方式。转换的字符包括：

+   和号（&）变成`&amp;`

+   双引号（"）变成`&quot;`

+   单引号（'）变成`&#039;`

+   小于号（<）变成`&lt;`

+   大于号（`>`）变成 `&gt;`

*style* 的可能取值与 *htmlentities* 中的相同。如果提供了 *encoding*，则确定字符的最终编码方式，其可能取值也与 *htmlentities* 中的相同。当 *double_encode* 关闭时，PHP 不会对已有的 *htmlentities* 进行编码。

#### htmlspecialchars_decode

`string htmlspecialchars_decode(string` *string*`[, int` *style*`])`

将 *string* 中的 HTML 实体转换为字符。使用的 HTML 实体子集覆盖了最常见的字符，用于执行转换。如果提供了 *style*，则决定引号的转换方式。有关 *style* 可能的取值，请参阅 `htmlentities()`。转换的字符是在 `htmlspecialchars()` 中找到的那些。

#### http_build_query

`string http_build_query(mixed` *values*`[, string` *prefix* `[, string` *arg_separator* `[, int` *enc_type*`]]])`

根据第一个参数中提供的 *format* 字符串，将时间和日期格式化为整数。如果未指定第二个参数，则使用当前的时间和日期。*format* 字符串中可以识别以下字符：

#### hypot

`float hypot(float` *x*`, float` *y*`)`

计算并返回直角三角形斜边的长度，其中其他两边的长度分别为 *x* 和 *y*。

#### idate

`int idate(string` *format*`[, int` *timestamp*`])`

返回一个 URL 编码的查询字符串，该字符串来自 *values*。数组值可以是数值索引或关联索引（或两者结合）。由于某些语言可能不允许严格的数值名称解析查询字符串（例如 PHP），如果在 *values* 中使用数值索引，则还应提供 *prefix*。*prefix* 的值将添加到结果查询字符串中的所有数值名称之前。*arg_separator* 允许分配自定义分隔符，*enc_type* 选项允许选择不同的编码类型。

| `B` | 斯沃奇因特网时间 |
| --- | --- |
| `d` | 月份中的天数 |
| `h` | 12 小时制的小时数 |
| `H` | 24 小时制的小时数 |
| `i` | 分钟数 |
| `I` | 如果是夏令时则为 `1`；否则为 `0` |
| `j` | 月份中的天数（例如，1 到 31） |
| `L` | 如果年份不是闰年则为 `0`；如果是则为 `1` |
| `m` | 月份（1 到 12） |
| `s` | 秒数 |
| `t` | 月份中的天数，从 28 到 31 |
| `U` | 从 Unix 纪元开始计算的秒数 |
| `w` | 一周中的星期数，从星期日开始，用 `0` 表示 |
| `W` | 根据 ISO 8601 定义的年份中的周数 |
| `Y` | 四位数表示的年份（例如，1998） |
| `y` | 一位或两位数字表示的年份（例如，98） |
| `z` | 年份中的第几天，从 1 到 365 |
| `Z` | 时区偏移量（以秒为单位），从 −43200（UTC 的极西）到 43200（UTC 的极东） |

*format* 字符串中不匹配上述任一内容的字符将被忽略。尽管 `idate` 中使用的字符字符串与 `date` 中的类似，但因为 `idate` 返回整数，在 `date` 返回带有前导零的两位数数字的地方，前导零不会保留；例如，对于 2005 年的时间戳，`date('y');` 将返回 `05`，而 `idate('y');` 将返回 `5`。

#### ignore_user_abort

`int ignore_user_abort([string` *ignore*`])`

设置客户端断开连接时是否应停止处理 PHP 脚本。如果 *ignore* 是 `true`，则脚本将继续处理，即使客户端断开连接。返回当前值；如果未提供 *ignore*，则返回当前值，而不设置新值。

#### implode

`string implode(string` *separator*`, array` *strings*`)`

返回由将 *strings* 中的每个元素用 *separator* 连接而创建的字符串。

#### inet_ntop

`string inet_ntop(string` *address*`)`

将打包的 IPv4 或 IPv6 IP 地址 *address* 解包并以人类可读的字符串形式返回。

#### inet_pton

`string inet_pton(string` *address*`)`

将人类可读的 IP 地址 *address* 打包成 32 位或 128 位值并返回。

#### in_array

`bool in_array(mixed` *value*`, array` *array*`[, bool` *strict*`])`

如果给定 *value* 存在于 *array* 中则返回 `true`。如果提供了第三个参数并且是 `true`，则该函数仅在元素存在于数组中且与提供的值具有相同类型时才返回 `true`（即数组中的 `"1.23"` 将不会与参数中的 `1.23` 匹配）。如果数组中未找到参数，则该函数返回 `false`。

#### ini_get

`string ini_get(string` *variable*`)`

返回配置选项 *variable* 的值。如果 *variable* 不存在，则返回 `false`。

#### ini_get_all

`array ini_get_all([string` *extension* `[, bool` *details*`]])`

以关联数组形式返回所有配置选项。如果指定了有效的 *extension*，则仅返回与该命名 *extension* 相关的值。如果 *details* 是 `true`（默认），则检索详细设置。数组中返回的每个值都是一个带有三个键的关联数组：

| `global_value` | 作为 *php.ini* 中设置的配置选项的全局值 |
| --- | --- |
| `local_value` | 通过 `ini_set()` 设置的配置选项的本地覆盖值，例如 |
| `access` | 一个位掩码，表示可以设置值的级别（有关访问级别的更多信息，请参阅 `ini_set`） |

#### ini_restore

`void ini_restore(string` *variable*`)`

运行完整个脚本后，通过 `ini_set()` 设置的所有配置选项将自动恢复。

#### ini_set

`string ini_set(string` *variable*`, string` *value*`)`

将配置选项 *variable* 设置为 *value*。如果成功，返回先前的值；否则返回 `false`。新值在当前脚本执行期间保持，并在脚本结束后恢复。

#### intdiv

`int intdiv (int` *dividend*`, int` *vdivisor*`)`

返回 *dividend* 除以 *divisor* 的商。商作为整数返回。

#### interface_exists

`bool interface_exists(string` *name* `[, bool` *autoload_**interface*`])`

返回 `true` 如果名为 *name* 的接口已定义，否则返回 `false`。默认情况下，函数将在接口上调用 `__autoload()`；如果设置了 `autoload_interface` 并且为 `false`，则不会调用 `__autoload()`。

#### intval

`int intval(mixed` *value*`[, int` *base*`])`

使用可选的基数 *base* 返回 *value* 的整数值（如果未指定，则使用基数 10）。如果 *value* 是非标量值（对象或数组），则函数返回 `0`。

#### ip2long

`int ip2long(string` *address*`)`

将标点格式的 IP 地址转换为 IPv4 地址。

#### is_a

`bool is_a(object` *object*`, string` *class* `[, bool` *allow_string*`])`

返回 `true` 如果 *object* 是 *class* 类的实例，或者其类有 *class* 作为其父类之一；否则返回 `false`。如果 *allow_string* 是 `false`，则不允许将字符串 *class* 名称作为 *object*。

#### is_array

`bool is_array(mixed` *value*`)`

返回 `true` 如果 *value* 是数组；否则返回 `false`。

#### is_bool

`bool is_bool(mixed` *value*`)`

如果 *value* 是布尔值，则返回 `true`；否则返回 `false`。

#### is_callable

`int is_callable(callable` *callback*`[, int` *lazy*`[, string` *name*`]])`

返回 `true` 如果 *callback* 是有效的回调函数，否则返回 `false`。要有效，*callback* 必须是函数名或包含两个值的数组——一个对象和该对象上的方法名称。如果给出了 *lazy* 并且为 `true`，则不检查函数存在性（第一种形式）或 *callback* 的第一个元素是否为对象且该对象有名为第二个元素的方法。参数只需具有正确类型的值即可符合条件。如果提供了最后一个参数，该函数的可调用名称将填充到 *name* 中——尽管在回调是对象方法的情况下，*name* 中的结果名称实际上无法直接用于调用函数。

#### is_countable

`bool is_countable(mixed` *variable*`)`

验证 *variable* 的内容是否为 [数组](https://oreil.ly/rjM9i) 或实现 [`Countable`](https://oreil.ly/b97Lx) 的对象。

#### is_dir

`bool is_dir(string` *path*`)`

如果 *path* 存在且为目录，则返回 `true`；否则返回 `false`。此信息已缓存；你可以使用 `clearstatcache()` 清除缓存。

#### is_executable

`bool is_executable(string` *path*`)`

返回 `true` 如果 *path* 存在且可执行；否则返回 `false`。此信息已缓存；你可以使用 `clearstatcache()` 清除缓存。

#### is_file

`bool is_file(string` *path*`)`

如果 *path* 存在且是文件，则返回 `true`；否则返回 `false`。此信息已缓存；您可以使用 `clearstatcache()` 清除缓存。

#### is_finite

`bool is_finite(float` *value*`)`

如果 *value* 不是正或负无穷大，则返回 `true`；否则返回 `false`。

#### is_float

`bool is_float(mixed` *value*`)`

如果 *value* 是浮点数，则返回 `true`；否则返回 `false`。

#### is_infinite

`bool is_infinite(float` *value*`)`

如果 *value* 是正或负无穷大，则返回 `true`，否则返回 `false`。

#### is_int

`bool is_int(mixed` *value*`)`

如果 *value* 是整数，则返回 `true`；否则返回 `false`。

#### is_iterable

`bool is_iterable(mixed` *value*`)`

如果 *value* 是可迭代伪类型、数组或可遍历对象，则返回 `true`；否则返回 `false`。

#### is_link

`bool is_link(string` *path*`)`

如果 *path* 存在且是符号链接文件，则返回 `true`；否则返回 `false`。此信息已缓存；您可以使用 `clearstatcache()` 清除缓存。

#### is_nan

`bool is_nan(float` *value*`)`

如果 *value* 是“非数字”值，则返回 `true`；如果 *value* 是数字，则返回 `false`。

#### is_null

`bool is_null(mixed` *value*`)`

如果 *value* 是 null（即关键字 `NULL`），则返回 `true`；否则返回 `false`。

#### is_numeric

`bool is_numeric(mixed` *value*`)`

如果 *value* 是整数、浮点数值或包含数字的字符串，则返回 `true`；否则返回 `false`。

#### is_object

`bool is_object(mixed` *value*`)`

如果 *value* 是对象，则返回 `true`；否则返回 `false`。

#### is_readable

`bool is_readable(string` *path*`)`

如果 *path* 存在且可读，则返回 `true`；否则返回 `false`。此信息已缓存；您可以使用 `clearstatcache()` 清除缓存。

#### is_resource

`bool is_resource(mixed` *value*`)`

如果 *value* 是资源，则返回 `true`；否则返回 `false`。

#### is_scalar

`bool is_scalar(mixed` *value*`)`

如果 *value* 是标量值——整数、布尔值、浮点数值、资源或字符串，则返回 `true`。如果 *value* 不是标量值，则函数返回 `false`。

#### is_string

`bool is_string(mixed` *value*`)`

如果 *value* 是字符串，则返回 `true`；否则返回 `false`。

#### is_subclass_of

`bool is_subclass_of(object` *object*`, string` *class* `[, bool` *allow_string*`])`

如果 *object* 是 *class* 类或 *class* 类的子类的实例，则返回 `true`。否则，返回 `false`。如果 *allow_string* 参数设置为 `false`，则不允许 *class* “作为对象”。

#### is_uploaded_file

`bool is_uploaded_file(string` *path*`)`

如果 *path* 存在且是通过 web 页面表单中的 `file` 元素上传到 Web 服务器的文件，则返回 `true`；否则返回 `false`。有关使用上传文件的更多信息，请参见第八章。

#### is_writable

`bool is_writable(string` *path*`)`

如果 *path* 存在并且是一个目录，则返回 `true`；否则返回 `false`。这些信息被缓存；您可以使用 `clearstatcache()` 清除缓存。

#### isset

`bool isset(mixed` *value1*`[, ... mixed` *valueN*`])`

如果已设置变量 *value*，则返回 `true`；如果变量从未设置过或已经被 `unset()`，则返回 `false`。如果提供了多个 *values*，则只有当它们都设置了时，`isset` 才会返回 `true`。

#### json_decode

`mixed json_decode(string` *json*`, [bool` *assoc* `[, int` *depth* `[, int` *options*`]]])`

将一个 JSON 编码的字符串 *json* 转换为 PHP 变量。如果无法解码 JSON，则返回 `NULL`。当 *assoc* 为 `true` 时，对象将转换为关联数组。*depth* 是用户控制的递归级别。*options* 控制如何替代返回字符串中提供的一些数据。

#### json_encode

`mixed json_encode(mixed` *value* `[, int` *options* `[, int` *depth*`]])`

返回包含 *value* 的 JSON 表示的字符串。*options* 控制如何替代返回字符串中提供的一些数据。如果使用了 *depth*，它必须大于零。

#### key

`mixed key(array &`*array*`)`

返回当前由内部数组指针指向的元素的键名。

#### krsort

`int krsort(array` *array*`[, int` *flags*`])`

通过键名以逆序对数组进行排序，保留数组值的键名。可选的第二个参数包含额外的排序标志。有关使用此函数的更多信息，请参阅 第五章 和 `sort`。

#### ksort

`int ksort(array` *array*`[, int` *flags*`])`

通过键名对数组进行排序，保留数组值的键名。可选的第二个参数包含额外的排序标志。有关使用此函数的更多信息，请参阅 第五章 和 `sort`。

#### lcfirst

`string lcfirst(string` *string*`)`

返回一个字符串，其中第一个字符（如果是字母）将转换为小写。用于转换字符的表是特定于区域设置的。

#### lcg_value

`float lcg_value()`

返回一个介于 0 和 1 之间的伪随机浮点数，使用线性同余数生成器。

#### lchgrp

`bool lchgrp(string` *path*`, mixed` *group*`)`

将符号链接 *path* 的组更改为 *group*；PHP 必须具有适当的特权才能使此函数正常工作。如果更改成功，则返回 `true`；如果不成功，则返回 `false`。

#### lchown

`bool lchown(string` *path*`, mixed` *user*`)`

将符号链接 *path* 的所有权更改为名为 *user* 的用户。PHP 必须具有适当的特权（通常是 root）才能使函数正常工作。如果更改成功，则返回 `true`；如果不成功，则返回 `false`。

#### levenshtein

`int levenshtein(string` *one*`, string` *two*`[, int` *insert*`, int` *replace*`,int` *delete*`]) int levenshtein(string` *one*`, string` *two*`[, mixed` *callback*`])`

计算两个字符串之间的 Levenshtein 距离。这是将*one*转换为*two*所需的替换、插入或删除字符数。默认情况下，替换、插入和删除具有相同的成本，但可以使用*insert*、*replace*和*delete*指定不同的成本。在第二种形式中，仅返回插入、替换和删除的总成本，而不是分解成部分成本。

#### link

`bool link(string` *path*`, string` *new*`)`

在路径 *new* 处创建到 *path* 的硬链接。如果成功创建链接，则返回`true`，否则返回`false`。

#### linkinfo

`int linkinfo(string` *path*`)`

如果*path*是一个链接并且*path*引用的文件存在，则返回`true`。如果*path*不是链接，*path*引用的文件不存在或发生错误，则返回`false`。

#### list

`array list(mixed` *value1*`[, ...` *valueN*`])`

从数组中的元素分配一组变量。例如：

```php
list($first, $second) = array(1, 2); // $first = 1, $second = 2
```

###### 注意

`list` 实际上是一种语言结构。

#### localeconv

`array localeconv()`

返回当前区域设置的数字和货币格式的关联数组信息。数组包含以下元素：

| `decimal_point` | 小数点字符 |
| --- | --- |
| `thousands_sep` | 千位分隔符字符 |
| `grouping` | 数字分组的数组；指示数字应在哪里使用千位分隔符字符分隔 |
| `int_curr_symbol` | 国际货币符号（例如，USD） |
| `currency_symbol` | 本地货币符号（例如，$） |
| `mon_decimal_point` | 货币值的小数点字符 |
| `mon_thousands_sep` | 用于货币值中千位分隔符的分隔符字符 |
| `positive_sign` | 正值的符号 |
| `negative_sign` | 负值的符号 |
| `int_frac_digits` | 国际小数位数 |
| `frac_digits` | 本地小数位数 |
| `p_cs_precedes` | 如果本地货币符号在正值之前，则为`true`；如果在值之后，则为`false` |
| `p_sep_by_space` | 如果本地货币符号与正值之间有空格，则为`true` |
| `p_sign_posn` | 如果符号用括号括起来且用于正值和货币符号，则为`0`；如果符号在货币符号和值之前，则为`1`；如果符号在货币符号和值之后，则为`2`；如果符号在货币符号之前，则为`3`；如果符号在货币符号之后，则为`4` |
| `n_cs_precedes` | 如果本地货币符号在负值之前，则为`true`；如果在值之后，则为`false` |
| `n_sep_by_space` | 如果本地货币符号与负值之间有空格，则为`true` |
| `n_sign_posn` | 如果符号用括号括起来且用于负值和货币符号，则为`0`；如果符号在货币符号和值之前，则为`1`；如果符号在货币符号和值之后，则为`2`；如果符号在货币符号之前，则为`3`；如果符号在货币符号之后，则为`4` |

#### localtime

`array localtime([int` *timestamp*`[, bool` *associative*`]])`

返回与同名 C 函数所给定的值数组。第一个参数是时间戳；如果提供第二个参数并且为`true`，则将作为关联数组返回值。如果未提供第二个参数或为`false`，则返回数字数组。返回的键和值为：

| `tm_sec` | 秒数 |
| --- | --- |
| `tm_min` | 分钟数 |
| `tm_hour` | 小时数 |
| `tm_mday` | 月份中的日期 |
| `tm_mon` | 年份中的月份 |
| `tm_year` | 自 1900 年以来的年数 |
| `tm_wday` | 星期中的日期 |
| `tm_yday` | 年份中的日数 |
| `tm_isdst` | 如果日期和时间处于夏令时，则为`1` |

如果返回数字数组，则其值按上述顺序给出。

#### log

`float log(float` *number* `[, float` *base* `])`

返回*number*的自然对数。*base*选项控制将使用的对数基数；默认为*e*，即自然对数。

#### log10

`float log10(float` *number* `)`

返回*number*的十进制对数。

#### log1p

`float log1p(float` *number* `)`

返回`log(1 +` *number* `)`，以确保即使*number*接近零，返回的值也是准确的。

#### long2ip

`string long2ip(string` *address* `)`

将 IPv4 地址转换为点分（标准格式）地址。

#### lstat

`array lstat(string` *path* `)`

返回关于文件*path*的信息的关联数组。如果*path*是符号链接，则返回关于*path*的信息，而不是指向的文件的信息。参见`fstat`以获取返回的值及其含义的列表。

#### ltrim

`string ltrim(string` *string* `[, string` *characters* `])`

返回从*characters*中剥离开头的所有字符的*string*。如果未指定*characters*，则剥离的字符为`\n`、`\r`、`\t`、`\v`、`\0`和空格。

#### mail

`bool mail(string` *recipient* `, string` *subject* `, string` *message* `[, string` *headers* `[, string` *parameters* `]])`

将*message*通过电子邮件发送给*recipient*，并在成功发送时返回`true`，如果发送失败则返回`false`。如果指定了*headers*，则将其添加到为消息生成的标题末尾，允许您添加 cc：、bcc：和其他标题。要添加多个标题，请使用`\n`字符（或 Windows 服务器上的`\r\n`字符）分隔它们。最后，如果指定了*parameters*，则将其添加到用于发送邮件的邮件程序的参数中。

#### max

`mixed max(mixed` *value1* `[, mixed` *value2* `[, ... mixed` *valueN* `]])`

如果*value1*是一个数组，则返回数组值中的最大数值。如果不是，则返回参数中的最大数值。

#### md5

`string md5(string` *string* `[, bool` *binary* `])`

计算*string*的 MD5 加密哈希并返回。如果*binary*选项为`true`，则返回的 MD5 哈希以原始二进制格式（长度为 16）；*binary*默认为`false`，因此`md5`返回一个完整的 32 字符十六进制字符串。

#### md5_file

`string md5_file(string` *path*`[, bool` *binary*`])`

计算并返回*path*处文件的 MD5 加密哈希值。MD5 哈希是一个 32 字符的十六进制值，可用于对文件数据进行校验和。如果提供了*binary*并且为`true`，则结果将作为 16 位二进制值发送。

#### memory_get_peak_usage

`int memory_get_peak_usage([bool` *actual*`])`

返回当前运行脚本的迄今为止的最大内存使用量（以字节为单位）。如果指定了*actual*且为`true`，则返回实际分配的字节数；否则，返回通过 PHP 内部内存分配例程分配的字节数。

#### memory_get_usage

`int memory_get_usage([bool` *actual*`])`

返回当前运行脚本的内存使用量（以字节为单位）。如果指定了*actual*且为`true`，则返回实际分配的字节数；否则，返回通过 PHP 内部内存分配例程分配的字节数。

#### metaphone

`string metaphone(string` *string*`, int` *max_phonemes*`)`

计算*string*的 metaphone 键。在计算值时，使用的最大音素数由*max_phonemes*指定。发音相似的英语单词生成相同的键。

#### method_exists

`bool method_exists(object` *object*`, string` *name*`)`

如果对象包含第二个参数指定名称的方法，则返回`true`，否则返回`false`。方法可以在对象是其实例的类中定义，或者在该类的任何超类中定义。

#### microtime

`mixed microtime([ bool` *get_as_float*`])`

返回以*秒数自 Unix 纪元（1970 年 1 月 1 日）起的秒数*微秒格式的字符串，其中*seconds*是秒数，*microseconds*是自 Unix 纪元以来的微秒部分。如果*get_as_float*为`true`，则返回浮点数而不是字符串。

#### min

`mixed min(mixed` *value1*`[, mixed` *value2*`[, ... mixed` *valueN*`]])`

如果*value1*是数组，则返回数组值中找到的最小数。否则，返回参数中找到的最小数。

#### mkdir

`bool mkdir(string` *path*`[, int` *mode* `[, bool` *recursive* `[, resource` *context*`]]])`

创建具有*mode*权限的目录*path*。*mode*期望是八进制数字，如`0755`。像`755`这样的整数值或像`"u+x"`这样的字符串值将无法按预期工作。如果操作成功，则返回`true`，否则返回`false`。如果使用了递归参数，允许创建嵌套目录。

#### mktime

`int mktime(int` *hours*`, int` *minutes*`, int` *seconds*`, int` *month*`, int` *day*`, int` *year* `[, int` *is_dst*`])`

返回与参数对应的 Unix 时间戳值，参数顺序为*hours*、*minutes*、*seconds*、*month*、*day*、*year*，以及（可选）时间是否处于夏令时。此时间戳是 Unix 纪元和给定日期时间之间经过的秒数。

参数的顺序与标准的 Unix `mktime()`调用不同，使得可以更简单地忽略不需要的参数。任何省略的参数都将使用当前的本地日期和时间。

#### move_uploaded_file

`bool move_uploaded_file(string` *from*`, string` *to*`)`

将文件从*from*移动到新位置*to*。该函数仅在*from*通过 HTTP `POST`上传时才移动文件。如果*from*不存在或不是已上传的文件，或者发生任何其他错误，则返回`false`；如果操作成功，则返回`true`。

#### mt_getrandmax

`int mt_getrandmax()`

返回`mt_rand()`可以返回的最大值。

#### mt_rand

`int mt_rand([int` *min*`, int` *max*`])`

返回使用 Mersenne Twister 伪随机数生成器生成的从*min*到*max*之间的随机数（包括*min*和*max*）。如果没有提供*min*和*max*，则返回从 0 到`mt_getrandmax()`返回值之间的随机数。

#### mt_srand

`void mt_srand(int` *seed*`)`

使用*seed*初始化 Mersenne Twister 生成器。在调用`mt_rand()`之前，应该使用类似`time()`返回的变化的数值来调用此函数。

#### natcasesort

`void natcasesort(array` *array*`)`

使用不区分大小写的“自然顺序”算法对给定数组的元素进行排序；有关更多信息，请参阅`natsort`。

#### natsort

`bool natsort(array` *array*`)`

使用“自然顺序”对数组的值进行排序：数字值按语言预期的方式排序，而不是计算机通常强迫将它们放入的怪异顺序（ASCII 顺序）。例如：

```php
$array = array("1.jpg", "4.jpg", "12.jpg", "2,.jpg", "20.jpg");
$first = sort($array); // ("1.jpg", "12.jpg", "2.jpg", "20.jpg", "4.jpg")
$second = natsort($array); // ("1.jpg", "2.jpg", "4.jpg", "12.jpg", "20.jpg")
```

#### next

`mixed next(array` *array*`)`

将内部指针增加到当前元素后的元素，并返回现在指针指向的元素的值。如果内部指针已经指向数组中最后一个元素之后，则函数返回`false`。

使用此函数迭代数组时要小心——如果数组包含空元素或键值为`0`的元素，则返回等效于`false`的值，导致循环结束。如果数组可能包含空元素或键值为`0`的元素，请改用`each`函数而不是带有`next`的循环。

#### nl_langinfo

`string nl_langinfo(int` *item*`)`

返回当前区域设置中关于*item*的信息字符串；*item*是许多不同值之一，例如日期名称、时间格式字符串等。实际的可能值因 C 库的不同实现而异；请参阅您的机器上的`<langinfo.h>`获取您的操作系统的值。

#### nl2br

`string nl2br(string` *string* `[, bool` *xhtml_lb*`])`

在*string*中的所有换行字符前插入`<br />`，并返回新字符串。如果*xhtml_lb*为`true`，则`nl2br`将使用符合 XHTML 的换行符。

#### number_format

`string number_format(float` *number*`[, int` *precision*`[, string` *decimal_separator*`, string` *thousands_separator*`]])`

创建*number*的字符串表示。如果给定*precision*，则将数字四舍五入到指定的小数位数；默认为不保留小数位数，创建整数。如果提供*decimal_separator*和*thousands_separator*，它们将分别用作小数点字符和千位分隔符，默认为英语区域设置版本（`.`和`,`）。例如：

```php
$number = 7123.456;
$english = number_format($number, 2); // 7,123.45
$francais = number_format($number, 2, ',', ' '); // 7 123,45
$deutsche = number_format($number, 2, ',', '.'); // 7.123,45
```

如果进行四舍五入，则执行适当的四舍五入，这可能不是您期望的（参见`round`）。

#### `ob_clean`

`void ob_clean()`

清空输出缓冲区的内容。与`ob_end_clean()`不同，输出缓冲区不会被关闭。

#### `ob_end_clean`

`bool ob_end_clean()`

关闭输出缓冲并清空当前缓冲区，但不发送到客户端。更多关于使用输出缓冲的信息，请参见第十五章。

#### `ob_end_flush`

`bool ob_end_flush()`

将当前输出缓冲区发送到客户端并停止输出缓冲。更多关于使用输出缓冲的信息，请参见第十五章。

#### `ob_flush`

`void ob_flush()`

将输出缓冲区的内容发送到客户端并丢弃内容。与调用`ob_end_flush()`不同，不会关闭输出缓冲区本身。

#### `ob_get_clean`

`string ob_get_clean()`

返回输出缓冲区的内容并结束输出缓冲。

#### `ob_get_contents`

`string ob_get_contents()`

返回当前输出缓冲区的内容；如果之前未使用`ob_start()`启用缓冲，则返回`false`。更多关于使用输出缓冲的信息，请参见第十五章。

#### `ob_get_flush`

`string ob_get_flush()`

返回输出缓冲区的内容，将输出缓冲刷新到客户端，并结束输出缓冲。

#### `ob_get_length`

`int ob_get_length()`

返回当前输出缓冲区的长度，如果输出缓冲未启用，则返回`false`。更多关于使用输出缓冲的信息，请参见第十五章。

#### `ob_get_level`

`int ob_get_level()`

返回嵌套输出缓冲区的计数，如果当前未启用输出缓冲，则返回`0`。

#### `ob_get_status`

`array ob_get_status([bool` *verbose*`])`

返回有关当前输出缓冲区的状态信息。如果提供了*verbose*并且为`true`，则返回所有嵌套输出缓冲区的信息。

#### `ob_gzhandler`

`string ob_gzhandler(string` *buffer*`[, int` *mode*`])`

此函数在将输出发送到浏览器之前对其进行*gzip*压缩。不直接调用此函数，而是使用`ob_start()`函数进行输出缓冲的处理。要启用*gzip*压缩，请使用此函数的名称调用`ob_start()`：

```php
<ob_start("ob_gzhandler");>
```

#### `ob_implicit_flush`

`void ob_implicit_flush([int` *flag*`])`

如果 *flag* 是 `true` 或未指定，则打开具有隐式刷新的输出缓冲。启用隐式刷新时，输出缓冲在任何输出（如 `printf()` 和 `echo()` 函数）后被清除并发送到客户端。有关使用输出缓冲的更多信息，请参见 第十五章。

#### ob_list_handlers

`array ob_list_handlers()`

返回一个数组，其中包含活动输出处理程序的名称。如果启用了 PHP 的内置输出缓冲，数组包含值 `default output handler`。如果没有活动的输出处理程序，则返回一个空数组。

#### ob_start

`bool ob_start([string` *callback* `[, int` *chunk* `[, bool` *erase*`]]])`

打开输出缓冲，导致所有输出都累积在缓冲区中，而不直接发送到浏览器。如果指定了 *callback*，它是一个函数（在将输出缓冲发送到客户端之前调用），可以以任何方式修改数据；提供了 `ob_gzhandler()` 函数以客户端感知的方式压缩输出缓冲。*chunk* 选项可用于在缓冲区大小等于块号时触发刷新缓冲区。如果将 *erase* 选项设置为 `false`，则缓冲区将在脚本结束时才被删除。有关使用输出缓冲的更多信息，请参见 第十五章。

#### octdec

`number octdec(string` *octal*`)`

将 *octal* 转换为其十进制值。最多可以转换为 32 位数字，或者十进制的 2,147,483,647（017777777777 八进制）。

#### opendir

`resource opendir(string` *path*`[, resource context])`

打开目录 *path* 并返回一个目录句柄，用于后续调用 `readdir()`、`rewinddir()` 和 `closedir()`。如果 *path* 不是有效的目录，如果权限不允许 PHP 进程读取目录，或者发生任何其他错误，则返回 `false`。

#### openlog

`bool openlog(string` *identity*`, int` *options*`, int` *facility*`)`

打开到系统日志记录器的连接。每个使用后续调用 `syslog()` 发送到记录器的消息都以 *identity* 开头。可以通过 *options* 指定各种选项；`OR` 任何你想要包括的选项。有效的选项包括：

| `LOG_CONS` | 如果在写入系统日志时发生错误，则将错误写入系统控制台 |
| --- | --- |
| `LOG_NDELAY` | 立即打开系统日志 |
| `LOG_ODELAY` | 直到写入第一条消息时才延迟打开系统日志 |
| `LOG_PERROR` | 将此消息打印到标准错误输出，同时写入系统日志 |
| `LOG_PID` | 每条消息中包含进程 ID |

第三个参数 *facility* 告诉系统日志正在记录到系统日志的程序类型。以下设施可用：

| `LOG_AUTH` | 安全和授权错误（已弃用；如果有 `LOG_AUTHPRIV` 可用，请使用它） |
| --- | --- |
| `LOG_AUTHPRIV` | 安全和授权错误 |
| `LOG_CRON` | 时钟守护进程（*cron* 和 *at*）错误 |
| `LOG_DAEMON` | 系统守护程序的错误 |
| `LOG_KERN` | 内核错误 |
| `LOG_LPR` | 行打印子系统错误 |
| `LOG_MAIL` | 邮件错误 |
| `LOG_NEWS` | USENET 新闻系统错误 |
| `LOG_SYSLOG` | *syslogd* 内部生成的错误 |
| `LOG_AUTHPRIV` | 安全和授权错误 |
| `LOG_USER` | 通用用户级错误 |
| `LOG_UUCP` | UUCP 错误 |

#### ord

`int ord(string` *string*`)`

返回 *string* 中第一个字符的 ASCII 值。

#### output_add_rewrite_var

`bool output_add_rewrite_var(string` *name*`, string` *value*`)`

开始使用值重写输出处理程序，通过将名称和值附加到所有 HTML 锚点元素和表单来实现。例如：

```php
output_add_rewrite_var('sender', 'php');

echo "<a href=\"foo.php\">\n";
echo '<form action="bar.php"></form>';

// outputs:
// <a href="foo.php?sender=php">
// <form action="bar.php"><input type="hidden" name="sender" value="php" />
// </form>
```

#### output_reset_rewrite_vars

`bool output_reset_rewrite_vars()`

重置值写入输出处理程序；如果值写入输出处理程序正在生效，则任何尚未刷新的输出在调用此方法后将不再受重写的影响，即使在此调用之前已放入缓冲区。

#### pack

`string pack(string` *format*`, mixed` *arg1*`[, mixed` *arg2*`[, ... mixed` *argN*`]])`

创建一个包含根据格式字符串对给定参数进行打包的二进制字符串。每个字符后面可以跟随一系列用于该格式的参数，或者一个星号（`*`），表示使用所有输入数据的剩余参数。如果未指定重复参数，则每个格式字符将使用一个参数。以下字符在 *format* 字符串中有特殊含义：

| `a` | 以 NUL 字节填充的字符串 |
| --- | --- |
| `A` | 空格填充的字符串 |
| `h` | 十六进制字符串，低半字节在前 |
| `H` | 十六进制字符串，高半字节在前 |
| `c` | 有符号字符型 |
| `C` | 无符号字符型 |
| `s` | 16 位，机器相关字节顺序的有符号短整型 |
| `S` | 16 位，机器相关字节顺序的无符号短整型 |
| `n` | 16 位，大端字节顺序的无符号短整型 |
| `v` | 16 位，小端字节顺序的无符号短整型 |
| `i` | 机器相关大小和字节顺序的有符号整型 |
| `I` | 机器相关大小和字节顺序的无符号整型 |
| `l` | 机器相关字节顺序的有符号长整型 |
| `L` | 32 位，机器相关字节顺序的无符号长整型 |
| `N` | 32 位，大端字节顺序的无符号长整型 |
| `V` | 32 位，小端字节顺序的无符号长整型 |
| `f` | 机器相关大小和表示的浮点型 |
| `d` | 机器相关大小和表示的双精度浮点型 |
| `x` | NUL 字节 |
| `X` | 向后备份一个字节 |
| `@` | 用 NUL 字节填充到绝对位置（由重复参数给出） |

#### parse_ini_file

`array parse_ini_file(string` *filename*`[, bool` *process_sections*`[, int` *scanner_mode*`]])`

载入 *filename* — 必须是标准 *php.ini* 格式的文件 — 并将其中的值作为关联数组返回，如果无法解析文件，则返回 `false`。如果设置了 *process_sections* 且为 `true`，则返回包含文件各部分值的多维数组。*scanner_mode* 选项为 `INI_SCANNER_NORMAL`（默认），或 `INI_SCANNER_RAW`，表示函数不应解析选项值。

#### parse_ini_string

`array parse_ini_string(string` *config*`[, bool` *process_sections*`[, int` *scanner_mode*`]])`

解析 *php.ini* 格式的字符串，并将其中的值作为关联数组返回，如果无法解析字符串，则返回 `false`。如果设置了 *process_sections* 且为 `true`，则返回包含文件各部分值的多维数组。*scanner_mode* 选项为 `INI_SCANNER_NORMAL`（默认），或 `INI_SCANNER_RAW`，表示函数不应解析选项值。

#### parse_str

`void parse_str(string` *string*`[, array` *variables*`])`

解析 *string*，就像来自 HTTP `POST` 请求一样，将找到的值设置为本地作用域中的变量。如果提供了 *variables*，则使用字符串中的键和值设置数组。

#### parse_url

`mixed parse_url(string` *url*`[, int` *component*`])`

返回包含 *url* 的各组成部分信息的关联数组。数组包含以下值：

| `fragment` | URL 中的命名锚点 |
| --- | --- |
| `host` | 主机名 |
| `pass` | 用户的密码 |
| `path` | 请求的路径（可能是目录或文件） |
| `port` | 协议使用的端口 |
| `query` | 查询信息 |
| `scheme` | URL 中的协议，如 “http” |
| `user` | URL 中提供的用户 |

数组不包含 URL 中未指定的组件值。例如：

```php
$url = "http://www.oreilly.net/search.php#place?name=php&type=book";
$array = parse_url($url);
print_r($array); // contains values for "scheme", "host", "path", "query",
 // and "fragment"
```

如果提供了组件选项，则仅返回 URL 的特定组件。

#### passthru

`void passthru(string` *command*`[, int` *return*`])`

通过 shell 执行 *command* 并将命令的结果输出到页面。如果指定了 *return*，则将其设置为命令的返回状态。如果想捕获命令的结果，请使用 `exec()`。

#### pathinfo

`mixed pathinfo(string` *path*`[, int` *options*`])`

返回包含关于 *path* 的信息的关联数组。如果给定了 *options* 参数，则指定要返回的特定元素。`PATHINFO_DIRNAME`、`PATHINFO_BASENAME`、`PATHINFO_EXTENSION` 和 `PATHINFO_FILENAME` 是有效的 *options* 值。

返回的数组包括以下元素：

| `dirname` | *path* 所在的目录 |
| --- | --- |
| `basename` | *path* 的基本名称（参见 `basename`），包括文件的扩展名。 |
| `extension` | 文件名的扩展名（如果有）。不包括扩展名开头的句点。 |

#### pclose

`int pclose(resource` *handle*`)`

关闭由*handle*引用的管道。返回在管道中运行的进程的终止代码。

#### pfsockopen

`resource pfsockopen(string` *host*`, int` *port*`[, int` *error*`[, string` *message* `[, float` *timeout*`]]])`

在特定*port*上对远程*host*开启持久的 TCP 或 UDP 连接。默认情况下使用 TCP；若要通过 UDP 连接，*host*必须以`udp://`开头。如果指定了*timeout*，则表示等待超时的秒数。

如果连接成功，则该函数返回一个虚拟文件指针，可用于函数（如`fgets()`和`fputs()`）。如果连接失败，则返回`false`。如果提供了*error*和*message*，则设置为错误号和错误字符串，分别。

与`fsockopen()`不同，此函数打开的套接字在完成读取或写入操作后不会自动关闭；您必须显式调用`fsclose()`关闭它。

#### php_ini_loaded_file

`string php_ini_loaded_file()`

如果存在当前的*php.ini*文件，则返回其路径；否则返回`false`。

#### php_ini_scanned_files

`string php_ini_scanned_files()`

返回包含 PHP 启动时解析的配置文件名称的字符串列表，以逗号分隔。如果编译时配置选项`--with-config-file-scan-dir`未设置，则返回`false`。

#### php_logo_guid

`string php_logo_guid()`

返回一个用于链接到 PHP 标志的 ID。例如：

```php
<?php $current = basename($PHP_SELF); ?>
<img src="<?= "$current?=" . php_logo_guid(); ?>" border="0" />
```

#### php_sapi_name

`string php_sapi_name()`

返回描述 PHP 运行的服务器 API 的字符串，例如，`"cgi"`或`"apache"`。

#### php_strip_whitespace

`string php_strip_whitespace(string` *path*`)`

返回从文件*path*中去除空白和注释标记的源代码字符串。

#### php_uname

`string php_uname(string` *mode*`)`

返回描述 PHP 运行的操作系统的字符串。*mode*参数是一个单字符，用于控制返回内容。可能的值包括：

| `a`（默认） | 包括所有模式（`s`，`n`，`r`，`v`，`m`） |
| --- | --- |
| `s` | 操作系统的名称 |
| `n` | 主机名 |
| `r` | 发布名称 |
| `v` | 版本信息 |
| `m` | 机器类型 |

#### phpcredits

`bool phpcredits([int` *what*`])`

输出有关 PHP 及其开发人员的信息；显示的信息基于*what*的值。要使用多个选项，请将值进行`OR`运算。*what*的可能值包括：

| `CREDITS_ALL`（默认） | 除了`CREDITS_SAPI`之外的所有贡献 |
| --- | --- |
| `CREDITS_GENERAL` | PHP 的一般贡献信息 |
| `CREDITS_GROUP` | PHP 核心开发人员列表 |
| `CREDITS_DOCS` | 文档团队信息 |
| `CREDITS_MODULES` | 当前加载的扩展模块及其各自的作者列表 |
| `CREDITS_SAPI` | 服务器 API 模块及其各自的作者列表 |
| `CREDITS_FULLPAGE` | 表示应返回完整的 HTML 页面，而不仅仅是 HTML 代码片段。必须与一个或多个其他选项一起使用，例如`phpcredits(CREDITS_MODULES &#124; CREDITS_FULLPAGE)` |

#### phpinfo

`bool phpinfo([int` *what*`])`

输出有关当前 PHP 环境状态的大量信息，包括加载的扩展、编译选项、版本、服务器信息等等。如果指定了*what*，可以限制输出到特定的信息片段；*what*可以包含多个使用`OR`操作符连接的选项。*what*的可能取值有：

| `INFO_ALL`（默认） | 所有信息 |
| --- | --- |
| `INFO_GENERAL` | PHP 的一般信息 |
| `INFO_CREDITS` | PHP 的贡献者信息，包括作者 |
| `INFO_CONFIGURATION` | 配置和编译选项 |
| `INFO_MODULES` | 当前加载的扩展 |
| `INFO_ENVIRONMENT` | PHP 环境信息 |
| `INFO_VARIABLES` | 当前变量及其值的列表 |
| `INFO_LICENSE` | PHP 许可证 |

#### phpversion

`string phpversion(string` *extension*`)`

返回当前运行的 PHP 解析器的版本。如果使用*extension*选项，并命名特定的扩展，那么将仅返回关于该扩展的版本信息。

#### pi

`float pi()`

返回 pi 的近似值（3.14159265359）。

#### popen

`resource popen(string` *command*`, string` *mode*`)`

打开一个到通过在 shell 上运行*command*执行的进程的管道。

参数*mode*指定以只读或只写的权限打开文件的模式。*mode*必须是以下之一：

| `r` | 以读取模式打开文件；文件指针将位于文件的开头 |
| --- | --- |
| `w` | 以写入模式打开文件。如果文件存在，则将其截断为零长度；如果文件不存在，则创建该文件 |

如果在尝试打开管道时发生任何错误，则返回`false`。如果成功打开管道，则返回管道的资源句柄。

#### pow

`number pow(number` *base*`, number` *exponent*`)`

返回*base*的*exponent*次幂。如果可能，返回值是整数；否则为浮点数。

#### prev

`mixed prev(array` *array*`)`

将内部指针移动到其当前位置之前的元素，并返回内部指针现在设置的元素的值。如果内部指针已经设置为数组的第一个元素，则返回`false`。在使用此函数迭代数组时要小心——如果数组具有空元素或键值为`0`的元素，则返回等效于`false`的值，导致循环结束。如果数组可能包含空元素或键为`0`的元素，请改用`each()`函数而不是使用`prev()`的循环。

#### print_r

`mixed print_r(mixed` *value*`[, bool` *return*`])`

以人类可读的方式输出 *value*。如果 *value* 是字符串、整数或浮点数，则输出该值；如果是数组，则显示键和元素；如果是对象，则显示对象的键和值。此函数返回 `true`。如果将 *return* 设置为 `true`，则返回输出而不是显示。

#### printf

`int printf(string` *format*`[, mixed` *arg1* `...])`

输出通过使用 *format* 和给定参数创建的字符串。参数放置到字符串中的各个位置，这些位置由 *format* 字符串中的特殊标记表示。

每个标记以百分号（`%`）开头，并按顺序包含以下元素。除了类型说明符外，所有说明符都是可选的。要在字符串中包含百分号，请使用 `%%`。

1.  强制使用符号（-或+）的可选符号说明符。默认情况下，仅在数字为负数时使用-号。此外，此说明符强制正数附加+号。

1.  补位说明符表示用于将结果填充到适当字符串大小的字符（如下所示）。可以指定 `0`、空格或以单引号前缀的任何字符；使用空格进行填充是默认的。

1.  对齐说明符。默认情况下，字符串被填充为右对齐。要使其左对齐，请在此处指定破折号（`–`）。

1.  此元素应包含的最小字符数。如果结果少于此数字的字符数，则前面的说明符决定填充到适当宽度的行为。

1.  浮点数的精度说明符由句点和数字组成；这决定了显示多少位小数。对于除了浮点数以外的类型，此说明符将被忽略。

1.  最后是类型说明符。此说明符告诉 `printf()` 正在向该标记的函数传递的数据类型。有八种可能的类型：

| `b` | 参数是整数，并显示为二进制数字 |
| --- | --- |
| `c` | 参数是整数，并显示为具有该值的字符 |
| `d` | 参数是整数，并显示为十进制数字 |
| `f` | 参数是浮点数，并显示为浮点数 |
| `o` | 参数是整数，并显示为八进制（基数为 8）数字 |
| `s` | 参数被视为字符串并显示 |
| `x` | 参数是整数，并显示为十六进制（基数为 16）数字；使用小写字母 |
| `X` | 与 `x` 相同，但使用大写字母 |

#### proc_close

`int proc_close(resource` *handle*`)`

关闭由 *handle* 引用并先前由 `proc_open()` 打开的进程。返回进程的终止代码。

#### proc_get_status

`array proc_get_status(resource` *handle*`)`

返回包含有关先前由 `proc_open()` 打开的 *handle* 进程的信息的关联数组。数组包含以下值：

| `command` | 打开此进程的命令字符串 |
| --- | --- |
| `pid` | 进程 ID |
| `running` | 如果进程当前正在运行，则为`true`，否则为`false` |
| `signaled` | 如果进程被未捕获的信号终止，则为`true`，否则为`false` |
| `stopped` | 如果进程被信号停止，则为`true`，否则为`false` |
| `exitcode` | 如果进程已终止，则来自进程的退出代码，否则为`–1` |
| `termsig` | 如果`signaled`为`true`，则导致进程终止的信号，否则为`undefined` |
| `stopsig` | 如果`stopped`为`true`，则导致进程停止的信号，否则为`undefined` |

#### proc_nice

`bool proc_nice(int` *increment*`)`

通过*increment*改变执行当前脚本的进程的优先级。负值提高进程的优先级，正值降低进程的优先级。如果操作成功则返回`true`，否则返回`false`。

#### proc_open

`resource proc_open(string` *command*`, array` *descriptors*`, array` *pipes*`[, string` *dir*`[, array` *env*`[, array` *options*`]]])`

打开管道以通过 shell 运行*command*执行的进程，并带有各种选项。descriptors 参数必须是一个包含三个元素的数组，依次描述`stdin`、`stdout`和`stderr`描述符。对于每个描述符，指定一个包含两个元素的数组或一个流资源。在第一种情况下，如果第一个元素是`"pipe"`，第二个元素可以是`"r"`以从管道读取或`"w"`以向管道写入。如果第一个是`"file"`，则第二个必须是文件名。*pipes*数组被填充为对应进程描述符的文件指针数组。如果指定了*dir*，则该进程的当前工作目录设置为该路径。如果指定了*env*，则该进程的环境设置为该数组中的值。最后，*options*包含一个带有额外选项的关联数组。可以在数组中设置以下选项：

| `suppress_errors` | 如果设置为`true`，则抑制由进程生成的错误（仅限 Windows） |
| --- | --- |
| `bypass_shell` | 如果设置为`true`，则在运行进程时绕过*cmd.exe* |
| `context` | 如果设置，则在打开文件时指定流上下文 |

如果尝试打开进程时发生任何错误，则返回`false`。否则返回进程的资源句柄。

#### proc_terminate

`bool proc_terminate(resource` *handle*`[, int` *signal*`])`

向由`proc_open()`打开并引用的进程发送终止信号。如果提供了*signal*，则发送该信号给进程。调用立即返回，这可能早于进程完成终止。要轮询进程的状态，使用`proc_get_status()`。如果操作成功则返回`true`，否则返回`false`。

#### property_exists

`bool property_exists(mixed` *class*`, string` *name*`)`

如果对象或*class*定义了名为*name*的数据成员，则返回`true`，否则返回`false`。

#### putenv

`bool putenv(string` *setting*`)`

使用*setting*设置环境变量，通常形式为*name* `=` *value*。如果成功则返回`true`，否则返回`false`。

#### quoted_printable_decode

`string quoted_printable_decode(string` *string*`)`

解码使用 quoted printable 编码的*string*数据，并返回结果字符串。

#### quoted_printable_encode

`string quoted_printable_encode(string` *string*`)`

返回用 quoted printable 编码格式化的*string*。详见 RFC 2045 描述的编码格式。

#### quotemeta

`string quotemeta(string` *string*`)`

通过向其追加反斜杠（`\`）来转义字符串中的某些字符，并返回结果字符串。需要转义的字符包括：句点（`.`）、反斜杠（`\`）、加号（`+`）、星号（`*`）、问号（`?`）、方括号（`[` 和 `]`）、插入符号（`^`）、括号（`(` 和 `)`）以及美元符号（`$`）。

#### rad2deg

`float rad2deg(float` *number*`)`

将*number*从弧度转换为度并返回结果。

#### rand

`int rand([int` *min*`, int` *max*`])`

返回从*min*到*max*的随机数（包括两端）。如果未提供*min*和*max*参数，则返回一个介于 0 和由`getrandmax()`函数返回的值之间的随机数。

#### random_bytes

`string random_bytes(int` *length*`)`

生成一串具有密码学随机字节的任意*length*长度字符串，适合用于密码学用途，例如生成盐、密钥或初始化向量。

#### random_int

`int random_int(int` *min,* `int` *max*`)`

生成可以用于必须具有无偏差结果的情况的密码学随机整数，例如用于 Bingo 游戏中的"balls"混合。*Min*设置要返回的最小值范围（必须是`PHP_INT_MIN`或更大），*max*设置最高值（必须是`PHP_INT_MAX`或更低）。

#### range

`array range(mixed` *first*`, mixed` *second*`[, number` *step*`])`

创建并返回一个包含从*first*到*second*之间的整数或字符的数组。如果*second*小于*first*，则以倒序返回序列。如果提供了*step*，则创建的数组将具有指定的步长间隔。

#### rawurldecode

`string rawurldecode(string` *url*`)`

返回从 URI 编码的*url*解码后创建的字符串。以`%`开头的十六进制数字序列将被替换为表示的字面字符。

#### rawurlencode

`string rawurlencode(string` *url*`)`

返回通过 URI 编码*url*创建的字符串。某些字符将被以以`%`开头的十六进制数字序列替换；例如，空格将被替换为`%20`。

#### readdir

`string readdir([resource` *handle*`])`

返回由 *handle* 引用的目录中的下一个文件的名称。如果未指定 *handle*，则 *handle* 默认为 `opendir()` 返回的最后一个目录句柄资源。调用 `readdir()` 时返回目录中文件的顺序是未定义的。如果目录中没有更多要返回的文件，则 `readdir()` 返回 `false`。

#### readfile

`int readfile(string` *path*`[, bool` *include*`[, resource` *context*`]])`

在提供了流上下文 *context* 的情况下，读取 *path* 处的文件并输出内容。如果指定了 *include* 并且为 `true`，则会在包含路径中搜索文件。如果 *path* 以 `http://` 开头，则打开 HTTP 连接并从中读取文件。如果 *path* 以 `ftp://` 开头，则打开 FTP 连接并从中读取文件；远程服务器必须支持被动 FTP。

此函数返回输出的字节数。

#### readlink

`string readlink(string` *path*`)`

返回包含在符号链接文件 *path* 中的路径。如果 *path* 不存在或不是符号链接文件，或者发生任何其他错误，则函数返回 `false`。

#### realpath

`string realpath(string` *path*`)`

展开所有符号链接，解析对 `/./` 和 `/../` 的引用，移除路径中多余的 `/` 字符，并返回结果。

#### realpath_cache_get

`array realpath_cache_get()`

返回 `realpath` 缓存的内容作为关联数组。每个条目的键是路径名，每个条目的值是一个关联数组，其中包含已为该路径缓存的值。可能的值包括：

| `expires` | 缓存条目将过期的时间 |
| --- | --- |
| `is_dir` | 此路径是否表示目录 |
| `key` | 缓存条目的唯一标识符 |
| `realpath` | 路径的解析后路径 |

#### realpath_cache_size

`int realpath_cache_size()`

返回 `realpath` 缓存当前在内存中占用的字节数。

#### register_shutdown_function

`void register_shutdown_function(callable` *function*`[, mixed` *arg1* `[, mixed` *arg2* `[, ... mixed` *argN*`]]])`

注册一个关闭函数。当页面完成处理时，将调用该函数以及给定的参数。可以注册多个关闭函数，并且它们将按照注册的顺序调用。如果关闭函数包含退出命令，则在该函数之后注册的函数将不会被调用。

由于关闭函数在页面完全处理后才调用，因此不能使用 `print()`、`echo()` 或类似函数或命令向页面添加数据。

#### register_tick_function

`bool register_tick_function(callable` *function*`[, mixed` *arg1* `[, mixed` *arg2* `[, ... mixed` *argN*`]]])`

注册将在每个时钟周期调用的函数 *name*。函数将使用给定的参数进行调用。显然，注册时钟函数可能会严重影响脚本的性能。如果操作成功，则返回 `true`，否则返回 `false`。

#### rename

`bool rename(string` *old*`, string` *new*`[, resource` *context*`])`

将文件 *old* 重命名为 *new*，若提供了流上下文 *context*，则使用该上下文；若重命名成功则返回 `true`，否则返回 `false`。

#### reset

`mixed reset(array` *array*`)`

将 *array* 的内部指针重置为第一个元素并返回该元素的值。

#### restore_error_handler

`bool restore_error_handler()`

恢复至最近一次调用 `set_error_handler()` 前的错误处理程序，并返回 `true`。

#### restore_exception_handler

`bool restore_exception_handler()`

恢复至最近一次调用 `set_exception_handler()` 前的异常处理程序，并返回 `true`。

#### rewind

`int rewind(resource` *handle*`)`

将 *handle* 文件指针设置到文件开头。如果操作成功则返回 `true`，否则返回 `false`。

#### rewinddir

`void rewinddir([resource` *handle*`])`

将 *handle* 文件指针设置到目录中文件列表的开头。若未指定，则 *handle* 默认为 `opendir()` 返回的最后一个目录句柄资源。

#### rmdir

`int rmdir(string` *path*`[, resource` *context*`])`

删除目录 *path*，若提供了流上下文 *context*，则使用该上下文。若目录非空、PHP 进程无适当权限或发生其他错误，则返回 `false`。若成功删除目录，则返回 `true`。

#### round

`float round(float` *number*`[, int` *precision*`[, int` *mode*`]])`

返回 *number* 在 *precision* 指定的小数位数上最接近的整数值。精度的默认值为 `0`（整数四舍五入）。*mode* 参数决定了使用的取整方法：

| `PHP_ROUND_HALF_UP`（默认） | 向上取整 |
| --- | --- |
| `PHP_ROUND_HALF_DOWN` | 向下取整 |
| `PHP_ROUND_HALF_EVEN` | 如果有效数字为偶数，则向上取整 |
| `PHP_ROUND_HALF_ODD` | 如果有效数字为奇数，则向下取整 |

#### rsort

`void rsort(array` *array*`[, int` *flags*`])`

使用值的逆序对数组进行排序。可选的第二个参数包含附加的排序标志。有关使用此函数的更多信息，请参阅第五章和 `unserialize()`。

#### rtrim

`string rtrim(string` *string*`[, string` *characters*`])`

返回 *string* 中去除尾部的所有 *characters* 字符。若未指定 *characters*，则去除的字符包括 `\n`、`\r`、`\t`、`\v`、`\0` 和空格。

#### scandir

`array scandir(string` *path* `[, int` *sort_order* `[, resource` *context*`]])`

返回 *path* 中存在的文件名数组，若提供了流上下文 *context*，则使用该上下文。若发生错误则返回 `false`。文件名根据 *sort_order* 参数排序，其类型为以下之一：

| `SCANDIR_SORT_ASCENDING`（默认） | 升序排序 |
| --- | --- |
| `SCANDIR_SORT_DESCENDING` | 降序排序 |
| `SCANDIR_SORT_NONE` | 不排序（结果顺序不确定） |

#### serialize

`string serialize(mixed` *value*`)`

返回包含*value*的二进制数据表示的字符串。例如，此字符串可用于将数据存储在数据库或文件中，并使用`unserialize()`稍后进行恢复。除了资源外，任何类型的值都可以被序列化。

#### set_error_handler

`string set_error_handler(string` *function*`)`

设置命名的函数作为当前的错误处理程序，或者如果*function*为`NULL`则取消当前的错误处理程序。每当发生错误时，将调用错误处理程序函数；该函数可以执行任何操作，但通常会打印错误消息并在发生严重错误后进行清理。

用户定义的函数将以两个参数调用，错误代码和描述错误的字符串。还可以提供三个额外的参数——发生错误的文件名、发生错误的行号以及发生错误的上下文（这是指向活动符号表的数组）。

`set_error_handler()` 返回先前安装的错误处理程序函数的名称，如果在设置错误处理程序时发生错误（例如*function*不存在），则返回`false`。

#### set_exception_handler

`callable set_exception_handler(callable` *function*`)`

设置命名的函数作为当前的异常处理程序。在`try...catch`块中抛出异常但未被捕获时，将调用异常处理程序；该函数可以执行任何操作，但通常会打印错误消息并在发生严重错误后进行清理。

用户定义的函数将以一个参数调用——被抛出的异常对象。

`set_exception_handler()` 返回先前安装的异常处理程序函数，如果未设置先前的处理程序则返回空字符串，如果在设置错误处理程序时发生错误（例如*function*不存在），则返回`false`。

#### set_include_path

`string set_include_path(string` *path*`)`

设置包含路径配置选项；它将持续到脚本执行结束，或者在脚本中调用`restore_include_path`。返回先前的包含路径值。

#### set_time_limit

`void set_time_limit(int` *timeout*`)`

设置当前脚本的超时时间为*timeout*秒，并重新启动超时计时器。默认情况下，超时时间设置为 30 秒或在当前配置文件中设置的`max_execution_time`的值。如果脚本在指定时间内未执行完毕，则会生成致命错误并终止脚本。如果*timeout*为`0`，则脚本将永不超时。

#### setcookie

`void setcookie(string` *name*`[, string` *value*`[, int` *expiration*`[, string` *path* `[, string` *domain*`[, bool` *is_secure*`]]]]])`

生成一个 cookie 并将其与其余的头信息一起传递。因为 cookie 是在 HTTP 头中设置的，所以必须在生成任何输出之前调用`setcookie()`。

如果仅指定*name*，则从客户端删除具有该名称的 cookie。*value*参数指定 cookie 应采用的值，*expiration*是 Unix 时间戳值，定义 cookie 应过期的时间，*path*和*domain*参数定义 cookie 应与之关联的域。如果*is_secure*为`true`，则 cookie 仅通过安全的 HTTP 连接传输。

#### setlocale

`string setlocale(mixed` *category*`, string` *locale*`[, string` *locale*`, ...]) string setlocale(mixed` *category*`, array` *locale*`)`

设置*category*函数的区域设置为*locale*。设置后返回当前区域设置，如果无法设置区域设置，则返回`false`。可以添加（或进行 OR 运算）任意数量的*category*选项。以下选项可用：

| `LC_ALL`（默认） | 所有以下类别 |
| --- | --- |
| `LC_COLLATE` | 字符串比较 |
| `LC_CTYPE` | 字符分类和转换 |
| `LC_MONETARY` | 货币函数 |
| `LC_NUMERIC` | 数字函数 |
| `LC_TIME` | 时间和日期格式化 |

如果*locale*为`0`或空字符串，则当前区域设置不受影响。

#### setrawcookie

`void setrawcookie(string` *name*`[, string` *value*`[, int` *expiration*`[, string` *path* `[, string` *domain*`[, bool` *is_secure*`]]]]])`

生成一个 cookie，并将其与其他标头信息一起传递。因为 cookie 是在 HTTP 标头中设置的，所以必须在生成任何输出之前调用`setcookie()`。

如果仅指定*name*，则从客户端删除具有该名称的 cookie。*value*参数指定 cookie 应采用的值——与`setcookie()`不同，此处指定的值在发送之前不会进行 URL 编码，*expiration*是 Unix 时间戳值，定义 cookie 应过期的时间，*path*和*domain*参数定义 cookie 应与之关联的域。如果*is_secure*为`true`，则 cookie 仅通过安全的 HTTP 连接传输。

#### settype

`bool settype(mixed` *value*`, string` *type*`)`

将*value*转换为给定的*type*。可能的类型包括`"boolean"`、`"integer"`、`"float"`、`"string"`、`"array"`和`"object"`。如果操作成功，则返回`true`；否则返回`false`。使用此函数与将*value*强制转换为适当类型相同。

#### sha1

`string sha1(string` *string*`[, bool` *binary*`])`

计算*string*的`sha1`加密哈希并返回。如果设置了*binary*并且为`true`，则返回原始二进制数据而不是十六进制字符串。

#### sha1_file

`string sha1_file(string` *path*`[, bool` *binary*`])`

计算并返回文件在*path*处的`sha1`加密哈希。`sha1`哈希是一个 40 字符的十六进制值，可用于对文件数据进行校验和。如果提供了*binary*并且为`true`，则结果将作为 20 位二进制值发送。

#### shell_exec

`string shell_exec(string` *command*`)`

通过 shell 执行*command*并返回命令结果的输出。当使用反引号运算符（`` ` ``）时调用此函数。

#### shuffle

`void shuffle(array` *array*`)`

将*array*中的值重新排列为随机顺序。值的键将丢失。

#### similar_text

`int similar_text(string` *one*`, string` *two*`[, float` *percent*`])`

计算字符串*one*和*two*之间的相似性。如果通过引用传递，则*percent*获取两个字符串不同之处的百分比。

#### sin

`float sin(float` *value*`)`

返回以弧度表示的*value*的正弦值。

#### sinh

`float sinh(float` *value*`)`

返回以弧度表示的*value*的双曲正弦值。

#### sleep

`int sleep(int` *time*`)`

暂停当前脚本的执行*time*秒。如果操作成功，则返回`0`，否则返回`false`。

#### sort

`bool sort(array` *array*`[, int` *flags*`])`

将给定*array*中的值按升序排序。要更好地控制排序行为，请提供第二个参数，该参数是以下值之一：

| `SORT_REGULAR`（默认） | 正常比较项目 |
| --- | --- |
| `SORT_NUMERIC` | 按数字方式比较项目 |
| `SORT_STRING` | 按字符串方式比较项目 |
| `SORT_LOCALE_STRING` | 使用当前区域设置的排序规则按字符串方式比较项目 |
| `SORT_NATURAL` | 使用“自然排序”方式按字符串比较项目 |
| `SORT_FLAG_CASE` | 使用按位`OR`操作符结合`SORT_STRING`或`SORT_NATURAL`以进行不区分大小写的比较 |

如果操作成功，则返回`true`，否则返回`false`。有关使用此函数的更多信息，请参见第五章。

#### soundex

`string soundex(string` *string*`)`

计算并返回*string*的 soundex 键。发音相似（并以相同字母开头）的单词具有相同的 soundex 键。

#### sprintf

`string sprintf(string` *format*`[, mixed` *value1*`[, ... mixed` *valueN*`]])`

使用给定的参数填充*format*并返回字符串。有关使用此函数的更多信息，请参见`printf()`。

#### sqrt

`float sqrt(float` *number*`)`

返回*number*的平方根。

#### srand

`void srand([int` *seed*`])`

使用*seed*初始化标准伪随机数生成器，如果未提供*seed*，则使用随机种子。

#### sscanf

`mixed sscanf(string` *string*`, string` *format*`[, mixed` *variableN* `...])`

解析*string*中与*format*指定的类型匹配的值；找到的值将作为数组返回，或者如果提供了引用传递的*variable1*至*variableN*（必须是变量），则将返回这些变量中的值。

*format*字符串与`sprintf()`中使用的相同。例如：

```php
$name = sscanf("Name: k.tatroe", "Name: %s"); // $name has "k.tatroe"
list($month, $day, $year) = sscanf("June 30, 2001", "%s %d, %d");
$count = sscanf("June 30, 2001", "%s %d, %d", &$month, &$day, &$year);
```

#### stat

`array stat(string` *path*`)`

返回关于文件*path*的信息的关联数组。如果*path*是符号链接，则返回*path*引用的文件的信息。有关返回的值及其含义，请参见`fstat`。

#### str_getcsv

`array str_getcsv(string` *input*`[, string` *delimiter*`[, string` *enclosure* `[, string` *escape*`]]]])`

将字符串解析为逗号分隔值（CSV）列表，并将其作为值数组返回。如果提供了 *delimiter*，则用其来分隔行中的值，而不是逗号。如果提供了 *enclosure*，则它是用于括起值的单个字符（默认为双引号字符 `"`）。*escape* 设置用于转义的转义字符；默认为反斜杠 `\`。

#### str_ireplace

`mixed str_ireplace(mixed` *search*`, mixed` *replace*`, mixed` *string*`[, int &`*count*`])`

对 *string* 中所有出现的 *search* 进行不区分大小写的搜索，并用 *replace* 替换它们。如果三个参数都是字符串，则返回字符串。如果 *string* 是数组，则对数组中的每个元素执行替换，并返回结果数组。如果 *search* 和 *replace* 都是数组，则将 *search* 中的元素用相同数值索引中的 *replace* 元素替换。最后，如果 *search* 是数组且 *replace* 是字符串，则将 *search* 中的任何元素的任何出现更改为 *replace*。如果提供了 *count*，则填充被替换的实例数。

#### str_pad

`string str_pad(string` *string*`, string` *length*`[, string` *pad*`[, int` *type*`]])`

使用 *pad* 填充 *string* 直到其至少有 *length* 个字符，并返回结果字符串。通过指定 *type*，您可以控制填充发生的位置。接受 *type* 的以下值：

| `STR_PAD_RIGHT`（默认） | 在 *string* 的右侧填充 |
| --- | --- |
| `STR_PAD_LEFT` | 在 *string* 的左侧填充 |
| `STR_PAD_BOTH` | 在 *string* 的两侧填充 |

#### str_repeat

`string str_repeat(string` *string*`, int` *count*`)`

返回由 *count* 个 *string* 的副本连接在一起的字符串。如果 *count* 不大于零，则返回空字符串。

#### str_replace

`mixed str_replace(mixed` *search*`, mixed` *replace*`, mixed` *string*`[, int &`*count*`])`

搜索 *string* 中所有出现的 *search* 并用 *replace* 替换它们。如果三个参数都是字符串，则返回字符串。如果 *string* 是数组，则对数组中的每个元素执行替换，并返回结果数组。如果 *search* 和 *replace* 都是数组，则将 *search* 中的元素用相同数值索引中的 *replace* 元素替换。最后，如果 *search* 是数组且 *replace* 是字符串，则将 *search* 中的任何元素的任何出现更改为 *replace*。如果提供了 *count*，则填充被替换的实例数。

#### str_rot13

`string str_rot13(string` *string*`)`

将 *string* 转换为其 `rot13` 版本并返回结果字符串。

#### str_shuffle

`string str_shuffle(string` *string*`)`

将 *string* 中的字符重新排列为随机顺序，并返回结果字符串。

#### str_split

`array str_split(string` *string*`[, int` *length*`])`

将*string*拆分为包含*length*个字符的字符数组；如果未指定*length*，则默认为`1`。

#### str_word_count

`mixed str_word_count(string` *string*`[, int` *format*`[, string` *characters*`]])`

使用特定于区域设置的规则计算*string*中单词的数量。*format*的值决定返回的值：

| `0`（默认） | *string* 中找到的单词数 |
| --- | --- |
| `1` | *string* 中找到的所有单词的数组 |
| `2` | 一个关联数组，键是*string*中找到的位置，值是这些位置处的单词 |

如果指定了*characters*，则提供被视为单词内部（即非单词边界）的附加字符。

#### strcasecmp

`int strcasecmp(string` *one*`, string` *two*`)`

比较两个字符串；如果*one*小于*two*，返回小于零的数，如果两个字符串相等，返回`0`，如果*one*大于*two*，返回大于零的数。此比较不区分大小写，即“Alphabet”和“alphabet”被视为相等。

#### strcmp

`int strcmp(string` *one*`, string` *two*`)`

比较两个字符串；如果*one*小于*two*，返回小于零的数，如果两个字符串相等，返回`0`，如果*one*大于*two*，返回大于零的数。此比较区分大小写，即“Alphabet”和“alphabet”被视为不相等。

#### strcoll

`int strcoll(string` *one*`, string` *two*`)`

使用当前区域设置的规则比较两个字符串；如果*one*小于*two*，返回小于零的数，如果两个字符串相等，返回`0`，如果*one*大于*two*，返回大于零的数。此比较区分大小写，即“Alphabet”和“alphabet”被视为不相等。

#### strcspn

`int strcspn(string` *string*`, string` *characters*`[, int` *offset*`[, int` *length*`]])`

返回从*offset*开始的*string*子集的长度，检查最多*length*个字符，直到首次出现*characters*中的字符。

#### strftime

`string strftime(string` *format*`[, int` *timestamp*`])`

根据第一个参数中提供的*format*字符串和当前区域设置格式化时间和日期。如果未指定第二个参数，则使用当前时间和日期。*format*字符串中识别以下字符：

| `%a` | 星期几的三个字母缩写（例如，Mon） |
| --- | --- |
| `%A` | 星期几的名称（例如，Monday） |
| `%b` | 月份的三个字母缩写（例如，Aug） |
| `%B` | 月份的名称（例如，August） |
| `%c` | 日期和时间的首选格式 |
| `%C` | 世纪的最后两位数 |
| `%d` | 日期的两位数表示，如果需要，包括前导零（例如，01 到 31） |
| `%D` | 与 `%m/%d/%y` 相同 |
| `%e` | 日期的两位数表示，如果需要，包括前导空格（例如，1 到 31） |
| `%h` | 与 `%b` 相同 |
| `%H` | 24 小时制小时数，如有必要，包括前导零（例如，00 到 23） |
| `%I` | 12 小时制小时数（例如，1 到 12） |
| `%j` | 一年中的日期，需要时包括前导零（例如，001 到 366） |
| `%m` | 月份，需要时包括前导零（例如，01 到 12） |
| `%M` | 分钟数 |
| `%n` | 换行符（`\n`） |
| `%p` | 上午或下午 |
| `%r` | 等同于`%I:%M:%S %p` |
| `%R` | 等同于`%H:%M:%S` |
| `%S` | 秒数 |
| `%t` | 制表符（`\t`） |
| `%T` | 等同于`%H:%M:%S` |
| `%u` | 一周中的数字表示，从星期一开始计数为 1 |
| `%U` | 一年中的数字表示周数，从第一个星期天开始计数 |
| `%V` | ISO 8601:1998 格式的一年中的数字表示周数—第一个星期至少有四天的星期一开始计数 |
| `%W` | 一年中的数字表示周数，从第一个星期一开始计数 |
| `%w` | 一周中的数字表示，从星期天开始计数为 0 |
| `%x` | 当前区域设置下的首选日期格式 |
| `%X` | 当前区域设置下的首选时间格式 |
| `%y` | 两位数表示的年份（例如，98） |
| `%Y` | 四位数表示的年份（例如，1998） |
| `%Z` | 时区或名称或缩写 |
| `%%` | 百分号（`%`） |

#### `stripcslashes`

`string stripcslashes(string` *string*`, string` *characters*`)`

通过删除*string*中反斜杠后的*characters*实例来转义字符。可以通过两个点分隔符指定字符范围；例如，要取消转义`a`到`q`之间的字符，请使用`"a..q"`。可以在*characters*中指定多个字符和范围。`stripcslashes()`函数是`addcslashes()`的反函数。

#### `stripslashes`

`string stripslashes(string` *string*`)`

通过删除*string*中 SQL 查询中具有特殊含义的转义序列的反斜杠来转义字符。单引号（`'`）、双引号（`"`）、反斜杠（`\`）和 NUL 字节（`"\0"`）被转义。该函数是`addslashes()`的反函数。

#### `strip_tags`

`string strip_tags(string` *string*`[, string` *allowed*`])`

从*string*中移除 PHP 和 HTML 标签，并返回结果。*allowed*参数可指定要保留的特定标签。字符串应为逗号分隔的标签列表；例如，`"<b>,<i>"`将保留粗体和斜体标签。

#### `stripos`

`int stripos(string` *string*`, string` *value*`[, int` *offset*`])`

返回在*string*中第一次出现*value*的位置，使用不区分大小写的比较。如果指定了*offset*，则从该位置开始搜索。如果未找到*value*，则返回`false`。

#### `stristr`

`string stristr(string` *string*`, string` *search*`[, int` *before*`])`

返回从*string*中第一次出现的*search*（使用不区分大小写比较）到*string*的末尾的部分，或者从*string*中第一次出现的*search*（如果指定了*before*为`true`，则是从*string*的开头）到*string*的开头的部分。如果未找到*search*，则函数返回`false`。如果*search*包含多个字符，则仅使用第一个字符。

#### strlen

`int strlen(string` *string*`)`

返回*string*中的字符数。

#### strnatcasecmp

`int strnatcasecmp(string` *one*`, string` *two*`)`

比较两个字符串；如果*one*小于*two*，返回一个小于零的数字，如果两个字符串相等，则返回`0`，如果*one*大于*two*，则返回一个大于零的数字。比较时不区分大小写，即“Alphabet”和“alphabet”被视为相等。该函数使用“自然顺序”算法——字符串中的数字比计算机通常更自然地比较。例如，值为`"1"`，`"10"`和`"2"`按照`strcmp()`的顺序排序，但`strnatcasecmp()`按`"1"`，`"2"`和`"10"`的顺序排序。此函数是`strnatcmp()`的不区分大小写版本。

#### strnatcmp

`int strnatcmp(string` *one*`, string` *two*`)`

比较两个字符串；如果*one*小于*two*，返回一个小于零的数字，如果两个字符串相等，则返回`0`，如果*one*大于*two*，则返回一个大于零的数字。比较时区分大小写，即“Alphabet”和“alphabet”不被视为相等。`strnatcmp()`函数使用“自然顺序”算法——字符串中的数字比计算机通常更自然地比较。例如，值为`"1"`，`"10"`和`"2"`按照`strcmp()`的顺序排序，但`strnatcmp()`按`"1"`，`"2"`和`"10"`的顺序排序。

#### strncasecmp

`int strncasecmp(string` *one*`, string` *two*`, int` *length*`)`

比较两个字符串；如果*one*小于*two*，返回一个小于零的数字，如果两个字符串相等，则返回`0`，如果*one*大于*two*，则返回一个大于零的数字。比较时不区分大小写，即“Alphabet”和“alphabet”被视为相等。这个函数是`strcmp()`的不区分大小写版本。如果任一字符串短于*length*个字符，则该字符串的长度决定要比较的字符数。

#### strncmp

`int strncmp(string` *one*`, string` *two*`[, int` *length*`])`

比较两个字符串；如果*one*小于*two*，返回一个小于零的数字，如果两个字符串相等，则返回`0`，如果*one*大于*two*，则返回一个大于零的数字。比较时区分大小写，即“Alphabet”和“alphabet”不被视为相等。如果指定了*length*，则最多比较*length*个字符。如果任一字符串短于*length*个字符，则该字符串的长度决定要比较的字符数。

#### strpbrk

`string strpbrk(string` *string*`, string` *characters*`)`

返回由*string*的子字符串组成的字符串，从*characters*中的字符在*string*中第一次出现的位置开始，直到字符串末尾，如果*string*中没有*characters*中的字符，则返回`false`。

#### strpos

`int strpos(string` *string*`, string` *value*`[, int` *offset*`])`

返回*string*中*value*第一次出现的位置。如果指定*offset*，则从该位置开始搜索。如果未找到*value*，则返回`false`。

#### strptime

`array strptime(string` *date*`, string` *format*`)`

根据*format*字符串和当前区域设置解析时间和日期。格式使用与`strftime()`相同的格式。返回一个关联数组，包含有关解析时间的信息，包括以下元素：

| `tm_sec` | 秒钟 |
| --- | --- |
| `tm_min` | 分钟 |
| `tm_hour` | 小时 |
| `tm_mday` | 月份中的某一天 |
| `tm_wday` | 数字表示的星期几（星期天为 0） |
| `tm_mon` | 月份 |
| `tm_year` | 年份 |
| `tm_yday` | 年中的某一天 |
| `unparsed` | 未按照给定格式解析的*date*部分 |

#### strrchr

`string strrchr(string` *string*`, string` *character*`)`

返回从*string*中*character*的最后一次出现直到字符串末尾的部分。如果未找到*character*，则函数返回`false`。如果*character*包含多个字符，则只使用第一个字符。

#### strrev

`string strrev(string` *string*`)`

返回*string*的字符顺序反转后的字符串。

#### strripos

`int strripos(string` *string*`, string` *search*`[, int` *offset*`])`

返回使用不区分大小写搜索在*string*中*search*的最后一次出现的位置，如果未找到*search*则返回`false`。如果指定且为正，则搜索从*string*开头的*offset*字符开始。如果指定且为负，则从*string*末尾的*offset*字符开始搜索。此函数是`strrpos()`的不区分大小写版本。

#### strrpos

`int strrpos(string` *string*`, string` *search*`[, int` *offset*`])`

返回*string*中*search*最后一次出现的位置，如果未找到*search*则返回`false`。如果指定且为正，则搜索从*string*开头的*offset*字符开始。如果指定且为负，则从*string*末尾的*offset*字符开始搜索。

#### strspn

`int strspn(string` *string*`, string` *characters*`[, int` *offset*`[, int` *length*`]])`

返回*string*中仅包含*characters*中字符的子字符串的长度。如果*offset*为正，则搜索从该字符开始；如果为负，则子字符串从字符串末尾的*offset*字符开始。如果指定*length*且为正，则从子字符串开头检查指定数量的字符。如果指定*length*且为负，则检查从子字符串末尾开始的*length*个字符。

#### strstr

`string strstr(string` *string*`, string` *character*`[, bool` *before*`])`

返回从*string*中*character*的第一次出现直到*string*的末尾的部分，或者如果指定了*before*并且为`true`，则从*string*中*character*的第一次出现直到*string*的开头。如果未找到*character*，则函数返回`false`。如果*character*包含多个字符，则只使用第一个字符。

#### strtok

`string strtok(string` *string*`, string` *token*`) string strtok(string` *token*`)`

将*string*按*token*中的任何字符分隔成标记，并返回找到的下一个标记。第一次在字符串上调用`strtok()`时，请使用第一个函数原型；之后，只需提供标记即可使用第二个原型。该函数为每个调用它的字符串保留内部指针。例如：

```php
$string = "This is the time for all good men to come to the aid of their 
country."
$current = strtok($string, " .;,\"'");
while(!($current === false)) {
 print($current . "<br />";
}
```

#### strtolower

`string strtolower(string` *string*`)`

返回*string*，其中所有字母字符均转换为小写。用于转换字符的表是特定于区域设置的。

#### strtotime

`int strtotime(string` *time*`[, int` *timestamp*`])`

将时间和日期的英文描述转换为 Unix 时间戳值。可选地，可以给定一个*timestamp*作为函数使用的“现在”值；如果省略此值，则使用当前日期和时间。如果无法将值转换为有效时间戳，则返回`false`。

描述字符串可以采用多种格式。例如，以下所有格式都可以使用：

```php
echo strtotime("now");
echo strtotime("+1 week");
echo strtotime("-1 week 2 days 4 seconds");
echo strtotime("2 January 1972");
```

#### strtoupper

`string strtoupper(string` *string*`)`

返回*string*，其中所有字母字符均转换为大写。用于转换字符的表是特定于区域设置的。

#### strtr

`string strtr(string` *string*`, string` *from*`, string` *to*`) string strtr(string` *string*`, array` *replacements*`)`

当给定三个参数时，返回一个字符串，该字符串通过将*string*中*from*中每个字符的每次出现转换为*to*中相应位置的字符而创建。当给定两个参数时，返回一个字符串，该字符串通过使用*replacements*中的键在*string*中替换键的每个出现来创建。与*replacements*中的相应值。

#### strval

`string strval(mixed` *value*`)`

返回*value*的字符串等效值。如果*value*是一个对象并且该对象实现了`__toString()`方法，则返回该方法的值。否则，如果*value*是一个没有实现`__toString()`方法的对象或者是一个数组，则函数返回空字符串。

#### substr

`string substr(string` *string*`, int` *offset*`[, int` *length*`])`

返回*string*的子字符串。如果*offset*为正数，则子字符串从该字符开始；如果为负数，则子字符串从字符串末尾的*offset*字符开始。如果给定*length*且为正数，则从子字符串的开头返回那么多字符。如果给定*length*且为负数，则子字符串在*string*末尾的*length*字符处结束。如果未给出*length*，则子字符串包含*string*的所有字符直到末尾。

#### substr_compare

`int substr_compare(string` *first*`, string` *second*`, string` *offset*`[, int` *length*`[, bool` *case_insensitivity*`]])`

比较*first*从*offset*位置开始的子字符串与*second*。如果指定了*length*，则最多比较那么多个字符。最后，如果指定了*case_insensitivity*且为`true`，则比较是不区分大小写的。如果*first*的子字符串小于*second*，则返回小于零的数，如果它们相等，则返回`0`，如果*first*的子字符串大于*second*，则返回大于零的数。

#### substr_count

`int substr_count(string` *string*`, string` *search*`[, int` *offset*`[, int` *length*`]])`

返回*string*中出现*search*的次数。如果提供了*offset*，则搜索从该字符偏移开始，最多搜索*length*个字符，或者如果未提供*length*，则搜索到字符串末尾为止。

#### substr_replace

`string substr_replace(mixed` *string*`, mixed` *replace*`, mixed` *offset*`[, mixed` *length*`])`

用*replace*替换*string*中的子字符串。选择要替换的子字符串的规则与`substr()`相同。如果*string*是一个数组，则在数组中的每个字符串上进行替换。在这种情况下，*replace*、*offset*和*length*可以是标量值，这些值用于*string*中的所有字符串，或者是值数组，用于每个*string*中相应的值。

#### symlink

`bool symlink(string` *path*`, string` *new*`)`

在路径*new*处创建对*path*的符号链接。如果成功创建链接则返回`true`，否则返回`false`。

#### syslog

`bool syslog(int` *priority*`, string` *message*`)`

将错误消息发送到系统日志设施。在 Unix 系统上，这是`syslog(3)`；在 Windows NT 上，消息记录在 NT 事件日志中。使用给定的*priority*记录消息，该*priority*是以下之一（按优先级降序排列）：

| `LOG_EMERG` | 错误导致系统不稳定 |
| --- | --- |
| `LOG_ALERT` | 错误指出需要立即采取行动的情况 |
| `LOG_CRIT` | 错误是临界条件 |
| `LOG_ERR` | 错误是一般错误条件 |
| `LOG_WARNING` | 错误消息是警告 |
| `LOG_NOTICE` | 错误消息是正常但重要的情况 |
| `LOG_INFO` | 错误是一个不需要采取行动的信息性消息 |
| `LOG_DEBUG` | 错误仅用于调试 |

如果*message*包含字符`%m`，则用当前设置的任何错误消息替换它。如果日志记录成功，则返回`true`，如果失败则返回`false`。

#### system

`string system(string` *command*`[, int &`*return*`])`

通过 shell 执行*command*并返回命令结果的最后一行输出。如果指定了*return*，则将其设置为命令的返回状态。

#### sys_getloadavg

`array sys_getloadavg()`

返回包含运行当前脚本的机器上最后 1 分钟、5 分钟和 15 分钟内采样的平均负载的数组。

#### sys_get_temp_dir

`string sys_get_temp_dir()`

返回临时文件所在的目录路径，例如由`tmpfile()`和`tempname()`创建的文件。

#### tan

`float tan(float` *value*`)`

返回*value*的弧度的正切值。

#### tanh

`float tanh(float` *value*`)`

返回*value*的弧度的双曲正切值。

#### tempnam

`string tempnam(string` *path*`, string` *prefix*`)`

在目录*path*中生成并返回唯一文件名。如果*path*不存在，则生成的临时文件可能位于系统的临时目录中。文件名以*prefix*为前缀。如果操作无法执行，则返回`false`。

#### time

`int time()`

返回自 Unix 纪元（1970 年 1 月 1 日 00:00:00 GMT）以来的秒数。

#### time_nanosleep

`bool time_nanosleep(int` *seconds*`, int` *nanoseconds*`)`

暂停当前脚本的执行*seconds*秒和*nanoseconds*纳秒。成功返回`true`，失败返回`false`；如果延迟被信号中断，则返回一个包含以下值的关联数组。

| `seconds` | 剩余的秒数 |
| --- | --- |
| `nanoseconds` | 剩余的纳秒数 |

#### time_sleep_until

`bool time_sleep_until(float` *timestamp*`)`

暂停当前脚本的执行，直到时间*timestamp*过去。成功返回`true`，失败返回`false`。

#### timezone_name_from_abbr

`string timezone_name_from_abbr(string` *name*`[, int` *gmtOffset*`[, int` *dst*`]])`

返回给定*name*的时区名称，如果找不到合适的时区，则返回`false`。如果给定*gmtOffset*，则它是从 GMT 的整数偏移量，用作查找合适时区的提示。如果给定*dst*，则指示时区是否具有夏令时作为查找合适时区的提示。

#### timezone_version_get

`string timezone_version_get()`

返回当前时区数据库的版本。

#### tmpfile

`int tmpfile()`

创建一个具有唯一名称的临时文件，以读写权限打开它，并返回文件的资源，如果发生错误则返回`false`。关闭`fclose()`或当前脚本结束时自动删除文件。

#### token_get_all

`array token_get_all(string` *source*`)`

将 PHP 代码*source*解析为 PHP 语言标记，并将它们作为数组返回。数组中的每个元素包含一个单字符标记或一个包含以下顺序的三元素数组：标记索引、表示标记的源字符串和*source*中出现的行号。

#### token_name

`string token_name(int` *token*`)`

返回由*token*标识的 PHP 语言标记的符号名称。

#### touch

`bool touch(string` *path*`[, int` *touch_time*`[, int` *access_time*`]])`

将*path*的修改日期设置为*touch_time*（Unix 时间戳值），将*path*的访问时间设置为*access_time*。如果未指定，*touch_time*默认为当前时间，而*access_time*默认为*touch_time*（如果也未提供该值，则为当前时间）。如果文件不存在，则创建该文件。如果函数完成时没有错误，则返回`true`；如果发生错误，则返回`false`。

#### trait_exists

`bool trait_exists(string` *name*`[, bool` *autoload*`])`

如果定义了与字符串同名的特性，则返回`true`；否则返回`false`。对特性名称的比较不区分大小写。如果设置了`autoload`并且为`true`，则在检查特性存在之前，自动加载器会尝试加载该特性。

#### trigger_error

`void trigger_error(string` *error*`[, int` *type*`])`

触发错误条件；如果未给出类型，则默认为`E_USER_NOTICE`。以下类型是有效的：

| `E_USER_ERROR` | 用户生成的错误 |
| --- | --- |
| `E_USER_WARNING` | 用户生成的警告 |
| `E_USER_NOTICE`（默认） | 用户生成的通知 |
| `E_USER_DEPRECATED` | 用户生成的弃用调用警告 |

如果长于 1,024 个字符，则将*error*截断为 1,024 个字符。

#### trim

`string trim(string` *string*`[, string` *characters*`])`

返回的*string*，从*characters*的开头和结尾剥离每个空白字符。您可以使用字符串中的`..`指定要剥离的字符范围。例如，`"a..z"`将剥离每个小写字母字符。如果未提供*characters*，则剥离`\n`、`\r`、`\t`、`\x0B`、`\0`和空格。

#### uasort

`bool uasort(array` *array*`, callable` *function*`)`

使用用户定义的函数对数组进行排序，保持值的键。有关如何使用此函数的更多信息，请参见第五章和`usort()`。如果成功排序数组，则返回`true`，否则返回`false`。

#### ucfirst

`string ucfirst(string` *string*`)`

返回的*string*，如果是字母的话，将第一个字符转换为大写。用于转换字符的表是特定于语言环境的。

#### ucwords

`string ucwords(string` *string*`)`

返回的*string*，如果是字母的话，将每个单词的第一个字符转换为大写。用于转换字符的表是特定于语言环境的。

#### uksort

`bool uksort(array` *array*`, callable` *function*`)`

使用用户定义的函数按键对数组进行排序，保持值的键。有关使用此函数的更多信息，请参见第五章和`usort()`。如果成功对数组进行了排序，则返回`true`，否则返回`false`。

#### umask

`int umask([int` *mask*`])`

将 PHP 的默认权限设置为*mask* `& 0777`，并返回前一个掩码（如果成功），或者如果发生错误则返回`false`。在当前脚本结束时将恢复先前的默认权限。如果未提供*mask*，则返回当前权限。

在运行于多线程 Web 服务器（例如 Apache）时，在创建文件后使用`chmod()`来更改其权限，而不是使用此函数。

#### uniqid

`string uniqid([string` *prefix*`[, bool` *more_entropy*`]])`

根据当前微秒级时间生成一个唯一标识符，以*prefix*为前缀。如果指定了*more_entropy*且为`true`，则在字符串末尾添加额外的随机字符。生成的字符串长度为 13 个字符（如果未指定*more_entropy*或为`false`）或 23 个字符（如果*more_entropy*为`true`）。

#### unlink

`int unlink(string` *path*`[, resource` *context*`])`

使用流上下文*context*（如果提供）删除文件*path*。如果操作成功返回`true`，否则返回`false`。

#### unpack

`array unpack(string` *format*`, string` *data*`)`

返回从二进制字符串*data*中检索的值数组，该字符串之前使用`pack()`函数和*format*格式打包。参见`pack()`以获取在*format*中使用的格式代码列表。

#### unregister_tick_function

`void unregister_tick_function(string` *name*`)`

移除之前使用`register_tick_function()`设置的函数*name*作为一个 tick 函数。在每次 tick 期间将不再调用它。

#### unserialize

`mixed unserialize(string` *data*`)`

返回存储在*data*中的值，*data*必须是之前使用`serialize()`序列化的值。如果值是一个对象且该对象有一个`__wakeup()`方法，则在重建对象后立即调用该方法。

#### unset

`void unset(mixed` *var*`[, mixed` *var2*`[, ... mixed` *varN*`]])`

销毁给定的变量。在函数作用域内调用的全局变量仅`unset`该变量的本地副本；要销毁全局变量，必须在`$GLOBALS`数组中的值上调用`unset`。传递的引用方式在函数作用域内仅销毁该变量的本地副本。

#### urldecode

`string urldecode(string` *url*`)`

返回通过解码 URI 编码的*url*生成的字符串。以`%`开头并后跟十六进制数的字符序列将被替换为其表示的字面值。此外，加号（`+`）将被替换为空格。参见`rawurlencode()`，其处理空格的方式除外。

#### urlencode

`string urlencode(string` *url*`)`

返回一个通过对 *url* 进行 URI 编码创建的字符串。除了破折号 (`–`)、下划线 (`_`) 和句点 (`.`) 字符外，*url* 中的所有非字母数字字符都将被以 `%` 开头的十六进制数替换；例如，斜杠 (`/`) 将被替换为 `%2F`。此外，*url* 中的任何空格都将被加号 (`+`) 替换。另请参阅 `rawurlencode()`，其除了处理空格外与本函数完全相同。

#### usleep

`void usleep(int` *time*`)`

暂停当前脚本的执行 *time* 微秒。

#### usort

`bool usort(array` *array*`, callable` *function*`)`

使用用户定义的函数对数组进行排序。提供的函数将使用两个参数调用。如果第一个参数小于第二个参数，则应返回小于零的整数，如果两个参数相等，则返回 `0`，如果第一个参数大于第二个参数，则返回大于零的整数。比较相等的两个元素的排序顺序是未定义的。有关如何使用此函数的更多信息，请参见 第五章。

如果函数成功对数组进行排序，则返回 `true`，否则返回 `false`。

#### var_dump

`void var_dump(mixed` *name*`[, mixed` *name2*`[, ... mixed` *nameN*`]])`

输出关于 *name*、*name2* 等的信息。信息输出包括变量的类型、值以及如果是对象，则包括对象的所有公共、私有和受保护属性。数组和对象的内容将以递归方式输出。

#### var_export

`mixed var_export(mixed` *expression*`[, bool` *variable_representation*`])`

返回 *expression* 的 PHP 代码表示。如果设置了 *variable_representation* 并且为 `true`，则返回 *expression* 的实际值。

#### version_compare

`mixed version_compare(string` *one*`, string` *two*`[, string` *operator*`])`

比较两个版本字符串，并返回 `-1` 如果 *one* 小于 *two*，`0` 如果它们相等，以及 `1` 如果 *one* 大于 *two*。版本字符串被分割成每个数字或字符串部分，然后作为 *string_value* `< "dev"`、`< "alpha"` 或 `"a" < "beta"`、`"b" < "rc"` `<` *numeric_value* `< "pl"` 或 `"p"` 进行比较。

如果指定了 *operator*，则使用该运算符对版本字符串进行比较，并返回使用该运算符进行比较的值。可能的运算符包括 `<` 或 `lt`、`<=` 或 `le`、`>` 或 `gt`、`>=` 或 `ge`、`==`、`=`` 或 `eq`，以及 `!=`、`<>` 或 `ne`。

#### vfprintf

`int vfprintf(resource` *stream*`, string` *format*`, array` *values*`)`

将由数组 *values* 中的参数填充 *format* 后的字符串写入流 *stream*，并返回发送的字符串长度。有关如何使用此函数的更多信息，请参见 `printf()`。

#### vprintf

`void vprintf(string` *format*`, array` *values*`)`

使用数组 *values* 中的参数填充 *format* 后，打印字符串。有关如何使用此函数的更多信息，请参见 `printf()`。

#### vsprintf

`string vsprintf(string` *format*`, array` *values*`)`

通过用数组 *values* 中给定的参数填充 *format* 来创建并返回一个字符串。有关使用此函数的更多信息，请参见 `printf()`。

#### wordwrap

`string wordwrap(string` *string*`[, int` *length*`[, string` *postfix*`[, bool` *force*`]]])`

在每 *length* 个字符以及字符串末尾将 *postfix* 插入到 *string* 中，并返回生成的字符串。在插入换行时，函数尝试避免在单词中间断开。如果未指定，*postfix* 默认为 `\n`，*length* 默认为 `75`。如果给定 *force* 且为 `true`，则始终将字符串包装到给定长度（这使函数行为与 `chunk_split()` 相同）。

#### zend_thread_id

`int zend_thread_id()`

返回当前运行的 PHP 进程线程的唯一标识符。

#### zend_version

`string zend_version()`

返回当前运行的 PHP 进程中 Zend 引擎的版本。
