# Jackson 漏洞复现合集

Jackson 是美国 Jackson Labs 公司开发的一套流行的 JSON 序列化/反序列化库，广泛应用于 Java Web 开发中。Jackson-databind 是其核心模块，支持通过注解将 JSON 数据绑定到 Java 对象。

本目录收录了与 Jackson-databind 相关的反序列化漏洞复现文档，涵盖利用特殊 Gadget 实现任意代码执行等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2017-7525 | 反序列化 RCE | Jackson-databind < 2.7.9.3 / 2.8.x < 2.8.14 | ⭐⭐ | [查看](./CVE-2017-7525/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/89) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
