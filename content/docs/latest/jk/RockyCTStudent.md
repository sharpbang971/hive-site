---
title: "RockyLinuxCT : Student Template"
date: 2026-04-15
---

# PVE
## NET
### ip forward
```javascript
root@pve2:~# sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0
root@pve2:~# grep net.ipv4.ip_forward /etc/sysctl.conf
#net.ipv4.ip_forward=1
root@pve2:~# sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf
root@pve2:~# grep net.ipv4.ip_forward /etc/sysctl.conf
net.ipv4.ip_forward=1
root@pve2:~# sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0
root@pve2:~# sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
root@pve2:~# sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 1
root@pve2:~# 
```

### iproute
```javascript
auto vmbr1
iface vmbr1 inet static
    address 192.168.102.1/24
    bridge-ports none
    bridge-stp off
    bridge-fd 0
    post-up iptables -t nat -A POSTROUTING -s '192.168.102.0/24' -o vmbr0 -j MASQUERADE
    post-down iptables -t nat -D POSTROUTING -s '192.168.102.0/24' -o vmbr0 -j MASQUERADE
```
### 安全重启网络
```javascript
# 安装工具（如果还没安装）
apt update && apt install -y ifupdown2

# 安全检查并应用配置
ifreload -a
```

# CT
## Mirror
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
dnf -y install sudo passwd openssh-server openssh-clients vim wget net-tools tar findutils procps-ng
```

## TimeZone
```bash
[root@stu00pc etc]# date
Wed Apr 15 20:50:30 UTC 2026
[root@stu00pc etc]# ls localtime  -l
lrwxrwxrwx 1 root root 25 Sep 29  2021 localtime -> ../usr/share/zoneinfo/UTC
[root@stu00pc etc]# ln -snfv ../usr/share/zoneinfo/Asia/Shanghai localtime
'localtime' -> '../usr/share/zoneinfo/Asia/Shanghai'
[root@stu00pc etc]# ls localtime -l
lrwxrwxrwx 1 root root 35 Apr 16 04:50 localtime -> ../usr/share/zoneinfo/Asia/Shanghai
[root@stu00pc etc]# date
Thu Apr 16 04:51:01 CST 2026
[root@stu00pc etc]# 
```

## SSHD
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

### JDK8
```javascript
wget -c http://10.193.139.44:8080/bigdata/jdk/jdk-8u461-linux-x64.tar.gz
```

## Clean
### clean dnf
```javascript
[hadoop@stu00pc Download]$ sudo dnf clean all
0 files removed

[hadoop@stu00pc Download]$ du -sh /var/cache/dnf/
133M    /var/cache/dnf/

[hadoop@stu00pc Download]$ sudo rm -rf /var/cache/dnf
[hadoop@stu00pc Download]$ 
```

### clean sshd
```javascript
[hadoop@stu00pc Download]$ sudo ls /etc/ssh/ssh_host_*
/etc/ssh/ssh_host_dsa_key      /etc/ssh/ssh_host_ecdsa_key.pub    /etc/ssh/ssh_host_rsa_key
/etc/ssh/ssh_host_dsa_key.pub  /etc/ssh/ssh_host_ed25519_key      /etc/ssh/ssh_host_rsa_key.pub
/etc/ssh/ssh_host_ecdsa_key    /etc/ssh/ssh_host_ed25519_key.pub
[hadoop@stu00pc Download]$ sudo rm -f /etc/ssh/ssh_host_*
[hadoop@stu00pc Download]$ sudo ls /etc/ssh/
moduli  ssh_config  ssh_config.d  sshd_config
[hadoop@stu00pc Download]$ 
```

# PVE BATCH

## PVE CREATE
```javascript
#!/bin/bash

# PVE 批量克隆脚本 - 大数据专业定制版（含磁盘限额）

# ================= 配置区 =================
TEMPLATE_ID=131           # 您的 Rocky Linux 8 模板 ID
BRIDGE="vmbr1"            # 学生私有网桥
GATEWAY="192.168.102.1"   # 宿主机网关
DISK_SIZE="40G"           # 限制每个学生的根磁盘大小
# ==========================================

echo "开始为大数据专业 44 名学生克隆环境 (配额: $DISK_SIZE)..."

for i in {10..10}; do
    # 格式化
    STU_ID=$(printf "%02d" $i)
    VMID=$((200 + i))             # 容器ID从 201 开始
    HOSTNAME="stu${STU_ID}pc"
    IP="192.168.102.$((100 + i))/24"
    
    echo "------------------------------------------------"
    echo "正在处理: $HOSTNAME (ID: $VMID)"
    
    # 1. 链接克隆 (--full 0 极速模式)
    # 并在克隆时直接指定 rootfs 存储位置和大小
    pct clone $TEMPLATE_ID $VMID \
        --hostname $HOSTNAME \
        --full 0
    
    # 2. 修改网络配置、DNS（建议增加）和开机自启
    pct set $VMID \
        --net0 name=eth0,bridge=$BRIDGE,ip=$IP,gw=$GATEWAY \
        --nameserver 114.114.114.114 \
        --onboot 1
    
    echo "✅ $HOSTNAME (IP: $IP) 克隆并限额成功。"
done

echo "------------------------------------------------"
echo "🎉 44 名学生容器已全部创建并限制在 $DISK_SIZE 内！"
```

## PVE START
```javascript

root@pve2:~# cat pvectstartall.sh 
for i in {201..244}; do
    pct start $i; echo "正在启动 ID: $i"
done

```

## PVE DESTROY
```javascript

root@pve2:~# cat pvectdestroy.sh 
for i in {201..244}; do
    pct destroy $i --force 1 --purge 1 --destroy-unreferenced-disks 1
done

```

# NETWORK
总网关
```javascript
    post-up ip route add 192.168.102.0/24 via 192.168.100.12
    post-up for i in $(seq 01 44); do iptables -t nat -A PREROUTING -p tcp --dport $((29900+i)) -j DNAT --to 192.168.102.$((100+i)):22; done

ifreload -a
```
本地路由
```javascript

auto vmbr0
iface vmbr0 inet static
	address 192.168.100.12/24
	gateway 192.168.100.1
	bridge-ports eno1
	bridge-stp off
	bridge-fd 0
	hwaddress ether dc:f4:01:e8:0d:fe

auto vmbr1
iface vmbr1 inet static
	address 192.168.102.1/24
	bridge-ports none
	bridge-stp off
	bridge-fd 0
	post-up iptables -t nat -A POSTROUTING -s '192.168.102.0/24' -o vmbr0 -j MASQUERADE
	post-down iptables -t nat -D POSTROUTING -s '192.168.102.0/24' -o vmbr0 -j MASQUERADE

```