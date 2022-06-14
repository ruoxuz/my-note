# Linux系统中的 Swap 交换分区的方法

1、查看 Linux 当前 Swap 分区

命令如下：

free -m
2、关闭 Swap 分区

如果我们需要增加 Swap 分区，则先关闭 Swap 分区，命令如下：

swapoff -a
如果无法关闭 Swap 分区，那可能是因为物理内存当前用量 + Swap 分区当前用量已经超过了物理内存的总大小，我们可以先重启服务器，在机器刚启动的时候关闭 Swap 分区。

3、创建 Swap 分区文件

创建命令如下，其中 /var/swapfile 是文件位置，bs*count 是文件大下，例如以下命令就会创建一个 4G 的文件：

dd if=/dev/zero of=/var/swapfile bs=1M count=4096
4、建立 Swap 文件系统

将刚才的文件格式化为 Swap 分区文件，命令如下：

mkswap /var/swapfile
5、启用 Swap 分区

基于新的 Swap 分区文件开启 Swap 分区：

swapon /var/swapfile
6、查看 Linux 当前分区

查看是否成功新建/增加 Swap 分区：

free -m
如下图所示，已经成功创建了一个 4GB 的 Swap 分区空间：

SWAP教程
7、设置开启启动

在 /etc/fstab 文件中加入以下代码让 Linux 交换分区开机自动挂载

[Linux系统中的 Swap 交换分区的方法教程（关闭、新建、增加）](https://baijiahao.baidu.com/s?id=1713423286818415642&wfr=spider&for=pc)

[在Linux系统上扩展swap空间](https://blog.csdn.net/weixin_43509037/article/details/123107973)
