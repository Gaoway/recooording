# Ollama

## Ollama
修改ollama配置使其可以监听0.0.0.0和修改端口，可以外网访问

vim /etc/systemd/system/ollama.service
修改文件

[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
重现加载
sudo systemctl daemon-reload
sudo systemctl restart ollama

sudo systemctl status ollama
查看日志
journalctl -u ollama.service


## maxkb

MaxKB的安装部署
同样先拉取maxkb的docker镜像：

docker pull 1panel/maxkb:v1.0.4
然后启动maxkb的服务：

docker run -d --name=maxkb -p 8080:8080 -v ~/.maxkb:/var/lib/postgresql/data 1panel/maxkb
接下来就可以通过浏览器访问maxkb的网页，ip即为maxkb和ollama提供服务的机器ip，端口为8080
初次登录的用户名为admin，密码为MaxKB@123..密码包含省略号
https://zhuanlan.zhihu.com/p/694850029