---
title: Abp vNext在Docker部署并导出镜像
date: 2023-12-08 11:30:12
tags:
 - .net
 - abp vnext
---


###  Docker介绍
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows操作系统的机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

### 创建Dockerfile文件
	1、WORKDIR:加载文件地址
	2、EXPOSE：开放端口，程序可以访问端口（根据程序默认开放端口配置，例：80）
	3、COPY：文件拷贝地址
	4、ENTRYPOINT：程序启动配置

```
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS base
WORKDIR /app
EXPOSE 9000:9000
COPY bin/release/net6.0/linux-x64/publish/ ./
ENTRYPOINT ["dotnet", "HttpApi.Host.dll"]
```

### 发布部署程序
	1、ABP程序指定端口方式appsettings.json
		"Kestrel": {
		    "EndPoints": {
		      "Http": {
		        "Url": "http://*:9000"
		      }
		    }
		}
	2、发布docker：docker build . -t abp
		--tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。
	3、镜像安装vim，用于修改配置文件
		apt-get update
		apt-get install vim
	4、运行docker run  -p 9000:9000 -d abp:latest
		进入镜像：docker exec -it abp:latest /bin/bash
		-p: 指定端口映射，格式为：主机(宿主)端口:容器端口
		-d: 后台运行容器，并返回容器ID；
### 导出镜像
	docker save abp:latest -o abp.tar
### 导入镜像
	1.文件放入root根目录
	2.加载docker load -i abp.tar
	3.查看docker images
	4.运行docker run  -p 9000:9000 -d abp:latest

### vim命令
	i：启用光标
	:w ：保存文件
	:q ：推出文件编辑
	:q!：强制退出，不保存
	:wq：保存后退出编辑