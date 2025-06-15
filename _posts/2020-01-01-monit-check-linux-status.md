---
title: Monit检测Linux运行状态
categories: [coding]
comments: true
---

> <span style="color:yellow; background-color:gray">本篇blog edit于2025年6月。</span>

Monit version 5.33.0

# 1. 安装 Monit

```shell
sudo apt install monit
```

# 2. 编辑配置文件

```shell
sudo vim /etc/monit/monitrc
```

启动 Monit

```shell
sudo systemctl start monit
```

检查 Monit 状态

```shell
sudo systemctl status monit
```

# 3. alert邮件发送接收的关键配置：

```shell
set mailserver smtp.qq.com port 465
    username "12345678@qq.com"
    password "your_auth_code"  # QQ邮箱授权码
    using ssl

set mail-format {
    from: 12345678@qq.com
    subject: Monit alert -- $EVENT $SERVICE
    message: $EVENT Service $SERVICE
        Date: $DATE
        Description: $DESCRIPTION
        Action: $ACTION
        Host: $HOST
}

set alert yourreceiver@example.com
```

配置代码来自 ChatGPT。

> <span style="color:red">2025-06-16 UPDATE: 出于安全和反垃圾邮件的原因，现在 QQ 邮箱的 SMTP 服务器（smtp.qq.com）默认已经不支持端口 25，推荐改用更安全的加密端口。</span>

发送邮件配置：用的是 QQ 邮箱的 SMTP 服务器，端口是 465，username 是 "12345678@qq.com" QQ邮箱地址。password 是 QQ 邮箱的授权码。

username 12345678@qq.com和set mail-format from: 12345678@qq.com的发送邮箱地址设置应该一致。

接受邮箱配置：set alert yourreceiver@example.com，这里这个邮箱是接收alert的邮箱。

# 4. Monit Web UI：

如果你从外部机器浏览器访问 Monit Web UI（例如从你本地电脑访问远程服务器的 Monit 状态），那么你需要在安全组中打开2812端口（即配置文件中的“set httpdport 2812 and”的2812）。

并且配置 /etc/monitrc 或 /etc/monit/monitrc 中，类似如下：

```shell
set httpd port 2812 and
    use address 0.0.0.0    # ⚠️ 表示监听所有 IP（远程可访问）
    allow admin:monit      # 用户名:密码，生产环境建议更复杂
    allow localhost        # 允许本机访问 或者 填写开发机器具体 IP 地址 或者 填写0.0.0.0/0.0.0.0 允许所有 IP 地址访问(不安全)
    # allow 192.168.1.0/24 # 可选，允许局域网段访问
```

配置代码来自 ChatGPT。

# 5. 服务器指标检测alert设置（也是在配置文件中配置）：

```shell
  check system $HOST
    if cpu usage > 95% for 10 cycles then alert
    if memory usage > 90% then alert
    if swap usage > 85% then alert
```

配置代码来自 `/etc/monit/monitrc` 示例配置uncommented。

# 参考链接：

> Monit website
>
> https://mmonit.com/monit/

> How to Install and Configure Monit on CentOS/RHEL 7/6
>
> https://medium.com/@anuketjain007/how-to-install-and-configure-monit-on-centos-rhel-7-6-73f8a10168ae

> 为 Ubuntu Server 配置自动发邮件警报的 Monit
>
> https://imtx.me/blog/use-monit-to-send-alert-email-on-ubuntu-server/
