# Zabbix-Update
Zabbix升级文档  2.X到3.X升级文档 
官方文档https://www.zabbix.com/documentation/3.0/manual/installation/upgrade   
Server upgrade process

1 停止Zabbix server
```
shell#service zabbix-server  stop
shell#ps -ef |grep zabbix_server #确保zabbix server进程已经停止，数据库不再有任何写入
```

2 备份Zabbix的数据库    
可以不用备份历史数据，用脚本进行备份:    
https://github.com/itnihao/zabbix-book/blob/master/03-chapter/Zabbix_MySQLdump_per_table_v2.sh  
备份的目的是为了防止升级失败回退   

3备份配置文件, PHP文件Zabbix server二进制文件    

cp  $(wihch zabbix_server)  $(wihch zabbix_server)_bak  

4 安装新的zabbix server二进制文件   

使用rpm或者是源码编译Zabbix server.  
4.1  rpm包安装
官方默认只提供centos7的rpm包，如需要使用centos6，见https://github.com/zabbixcn/zabbix3.0-rpm 
rpm2cpio zabbix-server-mysql-3.0.0-1.el6.x86_64.rpm|cpio -div  
cp ./usr/sbin/zabbix_server /usr/sbin/zabbix_server 

4.2源码安装
注意：curl版本必须大于等于7.20，centos6为7.19，用此项目提供的curl包升级   
https://github.com/zabbixcn/curl-rpm   
步骤      
```
configure  
make 
cp  src/zabbix_server/zabbix_server $(wihch zabbix_server) 
```

5 zabbix_server.conf配置文件修改   

由于zabbix 3.0新增了一些参数，故zabbix_server.conf应根据需要重新设置.   

6 开启Zabbix server进程   
启动进程  
```
server  zabbix_server start
tail -f /var/log/zabbix_server.log
```
zabbix_server会自动更改原来的数据库，进行数据库新的表创建或删除   

确保用户有足够权限(create table, drop table, create index, drop index)  

7 安装新的Zabbix web前端   

PHP版本要求大于等于5.4.0.   
