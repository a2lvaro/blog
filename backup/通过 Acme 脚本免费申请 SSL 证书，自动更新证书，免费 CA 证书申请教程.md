

1. 安装依赖软件
```
apt update
apt upgrade -y
apt install socat curl -y
```
2. 安装 acme 脚本
```
curl https://get.acme.sh | sh
```
3. 注册账号
替换 your@email.com为你的邮箱

```
~/.acme.sh/acme.sh --register-account -m your@email.com
```
4.1 80 端口验证申请
如果你的 80 端口是空闲，也就是没有运行任何 Web 服务，那么 acme.sh 可以假装自己是一个 Web 服务，临时监听 80 端口，从而完成验证

```
~/.acme.sh/acme.sh --issue -d your-domain.com --standalone
```
4.2 Nginx 验证申请
如果你的服务器已经部署了 Nginx 服务，并且域名已经在 Nginx 中进行了配置，那么可以通过验证 Nginx 的方式申请证书

```
~/.acme.sh/acme.sh --issue -d your-domain.com --nginx
```
4.3 http 验证申请
如果你的服务器已经部署了网站环境。acme 会自动在网站的根目录下放置一个文件，通过访问该文件来验证域名的所有权。注意替换路径为你的网站根目录的绝对路径

```
~/.acme.sh/acme.sh --issue -d your-domain.com -d www.your-domain.com --webroot /home/wwwroot/your-domain.com/
```
5. 安装证书到指定文件夹
acme 默认生成的证书都保存在安装目录 .acme.sh 中，执行下面的命令将证书安装到指定目录

```
~/.acme.sh/acme.sh --installcert -d your-domain.com --key-file /root/cert/private.key --fullchain-file /root/cert/cert.crt
```
6. 更新证书
acme 会在证书到期时自动更新，无需任何操作

7. 更新 acme 脚本
更新到最新版本：

```
~/.acme.sh/acme.sh --upgrade
```
也可以开启自动更新：

```
~/.acme.sh/acme.sh --upgrade --auto-upgrade```