---
title: Linux备份（Rsnapshot）
date: 2022-07-04 09:04:52
tags: Linux
---
# Linux备份（Rsnapshot）

[参考链接](https://www.txuw.top/article/rsnapshot)

[参考链接2](https://blog.csdn.net/chuyanwen8507/article/details/100906990)

### 安装

`apt intstall rsnapshot rsync`

### 修改配置文件

`vim /etc/rsnapshot.conf`

> 特别注意配置文件对tab有着严格的要求，建议复制内部自带的tab进行编写
> 
- 编写配置
    - `snapshot_root` 指定一个备份目录
    - `cmd_ssh`允许远程备份（直接取消注释即可）
    - 指定备份保留数量
      
        如下所示，其中的`hourly`参数是名称，后面数值对应备份保留数量
        
        对于备份，采用的是增量备份，无需担心过多的频率带来的额外空间开销
        
        ```bash
        #########################################
        #           BACKUP INTERVALS            #
        # Must be unique and in ascending order #
        # i.e. alpha, beta, gamma, etc.      #
        #########################################
        retain	hourly	6
        retain	daily	5
        retain	weekly	3
        retain	monthly	2
        ```
        
    - 指定当前服务器的SSH端口
      
        若更改了SSH默认端口，需要指定远程备份服务器的端口号，否则不变即可
        
        `ssh_args -p xxxx`
        
    - 备份目录
      
        对于本地备份，直接指定路径即可
        
        > 注意最后要添加/进行路径闭合，不然语法检查不通过
        > 
        
        ```bash
        # 本地备份路径
        backup        /       localhost/
        backup        /etc/       localhost/
        # 远程备份
        backup         root@example.com:/home/         /data/backup/
        # 排除不需要的目录，多个可用逗号隔开
        backup        /       localhost/       exclude=/mnt1,exclude=/mnt2,exclude=/mnt3
        ```
        
    - 配置概览
      
        ```bash
        config_version	1.2
        snapshot_root	/mnt3/back_up/
        cmd_cp		/bin/cp
        cmd_rm		/bin/rm
        cmd_rsync	/usr/bin/rsync
        cmd_logger	/usr/bin/logger
        retain	hourly	6
        retain	daily	5
        retain	weekly	3
        retain	monthly	2
        verbose		2
        loglevel	3
        lockfile	/var/run/rsnapshot.pid
        backup	/		localhost/	exclude=/mnt1,exclude=/mnt2,exclude=/mnt3
        ```
        
    - 参数解释
        1. `config_version 1.2`= 配置文件版本
        2. `snapshot_root`= 存储快照的备份目的地
        3. `cmd_cp`= 复制命令的路径
        4. `cmd_rm`= 删除命令的路径
        5. `cmd_rsync`= rsync 的路径
        6. `cmd_ssh`= SSH 路径
        7. `cmd_logger`= shell 命令接口到 syslog 的路径
        8. `cmd_du`= 磁盘使用命令的路径
        9. `interval hourly`= 要保留多少小时的备份。
        10. `interval daily`= 要保留多少每日备份。
        11. `interval weekly`= 要保留多少每周备份。
        12. `interval monthly`= 每月要保留多少备份。
        13. `ssh_args`= 可选的 SSH 参数，例如不同的端口 (-p)
        14. `verbose`= verbose
        15. `loglevel`= loglevel
        16. `logfile`= 日志文件的路径
        17. `exclude_file`= 排除文件的路径（将更详细地解释）
        18. `rsync_long_args`= 传递给 rsync 的长参数
        19. `lockfile`= lockfile
        20. `backup`= 要备份的内容的完整路径，然后是放置的相对路径。
        

对配置文件的语法测试

`rsnapshot configtest`

测试当前配置，但不使其生效

rsnapshot -t hourly

### 执行备份

`rsnapshot hourly`

### **自动化流程**

使流程自动化，你需要安排rsnapshot以一定的时间间隔运行Cron.默认情况下，rsnapshot自带cron文件在 `/etc/cron.d/rsnapshot`
，如果它不存在，则创建一个并向其添加以下几行。

```bash
# This is a sample cron file for rsnapshot.
# The values used correspond to the examples in /etc/rsnapshot.conf.
# There you can also set the backup points and many other things.
#
# To activate this cron file you have to uncomment the lines below.
# Feel free to adapt it to your needs.
0     */12    * * *    root    /usr/bin/rsnapshot hourly
30     3     * * *    root    /usr/bin/rsnapshot daily
15     3     * * 1    root    /usr/bin/rsnapshot weekly
30     2     1 * *    root    /usr/bin/rsnapshot monthly
```

> 实际上可以直接输入`crontab -e` 之后直接编辑即可