#### 一．介绍
之前的博客我讲了ubuntu下安装docker的教程，这次我们介绍如何用docker安装jumpserver，关于jumpserver的介绍，自己去看我之前的博客吧
<!-- more -->
#### 二．环境

1. Docker-compost version 1.22.0
2. Docker version 18.06.1-ce

#### 三．配置
首先在当前文件夹下创建mysql文件

    ➜　mkdir ｛init，data,data-jumpserver,logs}
    ➜　cd init
    ➜　vi init.sql

写入

    reate database jumpserver default charset 'utf8';
    grant all on jumpserver.* to 'jumpserver'@'%' identified by '你的密码';

1. 创建docker-compost配置文件


    ➜　vi docker-compose.yml


复制以下内容

    version: '3'
    services:
      db:
        image: 'mysql/mysql-server:5.7'
        restart: 'always'
        container_name: mysql
        networks:
        app_net:
          ipv4_address: '172.16.10.12'
        volumes:
          - ./data:/var/lib/mysql
          - ./init:/docker-entrypoint-initdb.d/
        ports:
          - '4396:3306'
      jump:
          image: 'wojiushixiaobai/jumpserver:1.4.2'
          restart: 'always'
          container_name: 'jumpserver'
          volumes:
          #备份日志和录像
            - ./data-jumpserver:/opt/jumpserver/data/
            - ./logs:/opt/jumpserver/logsa/
          environment:
            - DB_ENGINE=mysql
            - DB_HOST=172.16.10.12
            - DB_PORT=3306
            - DB_USER=jumpserver
            - DB_PASSWORD=sjj123@
            - DB_NAME=jumpserver
          networks:
            app_net:
            ipv4_address: '172.16.10.13'
          ports:
            - '8080:80'

    networks:
      app_net:
        driver: 'bridge'
        ipam:
          driver: 'default'
          config:
          -
            subnet: 172.16.10.0/24




然后运行

    ➜　docker-compose up

然后浏览器打开127.0.0.1:8080
默认账号/密码:admin/admin
![2018-10-12 10-38-49屏幕截图.png](https://upload-images.jianshu.io/upload_images/14119596-2e040dc8edfabbbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

