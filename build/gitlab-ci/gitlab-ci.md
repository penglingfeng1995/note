# 概述

gitlab-ci 是 gitlab 平台自带的ci系统，通过  `.gitlab-ci.yml` 文件进行配置，告诉gitlab需要在ci过程中执行什么工作，如何触发任务等，随后 gitlab 会寻找可以执行该任务的 gitlab-runner 来执行这个任务，并把结果记录。

所以官网文档，在 gitlab 上点击右上角的 help 进入。

# 安装 gitlab-runner

[gitlab-runner]( https://docs.gitlab.com/runner/) 官网

这边我们选择linux安装，可以选择官网下载，也可以使用[清华镜新](https://mirrors.tuna.tsinghua.edu.cn/gitlab-runner/yum/el7-x86_64/) 

首先需要安装 git ，使用 yum 在线安装，需要联外网

```bash
yum install git
```

安装 gitlab-runner ，这里使用在线安装

```bash
yum install gitlab-runner
```

注册 gitlab-runner

```bash
gitlab-runner register
```



# 配置 yml



