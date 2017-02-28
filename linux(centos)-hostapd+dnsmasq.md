# linux(centos)软AP无线上网


## 安装hostapd



```
tar -zxvf hostapd-1.1.tar.gz
```
进入解压后的目录

```
~/hostapd-1.1/hostapd （～更换为你解压后hostapd所在的地方）
cp defconfig .config
```


```
make #编译
make install #安装
```


完成以后会提示安装到了/usr/local/bin目录下，将~/hostapd-1.1/hostapd下面的hostapd.conf拷贝到/usr/local/bin目录下

```
cp ~/hostapd-1.1/hostapd/hostapd.conf /usr/local/bin
```


```
vim /usr/local/bin/hostapd.conf
```

配置见文件夹中hostapd.conf

```
/usr/local/bin/hostapd hostapd.conf
```
无线启动但没有网络，缺少数据转发


## 安装dnsmasq（默认安装，可直接修改配置文件）
DNSmasq是一个小巧且方便地用于配置DNS和DHCP的工具，适用于小型网络。
它提供了DNS功能和可选择的DHCP功能。
当前环境默认已安装运行dnsmasq，这里需要先把它杀掉，然后升级一下。

ps：一般状况下这里可忽略


```
ps -e | grep dnsmasq  
sudo killall dnsmasq  
sudo apt-get install dnsmasq  
ping www.163.com  
sudo reboot
```


配置DHCP服务器


```
vim /etc/dnsmasq.conf 
```
可按照如下配置，或参考文件夹中dnsmasq.conf
```
interface=wlan0
bind-interfaces
except-interface=lo
dhcp-range=172.168.128.50,172.168.128.150,24h
dhcp-option=3,172.168.128.1
#dhcp-option必须和接下来命令行中ifconfig wlan0 172.168.128.1 netmask 255.255.255.0 对应

#dhcp-range=192.168.0.100,192.168.0.200,24h  
#dhcp-option=3,192.168.0.1
#listen-address=192.168.128.6
#no-dhcp-interface=
```



其中
dhcp-range=172.168.128.100,172.168.128.200,24h #设置dhcp地址范围，即租借时间24小时
dhcp-option=3,192.168.0.1 #为手机配置网关
## **注意！！！**
无线网卡配置IP和网段一定不要和本地网卡网段相同，会出现本机无法连接相同网段内的服务器的情况


配置路由上网

/usr/local/bin/路径下,输入如下命令：
```
/etc/init.d/dnsmasq restart
ifconfig wlan0 172.168.128.1 netmask 255.255.255.0 
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf  
echo 1 > /proc/sys/net/ipv4/ip_forward  
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE 
hostapd hostapd.conf

````
为了方便也可将文件夹中wifi.ssh 放置/usr/local/bin/下
直接脚本启动

```
bash wifi.ssh
```




















