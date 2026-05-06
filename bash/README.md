# Bash 漏洞复现合集

Bash（Bourne Again Shell）是 Unix 系统和 Linux 系统的一种 Shell（命令行环境），是目前绝大多数 Linux 发行版的默认 Shell。bash 4.3 存在一处破壳漏洞（Shellshock），可导致远程代码执行。

本目录收录了与 Bash 相关的经典高危漏洞复现文档。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2014-6271 | 破壳漏洞 RCE | Bash < 4.3 | ⭐⭐ | [查看](./CVE-2014-6271/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/50) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
