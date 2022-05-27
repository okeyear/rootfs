# /etc/shadow文件
### 字段描述
```bash
# head /etc/shadow
root:$1$FbrAlmBH$ObhQBkkYlRhelR3AdnrcB0:18882:0:99999:7:::
bin:*:18027:0:99999:7:::
```
1. 用户名（也被称为登录名），在/etc/shadow中，用户名和/etc/passwd 是相同的，这样就把passwd 和shadow中用的用户记录联系在一起；这个字段是非空的；
2. 加密后的密码,这个字段是非空的。如果密码是“\*”或“!”，则表示不会用这个帐号来登录（通常是一些后台进程）

|ID|算法|
| ----- | ----- |
|1|MD5|
|2a|Blowfish|
|5|SHA-256|
|6|SHA-512|

3. 上次修改口令的时间。 从1970年1月1日起计算的天数。
   1. 可以通过passwd 来修改用户的密码，然后查看/etc/shadow中此字段的变化；
4. 两次修改口令间隔最少的天数。 如果是0,则表示可以随时修改密码。如果是N，表示N天后才可修改密码。如果这个字段的值为空，帐号永久可用；
5. 两次修改口令间隔最多的天数。如果设置为N，表示N天后必须更新密码。设置为99999通常表示无需更新密码。如果这个字段的值为空，帐号永久可用；
6. 提前多少天警告用户口令将过期。如果这个字段的值为空，帐号永久可用；
7. 在口令过期之后多少天禁用此用户。如果这个字段的值为空，帐号永久可用；
8. 用户过期日期。；此字段指定了用户作废的天数（从1970年的1月1日开始的天数），如果这个字段的值为空，帐号永久可用；
9. 保留字段。



### 生成密码
[https://www.cnblogs.com/wyzhou/p/9739004.html](https://www.cnblogs.com/wyzhou/p/9739004.html)

```bash
function Passwd(){
    authconfig --test | grep hashing
    python -c ``"import crypt, getpass, pwd; print crypt.crypt('123456', '\$6\$`openssl rand -base64 16 | tr -d '+=' | head -c 8`\$')"
    # openssl passwd -1 -salt $(< /dev/urandom tr -dc '[:alnum:]' | head -c 32)
    # echo "password123" | openssl passwd -1 -salt $(< /dev/urandom tr -dc '[:alnum:]' | head -c 32) -stdin 
}
```