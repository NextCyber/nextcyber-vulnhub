# Spring 漏洞复现合集

Spring Framework 是一个开源的 Java EE 全栈式应用程序框架，提供了一系列组件和扩展功能。本目录收录了与 Spring 框架相关的经典高危漏洞复现文档，涵盖 SpEL 表达式注入、远程代码执行、消息队列攻击等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2016-4977 | SpEL 表达式注入 RCE | Spring Security OAuth 2.0.x ~ 2.0.9 | ⭐⭐ | [查看](./CVE-2016-4977/README.md) |
| CVE-2017-4971 | SpEL 表达式注入 RCE | Spring WebFlow 2.4.x | ⭐⭐ | [查看](./CVE-2017-4971/README.md) |
| CVE-2017-8046 | SpEL 表达式注入 RCE | Spring Data REST < 3.0.1 | ⭐⭐ | [查看](./CVE-2017-8046/README.md) |
| CVE-2018-1270 | SpEL 表达式注入 RCE | Spring Messaging < 5.0.5 | ⭐⭐ | [查看](./CVE-2018-1270/README.md) |
| CVE-2018-1273 | SpEL 表达式注入 RCE | Spring Data Commons 2.0.5 及以前 | ⭐⭐ | [查看](./CVE-2018-1273/README.md) |
| CVE-2022-22947 | SpEL 表达式注入命令执行 | Spring Cloud Gateway 3.1.0 及 3.0.6 及以前 | ⭐⭐ | [查看](./CVE-2022-22947/README.md) |
| CVE-2022-22963 | SpEL 表达式注入 RCE | Spring Cloud Function 3.1.0 ~ 3.2.2 | ⭐⭐ | [查看](./CVE-2022-22963/README.md) |
| CVE-2022-22965 | 数据绑定 RCE | Spring Framework 5.3.0 ~ 5.3.17 | ⭐⭐ | [查看](./CVE-2022-22965/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/152) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
