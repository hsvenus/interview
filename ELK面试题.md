# ELK面试

相关服务的服务器上安装filebeat，通过filebeat配置文件中filebeat.input指定需要收集的日志路径进行数据采集，可以使用processors处理器进行简单的过滤比如dorp_fields，然后经在output.logstash中host配置ip和port的将数据推送到logstash，logstash中的input中的beats监听自己的port（5044）并接受数据的推送，然后经过filter利用grok插件中的match进行字段相应匹配，在经过mutate过滤比如字段的重命名和删除，通过output的elasticsearch字段推送到es集群，es集群会自己自动建立索引，也可以利用logstash推送的过程中在elasticsearch字段中通过if判断用index字段创建索引，之后用kibana配置文件中的elasticsearch.hosts字段进行配置连接es集群，进行可视化展示。

filebeat 和 logstash的比较

logstash采用java编写占用资源比较大插件是ruby编写，filebeat使用go语言开发 轻量级，两者都能进行日志采集

但logstash有丰富的插件。

es数据：index、type、document、filed、mapping



















