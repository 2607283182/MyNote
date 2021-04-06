## acme是什么
[ACME官网](https://github.com/acmesh-official/acme.sh)

acme是一个申请SSL非对称加密证书的工具，是 [let's encrypt官网](https://letsencrypt.org/)推荐的工具,方便用户申请一个https 的ssl.

## 申请准备
1. 一台VPS主机
## 申请方式
### http方式
1. 需要在DNS解析服务器解析IP地址,解析成功后可以用域名访问你的VPS主机。例：可以用www.caosong.com 访问我自己的VPS的IP
2. 如果你的机子没有在80端口启动一个http服务器，则使用
```
acme.sh  --issue -d mydomain.com   --standalone
官网解释：如果你还没有运行任何 web 服务, 80 端口是空闲的, 那么 acme.sh 还能假装自己是一个webserver, 临时听在80 端口, 完成验证:
```
3. 如果你的机子在80端口启动了http服务器，则使用
```
acme.sh  --issue  -d mydomain.com -d www.mydomain.com  --webroot  /home/wwwroot/mydomain.com/
重点：webroot是你的项目的根路径，实际上，acme是在你的webroot这个路径新生成个验证文件。例子：
在你的webroot下，新建一个 vertify.txt文件。
用它自己的服务器访问你的-d的域名 mydomain.com/vertify.txt 如果有的话，则可以确定你申请的域名mydomain.com确实属于你，所以可以给你公钥私钥和证书，如果没有，那这个域名不属于你，当然不可能给你公私钥和证书了。

```
### dns方式
1. 不需要在DNS解析服务器解析IP地址，因为给你颁发公私钥和证书其实就是要确认域名属于你，只要你证明了，就可以给你了。
2. 需要配置你的DNS解析服务商对外提供的KEY和EMAIL，不同DNS解析服务商需要配置不同的参数，见[官方参考文档](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)。
3. 以GoDaddy.com为例
```
导入key和secret
export GD_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export GD_Secret="asdfsdafdsfdsfdsfdsfdsafd"
获得证书
acme.sh --issue --dns dns_gd -d example.com -d www.example.com
多了个--dns 也就是从dns_gd这个服务商来验证，原理是：
它要在dns服务商解析中添加个txt记录，什么是txt记录，见 DNS记录.md 文档，然后使用 
nslookup -qt = txt example.com 
nslookup -qt = txt www.example.com
发现有记录，说明这个域名确实属于你，就给你颁发公私钥和证书了。
```
### http 和 dns优势
http：必须是在VPS本机运行acme命令并且需要在DNS解析商解析一个A记录，指向你的服务器。但是不需要你的服务商的key和secret
dns：可以在随便一台机子上acme为你的VPS申请证书，但是需要服务商的key和secret
