# 安装Java(1.8)

sudo yum install java-1.8.0-openjdk

配置Java环境

export JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre"

# 安装Hadoop(2.8.5)

wget http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.8.5/hadoop-2.8.5.tar.gz
tar -xzf hadoop-2.8.5.tar.gz
mv hadoop-2.8.5 ~/hadoop-2.8.5

export PATH="$PATH:$HOME/hadoop-2.8.5/bin"

# 配置Hadoop
验证：hdfs namenode -format
目录：hadoop-2.8.5/etc/

hadoop-env.sh

```
export JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre"
```

core-site.xml
```
<configuration>
   <property> 
      <name>fs.default.name</name> 
      <value>hdfs://localhost:9000</value> 
   </property>
   
</configuration>
```


hdfs-site.xml
```
<configuration>
   <property> 
      <name>dfs.replication</name> 
      <value>1</value> 
   </property> 
   <property> 
      <name>dfs.name.dir</name> 
      <value>file:///home/hadoop/hadoopinfra/hdfs/namenode</value> 
   </property> 
   <property> 
      <name>dfs.data.dir</name>
      <value>file:///home/hadoop/hadoopinfra/hdfs/datanode</value> 
   </property>
</configuration>
```

yarn-site.xml
```
<configuration>
   <property> 
      <name>yarn.nodemanager.aux-services</name> 
      <value>mapreduce_shuffle</value> 
   </property>
</configuration>
```

mapred-site.xml
```
<configuration>
   <property> 
      <name>mapreduce.framework.name</name> 
      <value>yarn</value> 
   </property>
</configuration>
```

# 启动Hadoop

## 启动HDFS

hadoop-2.8.5/sbin/hadoop-env.sh

分别启动namenode、datanode、备用namenode

## 启动yarn

start-yarn.sh

默认端口是50070


# 安装Hive
http://www.apache.org/dyn/closer.cgi/hive/

wget http://mirror.bit.edu.cn/apache/hive/hive-3.1.0/apache-hive-3.1.0-bin.tar.gz
tar -xzf apache-hive-3.1.0-bin.tar.gz
mv apache-hive-3.1.0-bin ~/.hadoop/hive-3.1.0

配置Hadoop根目录： hive-3.1.0/conf/hive-env.sh

export HADOOP_HOME=$HOME/.hadoop/hadoop-2.5.8

## 安装 Derby
https://db.apache.org/derby/derby_downloads.html

wget http://archive.apache.org/dist/db/derby/db-derby-10.12.1.1/db-derby-10.12.1.1-bin.tar.gz
tar xzf db-derby-10.12.1.1-bin.tar.gz

目录：/home/zengbin/.hadoop/derby-10.12.1.1

export DERBY_HOME="derby-10.12.1.1"
export PATH=$PATH:$DERBY_HOME/bin

mkdir $DERBY_HOME/data

## 配置 Metastore

hive-3.1.0/conf/hive-default.xml.template -> hive-site.xml

1. 配置Derby
2. 修正java.net.URISyntaxException(system:java.io.tmpdir)

```
<property>
   <name>javax.jdo.option.ConnectionURL</name>
   <value>jdbc:derby://localhost:1527/metastore_db;create=true </value>
   <description>JDBC connect string for a JDBC metastore </description>
</property>
 <property>
    <name>system:java.io.tmpdir</name>
    <value>/tmp/${user.name}/java</value>
  </property>
  <property>
    <name>system:user.name</name>
    <value>${user.name}</value>
  </property>
```
3. 初始化表：修正org.datanucleus.store.rdbms.exceptions.MissingTableException

bin/schematool -dbType derby -initSchema


# 安装hbase

https://hbase.apache.org/downloads.html

wget http://mirrors.shu.edu.cn/apache/hbase/2.1.0/hbase-2.1.0-bin.tar.gz


