# Spring Data REST 漏洞复现合集

Spring Data REST 是构建在 Spring Data 之上的 RESTful Web 服务框架，通过 RESTful API 暴露 Spring Data 仓库。本目录收录了与 Spring Data REST 相关的经典高危漏洞复现文档，涵盖 SpEL 表达式注入、反序列化漏洞等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2017-8046 | SpEL 表达式注入 RCE | Spring Data REST < 3.0.1 | ⭐⭐ | [查看](./CVE-2017-8046/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/152) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
