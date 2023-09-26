# 调整卷大小后扩展 Linux 文件系统

增加 EBS 卷的大小后，您必须使用文件系统特定的命令将文件系统扩展到新的较大大小。您可以在卷进入 optimizing 状态后立即执行此操作。

若要在 Linux 上扩展文件系统，您需要执行以下操作：

如果卷有分区，则扩展分区。

扩展文件系统。

开始前的准备工作
如果需要回滚更改，则创建卷的快照。有关更多信息，请参阅创建 Amazon EBS 快照。

确认卷修改成功并且其处于 optimizing 或 completed 状态。有关更多信息，请参阅监控卷修改的进度。

确保卷已附加到实例，并且已格式化和挂载。有关更多信息，请参阅格式化并挂载附加的卷。

如果在 Amazon EBS 卷上使用逻辑卷，则必须使用逻辑卷管理器 (LVM) 来扩展逻辑卷。有关如何执行此操作的说明，请参阅 AWS 知识中心文章如何在整个 EBS 卷上创建 LVM 逻辑卷？中的扩展逻辑卷部分 。

扩展 EBS 卷的文件系统
按照以下过程扩展调整大小后的卷的文件系统。

请注意，Xen 实例和 Nitro 实例的设备和分区命名有所不同。要确定实例是基于 Xen 还是基于 Nitro，请使用 describe-instance-types AWS CLI 命令，如下所示：

[ec2-user ~]$ aws ec2 describe-instance-types --instance-type instance_type --query "InstanceTypes[].Hypervisor"
nitro 表示实例基于 Nitro。xen 或 xen-on-nitro 则表示实例基于 Xen。

扩展 EBS 卷的文件系统
连接到您的 实例。

如果需要，调整分区的大小。为此，请执行以下操作：

检查卷是否有分区。使用 lsblk 命令。


Nitro instance example

Xen instance example
在以下示例输出中，根卷 (xvda) 有一个分区 (xvda1)，而额外的卷 (xvdf) 没有分区。


[ec2-user ~]$ sudo lsblk                
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  16G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  24G  0 disk
如果该卷具有分区，则继续执行以下步骤（2b）。如果该卷没有分区，请跳过步骤 2b、2c 和 2d，然后继续执行步骤 3。

故障排除技巧
如果没有在命令输出中看到该卷，请确保该卷已附加到实例并且已格式化和挂载。

检查是否需要扩展分区。在上一步的 lsblk 命令输出中，比较分区大小和卷大小。

如果分区大小小于卷大小，则继续执行下一步。如果分区大小等于卷大小，则无法扩展分区。

故障排除技巧
如果卷仍然反映原始大小，则请确认卷修改成功。

扩展分区。使用 growpart 命令并指定要扩展的分区。


Nitro instance example

Xen instance example
例如，若要扩展名为 xvda1 的分区，请使用以下命令。

重要
请注意，设备名称 (xvda) 和分区编号 (1) 之间有空格。


[ec2-user ~]$ sudo growpart /dev/xvda 1
故障排除技巧
mkdir: cannot create directory ‘/tmp/growpart.31171’: No space left on device FAILED: failed to make temp dir：表示卷上没有足够的可用磁盘空间供 growpart 创建执行调整大小所需的临时目录。请释放一些磁盘空间并重试。

must supply partition-number：表示您指定的分区不正确。使用 lsblk 命令以确认分区名称，并确保在设备名称和分区编号之间输入空格。

NOCHANGE: partition 1 is size 16773087. it cannot be grown：表示分区已经扩展了整个卷，无法再扩展。确认卷修改成功。

验证是否已扩展分区。使用 lsblk 命令。分区大小现在应等于卷大小。


Nitro instance example

Xen instance example
以下示例输出显示卷 (xvda) 和分区 (xvda1) 的大小相同 (16 GB)。


[ec2-user ~]$ sudo lsblk               
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  16G  0 disk
└─xvda1 202:1    0  16G  0 part /
xvdf    202:80   0  24G  0 disk
扩展文件系统。

获取需要扩展的文件系统的名称、大小、类型和挂载点。使用 df -hT 命令。


Nitro instance example

Xen instance example
以下示例输出显示了 /dev/xvda1 文件系统的大小为 8 GB，其类型为 ext4，其挂载点是 /。


[ec2-user ~]$ df -hT
Filesystem      Type   Size    Used   Avail   Use%   Mounted on
/dev/xvda1      ext4   8.0G    1.9G   6.2G    24%    /
/dev/xvdf1      xfs    24.0G   45M    8.0G    1%     /data
...
扩展文件系统的命令因文件系统类型而异。根据您在上一步中记录的文件系统类型选择以下正确命令。

[XFS 文件系统] 使用 xfs_growfs 命令并指定您在上一步中记录的文件系统的挂载点。


Nitro and Xen instance example
例如，若要扩展挂载在 / 上的文件系统，请使用以下命令。


[ec2-user ~]$ sudo xfs_growfs -d /
故障排除技巧
xfs_growfs: /data is not a mounted XFS filesystem：表示指定的挂载点不正确，或者文件系统不是 XFS。若要验证挂载点和文件系统类型，请使用 df -hT 命令。

data size unchanged, skipping：表示文件系统已经扩展了整个卷。如果卷没有分区，则请确认卷修改成功。如果卷有分区，则请确保该分区已按照步骤 2 中的说明进行扩展。

[Ext4 文件系统] 使用 resize2fs 命令并指定您在上一步中记录的文件系统的名称。


Nitro instance example

Xen instance example
例如，若要扩展名为 /dev/xvda1 的挂载文件系统，请使用以下命令。


[ec2-user ~]$ sudo resize2fs /dev/xvda1
故障排除技巧
resize2fs: Bad magic number in super-block while trying to open /dev/xvda1：表示文件系统不是 Ext4。若要验证文件系统类型，请使用 df -hT 命令。

open: No such file or directory while opening /dev/xvdb1：表示您指定的分区不正确。若要验证分区，请使用 df -hT 命令。

The filesystem is already 3932160 blocks long. Nothing to do!：表示文件系统已经扩展了整个卷。如果卷没有分区，则请确认卷修改成功。如果卷有分区，则请确保该分区已按照步骤 2 中的说明进行扩展。

[其他文件系统]，请参阅文件系统的文档，了解相关说明。

验证是否已扩展文件系统。使用 df -hT 命令并确认文件系统大小等于卷大小。

[引用](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html)
