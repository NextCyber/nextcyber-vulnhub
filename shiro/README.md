# Apache Shiro 漏洞复现合集

Apache Shiro 是一款开源安全框架，提供身份验证、授权、密码学和会话管理。Shiro 框架直观、易用，同时也能提供健壮的安全性。

本目录收录了与 Apache Shiro 相关的经典高危漏洞复现文档，涵盖 Java 反序列化、认证绕过等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2010-3863 | Apache Shiro URL 解析漏洞 | Apache Shiro 1.2.4 及以前 | ⭐⭐ | [查看](./CVE-2010-3863/README.md) |
| CVE-2016-4437 | Apache Shiro 1.2.4 反序列化漏洞 | Apache Shiro 1.2.4 及以前 | ⭐⭐ | [查看](./CVE-2016-4437/README.md) |
| CVE-2020-1957 | Apache Shiro 认证绕过漏洞 | Apache Shiro 1.5.2 以前 | ⭐⭐ | [查看](./CVE-2020-1957/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/147) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
