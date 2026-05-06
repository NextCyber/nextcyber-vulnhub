# Adminer 漏洞复现合集

Adminer 是一个 PHP 编写的开源数据库管理工具，支持 MySQL、MariaDB、PostgreSQL、SQLite、MS SQL、Oracle、Elasticsearch、MongoDB 等多种数据库，以单个 PHP 文件形式部署，广泛用于各类 Web 环境。本目录收录了与 Adminer 相关的漏洞复现文档，涵盖 SSRF 等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|---------|---------|---------|------|------|
| CVE-2021-21311 | SSRF 漏洞 | Adminer 4.0.0 – 4.7.9 | ⭐⭐ | [查看](./CVE-2021-21311/README.md) |
| CVE-2021-43008 | SSRF 漏洞 | Adminer 4.0.0 – 4.7.10 | ⭐⭐ | [查看](./CVE-2021-43008/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/42) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
