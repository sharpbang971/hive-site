---
title: "WSL : Hive on Tez"
date: 2026-04-06
---


# WSL

## 检查和安装
### 查看已安装的发行版
```javascript
C:\Users\clive> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
```

### 查看可安装的发行版
```javascript

C:\Users\clive>wsl -l -o
以下是可安装的有效分发的列表。
使用“wsl.exe --install <Distro>”安装。

NAME                            FRIENDLY NAME
Ubuntu                          Ubuntu
Ubuntu-24.04                    Ubuntu 24.04 LTS
Ubuntu-22.04                    Ubuntu 22.04 LTS
Ubuntu-20.04                    Ubuntu 20.04 LTS
openSUSE-Tumbleweed             openSUSE Tumbleweed
openSUSE-Leap-16.0              openSUSE Leap 16.0
SUSE-Linux-Enterprise-15-SP7    SUSE Linux Enterprise 15 SP7
SUSE-Linux-Enterprise-16.0      SUSE Linux Enterprise 16.0
kali-linux                      Kali Linux Rolling
Debian                          Debian GNU/Linux
AlmaLinux-8                     AlmaLinux OS 8
AlmaLinux-9                     AlmaLinux OS 9
AlmaLinux-Kitten-10             AlmaLinux OS Kitten 10
AlmaLinux-10                    AlmaLinux OS 10
archlinux                       Arch Linux
FedoraLinux-43                  Fedora Linux 43
FedoraLinux-42                  Fedora Linux 42
eLxr                            eLxr 12.12.0.0 GNU/Linux
OracleLinux_7_9                 Oracle Linux 7.9
OracleLinux_8_10                Oracle Linux 8.10
OracleLinux_9_5                 Oracle Linux 9.5
openSUSE-Leap-15.6              openSUSE Leap 15.6
SUSE-Linux-Enterprise-15-SP6    SUSE Linux Enterprise 15 SP6
```
### 安装特定版本
```javascript
    --install [Distro] [Options...]
        安装适用于 Linux 的 Windows 子系统分发版。
        有关有效分发版的列表，请使用 'wsl.exe --list --online'。

        选项:
            --enable-wsl1
                启用 WSL1 支持。

            --fixed-vhd
                创建固定大小的磁盘来存储分发版。

            --from-file <Path>
                从本地文件安装分发版。

            --legacy
                使用旧分发版清单。

            --location <Location>
                设置分发版的安装路径。

            --name <Name>
                设置分发版的名称。

            --no-distribution
                仅安装所需的可选组件，不安装分发版。

            --no-launch, -n
                安装后不要启动分发版。

            --version <Version>
                指定要用于新分发的版本。

            --vhd-size <MemoryString>
                指定用于存储分发版的磁盘的大小。

            --web-download
                从 Internet 而不是 Microsoft Store 下载分发版。
```
#### example v0
```javascript
PS C:\Users\clive> wsl --install -d OracleLinux_8_10 --web-download
wsl: 使用旧分发注册。请考虑改用基于 tar 的分发。
正在下载: Oracle Linux 8.10
[                           0.2%                           ]

```
#### example v1
```javascript
PS C:\Users\clive> wsl --install --name rocky8v2 --location D:\wsl\rocky8 --from-file D:\wsl\Rocky-8-Container-Base.latest.x86_64.tar.xz
正在安装: D:\wsl\Rocky-8-Container-Base.latest.x86_64.tar.xz
已成功安装分发。可以通过 “wsl.exe -d rocky8v2” 启动它====]
正在启动 rocky8v2...
[root@JINKENZHAO clive]# exit
PS C:\Users\clive> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
  rocky8v2        Running         2
  rocky8          Stopped         2
```
### 其他来源安装
```javascript
    --import <Distro> <InstallLocation> <FileName> [选项]
        将指定的 tar 文件作为新分发版导入。
        文件名可以是 - for stdin。

        选项:
            --version <Version>
                指定要用于新分发的版本。

            --vhd
                指定所提供的文件是 .vhdx 文件，而不是 tar 文件。
                此操作在指定的安装位置创建 .vhdx 文件的副本。
```
#### example v2
```javascript

PS C:\Users\clive> wsl --import rocky8v3 D:\wsl\rocky8v3 D:\wsl\Rocky-8-Container-Base.latest.x86_64.tar.xz
操作成功完成。
PS C:\Users\clive> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
  rocky8v2        Stopped         2
  rocky8          Stopped         2
  rocky8v3        Stopped         2
```
### 运行
```javascript
    --distribution, -d <DistroName>
        运行指定的分发版。

PS C:\Users\clive> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
  rocky8          Stopped         2
PS C:\Users\clive> wsl -d rocky8
[root@JINKENZHAO clive]# logout
PS C:\Users\clive> wsl -d rocky8 --cd ~
[root@JINKENZHAO ~]# logout
PS C:\Users\clive> wsl -d rocky8 -u hadoop --cd ~
<3>WSL (37 - Relay) ERROR: CreateProcessParseCommon:988: getpwnam(hadoop) failed 17
找不到用户。
错误代码: Wsl/WSL_E_USER_NOT_FOUND
PS C:\Users\clive>        
```

## 关闭
### 全局拉闸
```javascript
    --shutdown
        立即终止所有正在运行的分发版和 WSL 2
        轻型实用工具虚拟机。

        选项:
            --force
                即使正在执行操作，也终止 WSL 2 虚拟机。可能导致数据丢失。
```
### 单台关机
```javascript
--terminate, -t <Distro>
        终止指定的分发版。
```
## WSL总结
### WSL 常用命令分类表

该表格将 WSL 命令分为**全局**（影响整个 WSL 环境）和**局部**（针对特定分发版）两大类，方便学习与查阅。


| 分类 | 核心功能 | 常用命令示例 | 影响范围 |
| :--- | :--- | :--- | :--- |
| **全局 (Global)** | **状态查看** | `wsl --status` / `wsl --version` | 查看整体 WSL 运行状态和版本 |
| | **平台更新** | `wsl --update` / `wsl --uninstall` | 升级或彻底卸载 WSL 引擎 |
| | **全域关闭** | `wsl --shutdown` | **立即关闭所有** Linux 系统并释放内存 |
| | **默认配置** | `wsl --set-default-version <1/2>` | 设定以后安装新系统时默认用的内核版本 |
| | **全局挂载** | `wsl --mount` / `wsl --unmount` | 挂载物理/虚拟磁盘到**所有**系统中 |
| | **在线查询** | `wsl --list --online` | 查看云端有哪些可供下载的 Linux 系统 |
| **局部 (Local)** | **运行/进入** | `wsl -d <Distro>` / `wsl -u <User>` | 启动**特定**系统，或以**特定用户**登录 |
| | **生命周期** | `wsl --install` / `wsl --unregister` | **安装**或**彻底销毁**某个具体的系统 |
| | **启停管理** | `wsl --terminate <Distro>` | **强制关掉**指定的某一个 Linux 系统 |
| | **导入导出** | `wsl --export` / `wsl --import` | 备份或从文件恢复**某个**特定系统 |
| | **磁盘管理** | `wsl --manage <Distro> --move` | 移动**该系统**的存储位置或清理磁盘空间 |
| | **版本转换** | `wsl --set-version <Distro> <1/2>` | 修改**这一个**系统的内核版本（1 或 2） |
| | **默认指向** | `wsl --set-default <Distro>` | 设定当你只输入 `wsl` 时**默认进入哪一个** |

---
*注：`<Distro>` 代表分发版名称（如 Ubuntu, rocky8v2），`<User>` 代表 Linux 用户名。*

# RockyLinux

## 安装
- wsl -l -v
```javascript
PS C:\Users\clive> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
```
- wsl --import rocky8  D:\wsl\rocky8 D:\wsl\Rocky-8-Container-Base.latest.x86_64.tar.xz
```javascript
PS C:\Users\clive> wsl --import rocky8  D:\wsl\rocky8 D:\wsl\Rocky-8-Container-Base.latest.x86_64.tar.xz
操作成功完成。
PS C:\Users\clive> wsl -l -v
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
  rocky8          Stopped         2
PS C:\Users\clive> wsl -d rocky8
[root@JINKENZHAO clive]#
```

## 配置
### 镜像源
```javascript
sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.nju.edu.cn/rocky|g' \
    -i.bak /etc/yum.repos.d/Rocky-*.repo

dnf makecache
dnf update -y
```
### WSL
```javascript
# cat /etc/wsl.conf 
[boot]
systemd=true

[network]
hostname = master
generateHosts = true
# cat /etc/hostname 
master
```

## INIT
```javascript
dnf -y install sudo passwd openssh-server vim wget net-tools tar

useradd -m -s /bin/bash jkren
usermod -aG wheel jkren
passwd jkren
visudo /etc/sudoers
jkren   ALL=(ALL)   NOPASSWD: ALL 
```

### Windows
```javascript
wsl --manage rocky8 --set-default-user jkren
```

# Hive on Tez

| Hadoop      | TEZ          | Hive        | JDK  |
| :---------- | :----------- | :---------- | :--- |
| 2.7.2       | 0.9.2        |             |      |
| 3.3.1       | 0.10.2       |             |      |
| 3.3.6       | 0.10.4       | 3.1.3？？   |      |
| ```3.3.6``` | ```0.10.4``` | ```4.0.1``` |      |
| 3.4.1       | 0.10.5       | 4.1.0       | +17  |
| 3.4.1       | 0.10.5       | 4.2.0       | >=21 |

```javascript
wget -c http://datacenter.local:8080/bigdata/jdk/jdk-8u461-linux-x64.tar.gz
wget -c http://datacenter.local:8080/bigdata/hadoop/hadoop-3.3.6.tar.gz
wget -c http://datacenter.local:8080/bigdata/tez/apache-tez-0.10.4-bin.tar.gz
wget -c http://datacenter.local:8080/bigdata/hive/apache-hive-4.0.1-bin.tar.gz
wget -c http://datacenter.local:8080/bigdata/mysql/mysql-connector-j-8.0.33-1.el8.noarch.rpm
```

# Machine

## Firmware
| Role    | CPU  | Mem   | SSD  | hostname | Software |
| :------ | :--- | :---- | :--- | :------- | :------- |
| master  | 16   | 16384 | 80G  | master   | Hive     |
| worker1 | 8    | 8192  | 60G  | worker1  |          |
| worker2 | 8    | 8192  | 60G  | worker2  | MySQL    |

## /etc/hosts
```bash
192.168.100.124 master.local master
192.168.100.125 worker1.local worker1
192.168.100.126 worker2.local worker2
192.168.100.200 datacenter.local datacenter
```
# Hadoop配置

## Role

| Host | Master          | Worker1           | Worker2          |
| :--- | :-------------- | :---------------- | :--------------- |
| HDFS | NameNode        | SecondaryNameNode |                  |
|      | DataNode        | DataNode          | DataNode         |
| YARN | ResourceManager |                   |                  |
|      | NodeManager     | NodeManager       | NodeManager      |
|      | HiveServer2     |                   | JobHistoryServer |


## Prepare 
互通互信
```javascript
[jkren@master hadoop]$ ssh-keygen -t rsa

[jkren@master hadoop]$ ssh-copy-id master
[jkren@master hadoop]$ ssh-copy-id worker1
[jkren@master hadoop]$ ssh-copy-id worker2

[jkren@master hadoop]$ ssh master ls
[jkren@master hadoop]$ ssh worker1 ls
[jkren@master hadoop]$ ssh worker2 ls
```
.bashrc
```javascript
[jkren@master hadoop]$ vim ~/.bashrc 
export JAVA_HOME=/opt/modules/jdk
export HADOOP_HOME=/opt/modules/hadoop
export TEZ_HOME=/opt/modules/tez
export HIVE_HOME=/opt/modules/hive

export PATH=$PATH:$JAVA_HOME/bin
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export PATH=$PATH:$HIVE_HOME/bin
```

## conf
### etc/hadoop/hadoop-env.sh
```javascript
[jkren@master hadoop]$ vim etc/hadoop/hadoop-env.sh 
export JAVA_HOME=/opt/modules/jdk   
export TEZ_HOME=/opt/modules/tez
export TEZ_CONF_DIR=$TEZ_HOME/conf
export TEZ_JARS=$TEZ_HOME
export HADOOP_CLASSPATH=${TEZ_CONF_DIR}:${TEZ_JARS}/*:${TEZ_JARS}/lib/*:${HADOOP_CLASSPATH}:${JAVA_JDBC_LIBS}:${MAPREDUCE_LIBS}

```

### etc/hadoop/core-site.xml 
```javascript
[hadoop@Master hadoop]$ cat etc/hadoop/core-site.xml 
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://master:9000</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/modules/hadoop/tmp</value>
    </property>
    <property>
        <name>hadoop.proxyuser.jkren.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.jkren.groups</name>
        <value>*</value>
    </property>
```

### etc/hadoop/hdfs-site.xml 
```javascript
[hadoop@Master hadoop]$ cat etc/hadoop/hdfs-site.xml 
    <property>
        <name>dfs.replication</name>
        <value>3</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>worker1.local:9868</value>
    </property>
```

### etc/hadoop/mapred-site.xml
```javascript
[hadoop@Master hadoop]$ cat etc/hadoop/mapred-site.xml 
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>worker2.local:10020</value>
    </property>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
    </property>
```

### etc/hadoop/yarn-site.xml 
```javascript
[hadoop@Master hadoop]$ cat etc/hadoop/yarn-site.xml
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property> 
     <property>
         <name>yarn.resourcemanager.hostname</name>
         <value>master</value>
     </property>
     <property>
         <name>yarn.nodemanager.aux-services</name>
         <value>mapreduce_shuffle</value>
     </property>
     <property>
         <name>yarn.nodemanager.env-whitelist</name>
         <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
     </property>

```

### etc/hadoop/workers 
```javascript
[hadoop@Master hadoop]$ cat etc/hadoop/workers 
master
worker1
worker2
```

## JPS

```javascript
[jkren@master hadoop]$ hdfs namenode -format
[jkren@master hadoop]$ start-dfs.sh 
[jkren@master hadoop]$ start-yarn.sh 
[jkren@master hadoop]$ start-yarn.sh 
[jkren@master hadoop]$ ssh worker2 mapred --daemon start historyserver
```
- master
```javascript
[jkren@master hadoop]$ jps
8372 NameNode
10005 Jps
9257 NodeManager
9097 ResourceManager
8526 DataNode
```
- worker1
```javascript
[jkren@worker1 hadoop]$ jps
3088 NodeManager
2787 DataNode
3349 Jps
2919 SecondaryNameNode
```
- worker2
```javascript
[jkren@worker2 hadoop]$ jps
2962 JobHistoryServer
2740 NodeManager
3118 Jps
2543 DataNode
```

## Example
```javascript
[jkren@master hadoop]$ hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.4.1.jar pi 10 1000

[jkren@master hadoop]$ hdfs dfs -mkdir input
[jkren@master hadoop]$ hdfs dfs -put etc/hadoop/*.xml input
[jkren@master hadoop]$ hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.4.1.jar grep input output 'dfs[a-z.]+'
```

# MySQL

## C-J
```javascript
[jkren@master Download]$ jar tf java/mysql-connector-java.jar |grep -i driver
META-INF/services/java.sql.Driver
com/mysql/cj/jdbc/Driver.class
com/mysql/cj/jdbc/NonRegisteringDriver$1.class
com/mysql/cj/jdbc/NonRegisteringDriver.class
com/mysql/jdbc/Driver.class
[jkren@master Download]$ 
```

## Server & Client
### Sevice
```javascript
[hadoop@Worker2 ~]$ sudo yum -y install mysql-server
[hadoop@Worker2 ~]$ sudo systemctl enable mysqld
[hadoop@Worker2 ~]$ sudo systemctl restart mysqld
[hadoop@Worker2 ~]$ sudo systemctl status mysqld

[hadoop@Worker1 ~]$ cat /etc/my.cnf.d/mysql-server.cnf 
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
log-error=/var/log/mysql/mysqld.log
pid-file=/run/mysqld/mysqld.pid

[hadoop@Worker1 ~]$ cat /etc/my.cnf.d/mysql-default-authentication-plugin.cnf 
```
### 修改密码

```javascript
[jkren@worker2 Download]$ mysql -uroot
mysql> select user, host, plugin, authentication_string from mysql.user;
+------------------+-----------+-----------------------+------------------------------------------------------------------------+
| user             | host      | plugin                | authentication_string                                                  |
+------------------+-----------+-----------------------+------------------------------------------------------------------------+
| mysql.infoschema | localhost | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| mysql.session    | localhost | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| mysql.sys        | localhost | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| root             | localhost | mysql_native_password |                                                                        |
+------------------+-----------+-----------------------+------------------------------------------------------------------------+

4 rows in set (0.00 sec)

mysql> alter user 'root'@'localhost' identified by '123456';
Query OK, 0 rows affected (0.00 sec)

mysql> rename user 'root'@'localhost' to 'root'@'%';
Query OK, 0 rows affected (0.01 sec)

mysql> select user, host, plugin, authentication_string from mysql.user;
+------------------+-----------+-----------------------+------------------------------------------------------------------------+
| user             | host      | plugin                | authentication_string                                                  |
+------------------+-----------+-----------------------+------------------------------------------------------------------------+
| root             | %         | mysql_native_password | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9                              |
| mysql.infoschema | localhost | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| mysql.session    | localhost | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| mysql.sys        | localhost | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
+------------------+-----------+-----------------------+------------------------------------------------------------------------+
4 rows in set (0.00 sec)


mysql> FLUSH PRIVILEGES;
mysql> EXIT;
```

### 远程建库
```javascript
[jkren@master Download]$ mysql -uroot -hworker2 -p
Enter password: 
mysql> create database hive;
Query OK, 1 row affected (0.01 sec)
mysql> quit;
Bye
```
# HIVE
## DeployMode
### Embeded
Embedded mode is the default metastore deployment mode for CDH. In this mode, the metastore uses a Derby database, and both the database and the metastore service are embedded in the main HiveServer process. Both are started for you when you start the HiveServer process. This mode requires the least amount of effort to configure, but it can support only one active user at a time and is not certified for production use.
### Local Mode
In Local mode, the Hive metastore service runs in the same process as the main HiveServer process, but the metastore database runs in a separate process, and can be on a separate host. The embedded metastore service communicates with the metastore database over JDBC.
### Remote Mode
In Remote mode, the Hive metastore service runs in its own JVM process. HiveServer2, HCatalog, Cloudera Impala™, and other processes communicate with it using the Thrift network API (configured using the hive.metastore.uris property). The metastore service communicates with the metastore database over JDBC (configured using the javax.jdo.option.ConnectionURL property). The database, the HiveServer process, and the metastore service can all be on the same host, but running the HiveServer process on a separate host provides better availability and scalability.
## Server Config Worker2
```javascript
[jkren@worker1 hive]$ vim conf/hive-log4j2.properties
# list of properties
property.hive.log.level = ALL
property.hive.root.logger = DRFA
property.hive.log.dir = /opt/modules/hive/logs/${sys:user.name}
property.hive.log.file = hive.log
property.hive.perflogger.log.level = INFO
```

```javascript
[hadoop@Worker2 hive]$ cat conf/hive-site.xml 
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>123456</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://master.local:3306/hive</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.cj.jdbc.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
    </property>
    <property>
        <name>hive.execution.engine</name>
        <value>tez</value>
    </property>
    <property>
        <name>tez.use.cluster.hadoop-libs</name>
        <value>true</value>
    </property>

    <property>
        <name>hive.security.authorization.enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>hive.tez.container.size</name>
        <value>1024</value>
    </property>
    <property>
        <name>hive.tez.cpu.vcores</name>
        <value>1</value>
    </property>
</configuration>
```
## Server Command
```javascript
# 0. 初始化MYSQL 
schematool -initSchema -dbType mysql -verbose

# 1. 修正语法，并把日志重定向到一个指定文件，防止干扰终端
nohup hive --service hiveserver2 > $HIVE_HOME/hiveserver2.log 2>&1 &

# 2. 开启9083
nohup hive --service metastore > $HIVE_HOME/metastore.log 2>&1 &
```
# Tez
```javascript
[jkren@master tez]$ cat conf/tez-site.xml 
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
  <property>
    <name>tez.lib.uris</name>
    <value>${fs.defaultFS}/share/tez.tar.gz</value>
  </property>
</configuration>

[jkren@master tez]$ hdfs dfs put -put share /
```
