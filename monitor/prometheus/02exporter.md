# 概述

之前我们是监控的prometheus本身，如果我们要监控其他服务的指标，如当前主机，mysql，redis 等，就需要exporter去采集数据，暴露一个 `/metrics` 接口，提供给prometheus拉取指标。

# 安装

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



 