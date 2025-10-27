# Mesos

### 日志


服务生成的日志文件可以和服务本身一样重要。当问题产生时，详细可用的日志文件对故障的处理是非常重要的。



### 定位和解析日志文件





### 服务的日志文件


关于管理日志，Mesos给系统管理员提供了很大的灵活性。事件可以写到磁盘上Mesos管理的日志文件中，或者是系统日志（syslog）中。Mesos的安装包默认的配置确保Mesos master和slave服务将日志发送给syslog，以便可以被日志管理服务如Logstash或Splunk进行收集和分析。
Mesos master和slave都可以使用–log_dir来配置日志文件的存储位置。通常设置为`/var/log/mesos`，但也可以省略该配置使日志发送到standard error（标准错误输出）位置。如果Mesos为你管理日志文件，它将根据文件的大小来进行自动轮换，不过你应该确保定期删除这些旧的文件。



### 任务的日志文件


在每个任务的sandbox中，Mesos提供了两个默认的日志文件：stdout和stderr。这允许管理员通过web界面查看任务或者命令的控制台输出，而不用实际登录到运行此任务的节点上去查看。这些日志文件和其它任务需要的文件一起存储在任务的work_dir，它是通过slave的`--work_dir`来配置的。如果slave的work_dir被配置为/var/lib/mesos，则到任务的sandbox的路径类似于下面这样：
`/var/lib/mesos/slaves└── <slave-id>/    └── frameworks/        └── <framework-id>/            └── executors/                └── <task-name>/                    └── runs/                        ├── <run-id>/                        │   ├── stderr                        │   └── stdout                        └── latest`与Mesos服务的日志文件不同的是，当Mesos slave在旧的sandbox目录里进行垃圾收集时，这些文件自动清除。垃圾收集默认是一周一次，但根据可用的磁盘空间大小可能更频繁的进行。



### 配置日志文件


Mesos提供了很多配置选项用来配置日志，这些选项包括：
• log_dir—日志文件在磁盘上的存储路径。如果没有指定，将不会向磁盘写入，而将事件发送到stderr。该选项可以用在master和slave上，没有默认值。
• logging_level—只记录此级别（或高于）的日志信息。可能的值包括（级别依次递增）：INFO, WARNING, 和ERROR。此选项可以用于master和slave，默认值为INFO。
• work_dir—当在slave上配置时，此选项指定某框架的工作目录（sandbox），它包含任务的stdout和stderr日志文件。默认值为/tmp/mesos。
• external_log_file—指定到外部管理的日志文件的路径，用来再web界面上显示和HTTP API。可以用于master和slave上，没有默认值。
• quiet—当指定后，没有日志输出到stderr。可以用于master和slave上，此配置默认不指定。
• logbufsecs—在日志写入磁盘前，缓存日志信息的秒数。可以用于master和slave上，默认值为0（立即将日志信息写入磁盘）。
搭建日志基础设施，可以使用Elasticsearch, Logstash, and Kibana (ELK) stack，用来收集、分析和索引日志条目，方便将日志条目以数据的方式进行搜索和可视化。使用上面这些选项可以使Mesos的日志和您的日志基础设施相适应，不管你使用的是何种日志基础设施。