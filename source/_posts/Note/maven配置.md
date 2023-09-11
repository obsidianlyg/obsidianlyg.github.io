---
title: maven基础配置
date: 2022-04-30 13:30:58
tags: maven
---

1. 打开安装的maven的setting.xml
2. 修改仓库位置-localRepository标签(改成自己想改的位置)

3. 在mirrors标签添加

```xml
 <mirror>
      <id>alimaven</id>
      <mirrorOf>central</mirrorOf>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
    </mirror>
    <mirror>
      <id>repo1</id>
      <mirrorOf>central</mirrorOf>
      <name>central repo</name>
      <url>http://repo1.maven.org/maven2/</url>
    </mirror>
    <mirror>
     <id>aliyunmaven</id>
     <mirrorOf>apache snapshots</mirrorOf>
     <name>阿里云阿帕奇仓库</name>
     <url>https://maven.aliyun.com/repository/apache-snapshots</url>
    </mirror>
```

4. 在profiles标签添加

```xml
    <profile>  
          <repositories>
            <repository>
                  <id>aliyunmaven</id>
                  <name>aliyunmaven</name>
                  <url>https://maven.aliyun.com/repository/public</url>
                  <layout>default</layout>
                  <releases>
                          <enabled>true</enabled>
                  </releases>
                  <snapshots>
                          <enabled>true</enabled>
                  </snapshots>
              </repository>
              <repository>
                  <id>MavenCentral</id>
                  <url>http://repo1.maven.org/maven2/</url>
              </repository>
              <repository>
                  <id>aliyunmavenApache</id>
                  <url>https://maven.aliyun.com/repository/apache-snapshots</url>
              </repository>
          </repositories>             
      </profile>
```

