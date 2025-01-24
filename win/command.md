# Command Prompt (cmd)
## Basic Commands
``` bash
systeminfo # 显示系统信息
cd /d D:\\ # 切换盘符
dir # 列出当前目录下
mkdir <目录名> # 创建目录
rmdir <目录名> # 删除目录
del <文件名> # 删除文件
copy <源文件路径> <目标路径> # 复制文件
move <源文件路径> <目标路径> # 移动文件
ren <源文件名> <目标文件名> # 重命名文件
```

## task manager
``` bash
tasklist # 显示所有正在运行的进程
taskkill /F /IM <进程名> # 结束进程
taskkill /F /pid <进程ID> 
chkdsk  # 检查磁盘

D: # 切换盘符
shutdown /r /t 0 # 重启计算机
shutdown /s /t 0 # 关闭计算机

ver # 显示Windows版本
```

## useful commands
``` bash
findstr <关键字> <文件路径> # 在文件中查找关键字
findstr "error" C:\logs\server.log

dir > output.txt # 将命令输出保存到文件
dir >> output.txt # 将命令输出追加到文件
 
set # 显示环境变量
set PATH=%PATH%;C:\newpath # 添加环境变量


 
```

