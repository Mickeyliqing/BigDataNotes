# Spark_容错

### Executor 挂掉

Spark 有自己的容错机制，会自动寻找下一个 Executor。

### Driver 挂掉

1. 设置自动重启 Driver。
2. 设置 HDFS 的 checkPoint 的检查目录。（采用 checkPoint 机制，将 Driver 信息写入 HDFS 中）
