## Network connection of Windows

### DNS Configuration
DNS 在 Windows 中的配置，可以通过 `ipconfig /all` 命令查看当前的 DNS 配置。

DNS 的配置文件在 `C:\Windows\System32\drivers\etc\hosts`，可以通过编辑这个文件来配置 DNS。

DNS 服务
``` bash
阿里云公共DNS 223.5.5.5\223.6.6.6
DoH（DNS over HTTPS）：
https://dns.alidns.com/dns-query
IPv6：2400:3200::1 和 2400:3200:baba::1

腾讯DNSPod公共DNS 119.29.29.29\182.254.116.116（备用）
https://doh.dnspod.cn/dns-query

中国电信 114 DNS 114.114.114.114\114.114.115.115
https://doh.114dns.com/dns-query
IPv6：240c::6644 和 240c::6645

Google Public DNS 8.8.8.8\8.8.4.4
https://dns.google/dns-query
IPv6：2001:4860:4860::8888

Cloudflare Public DNS 1.1.1.1\1.0.0.1
https://cloudflare-dns.com/dns-query
IPv6：2606:4700:4700::1111
安全过滤地址（阻止恶意内容）：https://security.cloudflare-dns.com/dns-query
家庭过滤地址（阻止成人内容）：https://family.cloudflare-dns.com/dns-query

Quad9 Public DNS 9.9.9.9\149.112.112.112
https://dns.quad9.net/dns-query
2620:fe::fe
```


### Set DNS in Windows
1. 打开网络和Internet设置 
2. 选择更改适配器选项
3. 右键单击网络连接，选择属性
4. 选择Internet协议版本4（TCP/IPv4）或Internet协议版本6（TCP/IPv6） 设置DNS服务器地址/配置DoH

验证DoH是否启用成功
访问 https://dns.google 或 https://cloudflare-dns.com/help。检查页面是否显示您的查询已通过DoH完成。如果显示“已启用DoH”，说明配置成功。


### Network Commands
- ipconfig 
``` bash
# 查看网络连接
ipconfig /all MAC地址（物理地址）/DHCP状态（是否启用）/DNS服务器地址/自动配置状态等。

清除本地DNS缓存 ipconfig /flushdns

释放当前IP地址 ipconfig /release 向DHCP服务器请求释放当前分配的IP地址。

更新DHCP服务器分配的IP地址 ipconfig /renew 向DHCP服务器请求更新IP地址。

显示TCP/IP统计数据 ipconfig /displaydns 显示DNS解析缓存。显示Windows本地存储的DNS缓存记录，包括最近解析过的域名和对应的IP地址。

重置所有适配器的设置（包括DNS和网关） ipconfig /reset

```

- nslookup
``` bash
nslookup [域名] 命令用于查询DNS服务器，获取域名对应的IP地址。

nslookup [域名] [DNS服务器地址] 通过指定DNS服务器查询域名对应的IP地址。

nslookup [IP地址] 通过IP地址查询对应的域名。

nslookup 进入交互模式，可以连续查询多个域名。
set type=MX   # 查询邮件服务器记录（MX记录）
set type=NS   # 查询权威名称服务器记录（NS记录）
set type=A    # 查询IPv4地址记录（默认）
set type=AAAA # 查询IPv6地址记录
set type=PTR  # 查询反向解析记录
set type=CNAME # 查询别名记录
set type=SOA  # 查询区域起始授权记录
set type=SRV  # 查询服务位置记录
set type=ANY  # 查询所有记录
server [DNS服务器地址]  # 指定DNS服务器
```

PowerShell 中的操作
``` bash
获取网络适配器的 DNS 配置 
Get-DnsClientServerAddress | Format-Table -AutoSize
设置静态 DNS 服务器
Set-DnsClientServerAddress -InterfaceAlias "Wi-Fi" -ServerAddresses ("8.8.8.8","8.8.4.4")
恢复自动获取 DNS 配置
Set-DnsClientServerAddress -InterfaceAlias "Wi-Fi" -ResetServerAddresses
```

netsh 命令中的 DNS 设置

tracert 命令
（在Linux系统中对应的命令是 traceroute）是一个网络诊断工具，用于追踪从本地计算机到目标地址之间经过的路由路径。它会显示数据包从源到目的地经过的每一跳（路由器或网关）信息，包括延迟和IP地址。

tracert 使用 ICMP（Internet Control Message Protocol，通常是“回显请求”）或者UDP协议发送小数据包，并逐步增加数据包的TTL（生存时间，Time To Live）值。
每当TTL值减少到0时，路由器会丢弃该数据包并返回一个ICMP超时消息，从而记录该路由器的信息。
最终，当数据包到达目标地址时，目标主机会返回响应信息，从而结束追踪。

``` bash
tracert [选项] <目标地址> [数据包大小] [最大跃点数] [源地址] [超时时间]
tracert -h <跃点数> <目标地址>  # 设置最大跃点数   tracert -h 10 www.google.com
tracert -w <超时时间> <目标地址>  # 设置超时时间   tracert -w 100 www.google.com
tracert -d <目标地址> # 禁用反向DNS解析
tracert -4 <目标地址> # 强制使用IPv4
tracert -6 <目标地址> # 强制使用IPv6
tracert -j <主机列表> <目标地址> # 设置跃点列表
tracert -R <目标地址> # 启用路由跟踪

```

分析CDN分布
测试跨境连接质量
检查网络连接问题

ping 命令介绍
ping 命令用于测试网络连接，检查主机之间的连通性。它通过发送ICMP（Internet Control Message Protocol）回显请求数据包到目标主机，并等待目标主机返回响应信息，从而测量网络延迟和丢包率。
``` bash
ping [选项] <目标地址>
ping -n <次数> <目标地址>

ping -a <目标地址> # 使用主机名代替IP地址`
ping -t <目标地址> # 持续发送数据包，直到手动停止

```

检测网络延迟和丢包率 ping -n 50 www.example.com 
持续监控网络稳定性 ping -t www.google.com 
测试外部互联网连接 ping 8.8.8.8 
测试本地网络是否正常 ping 192.168.1.1 


### Wireshark
Wireshark 是一个网络封包分析软件，可以实时监控网络数据包的传输过程，捕获和分析网络数据包，帮助用户诊断网络问题。
