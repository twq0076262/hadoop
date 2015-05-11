# MapReduce - Shuffle

对Map的结果进行排序并传输到Reduce进行处理 Map的结果并不#x662F;直接存放到硬盘,而是利用缓存做一些预排序处理 Map会调用Combiner，压缩，按key进行分区、排序等，尽量减少结果的大小 每个Map完成后都会通知Task，然后Reduce就可以进行处理

![](images/mapreduce-process.png)

## Map端

当Map程序开始产生结果的时候，并不是直接写到文件的，而是利用缓存做一些排序方面的预处理操作

每个Map任务都有一个循环内存缓冲区（默认100MB），当缓存的内容达到80%时，后台线程开始将内容写到文件，此时Map任务可以&#x#x7EE7;续输出结果，但如果缓冲区满了，Map任务则需要等待

写文件使用round-robin方式。在写入文件之前，先将数据按照Reduce进行分区。对于每一个分区，都会在内存中根据key进行排序，如果配置了Combiner，则排序后执行Combiner（Combine之后可以减少写入文件和传输的数据）

每次结果达到缓冲区的阀值时，都会创建一个文件，在Map结束时，可能会产生大量的文件。在Map完成前，会将这些文件进行合并和排序。如果文件的数量超过3个，则&##x5408;并后会再次运行Combiner（1、2个文件就没有必要了）

如果配置了压缩，则最终写入的文件会先进行压缩，这样可以减少写入和传输的数据

一旦Map完成，则通知任务管理器，此时Reduce就可以开始复制结果数据

## Reduce端

Map的结果文件都存放到运行Map任务的机器的本地硬盘中

如果Map的结果很少，则直接放到内存，否则写入文件中

同时后台线程将这些文件进行合并和排序到一个更大的文件中（如果文件是压缩的ÿ#xFF0C;则需要先解压）

当所有的Map结果都被复制和合并后，就会调用Reduce方法

Reduce结果会写入到HDFS中

## 调优

一般的原则是给shuffle分配尽可能多的内存，但前提是要保证Map、Reduce任务有足够的内存

对于Map，主要就是避免把文件写入磁盘，例如使用Combiner，增大io.sort.mb的值

对于Reduce，主要是把Map的结果尽可能地保存到内存中，同样也是要避免把中间结果写入磁盘。默认情况下，所有的内存都是分配给Reduce方法的，如果Reduce方法不怎&##x4E48;消耗内存，可以mapred.inmem.merge.threshold设成0，mapred.job.reduce.input.buffer.percent设成1.0

在任务监控中可通过Spilled records counter来监控写入磁盘的数，但这个值是包括map和reduce的

对于IO方面，可以Map的结果可以使用压缩，同时增大buffer size（io.file.buffer.size，默认4kb）

## 配置

<table>

<thead>

<tr>

<th>属性</th>

<th>默认值</th>

<th>描述</th>

</tr>

</thead>

<tbody>

<tr>

<td>io.sort.mb</td>

<td>100</td>

<td>映射输出分类时所使用缓冲区的大小.</td>

</tr>

<tr>

<td>io.sort.record.percent</td>

<td>0.05</td>

<td>剩余空间用于映射输出自身记录.在1.X发布后去除此属性.随机代码用于使用映射所有内存并记录信息.</td>

</tr>

<tr>

<td>io.sort.spill.percent</td>

<td>0.80</td>

<td>针对映射输出内存缓冲和记录索引的阈值使用比例.</td>

</tr>

<tr>

<td>io.sort.factor</td>

<td>10</td>

<td>文件分类时合并流的最大数量。此属性也用于reduce。通常把数字设为100.</td>

</tr>

<tr>

<td>min.num.spills.for.combine</td>

<td>3</td>

<td>组合运行所需最小溢出文件数目.</td>

</tr>

<tr>

<td>mapred.compress.map.output</td>

<td>false</td>

<td>压缩映射输出.</td>

</tr>

<tr>

<td>mapred.map.output.compression.codec</td>

<td>DefaultCodec</td>

<td>映射输出所需的压缩解编码器.</td>

</tr>

<tr>

<td>mapred.reduce.parallel.copies</td>

<td>5</td>

<td>用于向reducer传送映射输出的线程数目.</td>

</tr>

<tr>

<td>mapred.reduce.copy.backoff</td>

<td>300</td>

<td> 时间的最大数量，以秒为单位，这段时间内若reducer失败则会反复尝试传输</td>

</tr>

<tr>

<td>io.sort.factor</td>

<td>10</td>

<td>组合运行所需最大溢出文件数目.</td>

</tr>

<tr>

<td>mapred.job.shuffle.input.buffer.percent</td>

<td>0.70</td>

<td>随机复制阶段映射输出缓冲器的堆栈大小比例</td>

</tr>

<tr>

<td>mapred.job.shuffle.merge.percent</td>

<td>0.66</td>

<td>用于启动合并输出进程和磁盘传输的映射输出缓冲器的阀值使用比例</td>

</tr>

<tr>

<td>mapred.inmem.merge.threshold</td>

<td>1000</td>

<td>用于启动合并输出和磁盘传输进程的映射输出的阀值数目。小于等于0意味着没有门槛，而溢出行为由 mapred.job.shuffle.merge.percent单独管理.</td>

</tr>

<tr>

<td>mapred.job.reduce.input.buffer.percent</td>

<td>0.0</td>

<td>用于减少内存映射输出的堆栈大小比例，内存中映射大小不得超出此值。若reducer需要较少内存则可以提高该值.</td>