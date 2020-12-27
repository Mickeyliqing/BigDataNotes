# SparkSQL_Json

- 代码

```scala
object Spark_Sql_Json {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Spark_Sql_Json")
      .master("local[2]")
      .getOrCreate()

    //第一种方法
    val json = spark.read.json("src\\main\\resources\\people.json")
    json.show()

    //第二种方法
    // 将DataFrame注册为SQL的临时视图
    // 该方法创建的是一个本地的临时视图，生命周期与其绑定的SparkSession会话相关
    // 即如果创建该view的session结束了，该view也就消失了
    json.createOrReplaceTempView("people")
    val jsonSql = spark.sql("select * from people")
    jsonSql.show()

    //第三种方法
    // 将DataFrame注册为全局临时视图(global temporary view)
    // 该方法创建的是一个全局的临时视图，生命周期与其绑定的Spark应用程序相关，
    // 即如果应用程序结束，会自动被删除
    // 全局临时视图是可以跨Spark Session的，系统保留的数据库名为`global_temp`
    // 当查询时，必须要加上全限定名，如`select * from global_temp.view`
    json.createOrReplaceGlobalTempView("peoples")
    val sqlJson = spark.sql("select * from global_temp.peoples")
    sqlJson.show()
    spark.stop()
  }
}
```

**注意**

需要引入架包

```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-sql_2.12</artifactId>
    <version>3.0.0</version>
</dependency>
```

