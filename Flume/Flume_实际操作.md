# Flume_实际操作
## 01：监听文件内容

**监听 `/tmp/log.txt` 文件内的内容，并输出到控制台。**

- 配置 `exec-memory-logger.properties`

```porperties
#指定agent的sources,sinks,channels
# 监听log.txt 文件，有数据的时候，输出到界面
a1.sources = s1  
a1.sinks = k1  
a1.channels = c1  
   
#配置sources属性
a1.sources.s1.type = exec
a1.sources.s1.command = tail -F /tmp/log.txt
a1.sources.s1.shell = /bin/bash -c

#将sources与channels进行绑定
a1.sources.s1.channels = c1
   
#配置sink 
a1.sinks.k1.type = logger

#将sinks与channels进行绑定  
a1.sinks.k1.channel = c1  
   
#配置channel类型
a1.channels.c1.type = memory
```
- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/exec-memory-logger.properties \
--name a1 \
-Dflume.root.logger=INFO,console
```
然后向 `/tmp/log.txt` 文件写入内容即可在控制台看到对应的文件。

## 02：监听文件内容，并将新增的数据写入到 Kafka 中

**监听 `/tmp/kafka.log` 文件内的内容，并写入到 Kafka 的主题。**

- 配置 `exec-memory-kafka.properties`

```porperties
# 监听 kafka.log 文件，有数据的时候，保存到 Kafka
a1.sources = s1
a1.channels = c1
a1.sinks = k1                                                                                         

a1.sources.s1.type=exec
a1.sources.s1.command=tail -F /tmp/kafka.log
a1.sources.s1.channels=c1 

#设置Kafka接收器
a1.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink
#设置Kafka地址
a1.sinks.k1.brokerList=master01:9092
#设置发送到Kafka上的主题
a1.sinks.k1.topic=flume-kafka
#设置序列化方式
a1.sinks.k1.serializer.class=kafka.serializer.StringEncoder
a1.sinks.k1.channel=c1     

a1.channels.c1.type=memory
a1.channels.c1.capacity=10000
a1.channels.c1.transactionCapacity=100   
```

- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/exec-memory-kafka.properties \
--name a1 -Dflume.root.logger=INFO,console
```

- 操作：启动 Kafka

```xshell
# 启动kafka
bin/kafka-server-start.sh -daemon config/server.properties
```

- 操作：创建 Kafka主题

```xshell
# 创建 Kafka主题
bin/kafka-topics.sh --zookeeper master01:2181  --create --replication-factor 1 --partitions 1 --topic flume-kafka
```

- 操作：启动 Kafka 消费者

```xshell
# 启动 Kafka 消费者
bin/kafka-console-consumer.sh --bootstrap-server master01:9092 --topic flume-kafka
```

然后向 `/tmp/kafka.log` 文件写入内容即可在控制台看到对应的文件。

- 解释说明：如果不创建 `Kafka` 主题，提交配置文件的时候会自动创建。

## 03：监听指定文件下的新增文件到 `HDFS` 中

**监听 `/tmp/logs` 文件夹内的文件并存储到 `HDFS` 中**

- 配置：`spooling-memory-hdfs.properties`

```porperties
#指定agent的sources,sinks,channels
# 监听 /tmp/logs 文件夹，有文件的时候，把文件保存到 HDFS
a1.sources = s1  
a1.sinks = k1  
a1.channels = c1  
   
#配置sources属性
a1.sources.s1.type = spooldir  
a1.sources.s1.spoolDir = /tmp/logs
a1.sources.s1.basenameHeader = true
a1.sources.s1.basenameHeaderKey = fileName 
#将sources与channels进行绑定  
a1.sources.s1.channels =c1 

   
#配置sink 
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H/
a1.sinks.k1.hdfs.filePrefix = %{fileName}
#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本
a1.sinks.k1.hdfs.fileType = DataStream  
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#将sinks与channels进行绑定  
a1.sinks.k1.channel = c1
   
#配置channel类型
a1.channels.c1.type = memory
```

- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/spooling-memory-hdfs.properties \
--name a1 -Dflume.root.logger=INFO,console
```

然后复制对应的文件到 `/tmp/logs` 目录中，该文件就会被存储到 `HDFS` 中。

- 解释说明：`logs` 文件夹需要新建，如果不存在，提交配置项的时候会报错。

## 04：监听指定文件下的新增文件到 `Kafka` 中

**监听 `/tmp/logs` 文件夹内的文件并存储到 `Kafka` 中**

- 配置：`spooling-memory-kafka.properties`

```poeperties
# 监听 /tmp/logs 文件夹，保存到 kafka 
a1.sources = s1
a1.channels = c1
a1.sinks = k1                                                                                         

#配置sources属性
a1.sources.s1.type = spooldir  
a1.sources.s1.spoolDir = /tmp/logs
a1.sources.s1.basenameHeader = true
a1.sources.s1.basenameHeaderKey = fileName 
#将sources与channels进行绑定  
a1.sources.s1.channels =c1 

#设置Kafka接收器
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
#设置Kafka地址
a1.sinks.k1.brokerList = master01:9092
#设置发送到Kafka上的主题
a1.sinks.k1.topic = flume-logs-kafka
#设置序列化方式
a1.sinks.k1.serializer.class = kafka.serializer.StringEncoder
a1.sinks.k1.channel = c1     

a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100   
```

- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/spooling-memory-kafka.properties \
--name a1 -Dflume.root.logger=INFO,console
```

启动 `Kafka` 消费者，然后复制对应的文件到 `/tmp/logs` 目录中，就会在 `Kafka` 消费者的输出界面输出对应的文件内容。

- 解释说明：`logs` 文件夹需要新建，如果不存在，提交配置项的时候会报错。

## 05：采集 `Mysql` 的数据到控制台

**采集 `Mysql` 里的数据并输出到控制台**

- 配置：`flume-memory-mysql-log.properties`

```properties
# 监听 Mysql 数据，到界面输出。
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = org.keedio.flume.source.SQLSource
a1.sources.r1.hibernate.connection.url = jdbc:mysql://192.168.0.104:3306/taotao
a1.sources.r1.hibernate.connection.user = root  
a1.sources.r1.hibernate.connection.password = oadat
a1.sources.r1.hibernate.connection.autocommit = true
a1.sources.r1.hibernate.connection.driver_class = com.mysql.jdbc.Driver
a1.sources.r1.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
a1.sources.r1.run.query.delay = 5000
a1.sources.r1.status.file.path = /home/flume/flume/mysql
a1.sources.r1.status.file.name = r1.status
a1.sources.r1.table = tb_item_cat
a1.sources.r1.columns.to.select = *
# a1.sources.r1.incremental.column.name=name
# a1.sources.r1.incremental.value=0
# a1.sources.r1.custom.query = select name from tb_item_cat 
# where id > $@$ order by id 注意加了此段SQL会报错：SQL语句异常。
# a1.sources.r1.start.from = 0 
# a1.sources.r1.hibernate.connection.provider_class = org.hibernate.connection.C3P0ConnectionProvider

a1.sinks.k1.type = logger

# Describe the channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

- 操作：将对应的包复制到 `Flume` 的 `lib` 文件夹下

> 需要将包 `mysql-connector-java-5.1.6.jar` 和 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar` 复制到对应的 `Flume` 的 `lib` 包下。
> 包 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar`为 [github-flume-mysql](https://github.com/keedio/flume-ng-sql-source) 下载后通过 `Maven` 命令编译生成的。

- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/flume-memory-mysql-log.properties \
--name a1 -Dflume.root.logger=INFO,console
```

- 操作：在对应的数据库内新增数据

>在数据库 `taotao` 的表 `tb_item_cat` 插入对应的数据即可在界面看到采集到的数据。

## 06：采集 `Mysql` 的数据到 `HDFS` 中

**采集 `Mysql` 里的数据并存储到 `HDFS` 中**

- 配置：`flume-memory-mysql-hdfs.properties`

```properties
# 采集 Mysql的数据到 HDFS
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = org.keedio.flume.source.SQLSource
a1.sources.r1.hibernate.connection.url = jdbc:mysql://192.168.0.104:3306/taotao
a1.sources.r1.hibernate.connection.user = root  
a1.sources.r1.hibernate.connection.password = oadat
a1.sources.r1.hibernate.connection.autocommit = true
a1.sources.r1.hibernate.connection.driver_class = com.mysql.jdbc.Driver
a1.sources.r1.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
a1.sources.r1.run.query.delay = 5000
a1.sources.r1.status.file.path = /home/flume/flume/mysql/hdfs
a1.sources.r1.status.file.name = r1.status
a1.sources.r1.table = tb_item_cat
a1.sources.r1.columns.to.select = *

#配置sink 
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://master01:9000/mysql/event/%y-%m-%d/%H/
# a1.sinks.k1.hdfs.filePrefix = %{fileName}
#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本
a1.sinks.k1.hdfs.fileType = DataStream 
a1.sinks.k1.hdfs.writeFormat = Text 
# a1.sinks.k1.hdfs.useLocalTimeStamp = true

# Describe the channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

```

- 操作：将对应的包复制到 `Flume` 的 `lib` 文件夹下

> 需要将包 `mysql-connector-java-5.1.6.jar` 和 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar` 复制到对应的 `Flume` 的 `lib` 包下。
> 包 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar`为 [github-flume-mysql](https://github.com/keedio/flume-ng-sql-source) 下载后通过 `Maven` 命令编译生成的。

- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/flume-memory-mysql-hdfs.properties \
--name a1 -Dflume.root.logger=INFO,console
```

- 操作：在对应的数据库内新增数据

>在数据库 `taotao` 的表 `tb_item_cat` 插入对应的数据即可。

## 07：采集 `Mysql` 的数据到 `Kafka` 中

**采集 `Mysql` 里的数据并存储到 `Kafka` 中**

- 配置：`flume-memory-mysql-kafka.properties`

```properties
# 采集 Mysql 的数据，保存到 Kafka
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = org.keedio.flume.source.SQLSource
a1.sources.r1.hibernate.connection.url = jdbc:mysql://192.168.0.104:3306/taotao
a1.sources.r1.hibernate.connection.user = root  
a1.sources.r1.hibernate.connection.password = oadat
a1.sources.r1.hibernate.connection.autocommit = true
a1.sources.r1.hibernate.connection.driver_class = com.mysql.jdbc.Driver
a1.sources.r1.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
a1.sources.r1.run.query.delay = 5000
a1.sources.r1.status.file.path = /home/flume/flume/mysql
a1.sources.r1.status.file.name = r1.status
a1.sources.r1.table=tb_item_cat
a1.sources.r1.columns.to.select=*

#设置Kafka接收器
a1.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink
#设置Kafka地址
a1.sinks.k1.brokerList=master01:9092
#设置发送到Kafka上的主题
a1.sinks.k1.topic=flume-mysql-kafka
#设置序列化方式
a1.sinks.k1.serializer.class=kafka.serializer.StringEncoder


# Describe the channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

```

- 操作：将对应的包复制到 `Flume` 的 `lib` 文件夹下

> 需要将包 `mysql-connector-java-5.1.6.jar` 和 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar` 复制到对应的 `Flume` 的 `lib` 包下。
> 包 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar`为 [github-flume-mysql](https://github.com/keedio/flume-ng-sql-source) 下载后通过 `Maven` 命令编译生成的。

- 操作：启动 `Kafka` 消费者

```xshell
bin/kafka-console-consumer.sh --bootstrap-server master01:9092 --topic flume-mysql-kafka
```
- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/flume-memory-mysql-kafka.properties \
--name a1 -Dflume.root.logger=INFO,console
```

- 操作：在对应的数据库内新增数据

>在数据库 `taotao` 的表 `tb_item_cat` 插入对应的数据即可。

## 08：采集 `Mysql` 的数据分别到 `HDFS`和 `Kafka` 中

**采集 `Mysql` 内的数据分别到 `HDFS` 和 `Kafka` 中**

- 配置：`flume-memory-mysql-hdfs-kafka.properties`

```properties
# 采集 Mysql 的数据，保存到 Kafka
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = org.keedio.flume.source.SQLSource
a1.sources.r1.hibernate.connection.url = jdbc:mysql://192.168.0.104:3306/taotao
a1.sources.r1.hibernate.connection.user = root  
a1.sources.r1.hibernate.connection.password = oadat
a1.sources.r1.hibernate.connection.autocommit = true
a1.sources.r1.hibernate.connection.driver_class = com.mysql.jdbc.Driver
a1.sources.r1.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect
a1.sources.r1.run.query.delay = 5000
a1.sources.r1.status.file.path = /home/flume/flume/flume_hdfs
a1.sources.r1.status.file.name = r1.status
a1.sources.r1.table = tb_item_cat
a1.sources.r1.columns.to.select= *

#设置Kafka接收器
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
#设置Kafka地址
a1.sinks.k1.brokerList = master01:9092
#设置发送到Kafka上的主题
a1.sinks.k1.topic = flume-hdfs-kafka
#设置序列化方式
a1.sinks.k1.serializer.class = kafka.serializer.StringEncoder

#配置sink 
a1.sinks.k2.type = hdfs
a1.sinks.k2.hdfs.path = hdfs://master01:9000/flume02/event/%y-%m-%d/%H/
# a1.sinks.k1.hdfs.filePrefix = %{fileName}
#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本
a1.sinks.k2.hdfs.fileType = DataStream 
a1.sinks.k2.hdfs.writeFormat = Text 
# a1.sinks.k1.hdfs.useLocalTimeStamp = true

# Describe the channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c1

```

- 操作：将对应的包复制到 `Flume` 的 `lib` 文件夹下

> 需要将包 `mysql-connector-java-5.1.6.jar` 和 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar` 复制到对应的 `Flume` 的 `lib` 包下。
> 包 `flume-ng-sql-source-1.5.3-SNAPSHOT.jar`为 [github-flume-mysql](https://github.com/keedio/flume-ng-sql-source) 下载后通过 `Maven` 命令编译生成的。

- 操作：启动 `Kafka` 消费者

```xshell
bin/kafka-console-consumer.sh --bootstrap-server master01:9092 --topic flume-hdfs-kafka
```
- 操作：提交配置文件

```xshell
flume-ng agent \
--conf conf \
--conf-file /home/flume/flume/conf/flume-memory-mysql-hdfs-kafka.properties \
--name a1 -Dflume.root.logger=INFO,console
```

- 操作：在对应的数据库内新增数据

>在数据库 `taotao` 的表 `tb_item_cat` 插入对应的数据即可。
