# Sandbox

关于 mesos 通过sandbox捕获 容器打印到console(控制台/终端)的日志

mesos运行的docker容器，容器打印到前台console的日志会记录到mesos的work目录中容器沙箱中stdout和stderr文件中，容器不重启，日志会一直变大，这样会到只宿主空间变大。

LogrotateContainerLogger
该LogrotateContainerLogger约束的容器的输出和错误文件的总大小。该模块通过基于模块的参数旋转日志文件来实现此目的。当日志文件达到其指定的最大大小时，可通过.N在文件名的末尾附加a 来重命名该日志文件，并在此N增加每次旋转。当达到指定的最大文件数时，将删除较旧的日志文件。

调用模块
所述LogrotateContainerLogger可以通过指定库被加载 liblogrotate_container_logger.so在 --modules标记开始代理时和由设置--container_logger代理标志 org_apache_mesos_LogrotateContainerLogger。



### 模块参数



| 键                                                 | 说明                                                         |
| -------------------------------------------------- | ------------------------------------------------------------ |
| max_stdout_size/max_stderr_size                    | 单个stdout / stderr日志文件的最大大小（以字节为单位）。达到大小后，文件将被旋转。默认为10 MB。最小大小为1（内存）的页面，通常约为4 KB。 |
| logrotate_stdout_options/ logrotate_stderr_options | 传递logrotate给stdout的其他配置选项。该字符串将被插入到logrotate配置文件中。即对于“ stdout”：/ path / to / stdout {   [logrotate_stdout_options]   大小[max_stdout_size] }注意：该size模块将覆盖该选项。 |
| environment_variable_prefix                        | 环境变量的前缀旨在针对要启动的特定容器修改logrotate记录器的行为。记录器会寻找四个前缀的环境变量在容器的CommandInfo的Environment：• MAX_STDOUT_SIZE • LOGROTATE_STDOUT_OPTIONS • MAX_STDERR_SIZE • LOGROTATE_STDERR_OPTIONS 如果存在，这些变量将覆盖通过模块参数设置的全局值。默认为CONTAINER_LOGGER_。 |
| launcher_dir                                       | Mesos二进制文件的目录路径。该LogrotateContainerLogger会发现 mesos-logrotate-logger这个目录下的二进制文件。默认为/usr/local/libexec/mesos。 |
| logrotate_path                                     | 如果指定，LogrotateContainerLogger则将使用指定的logrotate而不是系统的 logrotate。如果logrotate未找到，则模块将退出并显示错误。 |





### 怎么运行的


\1. 每次启动容器时，都会LogrotateContainerLogger 启动mesos-logrotate-logger二进制文件的伴随子进程。
\2. 该模块指示Mesos将容器的stdout / stderr重定向到mesos-logrotate-logger。
\3. 当容器输出到stdout / stderr时，mesos-logrotate-logger会将输出通过管道传递到“ stdout” /“ stderr”文件中。随着文件的增长， mesos-logrotate-logger将调用logrotate来将文件严格保持在配置的最大大小以下。
\4. 当容器退出时，mesos-logrotate-logger也会在退出之前完成日志记录。
在LogrotateContainerLogger被设计成跨代理故障转移弹性。如果代理进程终止，则任何实例都mesos-logrotate-logger 将继续运行。