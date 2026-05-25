# Nexus3 漏洞复现合集

Nexus Repository Manager 3 是一款软件仓库，可以用来存储和分发 Maven、NuGET 等软件源仓库。

本目录收录了与 Nexus Repository Manager 3 相关的漏洞复现文档，涵盖远程命令执行、目录穿越等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2019-7238 | 远程命令执行 | Nexus Repository Manager 3 ≤3.14.0 | ⭐⭐⭐ | [查看](./CVE-2019-7238/README.md) |
| CVE-2020-10199 | 远程命令执行 | Nexus Repository Manager 3 ≤3.21.1 | ⭐⭐⭐ | [查看](./CVE-2020-10199/README.md) |
| CVE-2020-10204 | 远程命令执行 | Nexus Repository Manager 3 ≤3.21.1 | ⭐⭐⭐ | [查看](./CVE-2020-10204/README.md) |
| CVE-2024-4956 | 未授权目录穿越 | Nexus Repository Manager 3 ≤3.68.0 | ⭐⭐⭐ | [查看](./CVE-2024-4956/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/119) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
