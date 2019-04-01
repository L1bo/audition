[TOC]

# vim 的基本操作

## 查找相应字符串
```
/str
```
跳转到上一个匹配，按下N（大写N）
查看下一个匹配，按下n(小写n)

## 文件跳转(文件头,文件尾)
gg : 跳转到文件头
Shift+g(G) : 跳转到文件末尾

## 上一页 下一页
Ctrl + f
Ctrl + b

# vim 高级操作

## vim 语法显示 (syntax)
:syntax enable -> 打开语法的颜色显示 (turn on syntax color)
:syntax clear -> 关闭语法颜色 (remove syntax color)
:syntax off -> 完全关闭全部语法功能 (turn off syntax)
:syntax manual -> 手动设定语法 (set the syntax manual, when need syntax use :set syntax=ON)

# 附录1

## 基本指令
命令 | 说明
-- | --
x | 删除光标下的字符
X | 删除光标左边的字符
s | 修改一个字符(与r的区别，修改后直接进入Insert模式)
i | 光标前插入字符
a | 光标后插入字符
A | 行尾插入字符
o | 光标下方新建一行并进入Insert模式
O (字母)  | 光标上方新建一行并进入Insert模式
r | 替换字符(单个)
R | 替换字符(多个)
cw | 替换文本直到单词末尾(ce也可)
c$ | 替换文本直到行尾
dw | 删除单词 (de, d2w)
d$ | 删除到行尾
D | 删除到行尾
S | 删除到行为，并进入Insert模式
dd | 删除整行
y | 复制
yy | 复制整行 （yw, ye)
p | 粘贴到光标之后
P | 粘贴到光标之前
u | 撤销
. | 重复上一次操作
:! | 执行外部名 (:!dir/:ls)
% | 查找配对的括号 ), }, ]
v motion :w | 选择性保存
Ctrl + D | 查看可能的补全

## 光标移动
命令 | 说明
-- | --
h | 左
l | 右
k | 上
j | 下
0(数字) | 行首
$ | 行尾
w | 下一个单词起始处
nw (2w) | n可为数字，表示移动两个单词
e | 单词末尾（与w的区别）
gg | 文件起始处
G | 文件末尾处

## 退出
命令 | 说明
-- | --
:wq | 保存并退出
ZZ | 保存并退出
:q! | 丢弃修改并退出 (q退出，有修改的话q不能退出)
:e! | 丢弃修改并打开原文件

## 查找并替换
命令 | 说明
-- | -- 
:s/old/new/g | 查找old并替换为new，g代表整行匹配
:#, #s/old/new | #, #代表首尾行号
:%s/old/new/g | 全文匹配替换
:%s/old/new/gc | 全文匹配替换，并每次替换会弹出确认提示

## 翻页
命令 | 说明
-- | --
Ctrl + e | 向下滚动一行
Ctrl + y | 向上滚动一行
Ctrl + f | 向下翻一页
Ctrl + b | 向上翻一页
Ctrl + u | 向上翻半页
Ctrl + d | 向下翻半页

## 文件操作
命令 | 说明
-- | --
:e + filename | 打开新文件
:r + filename | 读取文件并插入到当前文件

## 选项设置
命令 | 说明
-- | --
:set ic | 忽略大小写
:set is | 部分匹配（like)
:set hls | 高亮匹配
:set nu | 显示行号

## 分屏启动vim
命令 | 说明
-- | --
vim -on file1 file2... | 水平分屏
vim -On file1 file2... | 垂直分屏

## 关闭分屏
命令 | 说明
-- | --
Ctrl + w c | 关闭当前窗口（如果是最后一个，无法关闭）
Ctrl + w q | 关闭当前窗口（如果是最后一个，则退出vim)

## 分屏
命令 | 说明
-- | --
Ctrl + w s | 上下分屏
Ctrl + w v | 左右分屏
:sp filename 左右分屏，且打开一个新的文件
:vsp filename 上下分屏，且打开一个新的文件

## 切换分屏（光标切换）
命令 | 说明
-- | -- 
Ctrl + w l 右边
Ctrl + w h 左边
Ctrl + w j 上边
Ctrl + w k 下边

## 分屏尺寸调整
TIPS: (上下分屏生效)

命令 | 说明
-- | --
Ctrl + w = | 所有屏一样高度
Ctrl + w + | 增加高度
Ctrl + w - | 减少高度


https://blog.easwy.com/archives/advanced-vim-skills-catalog/
vi和vim的区别https://www.cnblogs.com/KiraEXA/p/5994078.html
vim常用命令总结https://www.cnblogs.com/yangjig/p/6014198.html
vi/vim使用进阶https://blog.easwy.com/archives/advanced-vim-skills-catalog/
