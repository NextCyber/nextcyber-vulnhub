# Weblogic 漏洞复现合集

Weblogic 是 Oracle 公司开发的一款 Java EE 应用服务器，广泛用于企业级 Web应用的部署。本目录收录了与 Weblogic 相关的经典高危漏洞复现文档，涵盖 XMLDecoder 反序列化、SSRF、任意文件上传、远程代码执行等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2017-10271 | XMLDecoder 反序列化漏洞 | Weblogic < 10.3.6 | ⭐⭐ | [查看](./CVE-2017-10271/README.md) |
| CVE-2018-2628 | WLS Core Components 反序列化命令执行漏洞 | Weblogic 10.3.6.0 等 | ⭐⭐ | [查看](./CVE-2018-2628/README.md) |
| CVE-2018-2894 | 任意文件上传漏洞 | Weblogic 12.2.1.3.0 等 | ⭐⭐ | [查看](./CVE-2018-2894/README.md) |
| CVE-2020-14882 | 管理控制台未授权远程命令执行漏洞 | Weblogic 10.3.6.0 等 | ⭐⭐ | [查看](./CVE-2020-14882/README.md) |
| CVE-2023-21839 | 未授权远程代码执行漏洞 | Weblogic 12.2.1.3.0 等 | ⭐⭐ | [查看](./CVE-2023-21839/README.md) |
| Weblogic SSRF漏洞 | UDDI Explorer SSRF漏洞 | Oracle WebLogic Server | ⭐⭐ | [查看](./Weblogic SSRF漏洞/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/162) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
