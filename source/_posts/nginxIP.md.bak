---
title: Nginx代理Tomcat服务器获取客户端真实IP
date: 2017-12-29 14:45:20
tags: [Nginx]
categories: Nginx
---
#### 一、背景
　　Java Web项目中经常需要在后端获取前端IP，通过`request.getRemoteAddr()`这种方法只会获取到上一级的IP，在通过了Nginx，Apache等反向代理服务器，此方法获取的就是服务器的IP，而不是客户端的真实IP地址。
　　这是反向代理的原因,TCP连接是在代理和网站之间，而非用户与网站之间的；HTTP协议只是第七层协议，不会把IP层访问者的源IP信息同时发送。因此服务器无法得到客户端地真实IP，但是可以通过`X-Forwarded-For`绕过服务器IP地址过滤。

#### X-Forwarded-For（简称XFF）
{% note success %}
　　X-Forwarded-For是一个HTTP扩展头部。HTTP/1.1（RFC 2616）协议并没有对它的定义，它最开始是由Squid这个缓存代理软件引入，用来表示 HTTP 请求端真实 IP。如今它已经成为事实上的标准，被各大 HTTP 代理、负载均衡等转发服务广泛使用，并被写入 RFC 7239（Forwarded HTTP Extension）标准之中。
XFF的格式为：`X-Forwarded-For: client, proxy1, proxy2`
　　XFF 的内容由「英文逗号 + 空格」隔开的多个部分组成，最开始的是离服务端最远的设备 IP，然后是每一级代理设备的 IP。（注意：如果未经严格处理，可以被伪造）
{% endnote %}

#### 二、解决方案
在Nginx配置中的location节点中加入
```xml
proxy_set_header  Host  $host;
proxy_set_header  X-Real-IP  $remote_addr;
proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
```
其中`$proxy_add_x_forwarded_for`会累加代理层的IP向后传递

服务器端通过`X-Forwarded-For`请求头获取IP
```java
public static String getIpAddress(HttpServletRequest request) {
　　String ipAddress = request.getHeader("X-Forwarded-For");
　　　　if (ipAddress == null || ipAddress.length() == 0 
　　　　　　　　|| "unknown".equalsIgnoreCase(ipAddress)) {
　　　　　　ipAddress = request.getHeader("Proxy-Client-IP");
　　　　}
　　　　if (ipAddress == null || ipAddress.length() == 0 
　　　　　　　　|| "unknown".equalsIgnoreCase(ipAddress)) {
　　　　　　ipAddress = request.getHeader("WL-Proxy-Client-IP");
　　　　}
　　　　if (ipAddress == null || ipAddress.length() == 0 
　　　　　　　　|| "unknown".equalsIgnoreCase(ipAddress)) {
　　　　　　ipAddress = request.getRemoteAddr();
　　　　}
　　　　// 对于通过多个代理的情况，第一个IP为客户端真实的IP地址，多个IP按照','分割
　　　　if (null != ipAddress && ipAddress.length() > 15) {
　　　　// "***.***.***.***".length() = 15
　　　　if (ipAddress.indexOf(",") > 0) {
　　　　　　ipAddress = ipAddress.substring(0, ipAddress.indexOf(","));
　　　　}
　　}
　　return ipAddress.equals("0:0:0:0:0:0:0:1") ? "127.0.0.1" : ipAddress;
}
```
#### 三、思考
* 通过`X-Forwarded-For`获取到了客户端真实IP，但其实际上是把IP放在请求头中传递给服务器端，所以这个XFF的名称是可以自定义的。比如可以定义为my-client-IP之类，然后记得在Web程序那边设定好去取这个名为my-client-IP的头标即可。
* 通过`request.getRemoteAddr()`获取的Remote Address无法伪造，因为建立 TCP 连接需要三次握手，如果伪造了源IP，无法建立TCP连接，更不会有后面的HTTP请求。但是在正常情况下，Web服务器获取Remote Address只会获取到上一级的IP。
* 当多层代理或使用CDN时，如果代理服务器不把用户的真实IP传递下去，那么业务服务器将永远不可能获取到用户的真实IP。
* `X-Forwarded-For`这种方式，客户端可以任意伪造IP，并且可以传入任意格式IP，这样会产生很多问题
    - 如果服务端通过IP做限制，前端很容易通过修改IP跳过限制。 
    - 如果直接使用这样的IP，会带来SQL注入，服务端报错，跨站攻击等漏洞。

　投票系统就可能被这种方式伪造IP刷票。

[X-Forwarded-For绕过服务器IP地址过滤](http://blog.csdn.net/caiqiiqi/article/details/72852083)
[Nginx 如何配置来获取用户真实IP](http://blog.csdn.net/bigtree_3721/article/details/72820081)


