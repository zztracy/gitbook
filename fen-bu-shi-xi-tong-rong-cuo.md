---
description: 整理在学习过程中遇到的容错策略
---

# 【分布式系统】-容错

### MapReduce

#### Worker Failure

在mapreduce的设计中，master会发送心跳，判断worker是否存活。如果在一段时间内（例如10s），master没有收到worker的响应，那么master将会将worker标记为failed。由该worker处理的任务将会回滚，并且将其状态设置为idle。

因为map任务的结果存储在worker的本地磁盘中，所以map任务需要重新执行。与其相反，reduce任务的结果存储在一个全局文件系统中，因此reduce任务不需要重新执行。

当一个map任务由于workerA宕机而转交给workerB执行的时候。所有的执行reduce任务的worker将会指导这个转变。reduce任务将会从workerB获取数据，而不是从workerA获取数据。

#### Master Failure

为master创建周期性的checkpoint。当master宕机了，可以从checkpoint处开启一个副本。

#### Failure的语义

**当map和reduce的结果时确定的时候**

每一个正在工作的任务都会将其输出写到一个私有的临时文件。一个reduce任务产生一个这样的文件，一个map任务则会产生R个（R = reduce worker的数量）。

当一个map任务完成之后，worker会告知master其输出的文件名。如果master检测到这个消息中的任务已经完成了，master会抛弃这条消息。反之，master会把文件名存储到其数据结构中

当一个reduce任务完成之后，reduce worker会原子性的将临时输出文件重命名为最终输出文件。如果一个reduce任务由多个机器执行，那么多个重命名操作会输出同一个最终输出文件。我们通过这种原子重命名的机制，保证reduce task输出结果的正确性

