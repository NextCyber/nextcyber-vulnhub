# Jupyter Notebook 未授权访问漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| **漏洞名称** | Jupyter Notebook 未授权访问漏洞 |
| **影响组件** | Jupyter Notebook / IPython notebook |
| **漏洞类型** | 未授权访问 |
| **危险等级** | 🟠 中危 |
| **默认端口** | 8888 |

## 漏洞描述

`Jupyter Notebook`（此前被称为 `IPython notebook`）是一个交互式笔记本，支持运行 40 多种编程语言。如果管理员未为 Jupyter Notebook 配置密码，将导致未授权访问漏洞，游客可在其中创建一个 `console` 并执行任意 `Python` 代码和命令。

## 漏洞复现

### 环境信息

- 靶机 IP：192.168.2.47
- Web 端口：8888

### 操作步骤

**1. 访问 Jupyter Notebook 管理界面**

运行后，访问 `http://192.168.2.47:8888` 将看到 Jupyter Notebook 的 `Web` 管理界面，并没有要求填写密码：

![管理界面](https://static.nextcyber.cn/attachments/images/course/task/3557312608b74d4e8501180a49598d29.png)

## 漏洞利用

### 创建终端

选择 `new -> terminal` 即可创建一个控制台：

![创建终端](https://static.nextcyber.cn/attachments/images/course/task/f13f038050e447d39770ff4666da7cb5.png)

### 执行命令

在控制台直接执行 `任意命令`：

![执行命令](https://static.nextcyber.cn/attachments/images/course/task/f93eca501fa348d9b26940c6577eb621.png)

## 漏洞原理

Jupyter Notebook 默认情况下会开启一个 Web 服务，如果管理员没有为其设置密码或配置正确的认证机制，攻击者可以直接访问 Web 界面而无需任何凭据。一旦获得访问权限，攻击者可以通过创建新的终端（terminal）来在服务器上执行任意命令，获得服务器的完全控制权。

## 修复建议

1. 为 Jupyter Notebook 设置强密码
2. 配置 `c.NotebookApp.password` 参数
3. 限制仅允许本地访问（`c.NotebookApp.ip = 'localhost'`）
4. 使用 SSL/TLS 加密通信
5. 配置防火墙规则限制访问来源
