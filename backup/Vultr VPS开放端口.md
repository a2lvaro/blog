


使用Vultr遇到的问题，创建了一个VPS后，系统是Debian, 并在控制台设置了Firewall Group开放HTTP之后，还是无法访问80端口。

网上说是Firewall或者iptables的问题，确实是有可能。但是VPS根本就没有Firewall和iptables。


后来发现系统中有个叫ufw(Uncomplicated firewall)。 下面是ufw的一些操作命令。

启用ufw
```
ufw enable
```

禁用ufw
```
ufw disable
```

开放80端口
```
ufw allow 80
```

禁用80端口
```
ufw deny 80
```

查看配置规则以及状态
```
ufw status
```

操作步骤：
首先通过 ufw disable禁用ufw，先测试是不是防火墙拦截了访问，如果没问题可以恢复防火墙ufw enable。
如果是防火墙拦截了端口, 开放端口ufw allow 80即可。
