# SparkSQL_使用

## 创建 SparkSession

SparkSession 是 Dataset 与 DataFrame 编程的入口。

- 官方代码

```scala
import org.apache.spark.sql.SparkSession

val spark = SparkSession
  .builder()
  .appName("Spark SQL basic example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate()

// For implicit conversions like converting RDDs to DataFrames
import spark.implicits._

```

- 实际使用

- Scala

```scala
//Scala
val spark = SparkSession
  .builder()
  .appName("")
  .master("")
  .getOrCreate()

```
- Java

```java
//Java
SparkSession spark = SparkSession
  .builder()
  .appName(appName)
  .master(master)
  .getOrCreate();

```

## 创建DataFrame

通过已存在的数据源创建 DataFrame。

- 通过 Json 文件创建 DataFrame

```scala
val df = spark.read.json("examples/src/main/resources/people.json")
df.show()
```

## 创建 DataSet

创建 DataSet 必须对传入的对象序列化

- Xshell

```xshell
//Xshell
case class Person(name: String, age: Long)
// 创建DataSet
val ds = Seq(Person("Andy", 32)).toDS()
ds.show()

```
- Scala

```scala
//Scala
object Spark_Sql_DS {
  //这个定义要写在 main 方法之外
  case class Person(name: String, age: Long)
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Spark_Sql_DS")
      .master("local[2]")
      .getOrCreate()

    //一定要导入这个，否则 toDS() 方法报错
    import spark.implicits._
    val ds = Seq(Person("Andy", 21)).toDS()
    ds.show()
    spark.stop()
  }
}
```

## RDD 与 DataFrame 的相互转换

### 利用反射 Schema

- 代码

- Scala

```scala
  /**
    * 利用反射推断Schema
    */
object Spark_Sql_RDD_DF {
  case class Person(name: String, age: Int)
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Spark_Sql_RDD_DF")
      .master("local[2]")
      .getOrCreate()

    import spark.implicits._
   // val txtDF = spark.read.textFile("src\\main\\resources\\people.txt")
   //建议采用下面的写法
    val txtDF = spark.sparkContext.textFile("src\\main\\resources\\people.txt")
      .map(_.split(","))
      .map(at => Person(at(0), at(1).trim.toInt))
      .toDF()

    txtDF.createOrReplaceTempView("people")
    val jsonSql = spark.sql("select * from people")
    jsonSql.show()
    spark.stop()
  }
}
```
- Java

```java
public class Spark_Rdd_Demo {
    /**
     * 利用反射推断Schema
     */
    private static String appName = "spark_rdd.demo";
    private static String master = "local[2]";

    public static void main(String[] args) {
        //创建 SparkSession
        SparkSession spark = SparkSession
                .builder()
                .appName(appName)
                .master(master)
                .getOrCreate();
        //解析 txt 的数据，然后转化成 RDD格式，在调用 map 方法封装数据
        JavaRDD<Persion> rdd = spark.read()
                .textFile("src\\main\\resources\\people.txt")
                .javaRDD()
                .map(new Function<String, Persion>() {
                    @Override
                    public Persion call(String v1) throws Exception {
                        String[] split = v1.split(",");
                        Persion persion = new Persion();
                        persion.setName(split[0]);
                        persion.setAge(Integer.parseInt(split[1].trim()));
                        return persion;
                    }
                });
        // 把 RDD 的数据转化成 dataSet 形式
        Dataset<Row> dataSet = spark.createDataFrame(rdd, Persion.class);
        dataSet.show();
        spark.stop();
    }

    public static class Persion implements Serializable {
        private String name;
        private Integer age;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public Integer getAge() {
            return age;
        }

        public void setAge(Integer age) {
            this.age = age;
        }
    }
}
```

### 编程指定 Schema

- 代码

- Scala

```scala
object Spark_Sql_RDD_DS {
  /**
     * 编程指定Schema
     */
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Spark_Sql_RDD_DS")
      .master("local[2]")
      .getOrCreate()

    //采用这种写法，‘spark.createDataFrame(rowRDD, schema)’ 这句话报错，不知道为什么？
    //val txt = spark.read.textFile("src\\main\\resources\\people.txt")
    val txt = spark.sparkContext.textFile("src\\main\\resources\\people.txt")

    //定义数据类型
    /**
      *  另外的写法
      val schema = StructType(Array(
      StructField("name", StringType, true),
      StructField("age", StringType, true)
    ))
      */
      //官网写法
    val schemaString = "name age"
    val fields = schemaString
      .split(" ")
      .map(fieldName => StructField(fieldName, StringType, nullable = true))
    val schema = StructType(fields)

    //将原始RDD转换为ROW类型的RDD
    val rowRDD  = txt
      .map(_.split(","))
      .map(at => Row(at(0), at(1).trim))

    //创建DF
    val txtDF = spark.createDataFrame(rowRDD, schema)

    //注册成临时视图
    txtDF.createOrReplaceTempView("people")
    val txtSql = spark.sql("select * from people")
    txtSql.show()
    spark.stop()
  }
}
```
- Java

```java
public class Spark_Schema_Demo {
    /**
     * 编程指定Schema
     */
    private static String appName = "spark_schema.demo";
    private static String master = "local[2]";

    public static void main(String[] args) {
        //创建 SparkSession
         SparkSession spark = SparkSession
                .builder()
                .appName(appName)
                .master(master)
                .getOrCreate();
        //获取指定文件信息，把数据转成 RDD
        JavaRDD<String> javaRDD = spark.read().textFile("src\\main\\resources\\people.txt").javaRDD();
        //指定 schema
        String schema = "name age";
        List<StructField> fields = new ArrayList<StructField>();
        for (String fileName : schema.split(" ")) {
            StructField structField = DataTypes.createStructField(fileName, DataTypes.StringType, true);
            fields.add(structField);
        }
        StructType schemas = DataTypes.createStructType(fields);
        JavaRDD<Row> rowRDD = javaRDD.map(new Function<String, Row>() {
            @Override
            public Row call(String v1) throws Exception {
                String[] split = v1.split(",");
                return RowFactory.create(split[0], split[1].trim());
            }
        });
        //把 RDD 转化成 dataSet
        Dataset<Row> dataSet = spark.createDataFrame(rowRDD, schemas);
        dataSet.show();

        dataSet.createOrReplaceTempView("people");
        Dataset<Row> dataset = spark.sql("select * from people");
        dataset.show();
        spark.stop();
    }
}
```

**注意**

在编程获取 Txt 文件的时候 Scala 代码里 `spark.read.textFile("src\\main\\resources\\people.txt")` 获取文件的时候 `spark.createDataFrame(rowRDD, schema)` 创建方法不成功，但 Java 代码里这样获取和创建就没问题。

## Spark 数据源方法

### 输入

对于 Spark SQL 的输入需要使用 sparkSession.read 方法

1. 通用模式 sparkSession.read.format("json").load("path") 支持类型：parquet、json、text、csv、orc、jdbc
2. 专业模式 sparkSession.read.json、csv  直接指定类型。

### 输出

对于 Spark SQL 的输出需要使用  sparkSession.write方法

1. 通用模式 dataFrame.write.format("json").save("path")  支持类型：parquet、json、text、csv、orc

2. 专业模式 dataFrame.write.csv("path")  直接指定类型

3. 如果你使用通用模式，Spark 默认 parquet 是默认格式、sparkSession.read.load 加载的默认是 parquet 格式 dataFrame.write.save 也是默认保存成 parquet 格式。

4. 如果需要保存成一个 text 文件，那么需要 dataFrame 里面只有一列（只需要一列即可）。

