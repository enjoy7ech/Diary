# DNS相关

### **1、**[**dnsmasq**](https://cloud.tencent.com/developer/article/1174717)**的简介**

[Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) 提供 DNS 缓存和 DHCP 服务功能。作为[域名解析](https://cloud.tencent.com/product/cns?from=10680)服务器(DNS)，dnsmasq可以通过缓存 DNS 请求来提高对访问过的网址的连接速度。作为DHCP 服务器，[dnsmasq](https://www.archlinux.org/packages/?name=dnsmasq) 可以用于为局域网电脑分配内网ip地址和提供路由。DNS和DHCP两个功能可以同时或分别单独实现。dnsmasq轻量且易配置，适用于个人用户或少于50台主机的网络。此外它还自带了一个 [PXE](https://wiki.archlinux.org/index.php/PXE_(简体中文)) 服务器。

### 2、配置

```
\# 默认情况下Dnsmasq会发送查询到它的任何上游DNS服务器上，如果取消注释， 
# 则Dnsmasq则会严格按照/etc/resolv.conf中的DNS Server顺序进行查询。 
strict-order

# 以下两个参数控制是否通过/etc/resolv.conf确定上游服务器，是否检测/etc/resolv.conf的变化。
# 如果你不想Dnsmasq读取/etc/resolv.conf文件或者其他文件，获得它的servers，则取消注释。
no-resolv
# 如果你不允许Dnsmasq通过轮询/etc/resolv.conf或者其他文件来获取配置的改变，则取消注释。
no-poll

# resolv-file配置Dnsmasq额外的向流的DNS服务器，通过下面的选项指定其他文件。
# 如果不开启就使用linux主机默认的/etc/resolv.conf里的nameserver。
resolv-file=/etc/dnsmasq.d/upstream_dns.conf
```

### 3、可运行简易配置

##### dnsmasq.conf

```
#dnsmasq config, for a complete example, see:
#  http://oss.segetech.com/intra/srv/dnsmasq.conf
#log all dns queries
log-queries
#dont use hosts nameservers

#use cloudflare as default nameservers, prefer 1^4

strict-order

resolv-file=/etc/resolv.dnsmasq.conf
strict-order

#serve all .company queries using a specific nameserver
#server=/company/192.168.110.2

#explicitly define host-ip mappings
address=/os1.com/192.168.110.129
address=/os2.com/192.168.110.130


#这个是重要选项，监听地址，要写上ip地址加上127.0.0.1，因为IP地址是给你
#的client机用的，127.0.0.1是给dnsmasq用的，为什么呢，是因为你要做dns缓
#存，要访问自己即是本机，格式就是ip,127.0.0.1
listen-address=127.0.0.1,192.168.110.130

#绑定了网卡之后会保证dnsmasq不去骚扰其他网卡，保证请求不乱发，一般跟
#interface一起使用
#bind-interfaces
bind-interfaces

#使用另外一个文件代替hosts，这样就可以不骚扰本机的host 从而保证服务器
#固有host不被影响，也可以给dnsmasq使用特别的hosts
#addn-hosts=/etc/banner_add_hosts
#addn-hosts=/etc/dnsmasq.host

```

##### resolv.dnsmasq.conf	(上游DNS配置)

```
nameserver 8.8.8.8
```

