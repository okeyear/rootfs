


TODO:
以下笔记较为混乱,待整理

# /etc/passwd文件
[https://linux.die.net/man/5/passwd](https://linux.die.net/man/5/passwd)

对于操作系统安装后存在的默认的系统用户，红帽是不建议对这些用户进行删除或修改的。  

具体原因请参考以下KCS：
Is it safe to remove/change system user account on Red Hat Enterprise Linux ?
[https://access.redhat.com/solutions/31669](https://access.redhat.com/solutions/31669)  

另外，关于您在问题单中提及到希望获得这些用户的具体作用，可以参考以下链接进一步了解：
Description and purpose of standard system user accounts in Red Hat Enterprise Linux
[https://access.redhat.com/solutions/225183](https://access.redhat.com/solutions/225183)

Have a look at `/usr/share/doc/setup-*/uidgid` file for a more comprehensive list of standard users, along with the names of the packages which provide them.

查看系统默认用户 `cat /usr/share/doc/setup-*/uidgid`



文件内容示例

```bash
head /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```
---
|字段|含义|
| ----- | ----- |
|1|用户名|
|2|用户的密码原来直接存储在第二字段，但是为了安全，最后专门有了/etc/shadow文件，现在默认用x替代|
|3|用户的uid,一般情况下root为0，1-499默认为系统账号，有的更大些到1000，500-65535为用户的可登录账号，有的系统从1000开始。|
|4|用户的gid,linux的用户都会有两个ID,一个是用户uid，一个是用户组id，在我们登录的时候，输入用户名和密码，其实会先到/etc/passwd查看是否有你输入的账号或者用户名，有的话将该账号与对应的UID和GID(在/etc/group中)读出来。然后读出主文件夹与shell的设置，然后再去检验密码是否正确，正确的话正常登录。|
|5|用户的账号说明解释|
|6|用户的家目录文件夹|
|7|用户使用的shell，如果换成/sbin/nologin/就是默认没有登录环境的。|

### fields desc
Each line of the file describes a single user, and contains seven colon-separated fields:

> name:password:UID:GID:GECOS:directory:shell

The field are as follows:

#### name
This is the user's login name. It should not contain capital letters.

#### password
This is either the encrypted user password, an asterisk (\*), or the letter aqxaq. (See ***pwconv****\**\*\*(8)\* for an explanation of aqxaq.)

现在的Unix/Linux系统中，口令不再直接保存在passwd文件中，通常将passwd文件中的口令字段使用一个“x”来代替，将/etc /shadow作为真正的口令文件，用于保存包括个人口令在内的数据。如果passwd字段中的第一个字符是“\*”的话，那么，就表示该账号被查封了，系统不允许持有该账号的用户登录。   

#### UID
The privileged *root* login account (superuser) has the user ID 0.

#### GID
This is the numeric primary group ID for this user. (Additional groups for the user are defined in the system group file; see ***group****\**\*\*(5)\*).

#### GECOS
This field (sometimes called the "comment field") is optional and used only for informational purposes. Usually, it contains the full username. Some programs (for example, ***finger****\**\*\*(1)\*) display information from this field.

GECOS stands for "General Electric Comprehensive Operating System",

which was renamed to GCOS when GE's large systems division was sold to Honeywell. Dennis Ritchie has reported: "Sometimes we sent printer output or batch jobs to the GCOS machine. The gcos field in the password file was a place to stash the information for the \$IDENTcard. Not elegant."

包含有关用户的一些信息，如用户的真实姓名、办公室地址、联系电话等。在Linux系统中，mail和finger等程序利用这些信息来标识系统的用户。

#### directory
This is the user's home directory: the initial directory where the user is placed after logging in. The value in this field is used to set the **HOME** environment variable.

也就是用户的起始工作目录，它是用户在登录到系统之后所处的目录。在大多数系统中，各用户的主目录都被组织在同一个特定的目录下，而用户主目录的名称就是该用户的登录名。各用户对自己的主目录有读、写、执行（搜索）权限，其他用户对此目录的访问权限则根据具体情况设置。用户登录后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或某个特定的程序，即Shell。Shell是用户与Linux系统之间的接口。Linux的Shell有许多种，每种都有不同的特点。常用的有sh(BourneShell),csh(CShell),ksh(KornShell),tcsh(TENEX/TOPS-20typeCShell),bash(BourneAgainShell)等。系统管理员可以根据系统情况和用户习惯为用户指定某个Shell。如果不指定Shell，那么系统使用sh为默认的登录Shell，即这个字段的值为/bin/sh。用户的登录Shell也可以指定为某个特定的程序（此程序不是一个命令解释器）。利用这一特点，我们可以限制用户只能运行指定的应用程序，在该应用程序运行结束后，用户就自动退出了系统。有些Linux系统要求只有那些在系统中登记了的程序才能出现在这个字段中。系统中有一类用户称为伪用户（psuedousers），这些用户在Linux /etc/passwd文件中也占有一条记录，但是不能登录，因为它们的登录Shell为空。它们的存在主要是方便系统管理，满足相应的系统进程对文件属主的要求。常见的伪用户如下所示。

**伪用户含义**

> bin              拥有可执行的用户命令文件

> sys              拥有系统文件

> adm            拥有帐户文件

> uucp           UUCP使用

> lplp或lpd    子系统使用

> nobody      NFS使用

拥有帐户文件

除了上面列出的伪用户外，还有许多标准的伪用户，例如：audit,cron,mail,usenet等，它们也都各自为相关的进程和文件所需要。

#### shell
This is the program to run at login (if empty, use */bin/sh*). If set to a nonexistent executable, the user will be unable to login through \_**login**(1)\_. The value in this field is used to set the **SHELL** environment variable.





---
### passwd\_esc(passwd中逗号后的计数)
　　Linux口令管理之/etc/passwd文件   

　　/etc/passwd文件是Linux/UNIX安全的关键文件之一.该文件用于用户登录时校验 用户的口令,当然应当仅对root可写.文件中每行的一般格式为:   

　　LOGNAME:PASSWORD:UID:GID:USERINFO:HOME:SHELL   

　　每行的头两项是登录名和加密后的口令,后面的两个数是UID和GID,接着的 一项是系统管理员想写入的有关该用户的任何信息,最后两项是两个路径名: 一个是分配给用户的HOME目录,第二个是用户登录后将执行的shell(若为空格则 缺省为/bin/sh).   

　　(1)口令时效   

　　/etc/passwd文件的格式使系统管理员能要求用户定期地改变他们的口令. 在口令文件中可以看到,有些加密后的口令有逗号,逗号后有几个字符和一个 冒号.如:   

　　steve:xyDfccTrt180x,M.y8:0:0:admin:/:/bin/sh   

　　restrict:pomJk109Jky41,.1:0:0:admin:/:/bin/sh   

　　pat:xmotTVoyumjls:0:0:admin:/:/bin/sh   

　　可以看到,steve的口令逗号后有4个字符,restrict有2个,pat没有逗号.   

　　逗号后第一个字符是口令有效期的最大周数,第二个字符决定了用户再次 修改口信之前,原口令应使用的最小周数(这就防止了用户改了新口令后立刻 又改回成老口令).其余字符表明口令最新修改时间.   

　　要能读懂口令中逗号后的信息,必须首先知道如何用passwd\_esc计数,计 数的方法是:   

　　.=0 /=1 0-9=2-11 A-Z=12-37 a-z=38-63   

　　系统管理员必须将前两个字符放进/etc/passwd文件,以要求用户定期的 修改口令,另外两个字符当用户修改口令时,由passwd命令填入.   

　　注意:若想让用户修改口令,可在最后一次口令被修改时,放两个".",则下 一次用户登录时将被要求修改自己的口令.   

　　有两种特殊情况:   

　　. 最大周数(第一个字符)小于最小周数(第二个字符),则不允许用户修改 口令,仅超级用户可以修改用户的口令.   

　　. 第一个字符和第二个字符都是".",这时用户下次登录时被要求修改口 令,修改口令后,passwd命令将"."删除,此后再不会要求用户修改口令.   

　　(2)UID和GID   

　　/etc/passwd中UID信息很重要,系统使用UID而不是登录名区别用户.一般 来说,用户的UID应当是独一无二的,其他用户不应当有相同的UID数值.根据惯 例,从0到99的UID保留用作系统用户的UID(root,bin,uucp等).   

　　如果在/etc/passwd文件中有两个不同的入口项有相同的UID,则这两个用 户对相互的文件具有相同的存取权限.  

　　/etc /group文件含有关于小组的信息,/etc/passwd中的每个GID在本文件中 应当有相应的入口项,入口项中列出了小组名和小组中的用户.这样可方便地了 解每个小组的用户,否则必须根据GID在/etc/passwd文件中从头至尾地寻找同组 用户.   

　　/etc/group文件对小组的许可权限的控制并不是必要的,因为系统用UID,GID (取自/etc/passwd)决定文件存取权限,即使/etc/group文件不存在于系统中,具 有相同的GID用户也可以小组的存取许可权限共享文件.   

　　小组就像登录用户一样可以有口令.如果/etc/group文件入口项的第二个域 为非空,则将被认为是加密口令,newgrp命令将要求用户给出口令,然后将口令加 密,再与该域的加密口令比较.   

　　给 小组建立口令一般不是个好作法.第一,如果小组内共享文件,若有某人猜 着小组口令,则该组的所有用户的文件就可能泄漏;其次,管理小组口令很费事, 因为对于小组没有类似的passwd命令.可用/usr/lib/makekey生成一个口令写入 /etc/group.   

　　以下情况必须建立新组:   

　　(1)可能要增加新用户,该用户不属于任何一个现有的小组.   

　　(2)有的用户可能时常需要独自为一个小组.   

　　(3)有的用户可能有一个SGID程序,需要独自为一个小组.   

　　(4)有时可能要安装运行SGID的软件系统,该软件系统需要建立一个新组.   

　　要 增加一个新组,必须编辑该文件,为新组加一个入口项. 由于用户登录时,系统从/etc/passwd文件中取GID,而不是从/etc/group中 取GID,所以group文件和口令文件应当具有一致性.对于一个用户的小组,UID和 GID应当是相同的.多用户小组的GID应当不同于任何用户的UID,一般为5位数,这 样在查看/etc/passwd文件时,就可根据5位数据的GID识别多用户小组,这将减少 增加新组,新用户时可能产生的混淆





### linux系统默认用户
* abrt (**Automatic Bug Reporting Tool**)

https://access.redhat.com/documentation/en-us/red\_hat\_enterprise\_linux/6/html/deployment\_guide/ch-abrt

* avahi 
Avahi is a system which facilitates service discovery on a local network via the mDNS/DNS-SD protocol suite.
https://www.avahi.org/
* avahi-autoipd
"Dynamic Configuration of IPv4 Link-Local Addresses"

https://access.redhat.com/solutions/27324

* bin

The bin user account typically owns the executable files for most user commands. Has ownership of system files. Ii is not used for execution of  programs.  ***This account's primary purpose is to help distribute the ownership of important system directories and files so that everything is not owned solely by the root and sys user accounts.***

* daemon
* dbus
* ftp
* games
* gdm
* gopher
* haldaemon

https://access.redhat.com/solutions/33224

The Linux **Hardware Abstraction Layer (HAL)** implements a coherent description of all the hardware on a system, including hot-plugged devices such as on USB (Universal Serial Bus). It works by merging information presented by the operating system, the physical hardware, device drivers, subsystems such as CUPS (Common Unix Printing System), and even from system administrators. This information is collated and made available to application programs via a HAL programming interface.

The haldaemon service invokes the **HAL daemon** process hald to obtain the kernel-level data via the kernel D-BUS communication channel to collect and to distribute this information. Device data are presented in key+value attribute pairs.

One client of the HAL service is the fstab-sync(8) facility that automatically updates/etc/fstab in response to disk drives coming online. This just applies to CentOS/RHEL 4. fatab-sync facility has been disabled and new mount support on CentOS/RHEL 5 is introduced.

* halt
* lp
* mail
* nfsnobody

Securing NFS

By default, NFS shares change the root user to the nfsnobody user, an unprivileged user account. In this way, all root-created files are owned by nfsnobody, which prevents uploading of programs with the setuid bit set.

If no\_root\_squash is used, remote root users are able to change any file on the shared file system and leave trojaned applications for other users to inadvertently execute.

* pulse

heartbeating daemon for monitoring the health of cluster nodes

https://linux.die.net/man/8/pulse

* qpidd

https://access.redhat.com/solutions/108673
Qpidd is a messaging daemon based on upstream Apache Qpid [http://qpid.apache.org/](http://qpid.apache.org/).
Qpid is bundled with Red Hat Enterprise MRG and within Satellite6. If the package is installed, then the qpidd daemon is configured to start automatically. If it is not required, the package can be removed without affecting any other RHEL services.

* rtkit

https://access.redhat.com/documentation/en-us/red\_hat\_enterprise\_linux/6/html/6.4\_technical\_notes/rtkit

RealtimeKit is a D-Bus system service that changes the scheduling policy of user processes/threads to SCHED\_RR (that is, realtime scheduling mode) on request. It is intended to be used as a secure mechanism to allow real-time scheduling to be used by normal user processes.

* saslauth
sasl authentication server
https://linux.die.net/man/8/saslauthd
* sync
* usbmuxd

usbmuxd stands for "USB multiplexing daemon". To the user/developer what it actually does is to proxy requests over a USB cable on directly to a listening TCP port on the iPhone.  

Read more at: [https://www.commandlinux.com/man-page/man1/usbmuxd.1.html](https://www.commandlinux.com/man-page/man1/usbmuxd.1.html)

* uucp







---
https://access.redhat.com/documentation/en-us/red\_hat\_enterprise\_linux/5/html/deployment\_guide/s1-users-groups-standard-users

|User|UID|GID|Home Directory|Shell|
| ----- | ----- | ----- | ----- | ----- |
|root|0|0|/root|/bin/bash|
|bin|1|1|/bin|/sbin/nologin|
|daemon|2|2|/sbin|/sbin/nologin|
|adm|3|4|/var/adm|/sbin/nologin|
|lp|4|7|/var/spool/lpd|/sbin/nologin|
|sync|5|0|/sbin|/bin/sync|
|shutdown|6|0|/sbin|/sbin/shutdown|
|halt|7|0|/sbin|/sbin/halt|
|mail|8|12|/var/spool/mail|/sbin/nologin|
|news|9|13|/etc/news| |
|uucp|10|14|/var/spool/uucp|/sbin/nologin|
|operator|11|0|/root|/sbin/nologin|
|games|12|100|/usr/games|/sbin/nologin|
|gopher|13|30|/var/gopher|/sbin/nologin|
|ftp|14|50|/var/ftp|/sbin/nologin|
|nobody|99|99|/|/sbin/nologin|
|rpm|37|37|/var/lib/rpm|/sbin/nologin|
|vcsa|69|69|/dev|/sbin/nologin|
|dbus|81|81|/|/sbin/nologin|
|ntp|38|38|/etc/ntp|/sbin/nologin|
|canna|39|39|/var/lib/canna|/sbin/nologin|
|nscd|28|28|/|/sbin/nologin|
|rpc|32|32|/|/sbin/nologin|
|postfix|89|89|/var/spool/postfix|/sbin/nologin|
|mailman|41|41|/var/mailman|/sbin/nologin|
|named|25|25|/var/named|/bin/false|
|amanda|33|6|var/lib/amanda/|/bin/bash|
|postgres|26|26|/var/lib/pgsql|/bin/bash|
|exim|93|93|/var/spool/exim|/sbin/nologin|
|sshd|74|74|/var/empty/sshd|/sbin/nologin|
|rpcuser|29|29|/var/lib/nfs|/sbin/nologin|
|nsfnobody|65534|65534|/var/lib/nfs|/sbin/nologin|
|pvm|24|24|/usr/share/pvm3|/bin/bash|
|apache|48|48|/var/www|/sbin/nologin|
|xfs|43|43|/etc/X11/fs|/sbin/nologin|
|gdm|42|42|/var/gdm|/sbin/nologin|
|htt|100|101|/usr/lib/im|/sbin/nologin|
|mysql|27|27|/var/lib/mysql|/bin/bash|
|webalizer|67|67|/var/www/usage|/sbin/nologin|
|mailnull|47|47|/var/spool/mqueue|/sbin/nologin|
|smmsp|51|51|/var/spool/mqueue|/sbin/nologin|
|squid|23|23|/var/spool/squid|/sbin/nologin|
|ldap|55|55|/var/lib/ldap|/bin/false|
|netdump|34|34|/var/crash|/bin/bash|
|pcap|77|77|/var/arpwatch|/sbin/nologin|
|radiusd|95|95|/|/bin/false|
|radvd|75|75|/|/sbin/nologin|
|quagga|92|92|/var/run/quagga|/sbin/login|
|wnn|49|49|/var/lib/wnn|/sbin/nologin|
|dovecot|97|97|/usr/libexec/dovecot|/sbin/nologin|

