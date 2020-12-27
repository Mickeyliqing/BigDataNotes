# SparkSQL_JDBC

- 代码

```scala
//Scala
object Spark_Sql_Jdbc {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .master("local[2]")
      .appName("Spark_Sql_Jdbc")
      .getOrCreate()

    val jdbc = spark.read
      .format("jdbc")
      .option("url", "jdbc:mysql://localhost:3306/taotao?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai")
      .option("driver", "com.mysql.jdbc.Driver")
      .option("dbtable", "tb_item")
      .option("user", "root")
      .option("password", "")
      .load();
    jdbc.select("*").show()
    spark.stop()
  }
}
```

```java
//Java
public class Spark_JDBC_Demo {
    private static String appName = "spark_jdbc.demo";
    private static String master = "local[2]";

    public static void main(String[] args) {
        SparkSession spark = SparkSession
                .builder()
                .appName(appName)
                .master(master)
                .getOrCreate();
        Dataset<Row> dataset = spark.read()
                .format("jdbc")
                .option("url", "jdbc:mysql://localhost:3306/taotao?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai")
                .option("driver", "com.mysql.jdbc.Driver")
                .option("dbtable", "tb_item")
                .option("user", "root")
                .option("password", "")
                .load();
       dataset.select("*").show();
       spark.stop();
    }
}
```

**注意**

官网上没有 .option("driver", "com.mysql.jdbc.Driver") 这一句，但不加上，系统会报错，系统不清楚到底要连接的是Mysql数据库还是其它数据库。

需要导入架包：

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.6</version>
</dependency>
```