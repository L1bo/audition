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