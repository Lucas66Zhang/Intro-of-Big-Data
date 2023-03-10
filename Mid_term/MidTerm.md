# 期中大作业

## 6.1 面试题

### 6.1.1 简述Hadoop小文件弊端

Hadoop是一个用于处理大数据的分布式计算框架，但是它并不擅长处理大量的小文件。这是因为在Hadoop中，每个小文件都会被分配一个数据块（block），并在集群中的不同节点上进行存储。由于Hadoop中的数据块大小通常为128MB或256MB，因此当存储许多小文件时，会导致存储空间的浪费，因为即使文件非常小，每个文件也会占用整个数据块的空间。同时，Hadoop在处理小文件时，也需要花费更多的时间来维护和管理每个小文件的元数据，这会增加计算负担和降低处理效率。

此外，Hadoop中的MapReduce任务通常以数据块为单位进行操作，因此如果小文件数量很多，就会导致任务调度和执行的效率降低。而且，在处理小文件时，由于每个小文件都需要进行一次MapReduce任务，会导致任务的数量增加，从而使整个系统变得更加复杂和难以管理。

因此，为了提高Hadoop的性能和效率，最好的方式是将小文件合并为较大的文件，以减少存储空间的浪费和元数据的管理开销，并且在MapReduce任务中以较大的文件块为单位进行处理。

### 6.1.2 HDFS中DataNode挂掉如何处理？

在HDFS中，DataNode是用于存储数据块的节点，如果其中一个DataNode挂掉了，可能会导致数据丢失或者不可用。为了保证数据的可靠性和可用性，HDFS有以下的处理方式：

1. 数据备份：HDFS默认会对数据块进行三份备份，存储在不同的DataNode节点上。当一个DataNode挂掉时，数据仍然可以从其他的节点上访问。
2. 数据块复制：如果一个DataNode挂掉，HDFS会自动将存储在该节点上的数据块复制到其他节点上，以确保备份数量不少于三份。
3. 节点恢复：当DataNode挂掉时，HDFS会监测该节点是否恢复正常。如果恢复正常，则HDFS会自动将该节点重新加入集群，并将需要复制的数据块复制到该节点上。
4. 备份节点替换：如果一个DataNode无法恢复正常，HDFS会自动将备份节点中的一个节点替换为新的DataNode，以确保备份份数量不少于三份。

总之，HDFS通过数据备份、数据块复制、节点恢复和备份节点替换等机制，保证了数据的可靠性和可用性，在DataNode挂掉的情况下，系统可以自动进行处理，保证了系统的稳定性。

### 6.1.3 HDFS中DataNode挂掉如何处理？

同6.1.2

### 6.1.4 HBase读写流程？

HBase是一个分布式的列存储数据库，它基于HDFS构建，支持实时随机读写海量结构化数据。下面是HBase的读写流程：

写流程：

1. 客户端向HBase主节点发送写请求，包括表名和行键。
2. HBase主节点根据表名找到该表的元数据，确定需要将数据写入到哪些RegionServer。
3. HBase主节点根据行键计算出需要写入的数据所在的Region，然后将写请求转发给对应的RegionServer。
4. RegionServer接收到写请求后，将数据先写入内存中的MemStore，然后在适当的时候将MemStore中的数据刷写到磁盘上的StoreFile中。
5. 当StoreFile的大小达到一定阈值时，HBase会将多个小的StoreFile合并成一个大的StoreFile，这样可以减少文件数量，提高查询性能。

读流程：

1. 客户端向HBase主节点发送读请求，包括表名和行键。
2. HBase主节点根据表名找到该表的元数据，确定需要从哪些RegionServer读取数据。
3. HBase主节点根据行键计算出需要读取的数据所在的Region，然后将读请求转发给对应的RegionServer。
4. RegionServer从磁盘上的StoreFile和内存中的MemStore中读取数据，并将其合并成一个结果集。
5. RegionServer将结果集返回给HBase主节点，然后HBase主节点将结果集返回给客户端。

以上是HBase的基本读写流程，HBase还支持一些高级特性，如列族、时间戳、版本等，这些特性可以让HBase更加灵活和强大。

### 6.1.5 MapReduce为什么一定要有Shuffle过程

MapReduce是一种分布式计算模型，它将计算任务分成Map和Reduce两个阶段，其中Map阶段将输入数据分割成若干个小数据块进行计算，然后Reduce阶段将Map阶段输出的结果进行合并和汇总。Shuffle过程是Map和Reduce阶段之间的一个重要环节，其作用是将Map阶段的输出结果按照Key值进行排序和分组，以便Reduce阶段可以对相同Key值的数据进行汇总和处理。以下是Shuffle过程的主要作用：

1. 排序：Shuffle过程可以对Map阶段的输出结果按照Key值进行排序，以便Reduce阶段可以更方便地对相同Key值的数据进行汇总和处理。如果没有Shuffle过程，Map阶段的输出结果是无序的，Reduce阶段需要先进行排序操作才能进行汇总，这会大大降低计算效率。
2. 分组：Shuffle过程可以将相同Key值的数据进行分组，以便Reduce阶段可以更方便地对相同Key值的数据进行汇总和处理。如果没有Shuffle过程，Reduce阶段需要遍历整个数据集才能找到相同Key值的数据，这会大大降低计算效率。
3. 数据传输：Shuffle过程将Map阶段的输出结果传输给Reduce阶段，以便Reduce阶段可以对数据进行汇总和处理。在传输过程中，Shuffle还会进行一些优化，如合并相同Key值的数据，压缩数据传输等，以减少网络带宽的消耗。

综上所述，Shuffle过程在MapReduce中具有重要的作用，它可以将Map阶段的输出结果进行排序、分组和传输，以便Reduce阶段可以更方便地进行数据汇总和处理，提高计算效率和性能。

### 6.1.6 MapReduce的三次排序

在MapReduce中，有三次排序涉及到排序操作，包括：

1. Map端的局部排序（Map-Side Sort）：Map任务的输出结果会按照Key值进行排序，以便进行分区和分组。这个排序是在Map任务内部完成的，称为局部排序。Map端的局部排序可以减少数据传输量，提高后续操作的效率。
2. 按Key分区（Partitioner）：在Map任务完成局部排序后，需要将输出结果分成多个分区，以便将数据传输到对应的Reduce任务中。分区的依据是Key值，Partitioner会根据Key值计算出对应的分区号，然后将数据传输到对应的Reduce任务中。分区是MapReduce中的第二次排序操作。
3. Reduce端的全局排序（Reduce-Side Sort）：在Reduce任务接收到来自Map任务的数据后，需要按照Key值进行排序，以便进行合并和归并操作。这个排序是在Reduce任务内部完成的，称为全局排序。Reduce端的全局排序可以将数据集合成有序的序列，使得后续操作更容易实现。

需要注意的是，MapReduce中的排序操作是可选的，具体是否需要排序取决于具体的需求和任务。在一些特殊情况下，可以通过适当调整MapReduce的参数或使用自定义的排序算法来实现不同的排序策略。

### 6.1.7 MapReduce为什么不能产生过多小文件

在MapReduce任务中，产生过多的小文件会带来以下几个问题：

1. 文件系统存储空间问题：如果MapReduce任务产生的输出文件过多，将会占用大量的存储空间。过多的小文件也会增加文件系统的管理和维护成本，导致系统的效率下降。
2. 数据处理效率问题：过多的小文件会增加MapReduce任务的输入数据分片数量，导致任务处理效率下降。这是因为MapReduce任务的输入数据分片数量影响了任务的并行度，分片数量过多会使得任务的并行度不足，导致任务的处理效率下降。
3. 任务调度问题：如果MapReduce任务产生的小文件过多，将会增加任务的调度时间和系统负载，降低整个系统的性能。

因此，在MapReduce任务中，应尽量避免产生过多的小文件。