---
title: "WSL : Hive on Tez"
date: 2026-04-06
---

# Version
## 
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
