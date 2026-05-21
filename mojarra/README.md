# Mojarra JSF 漏洞复现合集

Mojarra 是 Oracle 实现了 JavaServer Faces (JSF) Web 应用程序标准的开源框架，广泛应用于 Java Web 开发领域。

本目录收录了与 Mojarra JSF 相关的经典高危漏洞复现文档，涵盖 ViewState 反序列化远程代码执行等漏洞类型。

---

## 漏洞列表

| 漏洞名称 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| Mojarra JSF ViewState 反序列化漏洞 | 反序列化 RCE | Mojarra < 2.1.29-08 / < 2.0.11-04 | ⭐⭐⭐ | [查看](./Mojarra-JSF-ViewState-反序列化漏洞/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/114) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
