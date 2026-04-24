# Django 框架漏洞复现合集

Django 是一个高级 Python Web 框架，鼓励快速开发和简洁实用的设计。Django 遵循 MVT 架构模式，广泛应用于 Web 开发领域。

本目录收录了与 Django 框架相关的安全漏洞复现文档。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2017-12794 | Debug Page XSS | Django 1.11.0 - 1.11.4 | ⭐⭐ | [查看](./CVE-2017-12794/README.md) |
| CVE-2018-14574 | 任意URL跳转 | Django < 2.0.8 | ⭐ | [查看](./CVE-2018-14574/README.md) |
| CVE-2019-14234 | JSONField SQL注入 | Django 2.2.x < 2.2.4 | ⭐⭐⭐ | [查看](./CVE-2019-14234/README.md) |
| CVE-2021-35042 | QuerySet.order_by SQL注入 | Django 3.2.x < 3.2.4 | ⭐⭐⭐ | [查看](./CVE-2021-35042/README.md) |
| CVE-2022-34265 | Trunc/Extract SQL注入 | Django 3.2.x < 3.2.14 | ⭐⭐⭐ | [查看](./CVE-2022-34265/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/61) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
