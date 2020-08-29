# 一. owncloud云盘
### 1. pull image
- docker pull owncloud

### 2. run container
docker run --name cloud -p 8080:80  -v /data/db/owncloud:/var/www/html/data -d owncloud

### 3. permission
chmod 770 /data/db/owncloud
chmod 770 /var/www/html/data



### 4. 连接
##### linux
```
yum install davfs2
mount -t davfs http://IP:PORT/remote.php/webdav /mnt/
```
##### win
- 访问WEB ```http://IP:PORT```
- 映射网络驱动盘
    ```
    1. 修改册表方法
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WebClient\Parameters
    找到BasicAuthLevel把这个值从1改为2，然后进控制面板，服务，把WebClient服务重启
    2. 映射
    http://ip:port/remote.php/webdav
    ```
- 安装RaiDrive 软件


# 二. SAMBA服务docker
### 1. pull image
```
docker pull docker.io/dperson/samba 
```
### 2. 创建容器
```
docker run -it --name samba_docker -p 139:139 -p 445:445 -v /data/smb_docker:/data/share -d dperson/samba
```

### 3. 创建组和用户
##### (1). 创建组
```
groupadd groupNameA
groupadd groupNameB
groupadd groupNameC
```
##### (2). 创建用户并关联组
```
useradd -g groupNameA -s /sbin/nologin userNameA
useradd -g groupNameB -s /sbin/nologin userNameB
useradd -g groupNameC -s /sbin/nologin userNameC
```
##### (3). 创建samba用户并设置密码
```
1. 新版tdbsam管理帐号
pdbedit -a username    #新建Samba账户
pdbedit -x username    #删除Samba账户
pdbedit -v username    #显示账户详细信息
pdbedit -L             #列出Samba用户列表，读取passdb.tdb数据库文件
pdbedit -Lv            #列出Samba用户列表详细信息

2. 旧版smbpasswd管理帐号
smbpasswd -a userNameA
smbpasswd -a userNameB
smbpasswd -a userNameB
```
##### (4). 更改配置文件
```
cd /etc/samba/
cp smb.conf smbBak.conf # 备份
vi smb.conf
```
##### (5). 配置文件
```
[fileNameA]
comment = This is fileNameA 
path = /data/share/fileNameA
read only = no
guest os = no
writable = yes
admin users = userNameA
valid users = @groupNameA,@groupNameC
write list = @groupNameA
create mask = 0774
directory mask = 0775
```