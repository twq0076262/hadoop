# Hadoop配置


有两种配置文件：

一种是*_*_-default.xml（只读，默认的配置）

一种是#x662F;*_*_-site.xml（替换default中的配置）

*   core-site.xml 配置公共属性

*   hdfs-site.xml 配置HDFS

*   yarn-site.xml 配置YARN

*   mapred-site.xml 配置MapReduce

配置文件应用的顺序：

1.  在JobConf中指定的
2.  客户端机器上的*_*_-site.xml配置
3.  slave节点上的*_*_-site.xml配置
4.  *_*_-default.xml中的配置

如果某个属性不想被覆盖，可以将其设置成final

        <property>
            <name>{PROPERTY_NAME}</name>
            <value>{PROPERTY_VALUE}</value>
            <final>true</final>
        </property>
