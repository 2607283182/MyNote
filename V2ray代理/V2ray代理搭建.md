## 硬件准备
1.  一台可以访问外网的VPS
2.  一个静态网站模板
3.  域名与DNS解析
4.  Https 中的 SSL 证书
## VPS准备
平价VPS三大神兽，基本都是5$/月,推荐购买日本或者新加坡的机器。

1. [Linode](https://www.linode.com/) 三大神兽最好用，但是需要信用卡。

2. [搬瓦工](https://bandwagonhost.com/) 没有信用卡推荐使用这个。

3. [Vultr](https://my.vultr.com/) Vultr 官网是中文，对小白特别友好。

廉价灵车(不是很稳定，但是年付基本10$左右)
1. [racknerd](https://my.racknerd.com/) 
2. [pacificrack](https://pacificrack.com/) 

### VPS可用性测试
1. 是否可以访问Google
使用putty.exe工具或者使用VNC Viewer工具连接到VPS，输入以下命令
```
ping www.google.com
运行几秒钟后，Crtl+C取消
可以PING通，则证明该VPS可用。
```
2. VPS速度测试

可在GITHUB上找一些测试速度脚本查看VPS网络速度，可略过。

## 静态网站模板
我这里提供一个，见同目录下moban4083.7z,如果想搞自己模板，就Baidu.com查一下。
## 域名
[免费域名申请网站](http://freenom.com/) 

注意：当购买域名的最后一步的时候，界面下面有个当前订单的IP地址，查询一下该IP地址的所在的省市区，我是翻墙过去购买的，所以IP是国外的，所以个人信息需要修改当前购买域名的IP的省市区，姓名，否则购买不成功!


## DNS解析
[域名解析网站](https://dcc.godaddy.com/manage/dns)

把刚才买的域名需要在这个网站上配置DNS解析，目前可用，不知道以后可不可以用。具体需要查看[生成证书的文档](https://github.com/acmesh-official/acme.sh/wiki/dnsapi),之前我是用cloudflare的，但是cloudflare的免费域名不能DNS了。

[生成证书的文档](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)上写着
```
Currently Cloudflare doesn't allow API access in free tld domain. You will get the Txt records insert failed. Make sure to verify your api token before editing dns_cf, you can alternatively use DNS manual mode if you are using free tld on cloudflare.
```
所以到底用哪个DNS解析，全看文档。

配置DNS解析需要在[免费域名申请网站](http://freenom.com/) 把nameservers配置成DNS解析需要的域名。
## SSL申请
[ACME官网](https://github.com/acmesh-official/acme.sh)

基本都是使用acme工具的dns api方式。
1. 安装acme.sh
```
curl  https://get.acme.sh | sh
取别名，方便使用
alias acme.sh=~/.acme.sh/acme.sh
```
2. 生成SSL公私钥(这一步根据不同域名解析商需做修改)
```
// 在域名解析商获取
export GD_Key="eoghaRfFZzgw_HVXeCHTiD9czAA65QXXX" 
export GD_Secret="FUqySAms3mnf6t7cXXXX"
// 申请SSL
acme.sh --issue --dns dns_gd -d example.com -d www.example.com
// 获取公钥和签名证书
acme.sh --install-cert -d example.com \
--key-file       /path/to/keyfile/in/nginx/key.pem  \
--fullchain-file /path/to/fullchain/nginx/cert.pem \
--reloadcmd     "service nginx force-reload"
```
## 配置系统环境
```
apt-get update
```
## 配置Nginx
1. 安装nginx 
```
apt-get install nginx -y
```
2. 把静态网站放到nginx初始目录下。
3. 访问网站，能出现模板网站说明正确。
## 安装V2ray
[V2ray组织官网](https://github.com/v2fly) 这个是V2ray开源组织官网

[V2rayInstall官网](https://github.com/v2fly/fhs-install-v2ray) 这个是安装V2ray脚本官网

[V2fly官网](https://www.v2fly.org/) 可翻墙的话可看这个官网，配置文件也很详细
## Nginx和v2ray配置文件
我的自己的配置，可参考,见同文件夹下。

## 更进一步
[Trojan-go官网](https://github.com/p4gefau1t/trojan-go) 
trojan比v2ray 快了1/3得，如果追求极致速度，就搞个trojan更爽.
也给出了trojan的配置文件。
## 加速你的VPS
开启GOOGLE BBR
```
/etc/nginx/sites-enabled
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
lsmod | grep bbr
```










