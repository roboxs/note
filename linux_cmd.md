# Shell

## 介绍

shell一个壳程序，shell能够让我们输入的命令和内核沟通。shell其实就是提供用户操作系统的一个界面，因此shell需要调用其他应用程序才好。

shell的种类：

+ Bourne shell(sh)
+ C shell(csh)
+ K shell
+ Bourne Again SHell(bash, linux中使用的就是这个版本)

## 原理

![壳shell](linux_cmd.assets/壳shell.webp)

Linux系统提供给用户的最重要的系统程序是Shell命令语言解释程序。它不属于内核部分，而是在核心之外，以用户态方式运行。其基本功能是解释并执行用户敲入的各种命令，实现用户与Linux kernel 的接口。linux系统启动后，核心为每个终端用户建立一个进程去执行Shell解释程序。它的执行过程基本上按如下步骤：

① 读取用户由键盘输入(stdin)的命令行指令
② 分析命令，以命令名作为文件名，并将其它参数处理为系统调用execve( )内部处理所要求的形式
③ 终端进程调用fork( )创建一个子进程来代替执行命令行
④ 终端进程本身用系统调用wait_pid( )来等待子进程执行结束（如果是后台命令，则不等待）；当子进程运行时调用execve( )，子进程根据文件名（即命令名）到目录中查找有关文件（这是命令解释程序构成的文件），将它调入内存，执行这个程序（解释这条命令）
⑤ 如果命令末尾有&号（后台命令符号），则终端进程不用系统调用wait_pid( )等待，立即让用户输入下一个命令，转到①步骤；如果命令末尾没有&号，则终端进程要一直等待，当子进程（即运行命令的进程）完成处理后终止，向父进程（终端进程）发送退出信息(退出码和终止信号)，此时父进程被唤醒，在做必要的判别等工作后，终端进程发提示符，让用户输入新的命令，转到①重复上述处理过程



## shell scripts

将许多复杂的指令写成一个脚本文件，用户通过one touch的方式处理复杂的操作。

在写脚本时，开头要指明解释器：`#! /bin/bash`

+ 脚本运行的方式：

```bash
./test.sh

source ./test.sh #执行这句话可以让我们不重启bash的情况下，使用我们新加入的全局变量。

bash ./test.sh
```

+ 注释：

```bash
#单行注释


:<<ABV
多行注释
第一行注释
第二行注释
ABV
```

# 指令

## 常用指令

1.wget
wget支持HTTP，HTTPS和FTP协议，可以使用HTTP代理。所谓的自动下载是指，wget可以在用户退出系统的之后在后台执行。这意味这你可以登录系统，启动一个wget下载任务，然后退出系统，wget将在后台执行直到任务完成
2.sudo
sudo命令以系统管理者的身份执行指令
3.rm																		rm指令可以删除文件夹
4.echo
echo指令可以显示文字
5.source
使当前shell读入路径为filepath的shell文件并依次执行文件中的所有语句，通常用于重新执行刚修改的初始化文件，使之立即生效，而不必注销并重新登录。例如，当我们修改了/etc/profile文件，并想让它立刻生效，而不用重新登录，就可以使用source命令，如source /etc/profile。
source指令可以执行脚本文件。三个指令source filename, ./filename和 sh filename的区别：
当shell脚本具有可执行权限时，用sh filename与./filename执行脚本没有区别。
sh filename重新建立一个子shell，在子shell中执行脚本里面的语句，该子shell继承父shell的环境变量，但子shell新建的、改变的变量不会被带回父shell，除非使用export。
source filename：这个命令其实只是简单地读取脚本里面的语句依次在当前shell里面执行，没有建立新的子shell。那么脚本里面所有新建、改变变量的语句都会保存在当前shell里面。
6.ifconfig
主要用于网络，可以关闭打开网卡等等。
7.sync

磁盘同步写入磁盘命令。在文件写入U盘之前，会先保存在缓存区中，为了保证将缓存区中的文件写入U盘中，最后输入sync。
8.ls
查看当前文档中的文件。
ll等于ls -l。
9.touch
创建一个txt文件。
10.ps -a
查看当前系统中的所有进程
11.pgrep -l
查看某个进程的PID信息
12.gdb调试工具
r:运行程序
l:查看程序
quit:退出gdb
p:查看变量的地址
x/x <address>:查看地址的内容
13.windows访问linux
方法：windows PUTTY + linux ssh
遇到的问题：access denied
解决方法：修改 /etc/ssh/ssh_config中第32行，在33行添加PermitRootlogin yes

14.kill -KILL pid 或者kill -s 9 pid
强制杀死某个进程

15. ${}:变量替换

    ```shell
    A=linux
    echo ${A}B ##输出为linuxB
    ```

16. $():命令替换

    ```shell
    echo today is $(date "+%Y-%m-%d")##输出为today is 2022-05-18
    ```

17. udp/tcp端口号查询

    ```shell
    netstat -apu
    netstat -apt
    ```

18. 重启蓝牙

    ```bash
    rfkill block bluetooth
    rfkill unblock bluetooth
    ```

19. 显示文件的inode和类型

    ```shell
    stat main.cpp
    ```

20. 查看进程树

    ```shell
    pstree -A
    ```

21. 端口号相关指令

    ```shell
    lsof -i #查看所有进程所占有的端口号
    lsof -i :端口号#查看端口号所使用的进程号
    
    netstat -nltp | grep 进程号#查看进程号所占用的端口号
    ```

22. 

## 正则表达式

可以用于匹配字符串

+ 例子：比如寻找日志文件中的字符串中的某些关键信息

```shell
grep '2022-04-16 16...... install' name.log #.表示一定有一个任意字符
grep '2022-04-16 16...... install' name.log > test.txt# >表示输出
```

+ 例子：查找网卡信息

```shell
lspci #显示所有PCI总线设备或连接到该总线上的所有设备
lspci | grep -i net #"|"管道符号，上一个命令输出作为下一个命令的输入
```

+ 比较：`grep`和`cut`

```shell
echo ${PATH} | cut -d ':' -f 3,5 #cut是将一行的信息取出某部分，另外$是变量调用符号，调用变量，从而得到变量的值
ls -l | grep "6月" #grep是分析一行信息，如果当中有所需信息，就将该行拿出来
```

