# Cups-Browsed 漏洞复现合集

Cups-Browsed 是 CUPS（通用Unix打印系统）的组成部分，是一个专门用于浏览网络上其他 CUPS 服务器共享的远程打印机的守护进程。它可以自动发现和配置网络打印机，让用户更容易访问和使用网络上共享的打印资源。

本目录收录了与 Cups-Browsed 相关的漏洞复现文档，涵盖 PPD FoomaticRIPCommandLine 参数导致的远程命令执行漏洞。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2024-47177 | PPD FoomaticRIPCommandLine RCE | Cups-Browsed ≤ 2.0.1 | ⭐⭐ | [查看](./CVE-2024-47177/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/59) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
