# Hadoop 漏洞复现合集

Hadoop 是一个分布式计算应用程序框架，种类功能繁多，各种组件安全问题会带来很大的攻击面。Apache Hadoop YARN 是 Hadoop 的核心组件之一，负责将资源分配在 Hadoop 集群中运行的各种应用程序，并调度要在不同集群节点上执行的任务。

本目录收录了与 Hadoop 相关的安全漏洞复现文档。

---

## 漏洞列表

| 漏洞名称 | 漏洞类型 | 危险等级 | 难度 | 文档 |
|----------|---------|----------|------|------|
| Hadoop YARN ResourceManager 未授权访问 | 未授权访问 | 🟠 中危 | ⭐ | [查看](./unauthorized-access/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/85) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
