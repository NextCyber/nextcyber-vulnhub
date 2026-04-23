# Adobe ColdFusion 漏洞复现合集

Adobe ColdFusion 是美国 Adobe 公司研发的一套动态 Web 服务器产品，其运行的 CFML（ColdFusion Markup Language）是针对 Web 应用的一种程序设计语言，广泛应用于企业级 Web 开发中。

本目录收录了与 Adobe ColdFusion 相关的经典高危漏洞复现文档，涵盖目录穿越、Java 反序列化、文件包含、XML 反序列化等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2010-2861 | 目录穿越文件读取 | ColdFusion 8、9 | ⭐⭐ | [查看](./CVE-2010-2861/README.md) |
| CVE-2017-3066 | Java 反序列化漏洞 | ColdFusion 10/11/2016 | ⭐⭐ | [查看](./CVE-2017-3066/README.md) |
| CVE-2023-26360 | 本地文件包含漏洞 | ColdFusion 2018/2021 | ⭐⭐ | [查看](./CVE-2023-26360/README.md) |
| CVE-2023-29300 | XML 反序列化命令执行 | ColdFusion 2018/2021/2023 | ⭐⭐⭐ | [查看](./CVE-2023-29300/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/56) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
