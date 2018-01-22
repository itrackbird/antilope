# 概述

这是关于测试过程中使用工具的总结，包括项目管理工具、测试工具、环境搭建工具、文档编写工具、CI/CD、编程语言Node、Go、Python、性能安全测试工具以及树莓派使用。

### 项目管理工具

项目管理工具主要是用来记录版本下面问题单，方便统计与跟踪，控制版本质量。推荐两个软件，redmine与jira，前者开源功能较少，而后者商用功能较多，同时也可以与Atlassian公司其他产品搭配使用功能强大。关于redmine的使用，参考其[官网](http://www.redmine.org/)介绍，对于小型团队完全满足需求。关于jira的使用，参考[jira中文介绍](http://www.confluence.cn/dashboard.action)，同时也有该公司其他产品介绍。

#### redmine搭建

对于redmine的安装，推荐使用[bitnami公司](https://bitnami.com/)制作好的安装包，支持windows、linux安装包以及docker搭建。

对于二进制包安装，在其[下载链接](https://bitnami.com/stack/redmine)选择对应平台软件包下载安装即可。

对于docker安装，推荐使用docker-compose安装，然后在[github项目](https://github.com/bitnami/bitnami-docker-redmine)上下载docker-compose.yml到本地，在文件目录下使用`docker-compose up -d`自动安装，关于docker-compose介绍参考[docker官网](https://www.docker.com/)。

> **`docker-compose`是python的一个库，使用`pip install docker-compose`安装即可，参考各个系统的docker-compose[安装说明](https://docs.docker.com/compose/install/)**

#### jira搭建

推荐使用docker安装，方便快速。

```shell
root@raspberrypi:~# docker run --detach --publish 8080:8080 cptactionhank/atlassian-jira:latest
```

之后访问docker主机的8080端口进行设置即可，具体参考dockerhub官方[jira镜像](https://hub.docker.com/r/cptactionhank/atlassian-jira/)使用。

### 测试工具

接下来主要讨论工具，对于如何编写测试用例参考[用户故事与敏捷方法的设计规则](https://www.cnblogs.com/mixiaobo/archive/2008/11/03/1325809.html)。手工用例采用execl文本记录，接口用例、自动化用例推荐使用[robotframework](http://robotframework.org/)工具，web自动化使用selenium编写，可以搭配使用robotframework工具，进行用例积累。在接口测试过程中，对于有些部件接口需要模拟情况下，可以使用soapui模拟，同时也推荐使用node的[Mock.js](https://github.com/nuysoft/Mock)以及其他第三库来搭建，顺便积累编程知识。搭建[appium](http://www.cnblogs.com/Mushishi_xu/p/7685897.html)对手机app测试，团队协作的api测试平台[Hitchhiker](https://gitee.com/iwxiaot/Hitchhiker)。

### 环境搭建工具

环境的搭建需要各个方面的知识，包括bash编程、linux系统知识、编程知识等。

对于一个单web应用来说，需要配置jdk环境、tomcat安装、linux配置、vim编辑器、内存配置等，一般来说比较简单，参考搭建单个tomcat服务；

对于分布式应用来说，比如微服务，可以采用编写bash脚本来进行批量部署。推荐使用[ansible](http://docs.ansible.com/ansible/latest/index.html)与bash脚本搭配搭建与维护测试环境，实现自动化部署；

对于采用docker来安装来说，主流做法是与k8s容器编排系统搭配构建交付平台。

### 文档编写工具

对于文档的编写是需要贯穿到软件的整个生命周期，从需求文档、接口文档、数据库文档、安装文档以及测试文档，所以我们需要一个好的工具来帮助我们更好的、更快的编写文档，推荐使用markdown文本格式编写，以下介绍正在使用的markdown工具。

对于个人来说，Windows系统上推荐使用[Typora](https://www.typora.io/)，该工具可以同时导出html、word、pdf等多种文档格式，具体使用参考官方使用教程。

对于团队来说，推荐Atlassian公司的另一款产品[confluence](http://www.confluence.cn/)，一款强大的企业wiki软件，方便管理项目过程中输出的文档。	

#### confluence安装

关于其安装方案推荐使用docker搭建，参考[confluence安装](http://wuyijun.cn/shi-yong-dockerfang-shi-an-zhuang-he-yun-xing-confluence/)。

#### Typora安装

从官网下载二进制文件安装即可。

### CI/CD

持续集成和持续交付解决方案，实现产品快速迭代。基本流程代码编译打包 ==> 环境部署 ==> 执行测试 ==> 生产部署 ==> 升级回滚、扩容缩容，之前接触过以k8s与docker为基础实现的解决方案。

首先准备好各种语言的编译环境镜像，如java、go、node等，系统从SVN或者GIT服务器上拉取代码仓库到编译镜像中编译，等待结束后，执行仓库中Dockerfile文件加载上一步代码编译好的二进制文件来构建应用镜像。当构建通过之后，将该镜像上传到代码仓库，会通过[webhook机制](https://github.com/adnanh/webhook)触发更新所有以该镜像组成的应用，实现实时更新。同时，通过k8s本身的服务管理机制进行升级回滚、扩容缩容。

> **构建镜像是指已安装对应语言的打包工具的镜像，如mvn、ant、webpack等**

目前，上述流程的需要使用的工具基本都有，而且是开源的，如

* git服务器[gogs](https://gogs.io/)、[gitea](https://gitea.io/zh-CN/)
* 编译构建工具[drone](https://github.com/drone/drone)
* 容器编排[kubernets](https://kubernetes.io/)

其中还有docker的web管理工具[Portainer](https://github.com/portainer/portainer)、桌面工具[Kitematic](https://kitematic.com/)，全栈化企业级容器管理平台[rancherOS](https://www.cnrancher.com/)，持续交付与滚动升级工具[ansible](http://www.ansible.com.cn/)，镜像仓库[registry](https://docs.docker.com/registry/)，容器网络模式[macvlan](https://docs.docker.com/engine/userguide/networking/get-started-macvlan/)、[flannel](https://coreos.com/flannel/docs/latest/)、[calico](https://www.projectcalico.org/)、[ovs](https://github.com/openvswitch/ovs)，证书认证[cfssl](http://blog.simlinux.com/archives/1953.html)，服务注册和发现[etcd](https://coreos.com/etcd/docs/latest/docs.html#documentation)，无服务器[OpenFaas](https://github.com/openfaas/faas)。

> **对于CI/CD过程中需要规划各个流程，版本定义、Dockerfile等**

### Node、Go、Python

不想学习全栈的测试不是好的测试：）

前端框架[Vue.js](https://github.com/vuejs/vue)，后端Go框架[Gin](https://github.com/gin-gonic/gin)、Python框架[django](https://github.com/django/django)，桌面应用框架[Electron](https://github.com/electron/electron)

### 性能安全测试工具

性能压力测试工具ab、数据库扫描工具sqlmap、安全测试系统kailinux、漏洞扫描工具Vuls、网络扫描工具nmap。 

### 树莓派使用

树莓派是尺寸仅有信用卡大小的一个小型电脑，您可以将树莓派连接电视、显示器、键盘鼠标等设备使用。完全支持在树莓派上安装docker以及k8s，参考[HypriotOS博客](https://blog.hypriot.com/post/setup-kubernetes-raspberry-pi-cluster/)。

目前正计划完成在树莓派是上安装kubernets，进度在本仓库另一个项目[itrackbird/raspbian](https://github.com/itrackbird/raspbian/tree/master/kubernets)中。

### 其他

以下记录测试过程中需要关注的地方。

* 对于需求不明确的地方找产品确认
* 对于测试过程中编写文档
* 规划版本标识，设计安装脚本
* 。。。

