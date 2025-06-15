---
title: 配置 Cloudflare 免费内网穿透，远程登陆内网 windows 服务器
categories: [coding]
comments: true
---

# 1. 什么是 Cloudflare Tunnel？
• Cloudflare Tunnel 是一种安全、免费的内网穿透工具，可将本地服务暴露到互联网上，而无需公网 IP。

• 通过 Cloudflare 的网络和零信任架构保护你的本地服务。

# 2. 使用场景

• 将内网网站、应用、或 API 暴露到互联网。

# 3. 配置内网穿透

### 0.) 添加你的网站到 cloudflare：
（略）详见：
https://developers.cloudflare.com/learning-paths/get-started/add-domain-to-cf/add-site/
或者网络搜索关键词“增加网站到 cloudflare”。

### 1.) 配置 Cloudflare Tunnel


1. 将服务器连接到 Cloudflare

1. 创建 Cloudflare Tunnel

打开 Cloudflare仪表盘 - Zero-trust - Network - Tunnel - 创建一个 Tunnel。

tunnel type 选择推荐的 Cloudflared。Tunnel name 起一个自己熟悉的名字。

2. 下载 Cloudflared 可执行文件
打开 https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/ ，找到如图：

![2024-12-02-cloudflare-tunnel-download-win-program.png](https://image.fengrin.me/2024/2024-12-02-cloudflare-tunnel-download-win-program.png)

下载后，修改下载文件为：cloudflared.exe


3. 打开 cloudflared 并安装服务

打开 Cloudflare仪表盘 - Zero-trust - Network - Tunnel - 选择你的tunnel，就会打开如图界面：

![cloudflared client](https://image.fengrin.me/2024/2024-12-02-cloudflare-tunnel-run-service-command.png)

复制上图命令，打开 CMD 或者 Powershell 运行命令：

```
cloudflared.exe service install eyJhIjoiODIzNjE5MTU3ZDFiN*************************************************************************************************************
```

检查 Cloudflare tunnel status:

![tunnel status](https://image.fengrin.me/2024/2024-12-02-cloudflare-tunnel-status-check.png)
### 2.) 用 Cloudflared access 链接RDP服务器

参考：[Connect to RDP server with `cloudflared access`](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/rdp/#connect-to-rdp-server-with-cloudflared-access)


1. 设置公共主机名

在 “Public Hostnames” 标签中，从下拉菜单中选择一个域名，并指定一个子域名（例如，rdp.example.com）。

2. 配置服务

• 对于服务类型，选择 RDP，并输入服务器的 RDP 监听端口（例如，localhost:3389，通常是 3389 端口）。

3. 保存主机名

点击 Save hostname 保存配置。


4. 在你的开发机器（非在服务器端）运行以下命令以打开 RDP 监听端口：
```
cloudflared access rdp --hostname rdp.example.com --url rdp://localhost:3389
```

• 此过程需要保持运行并配置为自动启动。如果进程被终止，用户将无法连接。

注意：如果客户端设备运行的是 Windows，端口 3389 可能已被本地占用。请选择一个未被使用的替代端口。


### 3.） 连接到 RDP 客户端：

• 打开 Microsoft Remote Desktop，选择 “Add a PC”（添加 PC）。

• 在 “PC name” 中输入：localhost:3389。

• 在 “User account” 中输入 RDP 服务器的用户名和密码。

• 双击新添加的 PC。

• 当系统询问是否继续时，选择 “Continue”（继续）。

4. 身份验证：

• 客户端启动时，将打开一个浏览器窗口，提示用户进行身份验证。