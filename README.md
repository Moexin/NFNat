# NFNat
一个读取配置文件快捷添加nftables规则的端口转发工具

使用方法：

1. 安装nftables（关闭并卸载iptables/firewalld等类似防火墙工具）

   ```
   apt install -y nftables // Debian/Ubuntu
   yum install -y nftables // CentOS
   ```
2. 打开IPv4转发

   ```
   echo -e "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf && sysctl -p
   ```
   可能需要重启生效

2. 新建一个配置文件`nfnat.yml`

   ```yml
   rules:
     - srcPort: 8081 #源端口
       dstAddr: 2.2.2.2 #目标地址(支持域名)
       dstPort: 8082 #目标端口
       protocol: both #可选 tcp udp both
     - srcAddr: 3.3.3.3 #源地址，如果不知道填什么就把这一项删了，程序自动获取
       srcPort: 8081-8089 #源端口段
       dstAddr: 4.4.4.4 #目标地址(支持域名)
       dstPort: 8081-8089 #目标端口段
       protocol: tcp #可选 tcp udp both
   ```

3. 执行`./nfnat -w -s nfnat.yml`
   监听配置文件的变化实时更新NAT规则，同时每隔一分钟会更新域名所对应的IP
   也可执行`./nfnat -g -s nfnat.yml`，生成转发规则并输出到终端