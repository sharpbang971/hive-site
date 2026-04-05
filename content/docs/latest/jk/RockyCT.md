---
title: "RockyLinux : Manual Installation"
date: 2026-04-03
---

# CT
## INIT
### TimeZone
```bash
[root@pseudist ~]# date
Sat Mar 28 21:22:58 UTC 2026
[root@pseudist ~]# ls /etc/localtime -l
lrwxrwxrwx 1 root root 25 Sep 29  2021 /etc/localtime -> ../usr/share/zoneinfo/UTC
[root@pseudist ~]# cd /etc/
[root@pseudist etc]# ln -sf ../usr/share/zoneinfo/Asia/Shanghai localtime 
[root@pseudist etc]# ls /etc/localtime -l
lrwxrwxrwx 1 root root 35 Mar 29 05:23 /etc/localtime -> ../usr/share/zoneinfo/Asia/Shanghai
[root@pseudist ~]# date
Sat Mar 28 21:22:58 UTC 2026
```

### Mirror
REF: https://developer.aliyun.com/mirror/rockylinux
```bash
sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.nju.edu.cn/rocky|g' \
    -i.bak /etc/yum.repos.d/Rocky-*.repo

dnf makecache
dnf update -y
```

## INSTALL
```bash
sudo dnf -y install openssh-server vim wget net-tools tar
```

### SSHD
```bash
[root@pseudist etc]# systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
  Drop-In: /run/systemd/system/sshd.service.d
           └─zzz-lxc-service.conf
   Active: inactive (dead)
     Docs: man:sshd(8)
           man:sshd_config(5)
[root@pseudist etc]# systemctl enable sshd
[root@pseudist etc]# systemctl restart sshd
[root@pseudist etc]# systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
  Drop-In: /run/systemd/system/sshd.service.d
           └─zzz-lxc-service.conf
   Active: active (running) since Sun 2026-03-29 05:39:35 CST; 4s ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 2872 (sshd)
    Tasks: 1 (limit: 1646821)
   Memory: 1.2M
   CGroup: /system.slice/sshd.service
           └─2872 /usr/sbin/sshd -D -oCiphers=aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr,aes256-cbc,aes128-gcm@openssh.com,aes128-ctr,aes128-cbc -oMACs=hmac-sha2-256-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha2-256,hmac-sha1,umac-128@openssh.com,hmac-sha2-512 -oGSSAPIKexAlgorithms=gss-curve25519-sha256-,gss-nistp256-sha256-,gss-group14-sha256-,gss-group16-sha512-,gss-gex-sha1-,gss-group14-sha1- -oKexAlgorithms=curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1 -oHostKeyAlgorithms=ecdsa-sha2-nistp256,ecdsa-sha2-nistp256-cert-v01@openssh.com,ecdsa-sha2-nistp384,ecdsa-sha2-nistp384-cert-v01@openssh.com,ecdsa-sha2-nistp521,ecdsa-sha2-nistp521-cert-v01@openssh.com,ssh-ed25519,ssh-ed25519-cert-v01@openssh.com,rsa-sha2-256,rsa-sha2-256-cert-v01@openssh.com,rsa-sha2-512,rsa-sha2-512-cert-v01@openssh.com,ssh-rsa,ssh-rsa-cert-v01@openssh.com -oPubkeyAcceptedKeyTypes=ecdsa-sha2-nistp256,ecdsa-sha2-nistp256-cert-v01@openssh.com,ecdsa-sha2-nistp384,ecdsa-sha2-nistp384-cert-v01@openssh.com,ecdsa-sha2-nistp521,ecdsa-sha2-nistp521-cert-v01@openssh.com,ssh-ed25519,ssh-ed25519-cert-v01@openssh.com,rsa-sha2-256,rsa-sha2-256-cert-v01@openssh.com,rsa-sha2-512,rsa-sha2-512-cert-v01@openssh.com,ssh-rsa,ssh-rsa-cert-v01@openssh.com -oCASignatureAlgorithms=ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-ed25519,rsa-sha2-256,rsa-sha2-512,ssh-rsa

Mar 29 05:39:35 pseudist systemd[1]: Starting OpenSSH server daemon...
Mar 29 05:39:35 pseudist sshd[2872]: Server listening on 0.0.0.0 port 22.
Mar 29 05:39:35 pseudist sshd[2872]: Server listening on :: port 22.
Mar 29 05:39:35 pseudist systemd[1]: Started OpenSSH server daemon.
```

### Vim
```bash
" ==========================================
" 基础设置
" ==========================================
set nocompatible           " 不兼容 vi 模式
syntax on                  " 开启语法高亮
set number                 " 显示行号
set relativenumber         " 开启相对行号 (方便跳转)
set cursorline             " 高亮显示当前行
set wrap                   " 自动折行
set wildmenu               " 命令模式下补全提示

" ==========================================
" 缩进设置 (脚本编写核心)
" ==========================================
set autoindent             " 自动缩进
set smartindent            " 智能缩进
set tabstop=4              " Tab 占用 4 个空格
set softtabstop=4          " 统一 Tab 键缩进
set shiftwidth=4           " 自动缩进的宽度
set expandtab              " 将 Tab 转换为垂直空格

" ==========================================
" 搜索设置
" ==========================================
set hlsearch               " 高亮搜索结果
set incsearch              " 输入时实时跳转
set ignorecase             " 搜索忽略大小写
set smartcase              " 搜索含大写字母时强制匹配大小写

" ==========================================
" 编码与文件处理
" ==========================================
set encoding=utf-8         " 内部编码
set fileencodings=utf-8,gbk,utf-16 " 自动检测编码
set backspace=indent,eol,start     " 修正退格键行为

" ==========================================
" 脚本编写增强：自动生成文件头 (Shebang)
" ==========================================
autocmd BufNewFile *.sh,*.py exec ":call SetHeader()"
func SetHeader()
    if &filetype == 'sh'
        call setline(1, "#!/bin/bash")
    elseif &filetype == 'python'
        call setline(1, "#!/usr/bin/env python3")
        call append(line("."), "# -*- coding: utf-8 -*-")
    endif
    " 自动定位到文件末尾开始编辑
    normal G
endfunc
```
## User
```bash
[root@pseudist etc]# useradd -m -s /bin/bash jkren
[root@pseudist etc]# usermod -aG wheel jkren
[root@pseudist etc]# passwd jkren
Changing password for user jkren.
New password: 
BAD PASSWORD: The password contains the user name in some form
Retype new password: 
passwd: all authentication tokens updated successfully.

[root@pseudist etc]# cat /etc/sudoers
  1 # %wheel    ALL=(ALL)   NOPASSWD: ALL
111 jkren   ALL=(ALL)   NOPASSWD: ALL 
```
## Other
### ping
```bash
[jkren@pseudist ~]$ ping www.baidu.com
ping: socket: Operation not permitted
[jkren@pseudist ~]$ which ping
/usr/bin/ping
[jkren@pseudist ~]$ ls /usr/bin/ping -l
-rwxr-xr-x 1 root root 67664 Jun 12  2023 /usr/bin/ping
[jkren@pseudist ~]$ sudo chmod +s /usr/bin/ping 
[jkren@pseudist ~]$ ls /usr/bin/ping -l
-rwsr-sr-x 1 root root 67664 Jun 12  2023 /usr/bin/ping
[jkren@pseudist ~]$ ping www.baidu.com
PING www.a.shifen.com (180.101.49.44) 56(84) bytes of data.
64 bytes from 180.101.49.44 (180.101.49.44): icmp_seq=1 ttl=50 time=3.78 ms
64 bytes from 180.101.49.44 (180.101.49.44): icmp_seq=2 ttl=50 time=4.21 ms
^C
```
## Download
### Download
```bash
[jkren@pseudist Download]$ sudo mkdir /opt/modules
[jkren@pseudist Download]$ sudo chown jkren:jkren -R /opt/modules
[jkren@pseudist Download]$ ls -ld /opt/modules
drwxr-xr-x 2 jkren jkren 4096 Mar 29 06:04 /opt/modules
```

### JDK8/17
```javascript
wget -c http://datacenter.local:8080/bigdata/jdk/jdk-8u461-linux-x64.tar.gz
wget -c http://datacenter.local:8080/bigdata/jdk/jdk-17.0.17_linux-x64_bin.tar.gz
```
### HADOOP3.4.1
```javascript
wget -c http://datacenter.local:8080/bigdata/hadoop/hadoop-3.4.1.tar.gz
```
### TEZ0.10.5
```javascript
wget -c http://datacenter.local:8080/bigdata/tez/apache-tez-0.10.5-bin.tar.gz
```
### HIVE4.1.0
```javascript
wget -c http://datacenter.local:8080/bigdata/hive/apache-hive-4.1.0-bin.tar.gz
```
### C-J
```javascript
wget -c http://datacenter.local:8080/bigdata/mysql/mysql-connector-j-8.0.33-1.el8.noarch.rpm
```
### Config
## 解压

```
tar zxf jdk-8u461-linux-x64.tar.gz -C /opt/modules/
tar zxf hadoop-3.4.1.tar.gz -C /opt/modules/
tar zxf apache-tez-0.10.5-bin.tar.gz -C /opt/modules/
tar zxf apache-hive-4.1.0-bin.tar.gz -C /opt/modules/
rpm2archive mysql-connector-j-8.0.33-1.el8.noarch.rpm 
tar zxvf mysql-connector-j-8.0.33-1.el8.noarch.rpm.tgz ./usr/share/java/ --strip-components=3
```

## link
```javascript
[jkren@master modules]$ ln -sfv jdk1.8.0_461/ jdk
'jdk' -> 'jdk1.8.0_461/'
[jkren@master modules]$ ln -sfv hadoop-3.4.1/ hadoop
'hadoop' -> 'hadoop-3.4.1/'
[jkren@master modules]$ ln -sfv apache-tez-0.10.5-bin/ tez
'tez' -> 'apache-tez-0.10.5-bin/'
[jkren@master modules]$ ln -sfv apache-hive-4.1.0-bin/ hive
'hive' -> 'apache-hive-4.1.0-bin/'
[jkren@master modules]$ 
[jkren@master modules]$ ls -l
total 16
drwxrwxr-x 11 jkren jkren 4096 Apr  3 15:48 apache-hive-4.1.0-bin
drwxr-xr-x  5 jkren jkren 4096 May 12  2025 apache-tez-0.10.5-bin
lrwxrwxrwx  1 jkren jkren   13 Apr  3 15:56 hadoop -> hadoop-3.4.1/
drwxr-xr-x 10 jkren jkren 4096 Oct 10  2024 hadoop-3.4.1
lrwxrwxrwx  1 jkren jkren   22 Apr  3 15:57 hive -> apache-hive-4.1.0-bin/
lrwxrwxrwx  1 jkren jkren   13 Apr  3 15:56 jdk -> jdk1.8.0_461/
drwxrwxr-x  8 jkren jkren 4096 Apr  3 15:47 jdk1.8.0_461
lrwxrwxrwx  1 jkren jkren   22 Apr  3 15:56 tez -> apache-tez-0.10.5-bin/
[jkren@master modules]$ 
[jkren@master modules]$ 
[jkren@master modules]$ cd ~/Download/
[jkren@master Download]$ cp -av
cp: missing file operand
Try 'cp --help' for more information.
[jkren@master Download]$ cp -av java/mysql-connector-j* /opt/modules/hive/li
lib/          licenses/     licenses.xml  
[jkren@master Download]$ cp -av java/mysql-connector-j* /opt/modules/hive/lib/
'java/mysql-connector-java.jar' -> '/opt/modules/hive/lib/mysql-connector-java.jar'
'java/mysql-connector-j.jar' -> '/opt/modules/hive/lib/mysql-connector-j.jar'
[jkren@master Download]$ 
```