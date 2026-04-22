# Celery 漏洞复现合集

Celery 是一个简单、灵活且可靠的分布式系统，用于处理大量消息，同时为操作提供维护此类系统所需的工具。它是一个专注于实时处理的任务队列，同时也支持任务调度。

本目录收录了与 Celery 相关的漏洞复现文档，涵盖 Redis 未授权访问、Pickle 反序列化执行等多种漏洞类型。

---

## 漏洞列表

| 漏洞编号 | 漏洞类型 | 影响版本 | 难度 | 文档 |
|----------|---------|---------|------|------|
| celery-redis-unauthorized | Redis未授权访问+Pickle反序列化 | Celery < 4.0 | ⭐⭐ | [查看](./celery-redis-unauthorized/README.md) |

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/52) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
