# zabbix面试题

## **zabbix开启自定义监控项**

在zabbix客户端的配置文件zabbix_agentd.conf中添加UserParameter=key command

在zabbix服务端使用zabbix_get测试是否能够自己定义的参数获取zabbix客户端的数据

在zabbix web界面上新建模板，将键值设置成userparameter值

## **zabbix怎么开启微信报警**

需要一个微信企业号（实名认证的微信号）

下载并配置微信公众平台的私有接口

配置zabbix报警，增加示警媒介类型，添加用户报警媒介，添加报警动作

## **zabbix监控了多少客户端，怎么安装的？**

使用命令分发生成秘钥并分发公钥

安装ansible软件

创建一个安装zabbix客户端的脚本

执行并验证

## **zabbix做过哪些监控？**

cpu的负载、内存的使用率、磁盘io的速率、磁盘容量

nginx（waiting，reading、writing、active、accept、handled、requests）

mysql

```text
## For Tribily's MySQL Extended Role
#grant process,super on *.* to 'monitor'@'localhost' identified by 'monitor';
#[client]
#default-character-set=utf8 
#host=localhost
#user='monitor'
#password='monitor'

#监控MySQL版本
UserParameter=mysql.version,mysql -V | cut -f6 -d" " | sed 's/,//'
#监控MySQL进程数
UserParameter=mysql.process,ps -ef|grep "mysql"|grep -v "grep"|wc -l
#监控MySQL运行状态
UserParameter=mysql.ping,/usr/bin/mysqladmin ping|grep alive|wc -l
#监控MySQL运行时间
UserParameter=mysql.uptime,/usr/bin/mysqladmin extended-status|grep Uptime|head -1|cut -f3 -d"|"
#监控MySQL的连接数
UserParameter=mysql.Threads.connected,/usr/bin/mysqladmin extended-status|grep Threads_connected|head -1|cut -f3 -d"|"
#监控MySQL活动状态的连接数
UserParameter=mysql.Threads.running,/usr/bin/mysqladmin extended-status|grep Threads_running|head -1|cut -f3 -d"|"
#监控MySQL客户端异常中断的数值
UserParameter=mysql.aborted.clients,/usr/bin/mysqladmin extended-status|grep Aborted_clients|head -1|cut -f3 -d"|"
#监控MySQL主从状态
UserParameter=mysql.Slave.status,/usr/bin/mysqladmin extended-status|grep Slave_runnin | grep -o ON |wc -l
#监控MySQL每秒钟获得的查询量
UserParameter=mysql.questions,/usr/bin/mysqladmin extended-status | grep Questions | head -1 | cut -f3 -d"|" 
#监控MySQL从数据文件里取数据的次数
UserParameter=mysql.read.rnd,/usr/bin/mysqladmin extended-status|grep Handler_read_rnd_next|head -1 | cut -f3 -d"|"  
#监控MySQL往数据文件里写数据的次数
UserParameter=mysql.write.rnd,/usr/bin/mysqladmin extended-status|grep Handler_write|head -1|cut -f3 -d"|" 
#监控MySQL内部COMMIT命令的个数
UserParameter=mysql.commit,/usr/bin/mysqladmin extended-status|grep Handler_commit| head -1 | cut -f3 -d"|" 
#监控MySQL请求从一张表中删除行的次数。
UserParameter=mysql.delete,/usr/bin/mysqladmin extended-status|grep Handler_delete| head -1 | cut -f3 -d"|" 
#监控MySQL表缓存没有命中的数量
UserParameter=mysql.Opened.tables,/usr/bin/mysqladmin extended-status|grep Opened_tables| head -1 | cut -f3 -d"|" 
#监控MySQL没有使用索引查询数量
UserParameter=mysql.slowqueries,/usr/bin/mysqladmin extended-status|grep Slow_queries|cut -f3 -d"|"
#监控MySQL执行全表搜索查询的数量
UserParameter=mysql.select.scan,/usr/bin/mysqladmin extended-status|grep Select_scan|cut -f3 -d"|"
```

redis

```text
vim /usr/local/zabbix/etc/zabbix_agentd.conf.d/redis.conf
UserParameter=Redis.Status,/usr/local/redis/bin/redis-cli -h 127.0.0.1 -p 6379 ping |grep -c PONG
UserParameter=Redis_conn[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "connected_clients" | awk -F':' '{print $2}'
UserParameter=Redis_rss_mem[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "used_memory_rss" | awk -F':' '{print $2}'
UserParameter=Redis_lua_mem[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "used_memory_lua" | awk -F':' '{print $2}'
UserParameter=Redis_cpu_sys[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "used_cpu_sys" | awk -F':' '{print $2}'
UserParameter=Redis_cpu_user[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "used_cpu_user" | awk -F':' '{print $2}'
UserParameter=Redis_cpu_sys_cline[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "used_cpu_sys_children" | awk -F':' '{print $2}'
UserParameter=Redis_cpu_user_cline[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "used_cpu_user_children" | awk -F':' '{print $2}'
UserParameter=Redis_keys_num[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep -w "$$1" | grep -w "keys" | grep db$3 | awk -F'=' '{print $2}' | awk -F',' '{print $1}'
UserParameter=Redis_loading[*],/usr/local/redis/bin/redis-cli -h $1 -p $2 info | grep loading | awk -F':' '{print $$2}'

Redis.Status --检测Redis运行状态， 返回整数
Redis_conn  --检测Redis成功连接数，返回整数
Redis_rss_mem  --检测Redis系统分配内存，返回整数
Redis_lua_mem  --检测Redis引擎消耗内存，返回整数
Redis_cpu_sys --检测Redis主程序核心CPU消耗率，返回整数
Redis_cpu_user --检测Redis主程序用户CPU消耗率，返回整数
Redis_cpu_sys_cline --检测Redis后台核心CPU消耗率，返回整数
Redis_cpu_user_cline --检测Redis后台用户CPU消耗率，返回整数
Redis_keys_num --检测库键值数，返回整数
Redis_loding --检测Redis持久化文件状态，返回整数
```

tomcat



讲解一下警告屏蔽

1、出于维护中的主机、告警信息还是会报出来，但是不会触发动作

**告警收敛:**

1)所有产生告警均由zabbix调用脚本推入缓存redis当中。

2)分析系统将在规定时间（1分钟）内去redis中拉取数据，根据定义好的一系列规则进行，合并、分析或直接丢弃，并存入分析平台数据库，以便供历史查询。

3)根据预先定义好的规则将报警通过定义好的方式发送给相关人员。





