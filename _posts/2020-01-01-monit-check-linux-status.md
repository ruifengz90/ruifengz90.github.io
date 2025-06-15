---
title: Monit检测Linux运行状态
categories: [coding]
comments: true
---

Monit version 5.26.0

## 1，monit配置文件关键配置：

```shell
set mailserver smtp.qq.com port 25
    username "your-sender" password "qqshouquanma" # your-sender is the same with your-sender@qq.com in mail-format. password为qq邮箱授权码
    using tlsv1
    with timeout 30 seconds
    set alert send-mail-to1@qq.com not on { instance, action } 
    set alert send-mail-to2@hotmail.com not on { instance, action } 
    set alert send-mail-to3@qq.com not on { instance, action }

set mail-format { from: your-sender@qq.com }
 
set httpdport 2812 and
    use address 0.0.0.0  # only accept connection from localhost (drop if you use M/Monit)
    allow 0.0.0.0/0.0.0.0        # allow localhost to connect to the server and
    allow admin:monit      # require user 'admin' with password 'monit'
```

发送邮件配置：用的是qq邮箱的smtp端口，username是qq邮箱username@qq.com的username部分，password是qq邮箱的授权码。

username和set mail-format from设置应该一致。

接受邮箱配置：set alert send-mail-to1@qq.com，这里这个邮箱是接收警告的邮箱。

## 2，服务器关键配置：

安全组打开2812端口（即配置文件中的“set httpdport 2812 and”的2812）。

## 3，服务检测警告设置（也是在配置文件中配）：

主要条件是：15分钟的负载超过3.2，或者 10 次检查的 CPU 使用率超过 95%（也就是 10 分钟），或者内存占用超过 75%，或者 SWAP 使用超过 75%。

```shell
check system $HOST
    if loadavg (15min) > 3.2 then alert
    if cpu usage > 90% for 10 cyclesthen alert
    if memory usage > 75% thenalert
    if swap usage > 75% then alert
```

## 4, 检测process，如果不存在，则警告：

```shell
check process link
    matching "linkmanager" # regx matching
    if does not exist then alert
    #start program = "docker-compose -f /www/deviceaccesssystem/link-manager/docker-compose.yaml up -d"
    #stop program = "docker-compose -f /www/deviceaccesssystem/link-manager/docker-compose.yaml down"
```

## 4，完整配置文件
```shell
###############################################################################
## Monit control file
###############################################################################
##
## Comments begin with a '#' and extend through the end of the line. Keywords
## are case insensitive. All path's MUST BE FULLY QUALIFIED, starting with '/'.
##
## Below you will find examples of some frequently used statements. For
## information about the control file and a complete list of statements and
## options, please have a look in the Monit manual.
##
##
###############################################################################
## Global section
###############################################################################

set mailserver smtp.qq.com port 25
    username "your-sender" password "qqshouquanma" # your-sender is the same with your-sender.qq.com in mail-format. password为qq邮箱授权码
    using tlsv1
    with timeout 30 seconds
    set alert send-mail-to1@qq.com not on { instance, action } 
    set alert send-mail-to2@hotmail.com not on { instance, action } 
    set alert send-mail-to3@qq.com not on { instance, action }

set mail-format { from: your-sender@qq.com }

check system $HOST
    if loadavg (15min) > 3.2 then alert
    if cpu usage > 90% for 10 cycles then alert
    if memory usage > 75% then alert
    if swap usage > 75% then alert

set httpd port 2812 and
    use address 0.0.0.0  # only accept connection from localhost (drop if you use M/Monit)
    allow 0.0.0.0/0.0.0.0        # allow localhost to connect to the server and
    allow admin:monit      # require user 'admin' with password 'monit'

###############################################################################



##
## Start Monit in the background (run as a daemon):
#
set daemon  30              # check services at 30 seconds intervals
#   with start delay 240    # optional: delay the first check by 4-minutes (by
#                           # default Monit check immediately after Monit start)
#
#
## Set syslog logging. If you want to log to a standalone log file instead,
## specify the full path to the log file
#
set log syslog

include /etc/monit.d/*
#

```


## 5，参考链接：
> How to Install andConfigure Monit on CentOS/RHEL 7/6
>
> https://medium.com/@anuketjain007/how-to-install-and-configure-monit-on-centos-rhel-7-6-73f8a10168ae

> 为 Ubuntu Server 配置自动发邮件警报的 Monit
>
> https://imtx.me/blog/use-monit-to-send-alert-email-on-ubuntu-server/
