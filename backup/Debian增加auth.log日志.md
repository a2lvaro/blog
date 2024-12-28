
 
Debian默认没有auth.log文件的，auth.log日志文件，记录了ssh登录的一些信息，里面包括ip地址、端口号、用户名等信息，如下一条记录日志。


Dec  8 09:26:01 debian sshd[854]: Disconnected from user root 192.168.0.25 port 56766

一，新建auth.log文件，在ssh终端里输入以下命令，新建一个auth.log日志文件。日志文件的目录一般是/var/lo

sudo touch /var/log/auth.log

二，安装rsyslog

sudo apt-get install rsyslog

三，设置auth.log文件

sudo nano /etc/syslog.conf

在/etc/syslog.conf里添加一行

auth,authpriv.*         -/var/log/auth.log

root重启一下主机。


这样就可以在/var/log/auth.log里面看到ssh的登录记录了，包含正确的登录和错误的登录，对于错误的登录，一般是暴力破解或随机扫描的黑客，可以对不断尝试扫描的ip进行黑名单处理。


四，查看错误的登录

$ sudo grep "Failed password" /var/log/auth.log
Dec  8 09:26:07 debian sshd[888]: Failed password for root from 192.168.0.25 port 56910 ssh2
Dec  8 09:26:09 debian sshd[890]: Failed password for root from 192.168.0.25 port 56913 ssh2
Dec  8 09:26:10 debian sshd[892]: Failed password for root from 192.168.0.25 port 56915 ssh2
Dec  8 09:26:10 debian sshd[894]: Failed password for root from 192.168.0.25 port 56916 ssh2
从上面我看可以看到，192.168.0.25这个ip有四次错误的密码登录，当然我这个只是在测试，故意输错密码的，可以把这些ip写进黑名单。



五，把错误ip写入黑名单


echo "sshd:192.168.0.25" | sudo tee -a /etc/hosts.deny
将192.168.0.25写入hosts.deny里面，即黑名单



sudo ufw insert 1 deny from "192.168.0.25" &> /dev/null
防火墙将这个ip数据丢掉。


如果有大量的ip不断的暴力破解登录，可以参考下面的脚本

https://gitee.com/yanjun-pro/shell_script/tree/master/ssh_login_failed_host_deny