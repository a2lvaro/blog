
Published [2023年11月25日](https://www.xiaocaicai.com/2023/11/) by [admin](https://www.xiaocaicai.com/author/admin/)
最近搞了台软路由，在刷完 Open­Wrt 固件后发现根目录只有几百 M，浪费了大量的硬盘空间。而且仅仅几百 M 的系统分区也远远不够用，随着后期系统日志和软件包等增加，估计很快系统空间就不够用了。

在网上找了挺多关于扩容的教程，大多都是新建分区后挂载到 /over­lay，然后通过软链接或者指定软件安装目录的方式来使用新分区，这种方案一点都不 “优雅”。

下面的教程实现直接将系统的空闲空间扩容到 /

我的环境
自编译L大的 OpenWrt 固件（截止2021-03-19最新源码）
x86_64
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

分区名以cfdisk中显示的为准
分区名以cfdisk中显示的为准
挂载分区
在 Luci 面板中操作：系统 –> 挂载点 –> 生成配置：

生成配置
生成配置
然后往下拉，在 “挂载点” 中找到刚才新建的分区，点击修改：

修改 sda3 的挂载点
修改 sda3 的挂载点
在 “启动此挂载点” 后面打上勾，“挂载点” 中选择 “作为根文件系统使用”，然后复制最后列出的几条命令备用，保存并应用。

按照图中进行修改
按照图中进行修改
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

大功告成
大功告成
大功告成！