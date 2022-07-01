# 概述

之前我们是监控的prometheus本身，如果我们要监控其他服务的指标，如当前主机，mysql，redis 等，就需要exporter去采集数据，暴露一个 `/metrics` 接口，提供给prometheus拉取指标。

常用exporter

中间件 
redis  https://github.com/oliver006/redis_exporter
kafka https://github.com/danielqsj/kafka_exporter

数据库
mysql https://github.com/prometheus/mysqld_exporter
sqlserver https://github.com/awaragi/prometheus-mssql-exporter
mongodb https://github.com/dcu/mongodb_exporter
elasticsearch https://github.com/prometheus-community/elasticsearch_exporter
influxdb https://github.com/prometheus/influxdb_exporter

进程服务
jvm https://github.com/prometheus/jmx_exporter
springboot
tomcat
nginx https://github.com/nginxinc/nginx-prometheus-exporter

系统
linux https://github.com/prometheus/node_exporter
window https://github.com/prometheus-community/windows_exporter

# node_exporter

我们拿官方的主机监控的 node_exporter 来安装，用来监控一台主机的指标，如内存，cpu等数据

进入官方下载页面，https://prometheus.io/download/  下载 node_exporter

解压后直接启动

```bash
# 直接启动
./node_exporter

# 后台启动
nohup ./node_exporter > ./node_exporter.log 2>&1 &
```

默认端口为 9100 ，访问 `http://192.x.x.x:9100/metrics` 即可查看指标

![](img/p2.jpg)

如果想在 prometheus 监控，则关联配置，配置一个job即可

```yml
  - job_name: "nodes"
    static_configs:
      - targets: ["192.168.147.128:9100"]
```

重启 prometheus ，记得开启 `--web.enable-lifecycle` 

```bash
POST http://192.x.x.x:9090/-/reload
```

即可看见页面新增的target

## 自定义指标

当给到的指标不满足需求时，可以指定node_exporter 读取一个目录下指定格式的指标数据，格式为 `指标名{标签} 值`，文件的后缀为prom

ex：

添加脚本os_thread

```bash
#!/bin/bash
echo os_thread_total `ps -eo nlwp | tail -n +2 | awk '{ num_threads += $1 } END { print num_threads }'`
```

添加cron任务

```bash
crontab -e
```

每分钟执行下该脚本，并把结果输出到 *.prom

```bash
* * * * * sh /path/cus_textfile/os_thread > /path/cus_textfile/os_thread.prom
```

这样我们就会得到一个文件 os_thread.prom，每分钟刷新

```
os_thread_total 330
```



重新启动，指定 `--collector.textfile.directory` 目录，会自动读取该目录下的 .prom 文件

```bash
nohup ./node_exporter --collector.textfile.directory=./cus_textfile  > ./node_exporter.log 2>&1 &
```

这样就能读取到 自定义的指标 os_thread_total

## 常用指标算法

```
cpu使用率:(1-(sum by(instance) (increase(node_cpu_seconds_total{mode='idle'}[1m]))) / (sum by(instance) (increase(node_cpu_seconds_total[1m]))))*100
内存使用率: (1-(node_memory_MemFree_bytes+node_memory_Cached_bytes+node_memory_Buffers_bytes)/node_memory_MemTotal_bytes)*100
总物理内存：node_memory_MemTotal_bytes/(1024*1024*1024)
已用物理内存：(node_memory_MemTotal_bytes-node_memory_MemFree_bytes-node_memory_Buffers_bytes-node_memory_Cached_bytes)/(1024*1024*1024)
已用虚拟内存：(node_memory_SwapTotal_bytes - node_memory_SwapFree_bytes - node_memory_SwapCached_bytes )/(1024*1024*1024)
磁盘使用率: (1-sum by(instance)(node_filesystem_free_bytes) / sum by(instance)(node_filesystem_size_bytes)) * 100
总磁盘可用：sum by(instance)(node_filesystem_free_bytes) / (1024*1024*1024)
分磁盘可用：sum by (instance,mountpoint)(node_filesystem_free_bytes) / (1024*1024*1024)
inode使用占比：(1-sum by (device,instance)(node_filesystem_avail_bytes) / sum by (device,instance)(node_filesystem_size_bytes)) * 100
文件句柄数：node_filefd_allocated
进程数：node_procs_running 
总线程数(自定义配置)：os_thread_total
开机天数（分钟）：(time()-node_boot_time_seconds) / 60
```

# windows_exporter

下载好对应的 exe，直接运行即可

![](img/p8.jpg)

浏览器打开 地址验证，默认端口为 9182， http://localhost:9182/metrics

默认情况会读取当前目录下的 textfile_inputs 目录下的 .prom 文件，作为自定义指标，每行结尾要有个换行 `\n`

```
my_metric 123

```

## 常用指标算法

```
windows指标

cpu使用率: (1-(sum by (instance)(increase(windows_cpu_time_total{mode="idle"}[1m])))/(sum by (instance)(increase(windows_cpu_time_total[1m]))))* 100
内存使用率: (1-windows_os_physical_memory_free_bytes/windows_cs_physical_memory_bytes)*100
总物理内存：windows_cs_physical_memory_bytes / (1024*1024*1024)
已用物理内存：(windows_cs_physical_memory_bytes-windows_os_physical_memory_free_bytes)/(1024*1024*1024)
已用虚拟内存：(windows_os_virtual_memory_bytes -windows_os_virtual_memory_free_bytes )/(1024*1024*1024)
磁盘使用率: (1-sum by(instance)(windows_logical_disk_free_bytes) / sum by(instance)(windows_logical_disk_size_bytes)) * 100
总磁盘可用：sum by(instance)(windows_logical_disk_free_bytes) / (1024*1024*1024)
分磁盘可用：sum by (instance,volume)(windows_logical_disk_free_bytes)/ (1024*1024*1024)
文件句柄数：process_open_fds{job="windows"}
进程数：windows_os_processes 
总线程数：windows_system_threads 
开机天数（分钟）：(time()-windows_system_system_up_time ) / 60
```



