# Hadoop 测试
MRUnit单元测试Mapper和Reducer类在内存上独立运行, PipelineMapReduceDriver单线程运行.

LocalJobRunner单线程运行, 且仅有一个 Reducer能够启动conf.set("mapred.job.tracker", "local"); conf.set("fs.default.name", "file:////&ququot;); FileSystem fs = FileSystem.getLocal(conf);

MiniMRCluster, MiniYarnCluster, MiniDFSCluster 多线程