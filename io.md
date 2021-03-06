# Hadoop - IO

1.  输入文件从HDFS进行读取.
2.  输出文件会存入本地磁盘.
3.  Reducer和Mapper间的网络I/O,从Mapper节点得到Reducer的检索文件.
4.  使用Reducer实例从本地磁盘回读数据.
5.  Reducer输出- 回传到HDFS.

## 串行化

传输、存储都需要

Writable接口

Avro框架：IDL，版本支持，跨语言，JSON-linke

## 压缩

能够减少磁盘的占用空间和网络传输的量

Compressed Size, Speed, Splittable

gzip, bzip2, LZO, LZ4, Snappy

要比较各种压缩算法的压缩比和性能

重点：压缩和拆分一般是冲突的（压缩后的文件的block是不能很好地拆分独立运行，很多时候某个文件的拆分点是被&#x#x62C6;分到两个压缩文件中，这时Map任务就无法处理，所以对于这些压缩，Hadoop往往是直接使用一个Map任务处理整个文件的分析）

Map的输出结果也可以进行压缩，这样可以减少Map结果到Reduce的传输的数据量，加快传输速率

## 完整性

磁盘和网络很容易出错，保证数据传输的完整性一般是通过CRC32这种校验法

每次写数据到磁盘前都验证一下，同时保存校验码

每次读取数据时，也验证校验码，避免磁盘问题

同时#x65F6;每个datanode都会定时检查每一个block的完整性

当发现某个block数据有问题时，也不是立刻报错，而是先去Namenode找一块该数据的完整备份进行恢复，不能恢复才报错