# 申请阿里云
# root/Young1708

# 安装nodejs
	+ wget https://nodejs.org/dist/v6.9.5/node-v6.9.5-linux-x64.tar.xz
	+ tar xvf node-v6.9.5-linux-x64.tar.xz
	+ 切换到 root 用户
    + vi /etc/profile
    + ```在最下面加入
       # node （注释作用）
       export NODE_HOME=/usr/local/src/node
       export PATH=$PATH:$NODE_HOME/bin  
       export NODE_PATH=$NODE_HOME/lib/node_modules 
		:wq （保存并退出）
		source /etc/profile （使配置文件生效）
		```
	+ npm config set registry https://registry.npm.taobao.org 
	+ npm info underscore