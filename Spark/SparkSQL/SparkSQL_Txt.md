# SparkSQL_Txt

- 代码

```scala
object Spark_Sql_Txt {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Spark_Sql_Txt")
      .master("local[2]")
      .getOrCreate()

    val txt = spark.read.textFile("src\\main\\resources\\people.txt")
    txt.show()
    spark.stop()
  }
}
```