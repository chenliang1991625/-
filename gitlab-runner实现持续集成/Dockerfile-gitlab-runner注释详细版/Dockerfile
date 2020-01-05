#不要出现交互的命令:比如yes/no,选yes安装;所以要跟新哪个配置文件要先删除再拷贝(构建)
FROM gitlab/gitlab-runner:v11.0.2
MAINTAINER chenliang <@1484157619.qq.com>

# 修改软件源
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse' >> /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse' >> /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse' >> /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse' >> /etc/apt/sources.list && \
    apt-get update -y && \
    apt-get clean

# 安装 Docker
#RUN curl -fsSL get.docker.com -o get-docker.sh
# 可能会出现 404 错误，请移步下面的特别说明
#RUN sh get-docker.sh --mirror Aliyun
 
#RUN ARG DEBIAN_FRONTEND=noninteractive && apt-get update -y && apt-get install -y gnupg2 --assume-yes apt-utils && \
#RUN apt-get update -y && apt-get upgrade -y && apt-get -y install  apt-utils 
#RUN RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 
#RUN apt-get -y update && apt-get install -y \
   #linux-image-extra-$(uname -r) && apt-get install -y linux-image-extra-virtual

RUN apt-get install -y apt-transport-https ca-certificates curl software-properties-common && \
    curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add - && \
	#curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -  && \
    add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" && \
    #add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs)stable"
	apt-get update -y && \
    apt-get install -y docker-ce
#WORKDIR /root/install-package
#WORKDIR ~/install-package 
COPY daemon.json /etc/docker/daemon.json
# 安装 Docker Compose
COPY docker-compose /usr/local/bin/docker-compose
#RUN wget https://raw.githubusercontent.com/topsale/resources/master/docker/docker-compose
RUN chmod +x /usr/local/bin/docker-compose 

# copy配置文件、包和安装Java
COPY jdk-8u152-linux-x64.tar.gz /usr/local/java/jdk-8u152-linux-x64.tar.gz
COPY apache-maven-3.5.3-bin.tar.gz /usr/local/maven/apache-maven-3.5.3-bin.tar.gz
#COPY settings.xml /usr/local/maven/apache-maven-3.5.3/conf/settings.xml #apache-maven-3.5.3-bin.tar.gz解压后才能执行
#COPY docker-compose.yml /usr/local/docker/runner/docker-compose.yml
#COPY Dockerfile /usr/local/docker/runner/environment/Dockerfile
#RUN mkdir -p /usr/local/java #创建文件不往Dockerfile里面写,如果文件存在不能交互,会报错
WORKDIR /usr/local/java
#COPY jdk-8u152-linux-x64.tar.gz /usr/local/java
RUN tar -zxvf jdk-8u152-linux-x64.tar.gz && \
    rm -fr jdk-8u152-linux-x64.tar.gz

# 安装 Maven
#RUN mkdir -p /usr/local/maven
WORKDIR /usr/local/maven
# RUN wget https://raw.githubusercontent.com/topsale/resources/master/maven/apache-maven-3.5.3-bin.tar.gz
RUN tar -zxvf apache-maven-3.5.3-bin.tar.gz && \
    rm -fr apache-maven-3.5.3-bin.tar.gz
COPY settings.xml /usr/local/maven/apache-maven-3.5.3/conf/settings.xml

# 配置环境变量
#jdk-8u152-linux-x64.tar.gz解压后包名变为jdk1.8.0_171，要特别注意，可以先解压看一下
ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
ENV MAVEN_HOME /usr/local/maven/apache-maven-3.5.3
ENV PATH $PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin

WORKDIR /
