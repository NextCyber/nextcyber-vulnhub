# GoAhead Web Server 漏洞复现合集

GoAhead 是一个开源（商业许可）、简单、轻巧、功能强大的 Web Server，多用于嵌入式系统和智能设备。其支持运行 ASP、Javascript 和标准的 CGI 程序。

本目录收录了与 GoAhead Web Server 相关的经典漏洞复现文档，涵盖远程命令执行等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2017-17562 | LD_PRELOAD 远程命令执行 | GoAhead < 3.6.5 | ⭐⭐ | [查看](./CVE-2017-17562/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/81) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
