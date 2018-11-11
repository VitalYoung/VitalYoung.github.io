---
layout: post
title: "supervisor+logrotate 守护进程和管理日志"
date: 2017-11-11 12:35:00 +0800
categories: supervisor logrotate centos7
---

1、安装supervisor
```
pip install supervisor
```

2、配置

初始配置文件的生成，使用如下命令 `echo_supervisord_conf > /etc/supervisor/supervisord.conf`

配置文件位置 `/etc/supervisord.conf`

配置program
```
[program:uwsgi_ubt] ; 程序名称，可以通过supervisorctl指定名称进行控制(supervisorctl start/stop/status/restart uwsgi_ubt)
directory = /app/www/ubt ; 程序的启动目录
command = uwsgi --ini ubt.ini ; 启动命令，可以看出与手动在命令行启动的命令是一样的
autostart = true     ; 在 supervisord 启动的时候也自动启动
startsecs = 5        ; 启动 5 秒后没有异常退出，就当作已经正常启动了
autorestart = true   ; 程序异常退出后自动重启
startretries = 3     ; 启动失败自动重试次数，默认是 3
user = root         ; 用哪个用户启动
redirect_stderr = true  ; 把 stderr 重定向到 stdout，默认 false
stdout_logfile_maxbytes = 20MB  ; stdout 日志文件大小，默认 50MB
stdout_logfile_backups = 20     ; stdout 日志文件备份数
; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile = /app/www/ubt/uwsgi.log （ubt.ini 配置中不能用 daemonize 参数，不然supervisor无法管理该应用）
```
参考：https://www.cnblogs.com/kangoroo/p/8494274.html

3、启动supervisor管理服务

```
supervisord -c /etc/supervisord.conf
```

4、logrotate配置

配置文件位置 `/etc/logrotate.conf `

配置
```
/apps/www/ubt/uwsgi.log {
    daily # 每天执行
    rotate 30 # 保留最新30个日志
    missingok # 跳过错误
    create 644 root root
    postrotate
        supervisorctl restart uwsgi_ubt # 配合supervisor重启服务，不然日志不会写到新建的日志文件中
    endscript
}
```

参考：https://www.cnblogs.com/zhutianshi/p/4012385.html
