title: Jenkins安装及自动部署Maven项目
author: Nico
tags:
  - Jenkins
  - Maven
categories: []
date: 2018-11-06 12:47:00
---
## 一、环境配置
#### OS版本

```
[root@VM_0_11_centos /]# rpm -qa | grep centos-release
centos-release-7-4.1708.el7.centos.x86_64
```
#### Java版本
```
[root@VM_0_11_centos /]# java -version
openjdk version "1.8.0_181"
OpenJDK Runtime Environment (build 1.8.0_181-b13)
OpenJDK 64-Bit Server VM (build 25.181-b13, mixed mode)
```
#### Maven版本
```
[root@VM_0_11_centos /]# mvn -v
Apache Maven 3.0.5 (Red Hat 3.0.5-17)
Maven home: /usr/share/maven
Java version: 1.8.0_181, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.10.0-693.el7.x86_64", arch: "amd64", family: "unix"
```
#### Git版本
```
[root@VM_0_11_centos /]# git --version
git version 1.8.3.1
```
## 二、安装

#### Java安装
```
yum install java-1.8.0-openjdk.x86_64
```
#### Maven安装
```
yum install maven
```
#### Git安装
```
yum install git
```
#### Jenkins安装

rpm包地址：[https://pkg.jenkins.io/redhat-stable/](https://pkg.jenkins.io/redhat-stable/)
```
rpm -ivh xxx.npm
```
执行以上指令后即安装完毕，查看一下jenkins所在目录
```
whereis jenkins
```
控制台输出
```
[root@VM_0_11_centos /]# whereis jenkins
jenkins: /usr/lib/jenkins
```
默认jenkins的配置文件在``/etc/sysconfig/jenkins``

启动jenkins
```
service jenkins start
```
关闭jenkins
```
service jenkins stop
```
## 三、Jenkins部署Maven项目
Jenkins启动只有的默认端口为8080，在保证服务器安全组开放8080端口（或者自己在``/etc/sysconfig/jenkins``配置文件中修改端口）的前提下，我们可以直接通过浏览器访问Jenkins
```
http://xxxxxx:8080
```
第一次进入Jenkins会让你走几个步骤
 - 输入管理员密码，密码可以从页面提示的文件中看到
 - 下载默认插件，点击官方推荐的按钮继续往下走
 - 设置账号密码和邮箱地址
 - 登入

一顿操作，我们就来到了Jenkins的Dashboard页面
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6974344c0?w=1897&h=598&f=png&s=72196)

看到这里是不是很激动？别急，Jenkins的新建任务默认是没有Maven选项的,需要自行安装Jenkins的Maven插件！

#### 1、安装Jenkins-Maven插件Maven Integration
在首页中点击右侧的系统管理
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6976b76bd?w=1885&h=613&f=png&s=75878)
选择管理插件
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b697a978f8?w=1892&h=862&f=png&s=133614)
下载Maven Integration
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6979cd7a5?w=1892&h=651&f=png&s=67831)
点击立即获取之后，等待一分钟左右就下载好了

#### 2、配置全局工具
接着我们要做一些工具的配置

再次进入系统管理，点击列表中的**全局工具配置**
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b69783f279?w=1889&h=827&f=png&s=128413)
配置JDK
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b697743f0a?w=1529&h=362&f=png&s=20752)
配置Git
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6c277f7c6?w=1523&h=320&f=png&s=17690)
配置Maven
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6c740707d?w=1507&h=331&f=png&s=19931)
完毕之后点击``SAVE``按钮保存
#### 3、配置任务信息
经过前面两个步骤，我们的Jenkins可以正式开始工作了，不过在真正为我们提供服务之前，我们需要告诉任务该做什么事情，该怎么做。

例如？我们要构建的项目从何而来？通过什么样的方式或者指令就构建？不废话，开始创建一个新的任务，并且是一个Maven项目
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6c7c0c77f?w=1901&h=614&f=png&s=60467)
点击创建一个新任务进入下一步
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6ca5adfa0?w=1852&h=933&f=png&s=132108)
选择创建一个Maven项目，确定之后，进入任务配置界面

任务信息配置
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6d06d8e2b?w=1882&h=462&f=png&s=42624)
源码库配置，Jenkins要知道如何获取到项目的源码
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6d2fca2f2?w=1887&h=657&f=png&s=63920)
Maven打包指令配置
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6e4036b64?w=1886&h=425&f=png&s=37257)
构建策略配置
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6eb145739?w=1879&h=374&f=png&s=43226)
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6f1a7a410?w=1889&h=327&f=png&s=33364)
构建生命周期配置
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6f5b10abc?w=1884&h=726&f=png&s=76468)
这一步很关键，在Jenkins帮我们自动拉取代码并且打成jar包之后，我们需要执行shell指令去启动它们，``Pre Steps``和``Post Steps``使我们可以在整个周期内灵活的去控制流程~例如写个脚本启动它们！

简单配置之后，点击保存，完成任务配置编辑！

之后的事情就简单了，在首页可以看到一个任务列表，选中自己的任务点击进入
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6fa1e7b0f?w=1890&h=567&f=png&s=67414)
点击左侧工具栏的立即构建~ 
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b6fc49a77e?w=1891&h=879&f=png&s=108676)
Jenkins简单部署完成，看下我的任务构建控制台输出日志
![这里写图片描述](https://user-gold-cdn.xitu.io/2018/11/6/166e77b7806857f7?w=1883&h=907&f=png&s=90364)

看到最下方的成功就代表项目已经成功部署！