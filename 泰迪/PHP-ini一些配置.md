---
title: PHP.ini一些配置
date: 2017-09-21 12:15:15
tags: WEB安全
---
# php.ini 相关安全配置

1. 安全模式
    safe_mode = on
2. 用户组安全
    当safe_mode打开时，safe_mode_gid被关闭，那么php脚本能够对文件进行访问，而且相同
　　组的用户也能够对文件进行访问。
　　建议设置为：safe_mode_gid = off
3. 安全模式下执行程序主目录
    safe_mode_exec_dir = /usr/bin
4. 安全模式下包含文件
    safe_mode_include_dir = /usr/www/include/
5. 控制php脚本能访问的目录
    open_basedir = /usr/www
6. 关闭危险函数
    disable_functions = system,passthru,exec,shell_exec,popen,phpinfo
7. 关闭PHP版本信息在http头中的泄漏
    expose_php = Off
8. 关闭注册全局变量
    register_globals = Off
9. magic_quotes_gpc
    5.3废弃5.4移除
10. 错误信息控制
    display_errors = Off
11. 错误日志
    log_errors = On
　　error_log = /usr/local/apache2/logs/php_error.log
12. auto_prepend_file auto_append_file
    auto_prepend_file是告诉PHP，在执行目标文件之前，先包含auto_prepend_file中指定的文件；auto_append_file是告诉PHP，在执行完成目标文件后，包含auto_append_file指向的文件
