---
title: linux基础
date: 2021-01-23 15:22:34
tags: Linux
categories: Linux基础
---
**linux 基本命令操作**

# 一、基本命令

cd 进入个人主目录

cd /home

cd ..

cd - 返回上次所在的目录

pwd 显示工作路径

ls 查看目录中的文件

ls -l 查看文件和目录的详情

ls -a 显示隐藏文件

man

**文件处理命令**

**1.创建**

mkdir test 创建'test'目录

mkdir test01 test02 同时创建两个目录

mkdir -p test/aaa 创建级联目录

**2.删除**

rmdir test 删除 'test' 目录 rm -f file 删除文件 rm -rf dir1 删除其目录以及内容

**3.移动**

mv app test 将app目录移动至test目录下

**4.复制**

cp file1 file2 将file1文件复制至file2下

cp dir/* . 复制一个目录下所有文件至当前工作目录

cp -a dir dir2 复制一个目录

# 二、用户和用户组管理

groupadd group name 创建一个新用户组

groupdel group name 删除一个用户组

groupmod -n new_group_name old_group_name 重命名一个用户组

useradd -g groupname user1 创建一个新用户

userdel -r user1 删除一个用户

passwd 修改口令

passwd 修改一个用户的口令

**切换用户**

su - 是带运行环境的

su 不带运行环境， 切换用户

# 三、VIM使用

vi编辑模式

1.输入vi命令直接进入编辑模式

2.输入 "i" 进行输入模式 ；输入 ":" 进入末行模式

3.输入 "wq" 写入并退出

4.输入 "q!" 强制退出

注:source ./test 更新

查看 cat test.txt

# 四、帮助命令

type cd 系统给出的解释

is --help 查看命令所支持的参数说明

help ls 获得ls命令的帮助文档

man cd 查看某命令的正式文档

which cd 查看cd命令的所在位置

# 五、权限管理命令

所有者 文件所在组 其他组

文件权限

读(r)、写(w)、执行(x)

r=4 、 w=2、x=1 因此rwx = 7

改变权限的命令

chmod 755 abc 给abc赋予权限 rwxr-xr-x

chown xiaoming abc 改变abc的所有者为xiaoming

chgrp root abc 改变abc所属的组为root

usermod -g 组名 用户名 改变用户所在组

# 六、解压

## 解压.zip

upzip filename.zip

## 解压tar.gz

tar -zxvf filename.tar.gz

# 七、关机操作

## 1.关闭系统

shutdown -h now

init 0

telinit 0

## 2.按时间关闭

shutdown -h hours:minutes & 按预定时间关闭系统

shutdown -c 取消按预定时间关闭系统

## 3.重启

shutdown -r now

reboot

## 4.注销

logout