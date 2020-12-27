# Spark_使用
## 使用代码

Spark程序必须做的第一件事是创建一个SparkContext对象，该对象告诉Spark如何访问集群。要创建一个，SparkContext您首先需要构建一个SparkConf对象，其中包含有关您的应用程序的信息。

- Java 代码

```java
SparkConf conf = new SparkConf().setAppName(appName).setMaster(master);
JavaSparkContext sc = new JavaSparkContext(conf);
```
- Scala 代码

```scala
SparkConf conf = new SparkConf().setAppName(appName).setMaster(master);
SparkContext sc = new SparkContext(conf);
```

该appName参数是您的应用程序在群集UI上显示的名称。 master是Spark，Mesos或YARN群集URL或特殊的“本地”字符串，以本地模式运行。实际上，当在集群上运行时，您将不希望master在程序中进行硬编码，而是在其中启动应用程序spark-submit并在其中接收。但是，对于本地测试和单元测试，您可以传递“ local”以在内部运行Spark。

## 使用XShell

在Spark Shell中，已经在名为的变量中为您创建了一个特殊的可识别解释器的SparkContext sc。制作自己的SparkContext无效。您可以使用--master参数设置上下文连接到哪个主机，还可以通过将逗号分隔的列表传递给参数来将JAR添加到类路径--jars。您还可以通过在--packages参数中提供逗号分隔的Maven坐标列表，从而将依赖项（例如Spark Packages）添加到Shell会话中。可能存在依赖项的任何其他存储库（例如Sonatype）都可以传递给--repositories参数。例如，要bin/spark-shell在四个核心上运行，请使用：

```xshell
$ ./bin/spark-shell --master local[4]
```
或者，也要添加code.jar到其类路径中，请使用：

```xshell
$ ./bin/spark-shell --master local[4] --jars code.jar
```
要使用Maven坐标包含依赖项，请执行以下操作：

```xshell
$ ./bin/spark-shell --master local[4] --packages "org.example:example:0.1"
```

有关选项的完整列表，请运行spark-shell --help。在后台， spark-shell调用更通用的spark-submit脚本。


