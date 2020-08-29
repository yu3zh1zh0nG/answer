```
vpn 服务端
https://www.cyberciti.biz/faq/centos-7-0-set-up-openvpn-server-in-5-minutes/


vpn 客户端(https://blog.rj-bai.com/post/136.html)
0. cd /etc/openvpn
1. cp -r client2/* client3 (要用easyrsa,vars)
2. 删除client3 下 pki/
3. 创建新的pki
    ./easyrsa init-pki
3. 生成客户端证书: 
    ./easyrsa gen-req yourname nopass # 输入回车
        
4. 签约客户端证书
    cd /etc/openvpn/easy-rsa
    ./easyrsa import-req /etc/openvpn/yourclient/easy-rsa/3.0.3/pki/reqs/yourname.req yourname
    ./easyrsa sign client zz # 输入yes
5. 配置客户端 .oven文件
    <ca>
        ca.crt
    </ca>
    <cert>
        yourname.crt 
        #新生成的crt:/etc/openvpn/easy-rsa/pki/issued/yourname.crt
    </cert>
    <key>
        yourname.key
        # 新生成的私钥:/etc/openvpn/client3/easy-rsa/3.0.3/pki/private/yourname.key
    </key>
6. 指定客户端IP
    vim /etc/openvpn/server.conf
        server 10.8.4.0 255.255.255.0
        ifconfig-pool-persist ipp.txt
    vim ipp.txt
        zz,10.8.4.4
        yy,10.8.4.6
        deepcyto,10.8.4.8
7. 客户端
    #### centos ####
    yum -y install epel-release
    yum -y install openvpn
    /etc/openvpn 下新建 ca.crt tls-crypt.key
    /etc/openvpn 下新建 client.conf
    openvpn --daemon --cd /etc/openvpn --config client.ovpn --log-append /var/log/openvpn.log
    
    #### ubuntu ####
    下载安装OpenVPN所需的插件 openssl pam lzo
        sudo apt-get install openssl 
        sudo apt-get install libssl-dev
        sudo apt-get install libpam0g-dev
        sudo apt-get install liblzo2-dev
    cd 进入openvpn-<yourversion>
    ./configure
    make
    make install
    完了之后检查是否安装成功
    openvpn --version
    
    # 启动方式:
    /etc/openvpn 下新建 ca.crt tls-crypt.key
    /etc/openvpn 下新建 client.conf
    cd /etc/openvpn 
    cp client.ovpn client.conf
    openvpn --daemon --cd /etc/openvpn --config client.conf --log-append /var/log/openvpn.log
    
```