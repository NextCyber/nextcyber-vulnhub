# Apache Solr 漏洞复现合集

Apache Solr 是一个开源的搜索服务器，使用 Java 语言开发，基于 HTTP 和 Apache Lucene 实现，通过 XML/JSON 进行文档索引和查询。

本目录收录了与 Apache Solr 相关的漏洞复现文档，涵盖远程命令执行（RCE）、XXE、SSRF 等多种漏洞类型。

---

## 漏洞列表

| CVE 编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| CVE-2017-12629 | Apache Solr 远程命令执行漏洞（RCE） | Apache Solr < 7.1.0 | ⭐⭐ | [查看](./CVE-2017-12629-RCE/README.md) |
| CVE-2017-12629 | Apache Solr XML 实体注入漏洞（XXE） | Apache Solr < 7.1.0 | ⭐⭐ | [查看](./CVE-2017-12629-XXE/README.md) |
| CVE-2019-0193 | Apache Solr DataImportHandler 远程命令执行漏洞 | Apache Solr < 8.2.0 | ⭐⭐ | [查看](./CVE-2019-0193/README.md) |
| CVE-2019-17558 | Apache Solr Velocity 模板远程命令执行漏洞 | Apache Solr 5.0 - 8.3 | ⭐⭐ | [查看](./CVE-2019-17558/README.md) |
| CNVD-2019-xxxxx | Apache Solr RemoteStreaming 文件读取与SSRF漏洞 | 受影响版本 | ⭐⭐ | [查看](./Apache-Solr-RemoteStreaming-文件读取与SSRF漏洞/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/150) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
