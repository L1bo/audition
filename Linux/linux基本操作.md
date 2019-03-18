[TOC]

## 列举几个Linux日常操作指令
cd
chmod
cp
history
jps
ll
ls
mv
pwd
rm
tail
tree
wget
yum

## 

## 杀掉进程的指令
kill -9 xxx

## 查看进程
ps -ef|grep java

## 查看占用指定端口的进程
netstat -tunlp|grep 端口号

lsof -i:端口号

## vim 的操作

### 查找相应字符串
```
/str
```
跳转到上一个匹配，按下N（大写N）
查看下一个匹配，按下n(小写n)

### 文件跳转(文件头,文件尾)
gg : 跳转到文件头
Shift+g(G) : 跳转到文件末尾

## 通配符的使用

## Linux 的定时任务