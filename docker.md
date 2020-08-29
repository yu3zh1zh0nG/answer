## 1. 拉取ubuntu 镜像
- pull
```
docker pull ubuntu:18.04
```
- run 
```
ubuntu
docker run -it --name ubuntu.18.04 -p 80:80 -v S:\label:/opt/git/label [--privileged=true] -d ubuntu:18.04
centos
docker run -it --name fcmweb -p 80:80 -p 5901:5901 -v S:\label:/opt/git/label --privileged=true -d centos:7.7.1908 /sbin/init
```

- 进入容器
```
docker exec -it containerId bash
```
- 更新源 安装vim
```
cd /etc/apt 
mv sources.list sources.list.bak

echo deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse >>sources.list
echo deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse >>sources.list
echo deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse >>sources.list
echo deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse >>sources.list
echo deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse >>sources.list
echo deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse >>sources.list
echo deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse >>sources.list
echo deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse >>sources.list
echo deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse >>sources.list
echo deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse >>sources.list
```

```
gpg --keyserver keyserver.ubuntu.com --recv 3B4FE6ACC0B21F32
gpg --export --armor 3B4FE6ACC0B21F32 |  apt-key add - 
```


```
apt update 
apt install -y libgl1-mesa-glx # opencv

apt install vim
```


- setup 必要的包
```
apt update
apt install -y nano git libsm6 libxrender1 libxext-dev libglib2.0-dev
apt install -y libc6-dev gcc make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libffi-dev
```



## 2. 安装nginx 
- install 
```
apt install nginx
```
- /etc/nginx/sites-available/default
```
log_format access_main '[$time_local] - $status - [$request_time(s)] - "$request" - $body_bytes_sent(bit) - $remote_addr';
upstream api {
    server 127.0.0.1:5000;
}
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /opt/git/label/html;
    index index.html;
    server_name _;
    location /test {
        try_files $uri $uri/ =404;
        expires 1s;
    }
    location / {
        proxy_pass http://api;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    location /data {
        root /;
    }
    location /api/doc {
        root /opt/git/label;
    }
    location /data/doc {
        root /opt/git/label;
    }
}
```
- /etc/nginx/nginx.conf
```
将下行加入http 中
    client_max_body_size 500m;
```

## 3. 安装 mysql 
- 安装mysql
```
apt-get install mysql-server
```
- 设置mysql 权限
```
chown -R mysql:mysql /var/lib/mysql
```
- 启动mysql 服务
```
service mysql start
use mysql;

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';

FLUSH PRIVILEGES;

SELECT Host, User, plugin from user;

# 新建用户添加权限
grant all privileges on database.table to test@localhost identified by '1234';
FLUSH PRIVILEGES;
```

## 4. 安装 mongo
- https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/
- 下载
```
wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | apt-key add -
```
- 安装
```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-4.2.list

apt-get update

apt-get install -y mongodb-org

echo "mongodb-org hold" | dpkg --set-selections
echo "mongodb-org-server hold" | dpkg --set-selections
echo "mongodb-org-shell hold" | dpkg --set-selections
echo "mongodb-org-mongos hold" | dpkg --set-selections
echo "mongodb-org-tools hold" | dpkg --set-selections


```
- 启动 
```
需要将原有的/data/db 下的内容全部删除
mkdir -p /data/db

mongod &
```
## 5. 安装 redis
- 安装redis
```
apt install redis
```
- 启动redis
```
service redis-server start
```
## 6. 安装 python
- install pyenv
```
git clone git://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc
#exec $SHELL -l
```
- install python 3.6.7
```
apt install wget
# wget https://www.python.org/ftp/python/3.6.7/Python-3.6.7.tar.xz
wget https://mirrors.huaweicloud.com/python/3.8.5/Python-3.8.5.tar.xz
mkdir ~/.pyenv/cache
mv Python-3.6.7.tar.xz ~/.pyenv/cache

apt install libbz2-dev
apt install libssl-dev
apt install libreadline6-dev
apt install libsqlite3-dev
pyenv install 3.6.7
```
- set global
```
pyenv global 3.6.7
pyenv versions

```
- python librarys
```
while read r; do pip install -i https://pypi.tuna.tsinghua.edu.cn/simple $r;done < /data/workspace/requirements.txt
```
```
label 
pip install --upgrade pip
pip install jwt
pip install pyjwt
pip install flask
pip install pymongo
pip install sqlalchemy
pip install pyyaml
pip install pymysql
pip install pillow
pip install matplotlib
pip install opencv-python
pip install shapely
```

## 6. docker cmd
- entry container
```
docker exec -it containerId bash
docker exec -it containerId /bin/bash
```
- start container
```
docker start containerId
```
- stop container
```
docker stop containerId
```
- container -> image
```
docker commit -m "some msg" containerId REPOSITORY:TAG
```
- image->tar
```
docker save REPOSITORY:TAG -o path/**.tar
```
- tar ->image
```
docker load -i path/**.tar
```



