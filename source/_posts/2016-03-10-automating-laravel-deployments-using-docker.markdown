---
layout: post
title: "Automating Laravel Deployments Using Docker"
date: 2016-03-10 15:31:45 +0800
comments: true
categories: 
---
#Laravel的持续集成与自动部署(Docker)
你是否遇到过这样的场景：在项目开发阶段，每次你提交了你的代码，都需要在开发环境上部署一下你的代码，好让测试去验证你的bug是否改完，每一次。。。。

为了解决上述的问题，加速开发(不偷懒的程序员不是好程序员)，从歪果仁那学到的一套Docker的解决方案：
利用Docker+Jenkins+Git，Git提交代码的时候触发Jenkins的自动构建，构建出Laravel项目的镜像，部署到置顶服务器。

当然，这个在国内已经有商业化的产品了，例如：csphere、DaoCloud等，大家可以试试！

我们还是自己搞吧，Here we go！

###服务器环境

ubuntu 14.04 + [Docker](https://docs.docker.com/)


###搭建步骤
* 安装Docker 和 [Docker Compose](https://docs.docker.com/compose/)
* 安装Git 和Jenkins
	既然我们都装Docker了就不用手动去安装这两个软件了
	
	Git(我用的GitLab，其他的也可以):
		
		//如果下载不下来去https://github.com/sameersbn/docker-gitlab找到docker-compose.yml下载
		root@xutao:~# wget https://raw.githubusercontent.com/sameersbn/docker-gitlab/master/docker-compose.yml
		
		//在刚刚下载docker-compose.yml的目录下
		root@xutao:~# docker-compose up -d	
		
		//.....等待下载镜像安装 安装成功后，访问10080端口
	Jenkins：
		
		docker pull jenkins
		docker run -p 8080:8080 -p 50000:50000 jenkins
		
		//新版的Jenkins已经内部集成了Docker(DinD),如果是老版本需要手动把docker映射到Container中
		docker run -p 8080:8080 -u root \
		-v /srv/docker/jenkins/jenkins:/var/jenkins_home \
		-v /srv/docker/jenkins/gradle:/root/.gradle \
		-v /var/lib/docker:/var/lib/docker \
		-v /usr/bin/docker:/usr/local/bin/docker \
		-v /var/run/:/var/run/ --privileged -d  jenkins
		
		//安装好，访问8080端口
* 创建git项目
* 配置Jenkins
	* 	系统设置
		系统管理->管理用户->新建用户 
		
		新建一个用户，记住用户名密码
		
		系统管理->Configure Global Security
		
		选中启用安全、Jenkins专有用户数据库、登录用户可以做任何事，保存，登录你的账号
	*	新建一个自由风格的软件项目
	
		源码管理 选择git 将你刚创建的git url填到Repository URL，Credentials里新增你的gitlab的账号和密码
		
		构建触发器 选中触发远程构建 (例如,使用脚本) ，设置身份验证令牌
		
		增加构建步骤 Excute Shell
		
			cp .env.example .env
			docker build -t hithinksoft/noodles-hr-staging .
				
			if ssh root@121.42.51.177 docker ps -a | grep -i noodles-hr-staging;then
    			ssh root@121.42.51.177 docker rm -f noodles-hr-staging
			fi
			ssh root@121.42.51.177 docker run --name noodles-hr-staging --restart="always" -d -p 889:80 hithinksoft/noodles-hr-staging
		
