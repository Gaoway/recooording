### 磁盘管理合并U盘分区

命令提示符窗口
```
diskpart （进入磁盘部分交互环境。）
lis dis （显示所有的磁盘。）
sel dis 1 （选定磁盘1也就是U盘。）
clean （删除磁盘下的所有分区。）(DiskPart 遇到错误: 指定不存在的设备。有关详细信息，请参阅系统事件日志。)
create partition primary （并回车，在磁盘下创建一个主分区。）
active （激活主分区。）
format fs=exfat quick （快速格式化主分区为fat32格式。加quick的命令表示快速格式化，没加quick表示完全格式化。）
exit （退出diskpart交互环境。）
```