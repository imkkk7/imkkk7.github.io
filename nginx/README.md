# Nginx

## 什么是Nginx

*Nginx* (engine x) 是一个高性能的[HTTP](https://baike.baidu.com/item/HTTP)和[反向代理](https://baike.baidu.com/item/反向代理/7793488)web服务器，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为[俄罗斯](https://baike.baidu.com/item/俄罗斯/125568)访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，公开版本1.19.6发布于2020年12月15日。 [12] 

其将[源代码](https://baike.baidu.com/item/源代码/3814213)以类[BSD许可证](https://baike.baidu.com/item/BSD许可证/10642412)的形式发布，因它的稳定性、丰富的功能集、简单的配置文件和低系统资源的消耗而[闻名](https://baike.baidu.com/item/闻名/2303308)。2022年01月25日，nginx 1.21.6发布。 [13] 

Nginx是一款[轻量级](https://baike.baidu.com/item/轻量级/10002835)的[Web](https://baike.baidu.com/item/Web/150564) 服务器/[反向代理](https://baike.baidu.com/item/反向代理/7793488)服务器及[电子邮件](https://baike.baidu.com/item/电子邮件/111106)（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，[并发](https://baike.baidu.com/item/并发/11024806)能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好。

## Nginx作用

### 反向代理的理解

代理服务器，让你无感知的访问服务器。不需要安装什么

### Nginx提供的负载均衡策略

1.内置策略：轮询、加权轮询、IP hash

[![XpVPXT.png](https://s1.ax1x.com/2022/05/23/XpVPXT.png)

[[![XpVU3t.png](https://s1.ax1x.com/2022/05/23/XpVU3t.png)](https://imgtu.com/i/XpVU3t)](https://imgtu.com/i/XpVPXT)

[![XpVDHg.png](https://s1.ax1x.com/2022/05/23/XpVDHg.png)](https://imgtu.com/i/XpVDHg)

[![XpV5bF.png](https://s1.ax1x.com/2022/05/23/XpV5bF.png)](https://imgtu.com/i/XpV5bF)

2.拓展策略：天马行空...

## Nginx常用命令

~~~bash
cd /usr/local/nginx/sbin/

./nginx 启动

./nginx -s stop 停止

./nginx -s quit 安全退出

./nginx -s reload 重新加载配置文件

ps aux|grep nginx  查看nginx进程
#开启
service firewalld start
#重启
service firewalld restart
#关闭
service firewalld stop
#查看防火墙规则
firewall-cmd --list-all
#查询端口是否开放
firewall-cmd --query-port=8080/tcp

#重启防火墙（修改配置后要重启防火墙）
firewall-cmd --reload

#参数解释
firewall-cmd：是linux提供的操作firewall的工具
--permanent：表示设置为持久
--add-port：表示添加的端口
~~~

## 实际使用

[![Xp8nC8.png](https://s1.ax1x.com/2022/05/23/Xp8nC8.png)](https://imgtu.com/i/Xp8nC8)

上面的upstream为负载均衡所配置的

weight为权重：举例：weight为3和1的话，每四次请求就有三次打向上面的端口，一次打向下面的端口

下面的location /是根目录下...

proxy_pass为反向代理的配置