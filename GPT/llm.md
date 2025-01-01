## mixtral:8x7b
1. 混合专家模型（Mixture of Experts）
2. Mixtral 8x7B的参数总量并非简单的“8 X 7 = 56B”的参数量，但因为实际上独立存在的仅有各个层当中的专家网络，即FFN部分，而其余的例如Attention层则是共享参数，因此实际上只有47B左右的参数总量。



3. cd /etc/apt/&&
cp sources.list sources.list.bk&&
echo deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free > sources.list&&
echo deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free >> sources.list&&
echo deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free >> sources.list&&
echo deb https://mirrors.tuna.tsinghua.edu.cn/debian-security/ buster/updates main contrib non-free >> sources.list&&
echo deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free >> sources.list&&
echo deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free >> sources.list&&
echo deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free >> sources.list&&
echo deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security/ buster/updates main contrib non-free >> sources.list&&
apt update&&
echo 换源已完成！&&
cd /home


## 模型系列调研
LLaMA

GPT

OPT

QWen




## 推测解码
