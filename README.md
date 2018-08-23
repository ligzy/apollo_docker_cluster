# docker-apollo

[![Docker Build Status](https://img.shields.io/docker/build/idoop/docker-apollo.svg)](https://hub.docker.com/r/idoop/docker-apollo/)
[![Docker Pulls](https://img.shields.io/docker/pulls/idoop/docker-apollo.svg)](https://hub.docker.com/r/idoop/docker-apollo/)
[![Docker Automated build](https://img.shields.io/docker/automated/idoop/docker-apollo.svg)](https://hub.docker.com/r/idoop/docker-apollo/)
[![ImageLayers Size](https://img.shields.io/imagelayers/image-size/idoop/docker-apollo/latest.svg)](https://hub.docker.com/r/idoop/docker-apollo/)
[![ImageLayers Layers](https://img.shields.io/imagelayers/layers/idoop/docker-apollo/latest.svg)](https://hub.docker.com/r/idoop/docker-apollo/)

Docker image for [Ctrip/Apollo](https://github.com/ctripcorp/apollo)(携程Apollo)

## Docker Tags: 

- `1.0.0` `latest`
- `0.11.0` 
- `0.10.2`

## 使用 Docker Compose 启动
建立一个`docker-compose.yaml`文件,内容如下,将mysql数据库地址与库名以及账号密码为本地的即可:
``` yaml
version: '2'
services:
  apollo:
    image: idoop/docker-apollo:latest
    container_name: apollo
    hostname: apollo
    # 目前只能用host模式,否则将出现504错误,如果想改端口,参考下方修改端口的环境变量
    network_mode: "host"
    # 如果需要查看日志,挂载容器中的/opt路径出来即可.
    # volumes:
    #   - ./logs:/opt
    environment:
      # 开启Portal,默认端口: 8070
      PORTAL_DB: jdbc:mysql://192.168.1.28:3306/ApolloPortalDB?characterEncoding=utf8
      PORTAL_DB_USER: root
      PORTAL_DB_PWD: toor
      
      # 开启dev环境, 默认端口: config 8080, admin 8090
      DEV_DB: jdbc:mysql://192.168.1.28:3306/ApolloConfigDBDev?characterEncoding=utf8
      DEV_DB_USER: root
      DEV_DB_PWD: toor
      
      # 开启fat环境, 默认端口: config 8081, admin 8091
      FAT_DB: jdbc:mysql://192.168.1.28:3306/ApolloConfigDBFat?characterEncoding=utf8
      FAT_DB_USER: root
      FAT_DB_PWD: toor
      # 可修改端口.
      FAT_CONFIG_PORT: 8050
      FAT_ADMIN_PORT: 8051
           
      # 指定远程uat地址
      #UAT_URL: http://192.168.1.2:8080
      
      # 指定远程pro地址
      #PRO_URL: http://www.baidu.com:8080
```

*镜像包含Portal面板,以及Dev,Fat,Uat,Pro环境,皆可独立使用.若要开启相应环境,只需配置对应环境的env的数据库地址与账号密码。*

**启动前确认对应的数据库已建立,且数据库账号有权操作该库,否则将会启动失败.**[创建数据库指导](https://github.com/ctripcorp/apollo/wiki/%E5%88%86%E5%B8%83%E5%BC%8F%E9%83%A8%E7%BD%B2%E6%8C%87%E5%8D%97#21-%E5%88%9B%E5%BB%BA%E6%95%B0%E6%8D%AE%E5%BA%93)

镜像启动慢,且耗内存,因此有多开需求的请注意小鸡的内存是否够用. 

测试机为4核2.6G的x5650：

> 只启动Portal,用时20s,占内存280M.
>
> Portal+dev,用时90s,占内存999M.
>
> Portal+dev+fat,用时154s,占内存1674M.
>
> Protal+dev+fat+uat,自行估算.
>
> Protal+dev+fat+uat+pro,自行估算.

## Environment 参数

Portal:
> - PORTAL_DB: portal 的数据库地址, 留空则代表不开启
> - PORTAL_DB_USER: 数据库用户
> - PORTAL_DB_PWD: 数据库密码
> - PORTAL_PORT: portal服务的端口,默认8070.若网络模式为host,可更改.
> - DEV_URL: 远程dev服务,格式为http://**ip:port** 或 **domain:port** 不可与DEV_DB同时指定,数据库中ServerConfig中eureka.service.url的地址与端口需正确.
> - FAT_URL: 远程fat服务,格式为http://**ip:port** 或 **domain:port** 不可与FAT_DB同时指定,数据库中ServerConfig中eureka.service.url的地址与端口需正确.
> - UAT_URL: 远程uat服务,格式为http://**ip:port** 或 **domain:port** 不可与UAT_DB同时指定,数据库中ServerConfig中eureka.service.url的地址与端口需正确.
> - PRO_URL: 远程pro服务,格式为http://**ip:port** 或 **domain:port** 不可与PRO_DB同时指定,数据库中ServerConfig中eureka.service.url的地址与端口需正确.

Dev:
> - DEV_IP: 若使用分布式负载均衡,则输入负载均衡IP.
> - DEV_DB: dev 环境数据库地址, 留空则代表不开启
> - DEV_DB_USER: 数据库用户
> - DEV_DB_PWD: 数据库密码
> - DEV_ADMIN_PORT: admin服务端口,默认8090,若网络模式为host,可指定更改.
> - DEV_CONFIG_PORT: config服务端口,默认8080,若网络模式为host,可指定更改,需要与本数据库中的ServerConfig中eureka.service.url端口相同.

Fat:
> - FAT_IP: 若使用分布式负载均衡,则输入负载均衡IP.
> - FAT_DB: fat 环境数据库地址, 留空则代表不开启
> - FAT_DB_USER: 数据库用户
> - FAT_DB_PWD: 数据库密码
> - FAT_ADMIN_PORT: admin服务端口,默认8091.若网络模式为host,可指定更改.
> - FAT_CONFIG_PORT: config服务端口,默认8081.若网络模式为host,可指定更改,需要与本数据库中的ServerConfig中eureka.service.url端口相同.

Uat:
> - UAT_IP: 若使用分布式负载均衡,则输入负载均衡IP.
> - UAT_DB: uat 环境数据库地址, 留空则代表不开启
> - UAT_DB_USER: 数据库用户
> - UAT_DB_PWD: 数据库密码
> - UAT_ADMIN_PORT: admin服务端口,默认8092.若网络模式为host,可指定更改.
> - UAT_CONFIG_PORT: config服务端口,默认8082.若网络模式为host,可指定更改,需要与本数据库中的ServerConfig中eureka.service.url端口相同.

Pro:
> - PRO_IP: 若使用分布式负载均衡,则输入负载均衡IP.
> - PRO_DB: pro 环境数据库地址, 留空则代表不开启
> - PRO_DB_USER: 数据库用户
> - PRO_DB_PWD: 数据库密码
> - PRO_ADMIN_PORT: admin服务端口,默认8093.若网络模式为host,可指定更改.
> - PRO_CONFIG_PORT: config服务端口,默认8083.若网络模式为host,可指定更改,需要与本数据库中的ServerConfig中eureka.service.url端口相同.

Sql:
> - 创建数据库：
    CREATE DATABASE ApolloPortalDB DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
    CREATE DATABASE ApolloConfigDBDev DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
    CREATE DATABASE ApolloConfigDBFat DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
    CREATE DATABASE ApolloConfigDBUat DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
    CREATE DATABASE ApolloConfigDBPro DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

> - 导入数据，ApolloConfigDB.sql 导入 ApolloConfigDBDev 、 ApolloConfigDBFat 、 ApolloConfigDBUat、ApolloConfigDBPro
> - 导入数据，ApolloPortalDB.sql 导入 ApolloPortalDB

> - 添加环境，修改数据库
    ApolloPortalDB 数据库  ServerConfig 表 第一行  value 值改成：fat,dev,uat,pro
