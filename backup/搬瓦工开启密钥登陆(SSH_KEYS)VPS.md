**### 搬瓦工开启密钥登陆(SSH_KEYS)VPS**


自从搬瓦工对控制面板进行了改版设计后，就支持了SSH_KEYS密钥登陆的功能，虽然这个功能一直都是可以自我安装使用的，但是当前放在了更明显的位置，还是希望更多的用户能够使用这个更安全的方式维护VPS。

本文以debian10 作为演示配置系统，分为2种安装使用密钥：一 全新系统安装密钥；二 正在使用的系统安装密钥。

根据搬瓦工的使用说明这个密钥会在下一次重装时自动复制到/root/.ssh/authorized_keys这里，全新的系统安装可以参考这里，如果你有系统软件在运行，请参考第二部分。

密钥的生成可以使用命令行生成和puttygen生成，第一步将介绍使用puttygen生成密钥，命令行生产密钥请参考第二部分。

一 全新安装密钥
需要使用的工具：[putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)，[winscp](https://winscp.net/eng/download.php)，[puttygen](https://the.earth.li/~sgtatham/putty/latest/w64/puttygen.exe)

将SSH密钥添加到控制面板

下载并运行PuTTYgen。
点击“Generate”按钮,动动鼠标就能生成。
为了提高安全性，请输入密钥密码。这是使用SSH密钥所必需的，并且将阻止有权访问您的密钥文件的人使用该密钥。Key passphrase 这里输入密码。
生成密钥后，单击“Save Private Key”。确保将其保存在安全的地方，因为丢失该文件将无法恢复
在“Public key for pasting into OpenSSH authorized_keys file”中选择所有文本。这是您需要进入搬瓦工控制面板以使用SSH密钥
复制所有的SSH authorized_keys中的字符，保存到搬瓦工的页面中.


搬瓦工SSH_KEYS

然后选择Main controls 这里选择  stop 当前运行的服务器，点击左侧的Install new OS,选择debian 10x86_64 同意选项打勾，再Reload，完成系统重装。


搬瓦工重装系统后的密码和密钥

等待3-5分钟后我们就可以打开winSCP,首先使用系统重装给的密码和端口号登录服务器。查看authorized_keys这个文件，比较下是否是我们前面创建的密钥字符。

使用putty 运行一下命令：

vi /etc/ssh/sshd_config
找到下面相似2行的，并将内容修改为下面的内容

PubkeyAuthentication yes     1.去掉前面的#
 
PasswordAuthentication no  2.去掉前面的#号，并将yes 修改为no

搬瓦工密钥修改说明

对于不熟悉编辑命令的，可以使用winscp编辑这里，只要找到对应的路径,编辑文件即可，搬瓦工的上述文件编辑在37行和56行。


winscp修改sshd_config

按照图示说明修改后保存。再次输入以下命令，就将仅使用密钥登陆了。

service sshd restart
二 正在使用的系统安装密钥
进行这一步如果你对自己的操作不放心，那么建议你使用搬瓦工的免费备份服务先备份下。

[搬瓦工VPS切换机房及数据快照备份](https://www.bawagon.com/changelocation/)

首先用正确的密码并使用putty登陆你的VPS，然后执行以下命令：

运行：ssh-keygen -t rsa。要获得更安全的4096位密钥，请运行：ssh-keygen -t rsa -b 4096
在询问您要将密钥保存在何处时，按Enter键（这将使用默认位置）。
输入密钥密码。
运行cat ~/.ssh/id_rsa.pub  这将为您提供正确格式的密钥，以粘贴到控制面板中。
确保备份~/.ssh/id_rsa文件。如果丢失，将无法恢复。


现在，在 root 用户的家目录中生成了一个 .ssh 的隐藏目录，内含两个密钥文件。id_rsa 为私钥，id_rsa.pub 为公钥。使用winscp登陆服务器，并将id_rsa这个文件下载下来。

现在我们在服务器上安装公钥，putty里依次使用下面的命令

cd .ssh
cat id_rsa.pub  authorized_keys
这里我们看到出现的authorized_keys文件其实和搬瓦工生成的文件名是一样的。因此再第一部分的的内容步骤可以简化为产生密钥后，直接复制密钥文件到这个authorized_keys里保存。以下的命令和第一部分的一样。

为文件赋予正确的权限，其实默认系统也设置了这样的权限。

chmod 600 authorized_keys
chmod 700 ~/.ssh
编辑 vi /etc/ssh/sshd_config 文件，进行如下设置： 找到下面相似2行的，并将内容修改为下面的内容

PubkeyAuthentication yes     1.去掉前面的#
 
PasswordAuthentication no  2.去掉前面的#号，并将yes 修改为no

搬瓦工密钥修改说明

对于不熟悉编辑命令的，可以使用winscp编辑这里，只要找到对应的路径,编辑文件即可，搬瓦工的上述文件编辑在37行和56行。

按照图示说明修改后保存。再次输入以下命令，就将仅使用密钥登陆了。

service sshd restart
三 使用密钥登陆服务器
现在使用 PuTTY 登录VPS，在正常的填下IP和端口后，一次选择左侧的 Connection -> SSH -> Auth 中的 Private key file for authentication: 处选择你的私钥文件，然后即可登录了。如果你设置了密钥密码，也只需输入密钥锁码即可。



winscp使用密钥登陆是在常规输入IP和端口号后，选择高级--SSH--验证--密钥文件登陆即可。