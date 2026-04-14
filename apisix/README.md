# Apache APISIX 漏洞复现合集

Apache APISIX 是美国阿帕奇（Apache）软件基金会研发的一套开源 API 网关，支持动态路由、插件热加载、流量管理、安全防护等功能，广泛应用于云原生架构中。

本目录收录了与 Apache APISIX 相关的经典高危漏洞复现文档，涵盖默认密钥绕过、API 未授权访问等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2020-13945 | 默认密钥 RCE | Apache APISIX 未配置管理员 Token | ⭐⭐ | [查看](./CVE-2020-13945/README.md) |
| CVE-2021-45232 | Dashboard API 权限绕过 RCE | Apache APISIX Dashboard < 2.10.1 | ⭐⭐ | [查看](./CVE-2021-45232/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/47) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
