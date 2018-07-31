#操作系统安装及初始化规范



>    V1.0



##操作系统安装流程

1. 服务器采购
2. 服务器验收并设置raid
3. 服务商得供验收单，运维验收负责人签字。
4. 服务器上架
5. 资产录入
6. 开始自动化安装。
7. 将新服务器划入装机vlan
8. 根据资产清单上的mac地址，自定义安装
  * 机房
  * 机房区域
  * 机柜
  * 服务器位置 
  * 服务器网线接扩端口
  * 该端口mac地址
  * profile 操作系统 分区等 预分配的ip地址 主机名 子网 网关 dns 角色。

3.自动化装机平台，安装	
					   
  * 192.168.56.12
  * 主机名：linux-node2.oldboyedu.com
  * 掩码：255.255.255.0
  * 网关：192.168.56.2
  * DNS：192.168.56.2

<pre>
cobbler system add --name=linux-node2.oldboyedu.com --mac=00:50:56:3B:1F:CE --profile=CentOS-7.2-x86_64 \
--ip-address=192.168.56.12 --subnet=255.255.255.0 --gateway=192.168.56.2 --interface=eth0 \
--static=1 --hostname=linux-node2.oldboyedu.com --name-servers="192.168.56.2" \
--kickstart=/var/lib/cobbler/kickstart/CentOS-7-x86_64.ks
</pre>





##系统初始化规范
   1. 当前我公司使用操作系统为CentOS 6和 CentOS 7 ，均使用x86_64位系统，需要使用公司cobbler进行自动化安装，禁止自定义设置。
   2. 版本选择，数据库统一使用Cobbler上CentOS-7-DB这个专用的profile,其他web应用统一使用Cobbler上CentOS-7-Web。
   

###初始化
* 设置DNS 192.168.56.111 192.168.56.112
* 安装Zabbix Agent: Zabbix Server: 192.168.56.11
* 安装Saltstack Minion: Saltstack Master:192.168.56.11
* history记录时间
     <pre>
     export HISTTIMEFORMAT="%F %T `whoami`"
     </pre>
* 日志操作记录
     <pre>
     export PROMPT_COMMAND='(history 1| {read x y;echo $y;});logger"[euid=$(whoami)]":$(who am i):[`pwd`]"$msg";}'
     </pre>
* 内核参数优化 
* yum仓库
* 主机名解析

###目录规范

* 脚本放置目录：  /opt/shell
* 脚本日志目录：  /opt/shell/log
* 脚本锁文件目录： /opt/shell/lock

###服务安装规范

1.源码安装路径 /usr/local/appname.version
2.创建软链接  ln -s /usr/local/appname.version /usr/local/appname



###主机名命名规范

    ** 机房名称--项目--角色--地域--集群--节点.域名 **
例子 ：

     idc01-xxshop-nginx-bj-node1.shop.com
     注：dns不支持下划线解析

###服务启动用户规范

     所有服务。统一使用www用户，uid为666，除负载均衡需要监听80端口使用root启动外，所有服务必须使用www用户启动，使用大约1024的端口。