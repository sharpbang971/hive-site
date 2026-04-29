---
title: "Hive : Educoder"
date: 2026-04-14
---

```javascript
cd /opt
tar -zxvf apache-hive-3.1.0-bin.tar.gz
ln -sfvn apache-hive-3.1.0-bin hive

cat >> ~/.bashrc << EOF
export HIVE_HOME=/opt/hive
export PATH=\$HIVE_HOME/bin:\$PATH
EOF

source ~/.bashrc
rm /opt/hive/lib/log4j-slf4j-impl-2.10.0.jar
hive --version

apt -y update
apt -y install mysql-server   #安装mysql服务
apt -y install mysql-client        #安装mysql客户端
apt -y install libmysqlclient-dev  #安装相关依赖环境

tar -zxvf mysql-connector-java-5.1.45.tar.gz
cd mysql-connector-java-5.1.45
cp mysql-connector-java-5.1.45-bin.jar /opt/hive/lib/

mysql -uroot -p123123 -h127.0.0.1
create database hiveDB;
create user 'bee'@'localhost' identified by '123123';
grant all privileges on hiveDB.* to 'bee'@'localhost' identified by '123123';
flush privileges;

cat > /opt/hive/conf/hive-site.xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<!-- WARNING!!! This file is auto generated for documentation purposes ONLY! -->
<!-- WARNING!!! Any changes you make to this file will be ignored by Hive.   -->
<!-- WARNING!!! You must make your changes in hive-site.xml instead.         -->
<!-- Hive Execution Parameters -->
 <property>
 <name>hive.metastore.warehouse.dir</name>
 <value>/opt/hive/warehouse</value>
 </property>
 <property>
 <name>hive.exec.scratchdir</name>
 <value>/opt/hive/tmp</value>
 </property>
 <property>
 <name>hive.querylog.location</name>
 <value>/opt/hive/logs</value>
 </property>
 <property>
 <name>hive.server2.thrift.port</name>
 <value>10000</value>
 </property>
 <property>
 <name>hive.server2.thrift.bind.host</name>
 <value>localhost</value>
 </property>
 <property>
 <name>hive.server2.enable.doAs</name>
 <value>true</value>
 </property>
 <property>
 <name>hive.session.id</name>
 <value>false</value>
 </property>
 <property>
 <name>hive.session.silent</name>
 <value>false</value>
 </property>
 <property>
 <name>javax.jdo.option.ConnectionURL</name>
 <value>jdbc:mysql://localhost:3306/hiveDB?createDatabaseIfNotExist=true</value>
 </property>
 <property>
 <name>javax.jdo.option.ConnectionDriverName</name>
 <value>com.mysql.jdbc.Driver</value>
 </property>
 <property>
 <name>javax.jdo.option.ConnectionUserName</name>
 <value>bee</value>
 <!-- 这里是之前设置的数据库 -->
 </property>
 <property>
 <name>javax.jdo.option.ConnectionPassword</name>
 <!-- 这里是数据库密码 -->
 <value>123123</value>
 </property>
</configuration>


cp conf/hive-env.sh.template conf/hive-env.sh
cat > conf/hive-env.sh <<EOF
HADOOP_HOME=/usr/local/hadoop  #在本地环境安装，要根据自己hadoop的路径来确定
EOF

schematool -dbType mysql -initSchema
```