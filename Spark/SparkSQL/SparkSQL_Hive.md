# SparkSQL_Hive

- 代码

```scala
//Scala
object Spark_Sql_Hive {
  /**
    * 启动程序测试之前需要
    * 01：把Hive/conf下的hive-site.xml
    * 02: 把Hadoop/etc/hadoop/conf下的core-site.xml和hdfs-site.xml，复制到Spark/conf文件夹下
    * 03：服务器端需要启动Hadoop,Hive和Spark
    */
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .master("local[2]")
      .appName("Spark_Sql_Hive")
      .config("spark.sql.warehouse.dir", "hdfs://master01:8020/user/hive/warehouse")
      .config("hive.metastore.uris", "thrift://master01:9083")
      .enableHiveSupport()
      .getOrCreate()
    spark.sparkContext.setLogLevel("WARN");
    spark.sql("show tables");
    spark.sql("create table person (id int, name string, age int) row format delimited fields terminated by ' '");
    spark.sql("load data local inpath 'src/main/resources/person.txt' into table person");
    spark.sql("select * from person").show();
    spark.stop();
  }
}
```

```java
//Java
public class Spark_Hive_Demo {
    /**
     * 启动程序测试之前需要
     * 01：把Hive/conf下的hive-site.xml
     * 02: 把Hadoop/etc/hadoop/conf下的core-site.xml和hdfs-site.xml，复制到Spark/conf文件夹下
     * 03：服务器端需要启动Hadoop,Hive和Spark
     * 04：如果是外置的 Hive，第二步可以不操作
     */
    private static final String master = "local[2]";
    private static final String appName = "spark_hive_demo";
    public static void main(String[] args) {
        SparkSession spark = SparkSession.builder()
                .appName(appName)
                .master(master)
                .config("spark.sql.warehouse.dir", "hdfs://master01:8020/user/hive/warehouse")
                .config("hive.metastore.uris", "thrift://master01:9083")
                .enableHiveSupport()
                .getOrCreate();
        spark.sparkContext().setLogLevel("WARN");
        spark.sql("show tables");
        spark.sql("create table person (id int, name string, age int) row format delimited fields terminated by ' '");
        spark.sql("load data local inpath 'src/main/resources/person.txt' into table person");
        spark.sql("select * from person").show();
        spark.stop();
    }
```

**注意**

启动程序测试之前需要

- Spark 内置 Hive

1. 把 Hadoop/etc/hadoop/conf 下的 core-site.xml 和 hdfs-site.xml，复制到 Spark/conf 文件夹下。
2. 如果Spark路径下发现metastore_db，需要删除【仅第一次启动的时候】。



- Spark 外置 Hive

1. 把 Hive/conf 下的 hive-site.xml。
2. 如果hive的metestore使用的是mysql数据库，那么需要将mysql的jdbc驱动包放到spark的jars目录下。
3. 服务器端需要启动 Hadoop,Hive和Spark。

采用 Spark 内置的 Hive 时，需要第二步操作。

