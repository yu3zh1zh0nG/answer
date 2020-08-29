```
cd /etc/yum.repos.d/
# 更新源
mv CentOS-Base.repo CentOS-Base.repo.bak
wget http://mirrors.aliyun.com/repo/Centos-7.repo
mv Centos-7.repo CentOS-Base.repo
yum install -y epel-release
yum clean all
yum makecache
yum update

setenforce 0

yum -y install tigervnc tigervnc-server
yum groupinstall "GNOME Desktop"
#vncserver :5 -geometry 1920x1080

# 添加中文包
yum -y install kde-l10n-Chinese && yum -y reinstall glibc-common
localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
export LC_ALL=zh_CN.utf8
echo 'export LANG=en_US.UTF-8' >> ~/.bashrc
source ~/.bashrc

# mysql
mkdir -p /data/database/mysql/data
mkdir -p /data/database/mysql/logs
chmod 755 /data -R

# 下载mysql 
wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
rpm -ivh mysql57-community-release-el7-9.noarch.rpm
yum -y install mysql-server mysql-devel mysql mysql-workbench

chown mysql:mysql /data/database/mysql/ -R

# 配置mysql
vim /etc/my.cnf
	datadir=/data/database/mysql/data
	socket=/var/lib/mysql/mysql.sock

	# Disabling symbolic-links is recommended to prevent assorted security risks
	symbolic-links=0
    
    #需要新建文件
	log-error=/data/database/mysql/logs/mysqld.log
	pid-file=/data/database/mysql/mysqld.pid

	skip-grant-tables # 设置密码后将此行注释


# 启动mysql服务,修改mysql root密码
service mysqld start
mysql
	update mysql.user set authentication_string=PASSWORD('deepcyto1234') where user='root';
	flush privileges;
# 如果找不到mysql数据库中的user表
    /usr/bin/mysql_install_db --user=mysql --datadir=/usr/local/mysql/data/


# 下载redis
mkdir -p /data/database/redis/data
yum -y install redis
vim /etc/redis.conf
	logfile /data/database/redis/redis.log
	dir /data/database/redis/data
	daemonize = yes # 默认为no, 修改为yes则允许后台执行

# 添加快捷启动(rs)
vim /root/.bashrc
    alias rs='/usr/bin/redis-server /etc/redis.conf'
# 上述方法无效则 访问
    https://blog.csdn.net/sjhuangx/article/details/79633112


#######################################################################################

echo "start install Python3.6.7"
yum -y install zlib* python-pip
yum -y install openssl openssl-devel tk tk-devel

# copy python3.6.7到centos
wget https://www.python.org/ftp/python/3.6.7/Python-3.6.7.tgz
tar zxf Python-3.6.7.tgz
cd Python-3.6.7
mkdir -p /opt/Python/3.6.7
mkdir -p /opt/Python/virtualenv
yum -y install sqlite-devel
./configure --prefix=/opt/Python/3.6.7 --with-ssh --enable-optimizations
make && make install

# 添加环境变量(不操作,直接搭建虚拟环境)
export 查看现有的环境变量,
vim /etc/profile
在文档最后，添加:
# export PATH="原有的PATH+:/opt/Python/3.6.7/bin"
export PATH="/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/binexport:/opt/Python/3.6.7/bin"
保存，退出，然后运行：
#source /etc/profile
不报错则成功。:q


# 搭建 虚拟环境
1. 指定python3.6下载virtualenv
    /opt/Python/3.6.7/bin/python3.6 -m pip install virtualenv
2. 构建虚拟环境
    cd /opt/Python/virtualenv
    /opt/Python/3.6.7/bin/python3.6 -m virtualenv -p /opt/Python/3.6.7/bin/python3 fcm

3. 快捷进入flask虚拟环境
    vim ~/.bashrc
        添加下列一行
        alias py36='source /opt/Python/virtualenv/fcm/bin/activate'
        # alias sudo='sudo env PATH=$PATH'
    source ~/.bashrc
# pip 速度慢
1. Linux下，新建或修改 ~/.pip/pip.conf
    [global]
    index-url = https://pypi.tuna.tsinghua.edu.cn/simple
    [install]
    trusted-host=mirrors.aliyun.com
2. win 下 C:\Users\xx\pip，新建文件pip.ini
    

# 下载Pycharm
https://download.jetbrains.com/python/pycharm-professional-2018.3.4.tar.gz
tar -zxvf pycharm-professional-2018.3.4.tar.gz
# ln -s /data/pycharm/pycharm-2018.3.4/bin/pycharm.sh /usr/sbin/pycharm 链接
sudo gedit /usr/share/applications/Pycharm.desktop
    [Desktop Entry]
    Type=Application
    Name=Pycharm
    GenericName=Pycharm3
    Comment=Pycharm3:The Python IDE
    Exec=sh /路径/pycharm.sh
    Icon=/路径/pycharm.png
    Terminal=pycharm
    Categories=Pycharm; 


echo "start install erlang"
wget https://packages.erlang-solutions.com/erlang-solutions-1.0-1.noarch.rpm
rpm -Uvh erlang-solutions-1.0-1.noarch.rpm
rpm --import https://packages.erlang-solutions.com/rpm/erlang_solutions.asc
yum -y install erlang


echo "start install rabbitmq"
rpm --import https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey
rpm --import https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
rpm --import https://packagecloud.io/gpg.key
vim /etc/yum.repos.d/rabbitmq-server.repo
[bintray-rabbitmq-server]
    name=bintray-rabbitmq-rpm
    baseurl=https://dl.bintray.com/rabbitmq/rpm/rabbitmq-server/v3.7.x/el/7/
    gpgcheck=0
    repo_gpgcheck=0
    enabled=1

yum update
yum -y install rabbitmq-server
rabbitmq-plugins enable rabbitmq_management
service rabbitmq-server start


vim /etc/yum.repos.d/mongodb-enterprise.repo file
    [mongodb-enterprise]
    name=MongoDB Enterprise Repository
    baseurl=https://repo.mongodb.com/yum/redhat/$releasever/mongodb-enterprise/4.0/$basearch/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc

sudo yum install -y mongodb-enterprise
sudo yum install -y mongodb-enterprise-4.0.8 mongodb-enterprise-server-4.0.8 mongodb-enterprise-shell-4.0.8 mongodb-enterprise-mongos-4.0.8 mongodb-enterprise-tools-4.0.8


echo "start config font"
cd /usr/share/fonts/
chmod 755 *

mkfontscale(# 如果没有包 请安装yum install mkfontscale)
mkfontdir
fc-cache -fv
git@codehub.devcloud.huaweicloud.com:DeepFlow00001/fcmweb.git


<!--# 分区-->
<!--fdisk /dev/sbd-->
<!--# 格式化磁盘-->
<!--mkfs.xfs -f  -i size=512 -l size=128m,lazy-count=1 -d agcount=64 /dev/vdb1-->



## 下载谷歌浏览器
1. 官网下载 安装包
    https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm
    
2. 安装依赖包
    yum install -y lsb
    yum install -y libXScrnSaver
    # sudo yum install libappindicator3.so.1
    sudo yum install libappindicator-gtk3
    sudo yum install liberation-fonts

3. 解压 
    sudo rpm -ivh google-chrome-stable_current_x86_64.rpm
```
