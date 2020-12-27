# SparkStreaming_MySQL

**代码**

```scala
object SparkMysql {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf().setAppName("SparkMysql").setMaster("local[2]")
    val scc = new StreamingContext(conf, Seconds(2))
    val result = scc.textFileStream("hdfs://master01:9000/0806").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_)

    result.foreachRDD { rdd =>
      rdd.foreachPartition { partitionOfRecords =>
        val connection = createConnection()
        partitionOfRecords.foreach(record => {
          val sql = "insert into wordcount(word, wordcount) values ('" + record._1 +"'," + record._2 + ")"
          connection.createStatement().execute(sql)
        })
        connection.close()
      }
    }
    result.print()
    scc.start()
    scc.awaitTermination()
  }

  def createConnection() = {
    Class.forName("com.mysql.jdbc.Driver")
    DriverManager.getConnection("jdbc:mysql://localhost:3306/taotao?characterEncoding=utf8&amp;useSSL=true","MyUser","oadat")
  }
}
```

- 注意事项

1. 对 `SparkStreaming` 的数据进行处理，根据官网推荐的写法使用 `foreachRDD` 方法如下：

```scala
dstream.foreachRDD { rdd =>
  rdd.foreachPartition { partitionOfRecords =>
    // ConnectionPool is a static, lazily initialized pool of connections
    val connection = ConnectionPool.getConnection()
    partitionOfRecords.foreach(record => connection.send(record))
    ConnectionPool.returnConnection(connection)  // return to the pool for future reuse
  }
}
```
