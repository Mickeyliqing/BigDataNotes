# SparkStreaming_HDFS
## Spark Streaming 监控 Hadoop 的文件夹

使用 SparkStreaming 之前，需要先初始化，初始化的代码如下：

```java
SparkConf conf = new SparkConf().setMaster(master).setAppName(appName);
JavaStreamingContext context = new JavaStreamingContext(conf, Durations.seconds(2));
```

```scala
val sparkConf = new SparkConf().setAppName(appName).setMaster(master)
val scc = new StreamingContext(sparkConf, Seconds(2))
```
接下来就是使用，由于有过 SparkCore 的基础，下面直接放代码

**代码**

```java
public class SparkHadoopText {
    private static final String master = "local[2]";
    private static final String appName = "SparkStreamingTest";

    public static void main(String[] args) throws InterruptedException {
        SparkConf conf = new SparkConf().setMaster(master).setAppName(appName);
        JavaStreamingContext context = new JavaStreamingContext(conf, Durations.seconds(2));
        JavaDStream<String> dStream = context.textFileStream("hdfs://master01:9000/0806/");

        JavaDStream<String> flatMap = dStream.flatMap(new FlatMapFunction<String, String>() {
            @Override
            public Iterator<String> call(String s) throws Exception {
                return Arrays.asList(s.split(" ")).iterator();
            }
        });
        JavaPairDStream<String, Integer> mapToPair = flatMap.mapToPair(new PairFunction<String, String, Integer>() {
            @Override
            public Tuple2<String, Integer> call(String s) throws Exception {
                return new Tuple2<String, Integer>(s, 1);
            }
        });
        JavaPairDStream<String, Integer> reduceByKey = mapToPair.reduceByKey(new Function2<Integer, Integer, Integer>() {
            @Override
            public Integer call(Integer v1, Integer v2) throws Exception {
                return v1 + v2;
            }
        });

        reduceByKey.print();
        reduceByKey.dstream().saveAsTextFiles("hdfs://master01:9000/0815/", "spark");
        context.start();
        context.awaitTermination();
    }
}
```

```scala
object NetWorkWordCount {
  def main(args: Array[String]): Unit = {
    val sparkConf = new SparkConf().setAppName("NetWorkWordCount").setMaster("local[2]")
    val scc = new StreamingContext(sparkConf, Seconds(5))
    val lines = scc.textFileStream("hdfs://master01:9000/0708")
    val result = lines.flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_)
    result.print()
    scc.start()
    scc.awaitTermination()
  }
}
```

**提交脚本**

```xshell
# 本地模式提交应用
spark-submit \
--class com.spark.java.demo.streaming.SparkHadoopText \
--master local[2] \
/home/LogFiles/Spark_Java_Demo-1.0-SNAPSHOT.jar
```

- 注意事项：

1. 提交脚本的时候带上 `master local[2]` 参数，代码的初始化可以写出如下形式 `val conf = new SparkConf()` 。
2. `master` 的参数至少是 `local[2]` 形式，因为一个线程负责初始化，一个线程负责处理数据。如果是 `local[1]` ,那么就没线程负责处理数据了。
3. 要先启动 `SparkStreaming` ，再在对应的文件系统中写入数据。
4. 如果监听的是 `val lines = scc.textFileStream("D://test")` 路径下的文件，在启动程序后，向 `D://test` 放入文件，那么数据是不会被解析的。因为 `SparkStreaming` 监听的是流数据，而向`D://test` 放入文件，数据不是以流的形式写入的，所以数据不会被监听。