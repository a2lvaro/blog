下面的教程实现直接将系统的空闲空间扩容

我的环境
自编译L大的 OpenWrt 固件x86_64
系统分区为 ext4 格式（如果你用的只读文件系统，不用往后看了）
固件安装在 SSD 硬盘
扩容步骤
新建分区
先安装 cfdisk：

opkg install cfdisk

执行 cfdisk，上下键选择分区，左右键选择功能。先选择 “Free space”，然后选择 “New” 功能，设置新分区的大小（默认占用所有空闲空间）。

记住新分区的名称，我这里是 /dev/sda3。后面的操作中，把 sda3 改成你自己的分区名。

将新分区格式化：

mkfs.ext4 /dev/sda3

注意：记住新建的分区在 cfdisk 中的名字，如果你的存储设备不是普通的固态硬盘，那设备名就不是/dev/sdx。
后续所有操作（包括生成的几条命令），都一定要注意把/dev/sda3替换成你自己的分区名。

挂载分区
在 Luci 面板中操作：系统 –> 挂载点 –> 生成配置：

然后往下拉，在 “挂载点” 中找到刚才新建的分区，点击修改：

在 “启动此挂载点” 后面打上勾，“挂载点” 中选择 “作为根文件系统使用”，然后复制最后列出的几条命令备用，保存并应用。

移动文件
通过 SSH 连接 Open­Wrt，执行刚才复制出来的命令，我这里是这样的：

cd /
mkdir -p /tmp/introot
mkdir -p /tmp/extroot
mount --bind / /tmp/introot
mount /dev/sda3 /tmp/extroot # 你的可能不是 sda3，具体名称以cfdisk为准，就是新建的分区
tar -C /tmp/introot -cvf - . | tar -C /tmp/extroot -xf -
umount /tmp/introot
umount /tmp/extroot
执行完不报错的话，重启一下：reboot

验证
SSH 连接，执行 df -h

大功告成！