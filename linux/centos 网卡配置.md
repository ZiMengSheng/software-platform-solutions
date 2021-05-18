1）编辑网卡配置文件：vi /etc/sysconfig/network-scripts/ifcfg-eth0，其中eth0是网卡设备别名，代表第一块网卡，如果要编辑其它网卡的配置文件，可能就要把这个改为eth1,eth2,eth3....

2）按照下图红色框中的提示修改配置文件，ONBOOT=yes是指系统启动时激活此设备，BOOTPROTO=dhcp是指网卡的获取IP方式为dhcp，如果想要改成静态获取的，可以把这个值设为static

