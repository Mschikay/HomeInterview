---
title: PHP代码审计
date: 2017-09-20 12:13:33
tags: WEB安全
---
#代码审计
## 常见函数
1. 文件包含
include/require/require_once/include_once/file_get_contents
2. 命令执行
exec/system/popen/passthru/proc_open/pcntl_exec/shell_exec
3. PHP代码执行
eval/preg_replace/assert/call_user_func/call_user_func_array/create_function
下面的回调函数同理
```txt
array_map()
call_user_func()、call_user_func_array()、array_map()
usort()、uasort()、uksort()、array_filter()、array_reduce()、array_diff_uassoc()、array_diff_ukey()
array_udiff()、array_udiff_assoc()、array_udiff_uassoc()
array_intersect_assoc()、array_intersect_uassoc()
array_uintersect()、array_uintersect_assoc()
array_uintersect_uassoc()、array_walk()、array_walk_recursive()
xml_set_character_data_handler()、xml_set_default_handler()
xml_set_element_handler()、xml_set_end_namespace_decl_handler()
xml_set_external_entity_ref_handler()、xml_set_notation_decl_handler()
xml_set_processing_instruction_handler()
xml_set_start_namespace_decl_handler()
xml_set_unparsed_entity_decl_handler()、stream_filter_register()
set_error_handler()、register_shutdown_function()、register_tick_function()
```
4. 超全局变量
_GET/_POST/_COOKIE/_SERVER/_REQUEST/_ENV/php://input/getenv/
5. 会话
session/cookie
6. 变量覆盖 反序列化
extract/parse_str/mb_parse_str/import_request_variables/unserialize
7. 文件相关的操作
copy/rmdir/chmod/delete/fwrite/fopen/readfile/fpassthru/move_uploaded_file/
file_put_contents/unlink/upload/opendir/fgetc/fgets/ftruncate/fputs/fputcs/parse_ini_file
8. 数据库相关操作
select/insert/update/delete/order by/group by/limit/in(/stripslashes/urldecode
9. 缓冲区溢出
confirm_phpdoc_compiled/mssql_pconnect/mssql_connect/crack_opendict/snmpget/ibase_connect
10. xss
echo/print/printf/vprintf/document.write/document.innerHTML/document.innerHtmlText
11. 信息泄露
phpinfo/highlight_file/show_source
12. 编码转换导致的截断等等
iconv/mb_convert_encoding
附带php.ini中涉及安全配置选项。
13. 输入输出流
```txt
php：//stdin
php：//stdout
php：//stderr
php：//input
php：//output
php：//fd
php：//memory
php：//temp
php：//filter
php://input是读取post提交上来的数据,output是将数据流输出,php://filter是一个文件操作协议,效果类似readfile(),file(),file_get_contents(),
```

***

## 安全函数
### sqli
1. magic_quotes_gpc负责对GET、POST、COOKIE的值进行过滤。
2. magic_quotes_runtime对从数据库或者文件中获取的数据进行过滤。
3. addslashes
    过滤单引号('), 双引号(")反斜杠(\\),和null
4. mysql_[real_]_escape_string
    过滤单引号('), 双引号(")反斜杠(\\),null,\r,\n,\x1a
    mysql_real_escape_string 可以接收一个句柄，并根据当前的字符集进行转义
5. intval() floatval()
    白名单字符床
6. POD 预编译
    使用PDO::ATTR_ENMULATE_RREPARES=false 禁用本地预编译
### xss

### 命令执行
1. escapeshellcmd(), 过滤整条命令.
反斜线（\）会在以下字符之前插入： &#;`|*?~<>^()[]{}$\, \x0A 和 \xFF。 ' 和 " 仅在不配对儿的时候被转义。 在 Windows 平台上，所有这些字符以及 % 和 ! 字符都会被空格代替

2. escapeshellarg()
把字符串转码为可以在 shell 命令里使用的参数
过滤了
'&'、';'、'`'、'|'、'*'、'？'、'~'、'<'、'>'、'^'、'('、')'、'['、']'、'{'、'}'、'$'、'\'、'\x0A'、'\xFF'、'%'，'和”
###

safe_mode = off ( a lot of shit cannot be done with this on )
disabled_functions = N/A ( no one,we want all )
register_globals = on ( we can set variables by request )
allow_url_include = on ( for lfi/rfi )
allow_url_fopen = on ( for lfi/rfi )
magic_quotes_gpc = off ( this will escape ' ” and NUL's with a backslash and we don't want that )
short_tag_open = on ( some scripts are using short tags,better on )
file_uploads = on ( we want to upload )
display_errors = on ( we want to see the script errors,maybe some undeclared variables? )
open_basedir 限制访问目录
display_errors = off 显示错误信息

## 一些tips
1. 变量覆盖的利用
```txt
<？php $a="${@phpinfo（）}"；？>
$a = "${ phpinfo()}"；    @换为空格
$a = "${      phpinfo（）}"； tab
$a = "${/**/phpinfo（）}"；
甚至回车+-!~\都可以
```

2. 数组的妙用
```txt
file_put_contents()在写入文件时的第二个参数可以传入数组，如果是数组的话，
将被连接成字符串再进行写入。在正则匹配前，传入的是一个数组。得益于PHP的弱类型特性，
数组会被强制转换成字符串，也就是Array，Array肯定是满足正则\A[ _a-zA-Z0-9]+\z的，所以不会被拦截。
这样就可以绕过类似检测“<?”之类的waf
```

未完续待...
