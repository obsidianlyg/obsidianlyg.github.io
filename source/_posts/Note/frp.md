---
title: frp的v2版本配置
date: 2024-8-30 13:50:39
tags: Linux
---

## **服务端配置**

1. **下载**
    
    [下载地址](https://github.com/fatedier/frp/releases)
    
2. **解压**
    1. `tar -zxvf frp_0.60.0_linux_amd64.tar.gz`
3. **进入解压目录并打开配置文件**
    1. `vim frps.toml`
    2. 修改内容如下
    
    ```bash
    bindPort = 7000
    # 看板
    webServer.addr = "0.0.0.0"
    webServer.port = 7500
    webServer.user = "admin"
    webServer.password = "admin"
    # 加入http支持
    vhostHTTPPort = 8080
    # 设置加密
    auth.method = "token"
    auth.token = "123456"
    # 主域名-不推荐-因为配置nginx后仍然需要加本地映射端口才能访问
    # 如果不加主域名，直接fprc映射，nginx反而可以80端口直接访问
    subDomainHost = "yourdomain.top"
    ```
    
4. **设置和启动frp服务**
    
    ```bash
    sudo mkdir -p /etc/frp
    sudo cp frps.toml /etc/frp
    sudo cp frps /usr/bin
    # v2版本没有这个文件需要自己创建
    sudo cp frps.service /usr/lib/systemd/system/
    sudo systemctl enable frps
    sudo systemctl start frps
    ```
    
5. **防火墙开放端口(云服务器是外部开启)**
    
    ```bash
    # 添加监听端口
    sudo firewall-cmd --permanent --add-port=7000/tcp
    # 添加管理后台端口
    sudo firewall-cmd --permanent --add-port=7500/tcp
    sudo firewall-cmd --reload
    ```
    
    

## 客户端配置

1. **步骤1,2与服务端相同**
2. **进入解压目录并打开配置文件**
    1. `vim frpc.toml`
    
    ```bash
    serverAddr = "x.x.x.x"
    serverPort = 7000
    auth.method = "token"
    auth.token = "123456"
    # 看板
    webServer.addr = "127.0.0.1"
    webServer.port = 7400
    webServer.user = "admin"
    webServer.password = "admin"
    # 基础配置
    [[proxies]]
    name = "test-tcp"
    type = "tcp"
    localIP = "127.0.0.1"
    localPort = 8091
    remotePort = 7100
    # http配置
    # 如果frps配置主域名则只需要写前缀，此时只需写入test
    [[proxies]]
    name = "web"
    type = "http"
    localPort = 8080
    customDomains = ["test.yourdomain.top"]
    ```
    
3. **防火墙开放端口**
    
    ```bash
    sudo firewall-cmd --permanent --add-port=7400/tcp
    sudo firewall-cmd --permanent --add-port=8091/tcp
    sudo firewall-cmd --reload
    ```
    
4. **客户端启动**
    1. `./frpc -c frpc.toml`

## 新特性

### **支持子文件**

引入了类似nginx的include，可以包含子文件

```bash
# frpc.toml
serverAddr = "x.x.x.x"
serverPort = 7000
includes = ["./confd/*.toml"]
```

子文件

```bash
# ./confd/test.toml
[[proxies]]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22
remotePort = 6000
```

> 如果更改子文件配置，记得使用reload进行重新加载
>
> `frpc reload -c frpc.toml` 或 `systemctl reload frpc`
>
> 重载后记得查看是否出现`reload success` 没有出现则说明配置出问题了。

### 支持web端动态在线配置、热更新配置功能

需要在配置中先加入控制面板

```bash
# 如果需要开放外网访问改为0.0.0.0并添加映射端口配置
webServer.addr = "127.0.0.1"
webServer.port = 7400
webServer.user = "admin"
webServer.password = "admin"
```

![](https://raw.githubusercontent.com/obsidianlyg/gallery/master/image/20240830140022.png)

> 在配置界面修改完成后，点击upload上传即可进行重新加载并写入文件
> 
> 目前没有找到查看子文件配置的方法
> 

## 域名映射的nginx配置

```
 server {
     listen      80;
     server_name test.yourdomain.top;
     location / {
         proxy_set_header Host $host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_redirect http://$host/ http://$http_host/;
         proxy_pass http://localhost:8080;
     }
 }
```

## systemd配置

### 服务端

**文件名 frps.service**

```bash
[Unit]
Description=Frp Server Service
After=network.target

[Service]
Type=idle
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frps -c /etc/frp/frps.toml
ExecReload=/usr/bin/frps reload -c /etc/frp/frps.toml

[Install]
WantedBy=multi-user.target
```

### 客户端

**文件名 frpc.service**

```bash
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=idle
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frpc -c /etc/frp/frpc.toml
ExecReload=/usr/bin/frpc reload -c /etc/frp/frpc.toml

[Install]
WantedBy=multi-user.target
```

如果你有多个服务器，或者不想采用子文件配置，可以采用旧版方案，进行多个systemd进行管理,配置如下：

**文件名 frpc@.service**

```bash
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=idle
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frpc -c /etc/frp/%i.toml
ExecReload=/usr/bin/frpc reload -c /etc/frp/%i.toml

[Install]
WantedBy=multi-user.target
```

其中%i代表配置文件名<name>，使用方法如下：

```bash
systemctl start frpc@<name1>
systemctl start frpc@<name2>
```