# Spring Security OAuth 漏洞复现合集

Spring Security OAuth 是为 Spring 框架提供安全认证支持的模块，在企业级应用中有广泛部署。

本目录收录了与 Spring Security OAuth 相关的经典高危漏洞复现文档，涵盖 SpEL 表达式注入、远程命令执行等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2016-4977 | SpEL 表达式注入 RCE | Spring Security OAuth 2.0.x ~ 2.0.9 | ⭐⭐ | [查看](./CVE-2016-4977/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/152) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
