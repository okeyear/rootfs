修改完 /etc/default/grub ， 需要执行 sudo update-grub 生成 /boot/grub/grub.cfg，重启后生效

update-grub 是ubuntu下的命令,脚本的实际命令为 grub-mkconfig -o /boot/grub/grub.cfg
centos上没这个命令, 因此直接执行 grub-mkconfig -o /boot/grub/grub.cfg 

>   esr分区 ... pending


通过以下两个命令中的任意一个 可以知道，GRUB2被安装在了哪个分区：
- Device: sudo grub-probe -t device /boot/grub
- UUID: sudo grub-probe -t fs_uuid /boot/grub