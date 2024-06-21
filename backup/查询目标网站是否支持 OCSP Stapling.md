




如何寻找 TLS1.3/H2 的网站
目标网站最低标准：国外网站，支持 TLSv1.3 与 H2，域名非跳转用

加分项：IP 相近（更像，且延迟低），Server Hello 后的握手消息一起加密（如 dl.google.com），有 OCSP Stapling

配置加分项：禁回国流量，TCP/80、UDP/443 也转发（REALITY 对外表现即为端口转发，目标 IP 冷门或许更好）

可以使用这个网站：[点击访问](https://www.ssllabs.com/projects/index.html) ，来寻找 TLS1.3 / X25519 / H2 的指向站点
也可以使用这个网站：[点击访问](http://web.chacuo.net/netocspstapling) ，来查询目标网站是否支持 OCSP Stapling （加分项而已，不支持也没有太大关系）

