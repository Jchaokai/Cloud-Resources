## 二段提交

### 前言

如果一个业务，需要多个`tx` 来处理分布式mysql集群中不同的表

- 正常情况：

    多个`tx` 在DML过程中出现问题，我们可以在第一个tx  `commit`之前 `rollback 所有tx`

- 问题？

    如果DML没有出现问题，而是在多个tx commit过程中，有一个tx commit失败，怎么办？

    这个时候已经提交 commit的 tx已经无法 rollback！

*引出 二段提交的概念* 

### 介绍





