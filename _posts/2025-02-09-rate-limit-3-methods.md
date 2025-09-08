---
title: 服务调用速率限制 3 种方案
categories: [coding]
comments: true
---

 问：rate Limiting 如何实现每个ip，每 10 分钟请求 100 次，超过无法继续请求。比较不同方案的优劣， 1，使用 cloudflare 2，使用 Kong api gateway 3，使用 nginx

Claude AI：

让我来分析这三种实现 rate limiting 的方案...