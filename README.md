# nginx 常用配置

记录了一些常见的 nginx 配置。大部分配置项都以注释形式给出了解释。

`nginx.conf`为主要配置文件，对应`/etc/nginx/nginx.conf`。存放日志、TLS、默认服务器等通用设置。

`a.conf`为站点 A 的配置文件，可以放在`/etc/nginx/conf.d/a.conf`。

## 解释

### HTTP/3

nginx 1.25.0 及以上才支持 HTTP/3。

HTTP/3 使用 QUIC 协议，基于 UDP。和 HTTP/2 一样必须使用 TLS。

注意要在防火墙开启 UDP 的 443 端口。

### nginx 自定义状态码

nginx 自定义了一些 HTTP 状态码。除了 444 外，其他状态码如果未处理，客户端收到的都是 400 加一行错误提示。

| 状态码 | 含义 |
| - | - |
| 444 | 直接关闭连接，不返回任何响应 |
| 495 | 客户端证书验证失败 |
| 496 | 客户端未提供证书 |

### [Mozilla SSL 配置生成器](https://ssl-config.mozilla.org/)

Mozilla 提供的网页工具，输入服务器程序、配置集、服务器程序版本、OpenSSL 版本等，就会一键生成 TLS 相关配置。

### location 查找顺序

nginx 的 server 中可以定义多个 location。nginx 会根据一系列规则找到和请求最匹配的 location。

[官方文档](https://nginx.org/en/docs/http/ngx_http_core_module.html#location)明确说：

> A location can either be defined by a prefix string, or by a regular expression. Regular expressions are specified with the preceding “~ *” modifier (for case-insensitive matching), or the “~” modifier (for case-sensitive matching). To find location matching a given request, nginx first checks locations defined using the prefix strings (prefix locations). Among them, the location with the longest matching prefix is selected and remembered. Then regular expressions are checked, in the order of their appearance in the configuration file. The search of regular expressions terminates on the first match, and the corresponding configuration is used. If no match with a regular expression is found then the configuration of the prefix location remembered earlier is used.

简单解释一下：

一个 location 可以是前缀字符串，也可以是一个正则表达式。

要查找匹配请求的 location 时，nginx 首先从前缀字符串中找到**最长匹配**的前缀 location，并记住。然后检查正则表达式 location，按照在配置文件中出现的顺序（也就是**从上到下**）依次检查，在第一个正则表达式 location 匹配时停止并使用。如果没有匹配的正则表达式，刚才记住的前缀 location 会被使用。
