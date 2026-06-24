# Weblogic Weak Password, Arbitrary File Read and Remote Code Execution

> [中文文档](README.md)

| Attribute | Details |
|-----------|---------|
| **Vulnerability** | Weblogic Weak Password + Arbitrary File Read + RCE |
| **Affected Versions** | Weblogic 10.3.6 (11g) / Java 1.6 |
| **Vulnerability Type** | Weak Password + Arbitrary File Read + Remote Code Execution |
| **Severity** | 🔴 High |
| **Default Port** | 7001 |



## Vulnerability Overview

Oracle Weblogic Server is a Java-based enterprise application server.

This environment simulates a real Weblogic environment containing two vulnerabilities: a weak password in the admin console and an arbitrary file read vulnerability in the frontend. Through these two vulnerabilities, we can demonstrate common penetration testing scenarios on Weblogic servers.



## Environment Setup

Start the Weblogic server with the following command. The server is based on Weblogic 10.3.6 (11g) and Java 1.6.

```
docker compose up -d
```

After the environment starts, access `http://your-ip:7001/console` to enter the Weblogic Admin Console.



## Vulnerability Reproduction

The environment contains the following default credentials:

- Username: weblogic
- Password: Oracle@123

For more Weblogic default credentials, refer to: [http://cirt.net/passwords?criteria=weblogic](http://cirt.net/passwords?criteria=weblogic)

### Arbitrary File Read Exploitation

What if we cannot leverage a weak password? How can we penetrate the Weblogic server? This environment simulates an arbitrary file download vulnerability. Access `http://your-ip:7001/hello/file.jsp?path=/etc/passwd` to verify successful reading of the passwd file.

To effectively exploit this vulnerability, we can extract the administrator password through the following steps:

### Reading the Encrypted Password and Key File

Weblogic passwords are encrypted using AES (older versions use 3DES). Since this is symmetric encryption, if we obtain the ciphertext and encryption key, we can decrypt the password. These two files are located in the base_domain directory:

- `SerializedSystemIni.dat`: encryption key file
- `config.xml`: configuration file containing the encrypted password

In this environment, these files are located at:

- `./security/SerializedSystemIni.dat`
- `./config/config.xml`

(Relative to the `/root/Oracle/Middleware/user_projects/domains/base_domain` directory)

When downloading `SerializedSystemIni.dat`, Burp Suite must be used as this is a binary file. Direct browser download may introduce interfering characters. In Burp Suite, select the binary content and use "Copy to File" to save it correctly:

![Burp Suite Save Binary File](https://static.nextcyber.cn/attachments/images/course/task/f38a7622067c4f1990485fd31dda0fc2.png)

In `config.xml`, find the `<node-manager-password-encrypted>` value, which contains the encrypted administrator password:

![Encrypted Password in config.xml](https://static.nextcyber.cn/attachments/images/course/task/e9a2dad93340443d9bf5a4b31726f88b.png)

### Decrypting the Ciphertext

Use the `weblogic_decrypt.jar` tool in the decrypt directory to decrypt the ciphertext. To learn how to build your own decryption tool, refer to: [http://cb.drops.wiki/drops/tips-349.html](http://cb.drops.wiki/drops/tips-349.html)

![Using Decryption Tool](https://static.nextcyber.cn/attachments/images/course/task/c1798a75a5264bf88c06b11dd2204108.png)

The decrypted password matches the preset password, proving successful exploitation.

### Deploying WebShell

After obtaining the administrator credentials, log in to the admin console. Click "Deployments" in the left navigation bar to view the application list:

![Deployments List](https://static.nextcyber.cn/attachments/images/course/task/0126899514be485c82aa7e438fb4db6d.png)

Click "Install" and select "Upload your file(s)":

![Upload WAR Package](https://static.nextcyber.cn/attachments/images/course/task/7bae9c4b24c749b1bd4e38a0111ea8cc.png)

Upload the WAR package. Note that standard Tomcat WAR files may not work properly. You can use the `web/hello.war` package from this project as a template. Click "Next" after uploading.

Enter the application name:

![Enter Application Name](https://static.nextcyber.cn/attachments/images/course/task/d1f323ba6f974562a874a7e1f8f78f6b.png)

Continue to complete the remaining steps, and finally click "Finish".

The application path is specified in the `WEB-INF/weblogic.xml` file within the WAR package. Since the test environment already uses the `/hello` path, you need to modify this path when deploying the shell (for example, change to `/jspspy`):

![Modify WebShell Path](https://static.nextcyber.cn/attachments/images/course/task/99b4379b4c7442758aa1f810d7eda7f9.png)

Successfully accessing the webshell:

![Successfully Access WebShell](https://static.nextcyber.cn/attachments/images/course/task/9636e6dca8a84e99b317bfeb01ac3b52.png)



## Practice Lab

Practice this vulnerability online at [NextCyber Academy](https://app.nextcyber.cn/courses/162).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Please do not use it for illegal activities.
