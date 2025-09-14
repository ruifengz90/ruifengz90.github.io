---
title: Git小技巧：最简解决命令行git push需要GitHub密码
categories: [coding]
comments: true
---

## 终端上 git push 需要密码

以前输入 github 账户登陆密码即可，现在行不通

![https://images.ruifeng.me/2025-09-14-Git-Tips4.png](https://images.ruifeng.me/2025-09-14-Git-Tips4.png)


## password 字段需要输入 access token

* 打开 https://github.com/settings/
* 导航 Developer Settings -> Personal Access Tokens -> Generate new Tokens

Repository access 设置要设置成包含需要 push 的repo
Permissions 至少要包括 Contents 的 Read and write

![https://images.ruifeng.me/2025-09-14-Git-Tips5.png](https://images.ruifeng.me/2025-09-14-Git-Tips5.png)

## 复制 token 填到 password prompt 上

![https://images.ruifeng.me/2025-09-14-Git-Tips2.png](https://images.ruifeng.me/2025-09-14-Git-Tips2.png)

![https://images.ruifeng.me/2025-09-14-Git-Tips3.png](https://images.ruifeng.me/2025-09-14-Git-Tips3.png)

显示 Everything up-to-date 代表与 github 同步完成。