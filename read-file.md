# HDFS - 读文件


![](images/hdfs-read.png)

1.  客户端向NameNode发送读取请求
2.  NameNode#x8FD4;回文件的所有block和这些block所在的DataNodes（包括复制节点）
3.  客户端直接从DataNode中读取数据，如果该DataNode读取失败（DataNode失效或校验码不对），则从复制节点中读取（如果读取的数据就在本机，则直接读取，否则通过网络读取）