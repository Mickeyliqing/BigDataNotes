# SparkStreaming_Kafka

**代码**

```scala
object SparkCount {
  def main(args: Array[String]): Unit = {

    val conf = new SparkConf().setAppName("SparkCount").setMaster("local[2]")
    //val conf = new SparkConf()
    val ssc = new StreamingContext(conf, Seconds(5))

    val Array(brokers, groupId, topic) = args
    val topics = topic.split(",").toSet
    val kafkaParams = Map[String, Object](
      ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG -> brokers,
      ConsumerConfig.GROUP_ID_CONFIG -> groupId,
      ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG -> classOf[StringDeserializer],
      ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG -> classOf[StringDeserializer]
    )

    val messages = KafkaUtils.createDirectStream[String, String](
      ssc,
      LocationStrategies.PreferConsistent,
      ConsumerStrategies.Subscribe[String, String](topics, kafkaParams)
    )

    val lines = messages.map(_.value)
    val words = lines.flatMap(_.split(" "))
    val wordCounts = words.map(x => (x, 1L)).reduceByKey(_ + _)
    wordCounts.print()

    ssc.start()
    ssc.awaitTermination()
  }
}
```

- 注意事项：

1. 代码里使用的框架版本和服务器的版本必须一致。
2. 在本地 `IDEA` 测试的时候没有任何问题，但提交到服务器的时候却报错误：

```
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/kafka/common/serialization/StringDeserializer
...
Caused by: java.lang.ClassNotFoundException: org.apache.kafka.common.serialization.StringDeserializer
...
```

>分析思路：

spark提交任务是不会用kafka自带的jar包。之前提交的包是 `SparkStreamingTest-1.0-SNAPSHOT.jar`

> 解决方式：

1. 从把 `pom.xml` 文件的 `<build>` 修改为

```
<build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.6.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- maven 打包集成插件 -->
            <plugin>
                <artifactId>maven-assembly-plugin</artifactId>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <descriptorRefs>
                        <!-- 将依赖一起打包到 JAR -->
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <archive>
                        <manifest>
                            <!-- 配置主程序 java -jar 默认Class，这里设置为自己的主类的绝对路径 -->
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                            <mainClass>com.hw.stream.FlumeKafkaStream</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.1</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>
                            <goal>testCompile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```
2. 从新打新的 `Jar` 包 `SparkStreamingTest-1.0-SNAPSHOT-jar-with-dependencies.jar` 然后提交新的文件到服务器就正常了。(新的 `Jar` 包，包含了 `Kafka` 依赖的架包。)

提交命令：

```xshell
spark-submit \
--class com.synway.sparkstreaming.SparkCount \
--master local[2] \
/home/LogFiles/SparkStreamingTest-1.0-SNAPSHOT-jar-with-dependencies.jar master01:9092 test01 spark-kafka
```


