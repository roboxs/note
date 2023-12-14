# Docker

## 租云服务器及配环境

云平台的作用：

+ 存放我们的Docker容器，让计算跑在云端
+ 获得公网IP地址，让每个人都可以访问我们的服务

![image-20220623221702897](linux.assets/image-20220623221702897-1702577632843-11.png)

安全组介绍：

80端口：HTTP，访问网站端口

443端口：HTTPS(HTTP+证书)，加密端口

22端口：通过ssh登录的端口

## Ubuntu安装

https://docs.docker.com/engine/install/ubuntu/

## 配置

为了不用每次都输入sudo，我们将当前用户加入到docker组中

```shell
sudo usermod -aG docker roboxs
```

## 操作

### 镜像images

拉取官网的镜像

```shell
docker pull ubuntu:18.04	
```

列出本地所有镜像

```shell
docker image
```

删除镜像

```shell
docker image rm ubuntu18.04 
docker rmi ubuntu18.04
```

迁移镜像

```shell
docker save -o ubuntu_1804.tar ubuntu:18.04#在本地将镜像变为文件
docker load -i ubuntu_1804.tar#在将镜像从本地加载出来
```

### 容器container

创建一个容器

```shell
docker create -it ubuntu:18.04
```

查看本地所有容器

```shell
docker ps -a 
```

启动、停止、重启、进入、挂起容器

```shell
docker start CONTAINER
	   stop
	   restart
	   attach
挂起容器：ctrl+p ; ctrl+q
```

在容器中执行命令

```shell
docker exec CONTAINER COMMAND
```

删除容器

```shell
docker rm CONTAINER
docker container prune
```

导出/导入容器

```shell
docker export -o xxx.tar CONTAINER
docker import xxx.tar image_name:tag
docker export/import与docker save/load的区别：
		#export/import会丢弃历史记录和元数据信息，仅保存容器当时的快照状态
		#save/load会保存完整记录，体积更大
```

查看某个容器内的所有进程

```shell
docker top CONTAINER
```

查看所有容器的统计信息，包括CPU、内存、存储、网络等信息

```shell
docker stats
```

在本地和容器间复制文件

```shell
docker cp xxx CONTAINER:xxx
```

重命名容器

```shell
docker rename CONTAINER1 CONTAINER2
```

### 实战

进入Acwing终端后：

```shell
scp /var/lib/acwing/docker/images/docker_lesson_1_0.tar server_name:  # 将镜像上传到自己租的云端服务器
ssh roboxs  # 登录自己的云端服务器

docker load -i docker_lesson_1_0.tar  # 将镜像加载到本地
docker run -p 20000:22 --name my_docker_server -itd docker_lesson:1.0  # 创建并运行docker_lesson:1.0镜像

docker attach my_docker_server  # 进入创建的docker容器
passwd  # 设置root密码
```

去云平台控制台中修改安全组配置，放行端口`20000`。

登录自己的Docker

```shell
ssh root@xxx.xxx.xxx.xxx -p 20000  # 将xxx.xxx.xxx.xxx替换成自己租的服务器的IP地址
```

**tips**：tcp端口复用，



## 文件重定向

每个进程默认打开3个文件描述符：

+ `stdin`标准输入，从命令行读取数据，文件描述符为0
+ `stdout`标准输出，向命令行输出数据，文件描述符为1
+ `stderr`标准错误输出，向命令行输出数据，文件描述符为2

重定向命令列表

| 命令             | 说明                                  |
| :--------------- | :------------------------------------ |
| command > file   | 将stdout重定向到file中                |
| command < file   | 将stdin重定向到file中                 |
| command >> file  | 将stdout以追加方式重定向到file中      |
| command n> file  | 将文件描述符n重定向到file中           |
| command n>> file | 将文件描述符n以追加方式重定向到file中 |



# 文件管理

根目录：/

bin：存的是可用的可执行文件命令

etc：配置文件

var：日志文件

lib：存放安装包或者头文件

home：所有用户的家目录

proc：配置文件，比如cpu内核数、内存等

## 文件类型

### 普通文件

+ 二进制文件
+ 文本文件

### 目录文件

### 字符设备文件和块设备文件

+ 在/dev文件中，串口等都在这里面

### 符号链接文件

+ 类似与windows中的快捷文件方式
+ 读取一个符号链接文件内容时，实际读到的是它指向的文件的内容

### 管道文件

### 套接字文件

## 文件挂载

将磁盘挂载为一个目录，通过指令可以操作

### NFS(Network Filesystem)挂载

+ 硬件准备

  + 服务器hust：192.168.199.190
  + 客户端xiaodu：192.168.199.122

+ 软件准备

  + 服务器：

    ```shell
    sudo apt-get install nfs-kernel-server#安装nfs服务
    mkdir /home/hust/minicar#创建一个共享文件夹，用于共享给客户端
    sudo vim /etc/exports
    /home/hust/minica 192.168.199.122(rw,sync,no_root_squash,no_subtree_check)#配置nfs,建立资源共享
    sudo exportfs -a#创建保存共享导出的nfs表
    sudo service nfs-kernel-server start#运行nfs服务
    ```

  + 客户端：

    ```shell
    sudo apt-get install nfs-common#安装nfs软件包提供nfs功能，无需包含服务组件
    sudo mount -t nfs 192.168.199.190:/home/hust/minicar /mnt#将服务器共享文件挂载到指定目录/mnt
    ```

+ 测试NFS访问

  ```shell
  df -h
  ```

+ 卸载NFS远程共享

  ```shell
  cd ~
  fuser -n /mnt #如果卸载失败，检查一下设在使用/mnt
  sudo umount /mnt
  ```

## stat函数

stat命令可以查看文件的属性，这个命令内部就是通过调用stat()函数来获取文件属性的。



# thrift

用于服务器之间的交互

# 用户及用户组

+ 新增用户`adduser`

```shell
adduser roboxs#可以查看SFC，13.2节
```

+ 修改用户的相关数据usermod

```shell
usermode -aG sudo roboxs#将roboxs加入sudo组中
```

+ 查看当前用户所在组

```shell
groups roboxs#查看当前用户所在用户组
```

+ 查看UID(user ID)和GID(group ID)

```shell
cat /etc/passwd
```

+ 切换用户

```shell
sudo su roboxs
```

# SSH

+ 远程登录服务器

```shell
ssh user@hostname#user:用户名，hostname：IP地址121.196.169.77
ssh user@hostname -p 22#默认端口是22，如果像登录特定端口使用该指令
```

+ ssh免密登录

配置文件，可以使用别名登录服务器

```shell
cd .ssh/
mkdir config
vim config
#输入
Host myserver1
    HostName IP地址或域名
    User 用户名
```

密匙登录

```shell
ssh-kengen #创建密匙
```

执行结束后，~/.ssh/目录下会多两个文件：
id_rsa：私钥
id_rsa.pub：公钥

之后想免密码登录哪个服务器，就将公钥传给哪个服务器即可。例如，想免密登录myserver服务器。则将公钥中的内容，复制到myserver中的~/.ssh/authorized_keys文件里即可。

```shell
#首先登录到远程服务器，建立.ssh/authorized_keys
#将公钥复制到.ssh/authorized_keys
```

也可以使用如下命令一键添加公钥：

```shell
ssh-copy-id myserver1#myserver1是需要将公钥复制的服务器
```

+ ssh传输

在执行命令之前先建立ssh（Secure Shell）

通过本地上传到服务器的指令：

```shell
scp -r /Downloads/hit pi@192.168.0.111:/home/pi 
scp -r /Downloads/hit roboxs:/home/pi#使用别名
```

特定服务器端口进行上传：

```shell
scp -P 22 source1 source2 destination
```

# tmux

+ 作用：

可以分屏操作服务器

在tmux中执行命令，不会因为终端关闭使得命令停止

![image-20220625111048710](linux.assets/image-20220625111048710-1702577632843-12.png)

+ tmux快捷键

  tmux的快捷键有一个前缀。

  垂直平分pane：ctrl+a；%

  水平平分pane：ctrl+a；“

  退出pane：ctrl+d

  选中pane：ctrl+a；方向键

  调整pane的大小：ctrl+a+方向键

  全屏/取消全屏：ctrl+a；z

  挂起session：ctrl+a；d          唤醒session：tmux a
