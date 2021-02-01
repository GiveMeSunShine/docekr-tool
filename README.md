使用 docker-tool 编译打包镜像

本项目主要是用来将本地项目进行docker镜像化的小工具

本地环境要求

    docker版本：

    Client: Docker Engine - Community
    Version:           20.10.2

    Server: Docker Engine - Community
    Engine:
    Version:          20.10.2

    docker-compose 版本：
    
    docker-compose version 1.25.4, build 8d51620a
    docker-py version: 4.1.0
    CPython version: 3.7.5
    OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019



基础镜像环境

`java：1.8.191`

`golang:1.14.13`

`nodejs:14.15.4`

使用说明：

下载 jdk1.8.0_191.tar.gz，go1.14.13.linux-amd64.tar.gz，node-v14.15.4-linux-x64.tar.gz，并将压缩包放入 docker-tool/baseImage 目录下

1.编译本地项目

2.拷贝编译包并且修改start脚本

    将编译好的项目包拷贝到projectImages/project
    修改 start 启动脚本
注： *start 启动脚本中执行的程序不能为后台运行，如果一定要后天运行，则需要在start脚本最后添加 tail -f /home/null 来保证容器启动是一直在运行*

3.镜像编译
项目编译分 java 和其他 两种项目编译方式，区别在于：java项目可以默认使用jar包名称作为镜像名称，其他项目(go/node)需要使用这强制输入镜像名称

    ./build
    请选择构建的项目类型(1-java 2-other):1 
    基础镜像 centos7:ysh 不存在，开始编译基础镜像
    镜像名称：centos7:ysh
    Sending build context to Docker daemon  349.6MB
    Step 1/20 : FROM centos:centos7
    ---> 8652b9f0cb4c
    .....{编译过程}
    Successfully tagged centos7:ysh
    编译结果:
    centos7                    ysh                              227d1c61ae09   1 second ago   1.04GB
    centos                     centos7                          8652b9f0cb4c   2 months ago   204MB
    测试(容器内部): 检查镜像内java，go，node 环境，退出请 ctrl D
    [root@6951caf51f06 mpsp]# ls
    go  jdk  node
    [root@6951caf51f06 mpsp]# java -version
    java version "1.8.0_191"
    Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
    Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
    [root@6951caf51f06 mpsp]# exit
    停止基础镜像
    6951caf51f06
    删除基础镜像容器
    6951caf51f06
    是否删除此次构建的基础镜像? (y/n)n
    正在运行的基础镜像:
    基础镜像列表:
    centos7            ysh                              227d1c61ae09   30 seconds ago   1.04GB
    centos             centos7                          8652b9f0cb4c   2 months ago     204MB
    开始编译项目镜像
    请输入镜像名称):{java项目默认使用jar包名称，其他类型项目必须填写名字}
    请输入镜像版本号(默认取当前日期):
    此次构建项目镜像信息：镜像名[umf-blockchain-0.0.1-snapshot] 版本号[2021-01-28] ,开始构建
    项目镜像名称：umf-blockchain-0.0.1-snapshot:2021-01-28
    Sending build context to Docker daemon  53.02MB
    Step 1/5 : FROM centos7:ysh
    .....{编译过程}
    Successfully built 08868ecb447c
    Successfully tagged umf-blockchain-0.0.1-snapshot:2021-01-28
    编译结果:
    umf-blockchain-0.0.1-snapshot                  2021-01-28                       08868ecb447c   1 second ago     1.09GB
    项目镜像(容器内部)：请确认容器内部程序是否可用 ./start，退出请 ctrl D
    [root@199e4220d52c mpsp]# ll
    total 51792
    drwxr-xr-x 3 root root     4096 1月  28 16:24 go
    drwxr-xr-x 3 root root     4096 1月  28 16:23 jdk
    drwxr-xr-x 3 root root     4096 1月  28 16:23 node
    -rwxrw-r-- 1 root root       55 1月  28 11:48 start
    -rw-rw-r-- 1 root root 53015552 1月  28 11:08 umf-blockchain-0.0.1-SNAPSHOT.jar
    [root@199e4220d52c mpsp]# exit
    停止项目镜像容器
    199e4220d52c
    删除项目镜像容器
    199e4220d52c
    是否要作废本次构建的项目镜像? (y/n)n
    此时运行的项目镜像
    此时已构建的项目镜像
    umf-blockchain-0.0.1-snapshot               2021-01-28                       08868ecb447c   15 seconds ago       1.09GB
    此次镜像构建结束

4. 修改启动模板（需要了解 docker-compose 文件知识）
相关知识  https://www.runoob.com/docker/docker-compose.html

5. 启动服务，并验证

        ./run
        docker ps
        CONTAINER ID   IMAGE                                      COMMAND                CREATED              STATUS              PORTS                      NAMES
        c469bf63e151   umf-blockchain-0.0.1-snapshot:2021-01-28   "/bin/sh -c ./start"   About a minute ago   Up About a minute   0.0.0.0:18080->18080/tcp   deploy_blockchain_1
    
发送请求：
http://localhost:18080/blockchain/eth/ethnodes/byline?st=1&pg=2&ps=10

![image](https://github.com/GiveMeSunShine/docekr-tool/blob/main/demo.png)

