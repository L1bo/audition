[TOC]
# 事务

## REQUIRES_NEW  NESTED
REQUIRES_NEW 始终启动一个新事务
两个事务没有关联

NESTED 在原事务内启动一个内嵌事务
两个事务有关联
外部事务回滚内部事务也会回滚