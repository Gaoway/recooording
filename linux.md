
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
