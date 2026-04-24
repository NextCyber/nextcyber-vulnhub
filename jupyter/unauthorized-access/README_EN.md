# Jupyter Notebook Unauthorized Access Vulnerability

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability Name** | Jupyter Notebook Unauthorized Access |
| **Affected Component** | Jupyter Notebook / IPython notebook |
| **Vulnerability Type** | Unauthorized Access |
| **Severity** | 🟠 Medium |
| **Default Port** | 8888 |

## Vulnerability Description

`Jupyter Notebook` (formerly known as `IPython notebook`) is an interactive notebook that supports running over 40 programming languages. If the administrator does not configure a password for Jupyter Notebook, it will lead to an unauthorized access vulnerability, allowing attackers to create a `console` and execute arbitrary `Python` code and commands.

## Vulnerability Reproduction

### Environment Information

- Target IP: 192.168.2.47
- Web Port: 8888

### Operation Steps

**1. Access Jupyter Notebook Web Interface**

After the target starts, access `http://192.168.2.47:8888` to see the Jupyter Notebook `Web` management interface without being asked for a password:

![Management Interface](https://static.nextcyber.cn/attachments/images/course/task/3557312608b74d4e8501180a49598d29.png)

## Vulnerability Exploitation

### Create a Terminal**

Select `new -> terminal` to create a console:

![Create Terminal](https://static.nextcyber.cn/attachments/images/course/task/f13f038050e447d39770ff4666da7cb5.png)

### Execute Commands**

Execute `arbitrary commands` directly in the console:

![Execute Commands](https://static.nextcyber.cn/attachments/images/course/task/f93eca501fa348d9b26940c6577eb621.png)

## Vulnerability Principle

Jupyter Notebook starts a web service by default. If the administrator does not set a password or configure proper authentication, attackers can directly access the web interface without any credentials. Once access is obtained, attackers can create new terminals to execute arbitrary commands on the server, gaining full control of the server.

## Remediation Recommendations

1. Set a strong password for Jupyter Notebook
2. Configure the `c.NotebookApp.password` parameter
3. Restrict access to local only (`c.NotebookApp.ip = 'localhost'`)
4. Use SSL/TLS encryption for communications
5. Configure firewall rules to restrict access sources
