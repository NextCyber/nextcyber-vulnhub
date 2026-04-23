# Adobe ColdFusion

> 本文档汇总 Adobe ColdFusion 相关漏洞。

## 漏洞列表

| CVE 编号 | 漏洞类型 | 危险等级 |
|----------|----------|----------|
| CVE-2010-2861 | 目录穿越文件读取 | 🔴 高危 |
| CVE-2017-3066 | Java 反序列化漏洞 | 🔴 高危 |

## 环境说明

Adobe ColdFusion 默认端口：8500

## 相关工具

- Burp Suite（抓包修改）
- ColdFusionPwn（POC 生成）
- ysoserial（反序列化攻击）
- curl（发送 PoC 请求）
