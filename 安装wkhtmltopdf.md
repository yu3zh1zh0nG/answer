
# centos

1. 下载rpm包：
    - 直接去官网下载
    - wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox-0.12.5-1.centos7.x86_64.rpm
2. 安装：
    - rpm -ivh wkhtmltox-0.12.5-1.centos7.x86_64.rpm
3. 安装过程中可能会提示缺少某些依赖
    -（如：xorg-x11-fonts-75dpi、xorg-x11-fonts-Type1、libXrender）
4. 安装完成后就可以测试一下了 
    - wkhtmltopdf http://www.baidu.com ./test.png
5. 测试结果发现生成的pdf不是想要的，好多空白，这个时候我们可以尝试把Windows系统中C:\Windows\Fonts\simsun.ttc(或者之前服务器/usr/share/fonts)上传至Linux系统中的目录/usr/share/fonts中，然后再次测试下


# ubuntu

1. vi /etc/resolv.conf
    - nameserver 8.8.8.8
    - nameserver 8.8.4.4
2. 下载
    - wget https://downloads.wkhtmltopdf.org/0.12/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
    - apt update
    - apt install fontconfig libfontenc1 xfonts-encodings xfonts-utils xfonts-75dpi xfonts-base -y
    - dpkg -i wkhtmltox_0.12.5-1.bionic_amd64.deb

3. 安装中文语言包
    - apt install  language-pack-zh-han* -y
    - apt install  language-pack-gnome-zh-han* -y
    - apt install  language-pack-kde-zh-han* -y