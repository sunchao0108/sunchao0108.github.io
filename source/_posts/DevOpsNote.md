---
title: Devops学习总结
thumbnail: /images/sunGarden.jpg
cover: /images/sunGarden.jpg
date: 2024-11-15 12:59:39
tags: 工作
---

趁着最近空闲在家学学devops, B站上的视频教程很多，学起来不用费太多脑子，先总体有一个了解，让自己能保持兴趣学下去。

但是视频教程难免内容老化及有语焉不详之处，暂记此处以作查缺补漏之用。

## 注意事项

* 需要安装虚拟机，教程一般都是Centos7的，最好安装一致的版本，以免兼容性问题。
* 电脑内存至少16G以上，需要同时至少启动4个虚拟机，每个4G内存。
* 虚拟机创建好了默认是动态IP，最好改为静态IP
* 安装的docker和docker-compose版本要注意，安装教程中的方式可能过时了。
* docker的镜像源需要单独找，教程中的过时了。国内大多数镜像源都趴窝了，目前两种方式比较稳定，一种是github action搭配阿里云镜像服务，一种是CF中转。
* Jenkins的安装，视频里的也过时了，视频里的版本有很多Jenkins插件已经不兼容了。


## 常用命令
记录些常用命令，用的时候可以随时复制粘贴

#### 复制虚拟机后修改网卡信息使用新的IP
vi /etc/sysconfig/network-scripts/ifcfg-ens33
#### 重启网卡
systemctl restart network 
#### Centos换阿里云源
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

#### 虚拟机是动态IP，最好改成静态的
IPADDR="192.168.249.129"
NETMASK="255.255.255.0"
GATEWAY="192.168.249.2"
DNS1="8.8.8.8"
DNS2="8.8.4.4"

#### 配置docker-compose yml
    
```
version: '2.6.1'
services:
  gitlab:
    image: your_web_image_name:latest  # 替换为你的Web应用镜像名称和标签
    container_name: gitlab
    restart: always
    ports:
      - "8929:8929"  # 将主机的8080端口映射到容器内的80端口，可根据需要修改
      - "2224:2224"
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.249.130:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    volumes:
      - './config:/etc/gitlab'
      - './logs:/var/log/gitlab'
      - './data:/var/opt/gitlab'
```

#### 查看gitlab初始密码
cat /etc/gitlab/initial_root_password

#### jenkins yml模板
```
version: '2.6.1'
services:
  jenkins:
    image: jenkins/jenkins:2.319.1-lts  # 替换为你的Web应用镜像名称和标签
    container_name: jenkins
    ports:
      - 8080:8080  # 将主机的8080端口映射到容器内的80端口，可根据需要修改
      - 50000:50000
    volumes:
      - ./data/:/var/jenkins_home
```

#### Jenkins插件源
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

暂时先记录这么多