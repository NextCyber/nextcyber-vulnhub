# Apache ActiveMQ 漏洞复现合集

Apache ActiveMQ 是美国阿帕奇（Apache）软件基金会研发的一套开源消息中间件，支持 Java 消息服务（JMS）、集群、Spring Framework 等，广泛应用于企业级分布式系统中。

本目录收录了与 Apache ActiveMQ 相关的经典高危漏洞复现文档，涵盖反序列化、任意文件写入、后台 RCE 等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2015-5254 | 反序列化 RCE | ActiveMQ < 5.13.0 | ⭐⭐ | [查看](./CVE-2015-5254/README.md) |
| CVE-2016-3088 | 任意文件写入 RCE | ActiveMQ < 5.14.0 | ⭐⭐ | [查看](./CVE-2016-3088/README.md) |
| CVE-2022-41678 | Jolokia 后台 RCE | ActiveMQ ≤ 5.16.5 / 5.17.3 | ⭐⭐⭐ | [查看](./CVE-2022-41678/README.md) |
| CVE-2023-46604 | OpenWire 反序列化 RCE | ActiveMQ ≤ 5.18.2 | ⭐⭐ | [查看](./CVE-2023-46604/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/41) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
