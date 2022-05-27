# /etc/resolv.conf文件
### systemd下的建议别名
```bash
# RHEL8及以上版本
# 请查阅 systemd-resolvd 相关内容
ln -sf /usr/lib/systemd/resolv.conf /etc/resolv.conf
```
### 当前系统的参数是
建议加上 single-request

```bash
options timeout:1 attempts:1 rotate single-request
```
### aliyun ECS推荐参数
后面single-request-reopen 

```Plain Text
man 5 resolv.conf
```
[http://coolnull.com/3820.html](http://coolnull.com/3820.html)

```bash
options timeout:2 attempts:3 rotate single-request-reopen
```
### 字段解释
**注意**: domain和search不能共存；如果同时存在，后面出现的将会被使用。



* nameserver

表示解析域名时使用该地址指定的主机为域名服务器。其中域名服务器是按照文件中出现的顺序来查询的,且只有当第一个nameserver没有反应时才查询下面的nameserver。

* domain

声明主机的域名。很多程序用到它，如邮件系统；当为没有域名的主机进行DNS查询时，也要用到。如果没有域名，主机名将被使用，删除所有在第一个点( .)前面的内容。

* search 　　

它的多个参数指明域名查询顺序。当要查询没有域名的主机，主机将在由search声明的域中分别查找。

有这个参数之后, 解析查询时候,可以只用hostname;  

如 `nslookup appsrv001` , 如果没有这一行, 就需要FQDN查询, 如`nslookup appsrv001.domain.com`

* sortlist 　

允许将得到域名结果进行特定的排序。它的参数为网络/掩码对，允许任意的排列顺序。

* options 

其他参数

### 