| UCF101 | [download](https://drive.google.com/file/d/1mO4KbGJDX-Ez0pNAbsjlwriMuIu9UB0D/view?usp=drive_link) | 3 GB |

SCP
https://www.runoob.com/linux/linux-comm-scp.html
`scp [-1246BCpqrv] [-c cipher] [-F ssh_config] [-i identity_file] [-l limit] [-o ssh_option] [-P port] [-S program] [[user@]host1:]file1 [...] [[user@]host2:]file2`

wget --no-check-certificate http://ftp.tugraz.at/pub/feichtenhofer/tsfusion/data/ucf101_jpegs_256.zip.001

item() 方法将一个只包含单个元素的 tensor 转换为一个 Python 标准数据类型。


在Python中，您可以使用几种方法来对齐打印输出。这里有一些例子：
使用 f-string（Python 3.6+）
python
Copy code
# 左对齐
print(f'{"左对齐":<20}')

# 右对齐
print(f'{"右对齐":>20}')

# 居中对齐
print(f'{"居中对齐":^20}')

# 指定填充字符
print(f'{"居中对齐":*^20}')

使用 ljust(), rjust(), center() 方法
python
Copy code
# 左对齐
print("左对齐".ljust(20))

# 右对齐
print("右对齐".rjust(20))

# 居中对齐
print("居中对齐".center(20))

# 指定填充字符
print("居中对齐".center(20, '*'))

在Linux系统中，你可以使用du命令来检查文件夹的大小。这个命令会递归地遍历文件夹，计算其总大小。
查看指定文件夹的总大小，并显示人类可读的格式（例如KB，MB，GB）：
du -sh /path/to/directory

列出指定文件夹下所有子文件夹的大小：
du -h --max-depth=1 /path/to/directory

在PyTorch中，如果你想计算一个多维张量的总元素数，你可以使用numel()函数，它会返回张量中元素的总数。假设你有一个张量tensor，你可以这样计算
```
total_elements = tensor.numel()```

将多个图像样本转换为一个批次（batch），你需要使用torch.stack函数，它可以将一个图像列表堆叠成一个新的4D张量。这里假设每个self.transform(img)调用返回的是一个3D张量，形状为 [C, H, W]（通道，高度，宽度）。