# HTTPoxy

HTTPoxy 是一组影响 CGI/FastCGI 环境的 HTTP 代理头注入漏洞。由于 RFC 3875 规定 CGI 会将 HTTP 头加上 `HTTP_` 前缀放入环境变量，而许多类库会读取 `HTTP_PROXY` 作为代理，导致攻击者可通过注入 `Proxy` 头劫持服务器外发请求。

## 漏洞列表

| CVE 编号 | 影响组件 | 描述 |
|----------|----------|------|
| CVE-2016-5385 | PHP | PHP CGI HTTPoxy 漏洞（首个公开披露的 HTTPoxy CVE） |
| CVE-2016-5386 | Go | Go 语言 HTTPoxy 漏洞 |
| CVE-2016-5387 | Apache HTTP Server | Apache HTTP Server HTTPoxy 漏洞 |
| CVE-2016-5388 | Apache Tomcat | Apache Tomcat HTTPoxy 漏洞 |
| CVE-2016-6286 | CHICKEN spiffy-cgi-handlers | CHICKEN spiffy-cgi-handlers HTTPoxy 漏洞 |
| CVE-2016-6287 | CHICKEN http-client | CHICKEN http-client HTTPoxy 漏洞 |
| CVE-2016-1000104 | mod_fcgi | mod_fcgi HTTPoxy 漏洞 |
| CVE-2016-1000105 | Nginx cgi script | Nginx cgi script HTTPoxy 漏洞 |
| CVE-2016-1000107 | Erlang inets | Erlang inets HTTPoxy 漏洞 |
| CVE-2016-1000108 | YAWS | YAWS HTTPoxy 漏洞 |
| CVE-2016-1000109 | HHVM FastCGI | HHVM FastCGI HTTPoxy 漏洞 |
| CVE-2016-1000110 | Python CGIHandler | Python CGIHandler HTTPoxy 漏洞 |
| CVE-2016-1000111 | Python Twisted | Python Twisted HTTPoxy 漏洞 |
| CVE-2016-1000212 | lighttpd | lighttpd HTTPoxy 漏洞 |

## 修复建议

1. **升级 PHP** 至 5.6.24 或更高版本
2. **使用 FastCGI** 而非 CGI（部分缓解）
3. **使用环境变量过滤**：在 Web 服务器配置中拒绝或剥离 `Proxy` 请求头
4. **应用层防护**：在应用代码中显式设置代理地址，覆盖环境变量

## 缓解方案

对于无法立即升级的系统，可通过以下方式缓解：

- **Nginx**: 在配置中添加 `fastcgi_param HTTP_PROXY "";`
- **Apache**: 使用 `RequestHeader unset Proxy` 指令
- **PHP**: 在代码开头添加 `unset($_SERVER['HTTP_PROXY']);`
