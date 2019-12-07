# note
自己写些笔记

## github访问慢

1,访问 <http://tool.chinaz.com/dns/>  或 <https://www.ipaddress.com/>  

2，查询 `github.global.ssl.fastly.net` 和 `github.com`的ip地址

3,在host文件中配置

```
69.63.184.142 github.global.ssl.fastly.net
13.250.177.223 github.com
```

4,cmd中运行 `ipconfig /flushdns` ,刷新dns

# markdown 语法

后缀名为md，类似于html，是一种标记语言

##  标题

以`#` 开头，后接一个空格

```markdown
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
```

## 代码块

以三个反引号包住代码块，可以选择编程语言

```markdown
​```js
var a = 1;
​```
```

## 超链接

```markdown
[Google](http://www.google.com/)
```

## 图片

```markdown
![mypic](./images/a.img)
```

## 文本修饰

加粗

```markdown
**加粗**
```

代码

```markdown
`div`
```



