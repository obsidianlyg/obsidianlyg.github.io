---
title: Jenkins安装及使用
date: 2022-04-30 12:54:43
tags: Java
---

# Jenkins安装及使用

ubuntuserver安装步骤

## 前置要求

### 安装java和maven

### 下载jenkins

```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```

### 需要有jdk镜像

构建最小jdk镜像

[jre8下载地址](https://www.java.com/en/download/manual.jsp)

选择  Linux x64filesize

解压文件

```bash
tar -xzvf jre-8u181-linux-x64.tar.gz
```

删除无用的文件

```bash
cd jre1.8.0_181/
rm -rf COPYRIGHT LICENSE README release THIRDPARTYLICENSEREADME-JAVAFX.txt THIRDPARTYLICENSEREADME.txt Welcome.html
rm -rf  lib/plugin.jar \
        lib/ext/jfxrt.jar \
        bin/javaws \
        lib/javaws.jar \
        lib/desktop \
        plugin \
        lib/deploy* \
        lib/*javafx* \
        lib/*jfx* \
        lib/amd64/libdecora_sse.so \
        lib/amd64/libprism_*.so \
        lib/amd64/libfxplugins.so \
        lib/amd64/libglass.so \
        lib/amd64/libgstreamer-lite.so \
        lib/amd64/libjavafx*.so \
        lib/amd64/libjfx*.so
```

重新打包

`tar zcvf jre8.tar.gz *`

新建Dockerfile

```bash
# using alpine-glibc instead of alpine  is mainly because JDK relies on glibc
FROM docker.io/jeanblanchard/alpine-glibc
# author
MAINTAINER simon
# A streamlined jre
ADD jre8.tar.gz /usr/java/jdk/
# set env
ENV JAVA_HOME /usr/java/jdk
ENV PATH ${PATH}:${JAVA_HOME}/bin
# run container with base path:/opt
WORKDIR /opt
```

构建docker镜像(Dockerfile与压缩包在同一目录下)

`docker build -t jdk8 .`

java中pom文件修改,指定jdk8镜像

```xml
<plugin>
  <groupId>com.spotify</groupId>
  <artifactId>docker-maven-plugin</artifactId>
  <version>1.0.0</version>
  <configuration>
    <imageName>ibase/${project.artifactId}:${project.version}</imageName>
    <!--<dockerDirectory>src/main/docker</dockerDirectory>-->
    <forceTags>true</forceTags>
    <baseImage>jdk8</baseImage>
    <entryPoint>["java","-jar","/${project.build.finalName}.jar"]</entryPoint>
    <resources>
      <resource>
        <targetPath>/</targetPath>
        <directory>${project.build.directory}</directory>
        <include>${project.build.finalName}.jar</include>
      </resource>
    </resources>
  </configuration>
</plugin>
```

另一种写法(这是我用过的)

```xml
<build>
      <finalName>lwapp-config</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>1.2.2</version>
                <configuration>
                    <!-- 安装docker的主机的地址 -->
                    <imageName>192.168.98.205:5092/${project.artifactId}:${project.version}</imageName>
                    <baseImage>jdk8</baseImage>
                    <entryPoint>["java","-jar","/${project.build.finalName}.jar"]</entryPoint>
                    <!--<dockerCertPath>/etc/ca/docker-ca</dockerCertPath>-->
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                    <!-- 安装docker的主机的地址 -->
                    <dockerHost>http://192.168.98.205:2375</dockerHost>  
                </configuration>
            </plugin>
        </plugins>
    </build>
```

### 安装私有docker仓库

`docker run -di --name=myregistry -p 5092:5000 registry`

尝试访问registry容器 ：[http://10.62.17.101:10092/v2/_catalog](http://10.62.17.101:10092/v2/_catalog)

想要上传还需要docker开放2375端口

`vi /usr/lib/systemd/system/docker.service`

进入后修改

```shell
# 注释掉或删除这句话
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
# 加入下面这句
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock -H fd:// --containerd=/run/containerd/containerd.sock
```



### 让虚拟机识别到更改的仓库

在/etc/docker/daemon.json添加

`"insecure-registries":["192.168.98.205:5092"]`

重新加载docker和daemon

```bash
systemctl daemon-reload
systemctl restart docker
```

修改要上传的标签并上传

```bash
docker tag jdk8 192.168.98.205:5092/jdk8
docker push 192.168.98.205:5092/jdk8
```

### (可选项)安装gogs

```bash
docker run -d -p 20022:22 -p 23000:3000 -v /root/gogs_data:/data gogs/gogs
```

注：将自己的代码上传到gogs或者其他的git仓库中，Jenkins会根据这个仓库地址的代码进行生成镜像

## Jenkins的使用

1. 安装好后Jenkins默认启动了8080端口，输入网址进入需要输入密钥

密钥可以使用 `systemctl status jenkins` 查看

或者 `cat /var/lib/jenkins/secrets/initialAdminPassword`

2. 进入后会有两个选项可以不用选直接点击右上角关闭
3. 主界面侧边栏找到系统管理

![image-20220430125903251](https://i.imgtg.com/2023/06/19/OTGTcC.png)

1. 点击插件管理![image-20220430125956038](https://i.imgtg.com/2023/06/19/OTGWOL.png)

2. 选择可选插件搜索想要的插件选中后点击install（安装 Git plugin 和 Maven Integration plugin）

![image-20220430130423000](https://i.imgtg.com/2023/06/19/OTGbNi.png)

6. 退出插件管理进入全局工具配置
7. 配置maven的setting文件的路径和jdk的路径(jdk可以不用改使用默认的)，配置完成点击保存即可

![image-20220430130900088](https://i.imgtg.com/2023/06/19/OTGkLX.png)

8. 返回主页点击新建任务，输入名称选择-构建一个maven项目，点击确定
9. 源码管理选择Git，输入之前上传仓库的clone地址
10. (之前的都不用动)Build的RootPom选择git仓库中的位置。(在表面则不用改，文件夹内部则为：文件夹名/pom.xml)
11. Goals and options根据需求填写，一般为 `clean package docker:build -DpushImage`
12. 点击保存后返回主界面点击三角开关即可，成功则显示绿色√，否则为红色×

![image-20220430131609017](https://i.imgtg.com/2023/06/19/OTGxut.png)