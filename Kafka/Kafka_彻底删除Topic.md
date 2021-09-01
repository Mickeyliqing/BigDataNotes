# Kafka_彻底删除Topic

### 出现的问题：
生产者可以在生产数据，也可以创建`Topic`，但消费者消费不到数据。

处理方式：彻底删除所有的 `Topic`，然后在创建新的 `Topic`，生产者在生产，消费者可以消费。


### 彻底删除Topic
1：先把 `Kafka`停下。

2：删除 `Kafka `存储目录（ `server.properties` 文件 `log.dirs` 配置，默认为 `"/tmp/kafka-logs"` ）相关 `topic` 目录。

3：配置文件中 `server.properties` 配置`delete.topic.enable=true`，执行
`bin/kafka-topics.sh --bootstrap-server master01:9092 --delete --topic topic_start`

4：进入ZK的bin 目录执行 `zkCli.sh -server master01:2181`,然后执行 `ls /brokers/topics`, 在然后执行 `rmr /brokers/topics/【topic name】`

### 解释说明：
如果配置文件中 `server.properties` 没有配置`delete.topic.enable=true`，那么执行命令删除 `Topic` 的时候会被标记`marked for deletion`。

进入 `ZK` ，（接第四步 ）执行 `ls /admin/delete_topics/【topic name】` 删除即可。