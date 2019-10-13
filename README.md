# note
自己写些笔记

## github访问慢

1,访问 <http://tool.chinaz.com/>  或 <https://www.ipaddress.com/>  

2，查询 `github.global.ssl.fastly.net` 和 `github.com`的ip地址

3,在host文件中配置

```
69.63.184.142 github.global.ssl.fastly.net
13.250.177.223 github.com
```

4,cmd中运行 `ipconfig /flushdns` ,刷新dns

