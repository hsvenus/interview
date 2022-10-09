# mysql调优

1、设置innodb_buffer_pool_size大小，可以从innodb_buffer_pool_pages_free查看内存使用情况，保证在内存中读取数据，将数据保存到内存中，减少io操作,专用mysql服务器禁用swap

2、定期优化重新构建数据库，ibdata1会随着时间推移，会越来越大，所以需要重新构建数据库

3、使用足够大的写缓存innodb_log_file_size,推荐大小设置为innodb_buffer_pool_size 0.25倍

如果应用 不涉及很高的安全性或者基础架构足够安全或者事务都很小，都可以用innodb_flush_log_at_trx_commit=0或者2来降低磁盘的操作

4、分析查询日志和慢查询日志

long_query_time=1

log-slow-queries=/var/log/mysql/log-slow-queries.log

5、使用内存磁盘、不用 MYSQL 内置的函数，因为内置函数不会建立查询缓存、充分使用索引、使用 IP 而不是域名做数据库路径，避免 DNS 解析问题、事务切割



