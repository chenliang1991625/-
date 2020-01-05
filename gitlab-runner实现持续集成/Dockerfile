#不要出现交互的命令:比如yes/no,选yes安装;所以要更新(替换)哪个配置文件要先删除再拷贝(构建),文件/文件夹创建也不在Dockerfile中写,
#如果文件/文件夹已存在就会因为不能交互而报错
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
#RUN sh get-docker.sh --mirror Aliyun
 
RUN apt-get install -y apt-transport-https ca-certificates curl software-properties-common && \
    curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add - && \
    add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" && \
	apt-get update -y && \
    apt-get install -y docker-ce
    COPY daemon.json /etc/docker/daemon.json
# 安装 Docker Compose
COPY docker-compose /usr/local/bin/docker-compose
#RUN wget https://raw.githubusercontent.com/topsale/resources/master/docker/docker-compose
RUN chmod +x /usr/local/bin/docker-compose 

# copy配置文件、包和安装Java
COPY jdk-8u152-linux-x64.tar.gz /usr/local/java/jdk-8u152-linux-x64.tar.gz
COPY apache-maven-3.5.3-bin.tar.gz /usr/local/maven/apache-maven-3.5.3-bin.tar.gz
#COPY settings.xml /usr/local/maven/apache-maven-3.5.3/conf/settings.xml #apache-maven-3.5.3-bin.tar.gz解压后才能执行

#RUN mkdir -p /usr/local/java #创建文件不往Dockerfile里面写,如果文件存在不能交互,会报错
WORKDIR /usr/local/java
RUN tar -zxvf jdk-8u152-linux-x64.tar.gz && \
    rm -fr jdk-8u152-linux-x64.tar.gz

# 安装 Maven
#RUN mkdir -p /usr/local/maven
WORKDIR /usr/local/maven
RUN tar -zxvf apache-maven-3.5.3-bin.tar.gz && \
    rm -fr apache-maven-3.5.3-bin.tar.gz
COPY settings.xml /usr/local/maven/apache-maven-3.5.3/conf/settings.xml

# 配置环境变量
#jdk-8u152-linux-x64.tar.gz解压后包名变为jdk1.8.0_171，要特别注意，可以先解压看一下
ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
ENV MAVEN_HOME /usr/local/maven/apache-maven-3.5.3
ENV PATH $PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin
WORKDIR /

def ver_keys = [ 'bash', '-c', 'cd /usr/local/docker/jenkins/data/workspace/spring-eureka;
git pull>/dev/null; git remote prune origin >/dev/null; git tag -l|sort -r |head -10 ' ]
ver_keys.execute().text.tokenize('\n')

echo $RELEASE_VERSION
cd /usr/local/docker/jenkins/data/workspace/spring-eureka
git checkout $RELEASE_VERSION
git pull origin $RELEASE_VERSION
mvn clean install 

**/*.jar,docker/**
spring-eureka

cd /usr/local/docker/jenkins/spring-eureka
cp target/spring-eureka-0.0.1-SNAPSHOT.jar docker
cd docker
docker build -t 192.168.1.8:5000/spring-eureka-v0.0.1 .
docker push 192.168.1.8:5000/spring-eureka-v0.0.1
docker-compose down
docker-compose up -d
docker image prune -f

