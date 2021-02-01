FROM centos:centos7
USER root
RUN useradd --create-home --no-log-init --shell /bin/bash mpsp
RUN echo 'mpsp:mpsp' | chpasswd
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone
RUN localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
ENV LC_ALL zh_CN.UTF-8
WORKDIR /home/mpsp
#配置java环境变量
ADD jdk1.8.0_191.tar.gz /home/mpsp/jdk/
ENV JAVA_HOME=/home/mpsp/jdk/jdk1.8.0_191
ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV PATH=$JAVA_HOME/bin:$PATH
#配置node环境变量
ADD node-v14.15.4-linux-x64.tar.gz /home/mpsp/node/
ENV NODE_HOME=/home/mpsp/node/node-v14.15.4-linux-x64
ENV PATH=$PATH:$NODE_HOME/bin
#配置golang环境变量
ADD go1.14.13.linux-amd64.tar.gz /home/mpsp/go/
ENV GOROOT=/home/mpsp/go/go
ENV GOBIN=$GOROOT/bin
ENV GOPATH=$GOROOT/src
ENV PATH=$PATH:$GOBIN


