---
description: MIT 6.824课程笔记
---

# Raft-过半票决

过半票决

能够容错的公式

2f + 1 -> f failures&#x20;

例如5台服务器，最多能够容忍2台服务器崩溃

* 发起投票的时，召集的服务器内，必须有一台是之前的leader，这是raft协议避免脑裂的重要机制
* 在发起投票的时，这半数的服务器必须包括上一个投票期间的半数服务器。换言之，任意两组过半服务器之前，至少有一台服务器是重叠的。这是Raft用来避免脑裂的最重要的机制。例如，当一个Leader竞选成功，那么这个领导者必然集齐了过半的服务器。如果此次的过半服务器保证会包括上次竞选时的过半服务器，也就是说新的领导者保证知道上一个领导者的任期编号。
* 类似的，任何旧领导者提交的操作，必然存在于过半的服务器中，而任何新的领导者的过半服务器中，必然有至少一个服务器包含了旧领导者的所有操作。这是Raft能够正确运行的重要原因。
* 通常这也被称为多数投票系统（Quorum）系统，因为3个服务器中的2个，就可以完成多数投票。
