# MapReduce - 编程

## 在线练习

[http://cloudcomputing.ruc.edu.cn](http://cloudcomputing.ruc.edu.cn)

## 处理

1.  select：直接分析输入数据，取出需要的字段数据即可
2.  where: 也是对输入数据处理的过程中进行处理，判断是否需要该数据
3.  aggregation:min, max, sum
4.  group by: 通过Reducer实现
5.  sort
6.  join: map join, reduce join

## Third-Party Libraries

export LIBJARS=$MYLIB/commons-lang-2.3.jar, <other_jars_used_by_remote_components>hadoop jar prohadoop-0.0.1-SNAPSHOT.jar org.aspress.prohadoop.c3\. WordCountUsingToolRunner -libjars $LIBJARS<input_path><output_path></output_path></input_path></other_jars_used_by_remote_components>

hadoop jar prohadoop-0.0.1-SNAPSHOT-jar-with-dependencies.jar org.aspress.prohadoop.c3\. WordCountUsingToolRunner <input_path><output_path>The dependent libraries are now included inside the application JAR file</output_path></input_path>

一般还是上面的好，指定依赖可以利用Public Cache，如果是包含依赖，则每次都需要拷贝

## 参考书籍

[MapReduce Design Patterns](http://book.douban.com/subject/11229683/)