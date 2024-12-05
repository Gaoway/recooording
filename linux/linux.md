
### VsCode配置ssh免密远程登录
``` bash
mkdir /home/lygao/.ssh
touch /home/lygao/.ssh/authorized_keys
echo rsh-key >> /home/lygao/.ssh/authorized_keys
```

ssh-keygen windows在.ssh目录下生成公钥和私钥

### echo
请注意echo指令默认在行尾增加回车(\n)，如果不想增加回车，可以使用-n参数
``` bash
echo "Raspberry" > test.txt
echo "Intel Galileo" >> test.txt

```

### 移动 并软链接脚本
``` bash
#!/bin/bash

# 检查是否传入了用户名
if [ $# -eq 0 ]; then
    echo "请提供一个用户名作为参数。"
    exit 1
fi

# 目标和源目录，使用传入的参数
USERNAME=$1
SOURCE="/home/$USERNAME"
TARGET="/data/$USERNAME/home"

# 检查并创建目标目录
if [ ! -d "$TARGET" ]; then
    echo "在/data中目录不存在，正在创建..."
    mkdir -p "$TARGET"
fi

# 移动文件
echo "正在移动文件..."
mv $SOURCE/* $TARGET/

# 检查是否移动成功
if [ $? -eq 0 ]; then
    echo "文件移动成功！"
    # 删除旧目录并创建软链接
    rm -r $SOURCE
    ln -s $TARGET $SOURCE
    echo "软链接已创建。"
else
    echo "文件移动失败，请检查原因。"
fi

```

查看文件夹下各个文件夹的大小
``` bash
du -h --max-depth=1
```


### nohup的使用

``` bash
# 运行Python脚本：
nohup python my_script.py &
# 输出重定向：
nohup python my_script.py > output.log 2>&1 &
```

查看后台运行的进程
``` bash
# 使用 ps 查看所有后台进程：
jobs
ps aux | grep my_script.py
# kill 命令终止进程：
kill -9 PID
```


### pdb的使用
``` bash
# 在脚本中插入断点：
import pdb; pdb.set_trace()
# 运行脚本：
python my_script.py
# 使用 pdb 命令：
l
n
c
q 
```

### scp 使用
``` bash
# 从远程主机复制文件夹到本地：
scp -P 8407 -r username@47.122.4.125:/path/ /local/path/ 
# 从本地复制文件到远程主机：
scp -P 8407 -r /local/path/ username@47.122.4.125:/path/
```


### linux使用命令行打印文件
参考网站：`https://www.linuxprobe.com/linux-print-manager.html`
CUPS(Common UNIX Printing System，即通用Unix打印系统) 是一个在Linux和UNIX操作系统上实现打印功能的软件包。CUPS支持多种打印协议，包括IPP(互联网打印协议)、LPD(打印守护程序)、SMB(Windows共享打印机)等。

安装CUPS
``` bash
sudo apt-get install cups
sudo apt install lpr
```
HP驱动：
``` bash
sudo apt-get install hplip
```
启动CUPS服务
``` bash
sudo service cups start
```
打开浏览器，输入地址 `http://localhost:631/`，进入CUPS管理页面。

默认使用USB连接的打印机会自动识别，如果没有识别，可以手动添加。
`lsusb` 命令用于列出系统中所有连接的USB设备的信息。 

lp命令
``` bash   
lp -d printer_name file_name ：打印文件

lpinfo -v  :要求CUPS给出可以使用的后端列表（设备列表）
lpinfo -m  :要求CUPS给出可以使用的驱动列表
lpadmin -p 打印机名称 / -E -v 打印机链接 ：增加打印机并且启动打印机
lpadmin -x 打印机名 ：删除打印机，打印机名先用lpstat -a 查看，删除后用指令确认一下
lpoptions -d 打印机名称 ：指定默认打印机

lpq -a   ：显示出目前所有打印机的工作队列情况
lprm 打印任务id    ：删去一个打印作业

lpstat -p 查看已有打印机
lpstat -a 查看已有打印机         
lpstat -d 查看默认打印机
lpoptions | tr ' ' '\n' : 查看打印机的选项
```