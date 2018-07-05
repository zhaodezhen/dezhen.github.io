---
published: false
---
---
title: docker安装LNMP
key: 20180705
tags: docker
published: true
---
## docker加速

vi /etc/docker/daemon.json

{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}

重启docker服务 service docker restart

## 安装docker-compose

在linux命令端运行下面命令进行下载并进行授权：

curl -L https://github.com/docker/compose/releases/download/1.14.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

## 注意事项

内存大于2G
如果启动时出现 No command specified 可以查看 docker镜像是否制定cmd命令 inspect

([配置文件]（https://github.com/zhaodezhen/docker-LNMP))







