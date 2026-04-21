# HTTPoxy 漏洞复现合集

HTTPoxy 是一组影响 CGI/FastCGI 环境的 HTTP 代理头注入漏洞。由于 RFC 3875 规定 CGI 会将 HTTP 头加上 `HTTP_` 前缀放入环境变量，而许多类库约定俗成会读取 `HTTP_PROXY` 作为代理地址，导致攻击者可通过注入 `Proxy` 请求头劫持服务器外发 HTTP 请求，窃取敏感数据。

本目录收录了与 HTTPoxy 相关的漏洞复现文档，涵盖 PHP、Apache、Nginx、Go 等多种受影响组件。

---

## 漏洞列表

| CVE 编号 | 影响组件 | 描述 | 难度 | 文档 |
|----------|---------|------|------|------|
| CVE-2016-5385 | PHP | PHP CGI HTTPoxy 漏洞（首个公开披露的 HTTPoxy CVE） | ⭐⭐ | [查看](./CVE-2016-5385/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/54) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
