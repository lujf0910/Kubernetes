# 1.环境

# 文档中所需的文件资料
# 参考：https://github.com/cby-chen/Kubernetes/releases/tag/cby

| 主机名称     | IP地址         | 说明       | 软件                                                                                             |
| -------- | ------------ | -------- | ---------------------------------------------------------------------------------------------- |
| Master01 | 192.168.1.30 | master节点 | kube-apiserver、kube-controller-manager、kube-scheduler、etcd、<br />kubelet、kube-proxy、nfs-client |
| Master02 | 192.168.1.31 | master节点 | kube-apiserver、kube-controller-manager、kube-scheduler、etcd、<br />kubelet、kube-proxy、nfs-client |
| Master03 | 192.168.1.32 | master节点 | kube-apiserver、kube-controller-manager、kube-scheduler、etcd、<br />kubelet、kube-proxy、nfs-client |
| Node01   | 192.168.1.33 | node节点   | kubelet、kube-proxy、nfs-client                                                                  |
| Node02   | 192.168.1.34 | node节点   | kubelet、kube-proxy、nfs-client                                                                  |
| Node03   | 192.168.1.35 | node节点   | kubelet、kube-proxy、nfs-client                                                                  |
| Node04   | 192.168.1.36 | node节点   | kubelet、kube-proxy、nfs-client                                                                  |
| Node05   | 192.168.1.37 | node节点   | kubelet、kube-proxy、nfs-client                                                                  |
| Lb01     | 192.168.1.38 | Lb01节点   | haproxy、keepalived                                                                             |
| Lb02     | 192.168.1.39 | Lb02节点   | haproxy、keepalived                                                                             |
|          | 192.168.1.88 | VIP      |                                                                                                |
|          |              |          |                                                                                                |

| 软件                                                                       | 版本                         |
|:------------------------------------------------------------------------ |:-------------------------- |
| 内核                                                                       | 5.16.7-1.el8.elrepo.x86_64 |
| CentOS 8                                                                 | v8                         |
| kube-apiserver、kube-controller-manager、kube-scheduler、kubelet、kube-proxy | v1.23.4                    |
| etcd                                                                     | v3.5.2                     |
| docker-ce                                                                | v20.10.9                   |
| containerd                                                               | v1.6.0                     |
| cfssl                                                                    | v1.6.1                     |
| cni                                                                      | v1.6.0                     |
| crictl                                                                   | v1.23.0                    |
| haproxy                                                                  | v1.8.27                    |
| keepalived                                                               | v2.1.5                     |

网段

物理主机：192.168.1.0/24

service：10.96.0.0/12

pod：172.16.0.0/12

如果有条件建议k8s集群与etcd集群分开安装

## 1.1.k8s基础系统环境配置

### 1.2.配置IP

```shell
ssh root@192.168.1.76 "nmcli con mod ens18 ipv4.addresses 192.168.1.30/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.77 "nmcli con mod ens18 ipv4.addresses 192.168.1.31/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.78 "nmcli con mod ens18 ipv4.addresses 192.168.1.32/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.79 "nmcli con mod ens18 ipv4.addresses 192.168.1.33/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.80 "nmcli con mod ens18 ipv4.addresses 192.168.1.34/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.86 "nmcli con mod ens18 ipv4.addresses 192.168.1.35/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.87 "nmcli con mod ens18 ipv4.addresses 192.168.1.36/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.166 "nmcli con mod ens18 ipv4.addresses 192.168.1.37/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.100 "nmcli con mod ens18 ipv4.addresses 192.168.1.38/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
ssh root@192.168.1.191 "nmcli con mod ens18 ipv4.addresses 192.168.1.39/24; nmcli con mod ens18 ipv4.gateway 192.168.1.99; nmcli con mod ens18 ipv4.method manual; nmcli con mod ens18 ipv4.dns "8.8.8.8"; nmcli con up ens18"
```

### 1.3.设置主机名

```shell
hostnamectl set-hostname k8s-master01
hostnamectl set-hostname k8s-master02
hostnamectl set-hostname k8s-master03
hostnamectl set-hostname k8s-node01
hostnamectl set-hostname k8s-node02
hostnamectl set-hostname k8s-node03
hostnamectl set-hostname k8s-node04
hostnamectl set-hostname k8s-node05
hostnamectl set-hostname lb01
hostnamectl set-hostname lb02
```

### 1.4.配置yum源

```shell
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org/$contentdir|baseurl=http://192.168.1.123/centos|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo


sed -e 's|^mirrorlist=|#mirrorlist=|g' -e 's|^#baseurl=http://mirror.centos.org/\$contentdir|baseurl=http://192.168.1.123/centos|g' -i.bak  /etc/yum.repos.d/CentOS-*.repo
```

### 1.5.安装一些必备工具

```shell
yum -y install wget jq psmisc vim net-tools  telnet yum-utils device-mapper-persistent-data lvm2 git network-scripts tar curl -y
```

### 1.6.安装docker工具

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

### 1.7.关闭防火墙

```shell
systemctl disable --now firewalld
```

### 1.8.关闭SELinux

```shell
setenforce 0
sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/sysconfig/selinux
```

### 1.9.关闭交换分区

```shell
sed -ri 's/.*swap.*/#&/' /etc/fstab
swapoff -a && sysctl -w vm.swappiness=0
cat /etc/fstab

# /dev/mapper/centos-swap swap                    swap    defaults        0 0
```

### 1.10.关闭NetworkManager 并启用 network

```shell
systemctl disable --now NetworkManager
systemctl start network && systemctl enable network
```

### 1.11.进行时间同步

```shell
服务端

yum install chrony -y
cat > /etc/chrony.conf << EOF 
pool ntp.aliyun.com iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
allow 192.168.1.0/24
local stratum 10
keyfile /etc/chrony.keys
leapsectz right/UTC
logdir /var/log/chrony
EOF

systemctl restart chronyd
systemctl enable chronyd

客户端

yum install chrony -y
vim /etc/chrony.conf
cat /etc/chrony.conf | grep -v  "^#" | grep -v "^$"
pool 192.168.1.30 iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
keyfile /etc/chrony.keys
leapsectz right/UTC
logdir /var/log/chrony

systemctl restart chronyd ; systemctl enable chronyd


yum install chrony -y ; sed -i "s#2.centos.pool.ntp.org#192.168.1.30#g" /etc/chrony.conf ; systemctl restart chronyd ; systemctl enable chronyd


使用客户端进行验证

chronyc sources -v
```

### 1.12.配置ulimit

```shell
ulimit -SHn 65535
cat >> /etc/security/limits.conf <<EOF
* soft nofile 655360
* hard nofile 131072
* soft nproc 655350
* hard nproc 655350
* seft memlock unlimited
* hard memlock unlimitedd
EOF
```

### 1.13.配置免密登录

```shell
yum install -y sshpass
ssh-keygen -f /root/.ssh/id_rsa -P ''
export IP="192.168.1.30 192.168.1.31 192.168.1.32 192.168.1.33 192.168.1.34 192.168.1.35 192.168.1.36 192.168.1.37 192.168.1.38 192.168.1.39"
export SSHPASS=123123
for HOST in $IP;do
     sshpass -e ssh-copy-id -o StrictHostKeyChecking=no $HOST
done
```

### 1.14.添加启用源

```shell
为 RHEL-8或 CentOS-8配置源
yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm

为 RHEL-7 SL-7 或 CentOS-7 安装 ELRepo 
yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm

查看可用安装包
yum  --disablerepo="*"  --enablerepo="elrepo-kernel"  list  available
```

### 1.15.升级内核至4.18版本以上

```shell
安装最新的内核
# 我这里选择的是稳定版kernel-ml   如需更新长期维护版本kernel-lt  
yum  --enablerepo=elrepo-kernel  install  kernel-ml

查看已安装那些内核
rpm -qa | grep kernel
kernel-core-4.18.0-358.el8.x86_64
kernel-tools-4.18.0-358.el8.x86_64
kernel-ml-core-5.16.7-1.el8.elrepo.x86_64
kernel-ml-5.16.7-1.el8.elrepo.x86_64
kernel-modules-4.18.0-358.el8.x86_64
kernel-4.18.0-358.el8.x86_64
kernel-tools-libs-4.18.0-358.el8.x86_64
kernel-ml-modules-5.16.7-1.el8.elrepo.x86_64


查看默认内核
grubby --default-kernel
/boot/vmlinuz-5.16.7-1.el8.elrepo.x86_64


若不是最新的使用命令设置
grubby --set-default /boot/vmlinuz-「您的内核版本」.x86_64

重启生效
reboot

整合命令为：
yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm -y ; yum  --disablerepo="*"  --enablerepo="elrepo-kernel"  list  available -y ; yum  --enablerepo=elrepo-kernel  install  kernel-ml -y ; grubby --default-kernel ; reboot
```

### 1.16.安装ipvsadm

```shell
yum install ipvsadm ipset sysstat conntrack libseccomp -y

cat >> /etc/modules-load.d/ipvs.conf <<EOF
cat 
ip_vs
ip_vs_rr
ip_vs_wrr
ip_vs_sh
nf_conntrack
ip_tables
ip_set
xt_set
ipt_set
ipt_rpfilter
ipt_REJECT
ipip
EOF

systemctl restart systemd-modules-load.service

lsmod | grep -e ip_vs -e nf_conntrack
ip_vs_sh               16384  0
ip_vs_wrr              16384  0
ip_vs_rr               16384  0
ip_vs                 180224  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
nf_conntrack          176128  1 ip_vs
nf_defrag_ipv6         24576  2 nf_conntrack,ip_vs
nf_defrag_ipv4         16384  1 nf_conntrack
libcrc32c              16384  3 nf_conntrack,xfs,ip_vs
```

### 1.17.修改内核参数

```shell
cat <<EOF > /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-iptables = 1
fs.may_detach_mounts = 1
vm.overcommit_memory=1
vm.panic_on_oom=0
fs.inotify.max_user_watches=89100
fs.file-max=52706963
fs.nr_open=52706963
net.netfilter.nf_conntrack_max=2310720


net.ipv4.tcp_keepalive_time = 600
net.ipv4.tcp_keepalive_probes = 3
net.ipv4.tcp_keepalive_intvl =15
net.ipv4.tcp_max_tw_buckets = 36000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_max_orphans = 327680
net.ipv4.tcp_orphan_retries = 3
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.ip_conntrack_max = 65536
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.tcp_timestamps = 0
net.core.somaxconn = 16384
EOF

sysctl --system
```

### 1.18.所有节点配置hosts本地解析

```shell
cat > /etc/hosts <<EOF
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.1.30 k8s-master01
192.168.1.31 k8s-master02
192.168.1.32 k8s-master03
192.168.1.33 k8s-node01
192.168.1.34 k8s-node02
192.168.1.35 k8s-node03
192.168.1.36 k8s-node04
192.168.1.37 k8s-node05
192.168.1.38 lb01
192.168.1.38 lb02
192.168.1.88 lb-vip
EOF
```

# 2.k8s基本组件安装

## 2.1.所有k8s节点安装Containerd作为Runtime

```shell
yum install containerd -y
```

### 2.1.1配置Containerd所需的模块

```shell
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
```

### 2.1.2加载模块

```shell
systemctl restart systemd-modules-load.service
```

### 2.1.3配置Containerd所需的内核

```shell
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

# 加载内核

sysctl --system
```

### 2.1.4创建Containerd的配置文件

```shell
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml


修改Containerd的配置文件
sed -i '/containerd\.runtimes\.runc\.options/ a\\t\tSystemdCgroup\ =\ true' /etc/containerd/config.toml

cat /etc/containerd/config.toml

# 找到containerd.runtimes.runc.options，在其下加入SystemdCgroup = true

[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
              SystemdCgroup = true
    [plugins."io.containerd.grpc.v1.cri".cni]


# 将sandbox_image默认地址改为符合版本地址

    sandbox_image = "registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.6"
```

### 2.1.5启动并设置为开机启动

```shell
systemctl daemon-reload
systemctl enable --now 
```

### 2.1.6配置crictl客户端连接的运行时位置

```shell
cat > /etc/crictl.yaml <<EOF
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
debug: false
EOF
```

## 2.2.k8s与etcd下载及安装（仅在master01操作）

### 2.2.1下载k8s安装包（你用哪个下哪个）

```shell
1.下载kubernetes1.23.+的二进制包
github二进制包下载地址：https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md

wget https://dl.k8s.io/v1.23.4/kubernetes-server-linux-amd64.tar.gz

2.下载etcdctl二进制包
github二进制包下载地址：https://github.com/etcd-io/etcd/releases

wget https://github.com/etcd-io/etcd/releases/download/v3.5.2/etcd-v3.5.2-linux-amd64.tar.gz

3.docker-ce二进制包下载地址
二进制包下载地址：https://download.docker.com/linux/static/stable/x86_64/

这里需要下载20.10.+版本

wget https://download.docker.com/linux/static/stable/x86_64/docker-20.10.9.tgz

4.containerd二进制包下载
github下载地址：https://github.com/containerd/containerd/releases

containerd下载时下载带cni插件的二进制包。

wget https://github.com/containerd/containerd/releases/download/v1.6.0-rc.2/cri-containerd-cni-1.6.0-rc.2-linux-amd64.tar.gz

5.下载cfssl二进制包
github二进制包下载地址：https://github.com/cloudflare/cfssl/releases

wget https://github.com/cloudflare/cfssl/releases/download/v1.6.1/cfssl_1.6.1_linux_amd64
wget https://github.com/cloudflare/cfssl/releases/download/v1.6.1/cfssljson_1.6.1_linux_amd64
wget https://github.com/cloudflare/cfssl/releases/download/v1.6.1/cfssl-certinfo_1.6.1_linux_amd64

6.cni插件下载
github下载地址：https://github.com/containernetworking/plugins/releases

wget https://github.com/containernetworking/plugins/releases/download/v1.0.1/cni-plugins-linux-amd64-v1.0.1.tgz

7.crictl客户端二进制下载
github下载：https://github.com/kubernetes-sigs/cri-tools/releases

wget https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.23.0/crictl-v1.23.0-linux-amd64.tar.gz




解压k8s安装文件
tar -xf kubernetes-server-linux-amd64.tar.gz  --strip-components=3 -C /usr/local/bin kubernetes/server/bin/kube{let,ctl,-apiserver,-controller-manager,-scheduler,-proxy}

解压etcd安装文件
tar -xf etcd-v3.5.2-linux-amd64.tar.gz --strip-components=1 -C /usr/local/bin etcd-v3.5.2-linux-amd64/etcd{,ctl}

# 查看/usr/local/bin下内容

ls /usr/local/bin/
etcd  etcdctl  kube-apiserver  kube-controller-manager  kubectl  kubelet  kube-proxy  kube-scheduler
```

### 2.2.2查看版本

```shell
[root@k8s-master01 ~]# kubelet --version
Kubernetes v1.23.4
[root@k8s-master01 ~]# etcdctl version
etcdctl version: 3.5.1
API version: 3.5
```

### 2.2.3将组件发送至其他k8s节点

```shell
Master='k8s-master02 k8s-master03'
Work='k8s-node01 k8s-node02 k8s-node03 k8s-node04 k8s-node05'
for NODE in $Master; do echo $NODE; scp /usr/local/bin/kube{let,ctl,-apiserver,-controller-manager,-scheduler,-proxy} $NODE:/usr/local/bin/; scp /usr/local/bin/etcd* $NODE:/usr/local/bin/; done
for NODE in $Work; do     scp /usr/local/bin/kube{let,-proxy} $NODE:/usr/local/bin/ ; done
```

### 2.2.4克隆证书相关文件

```shell
git clone https://github.com/cby-chen/Kubernetes.git
```

### 2.2.5所有k8s节点创建目录

```shell
mkdir -p /opt/cni/bin
```

# 3.相关证书生成

```shell
master01节点下载证书生成工具
wget "https://github.com/cloudflare/cfssl/releases/download/v1.6.1/cfssl_1.6.1_linux_amd64" -O /usr/local/bin/cfssl
wget "https://github.com/cloudflare/cfssl/releases/download/v1.6.1/cfssljson_1.6.1_linux_amd64" -O /usr/local/bin/cfssljson
chmod +x /usr/local/bin/cfssl /usr/local/bin/cfssljson
```

## 3.1.生成etcd证书

特别说明除外，以下操作在所有master节点操作

### 3.1.1所有master节点创建证书存放目录

```shell
mkdir /etc/etcd/ssl -p
```

### 3.1.2master01节点生成etcd证书

```shell
cd Kubernetes/pki/

# 生成etcd证书和etcd证书的key（如果你觉得以后可能会扩容，可以在ip那多写几个预留出来）

cfssl gencert -initca etcd-ca-csr.json | cfssljson -bare /etc/etcd/ssl/etcd-ca

cfssl gencert \
   -ca=/etc/etcd/ssl/etcd-ca.pem \
   -ca-key=/etc/etcd/ssl/etcd-ca-key.pem \
   -config=ca-config.json \
   -hostname=127.0.0.1,k8s-master01,k8s-master02,k8s-master03,192.168.1.30,192.168.1.31,192.168.1.32 \
   -profile=kubernetes \
   etcd-csr.json | cfssljson -bare /etc/etcd/ssl/etcd
```

### 3.1.3将证书复制到其他节点

```shell
Master='k8s-master02 k8s-master03'
for NODE in $Master; do
     ssh $NODE "mkdir -p /etc/etcd/ssl"
     for FILE in etcd-ca-key.pem  etcd-ca.pem  etcd-key.pem  etcd.pem; do
       scp /etc/etcd/ssl/${FILE} $NODE:/etc/etcd/ssl/${FILE}
     done
 done
```

## 3.2.生成k8s相关证书

特别说明除外，以下操作在所有master节点操作

### 3.2.1所有k8s节点创建证书存放目录

```shell
mkdir -p /etc/kubernetes/pki
```

### 3.2.2master01节点生成k8s证书

```shell
# 生成一个根证书

cfssl gencert -initca ca-csr.json | cfssljson -bare /etc/kubernetes/pki/ca

# 10.96.0.1是service网段的第一个地址，需要计算，192.168.1.88为高可用vip地址

cfssl gencert   \
-ca=/etc/kubernetes/pki/ca.pem   \
-ca-key=/etc/kubernetes/pki/ca-key.pem   \
-config=ca-config.json   \
-hostname=10.96.0.1,192.168.1.88,127.0.0.1,kubernetes,kubernetes.default,kubernetes.default.svc,kubernetes.default.svc.cluster,kubernetes.default.svc.cluster.local,x.oiox.cn,k.oiox.cn,l.oiox.cn,o.oiox.cn,192.168.1.30,192.168.1.31,192.168.1.32,192.168.1.33,192.168.1.34,192.168.1.35,192.168.1.36,192.168.1.37,192.168.1.38,192.168.1.39,192.168.1.40,192.168.1.41   \
-profile=kubernetes   apiserver-csr.json | cfssljson -bare /etc/kubernetes/pki/apiserver
```

### 3.2.3生成apiserver聚合证书

```shell
cfssl gencert   -initca front-proxy-ca-csr.json | cfssljson -bare /etc/kubernetes/pki/front-proxy-ca 

# 有一个警告，可以忽略

cfssl gencert   -ca=/etc/kubernetes/pki/front-proxy-ca.pem   -ca-key=/etc/kubernetes/pki/front-proxy-ca-key.pem   -config=ca-config.json   -profile=kubernetes   front-proxy-client-csr.json | cfssljson -bare /etc/kubernetes/pki/front-proxy-client
```

### 3.2.4生成controller-manage的证书

```shell
cfssl gencert \
   -ca=/etc/kubernetes/pki/ca.pem \
   -ca-key=/etc/kubernetes/pki/ca-key.pem \
   -config=ca-config.json \
   -profile=kubernetes \
   manager-csr.json | cfssljson -bare /etc/kubernetes/pki/controller-manager

# 设置一个集群项

kubectl config set-cluster kubernetes \
     --certificate-authority=/etc/kubernetes/pki/ca.pem \
     --embed-certs=true \
     --server=https://192.168.1.88:8443 \
     --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig

# 设置一个环境项，一个上下文

kubectl config set-context system:kube-controller-manager@kubernetes \
    --cluster=kubernetes \
    --user=system:kube-controller-manager \
    --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig

# 设置一个用户项

kubectl config set-credentials system:kube-controller-manager \
     --client-certificate=/etc/kubernetes/pki/controller-manager.pem \
     --client-key=/etc/kubernetes/pki/controller-manager-key.pem \
     --embed-certs=true \
     --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig

# 设置默认环境

kubectl config use-context system:kube-controller-manager@kubernetes \
     --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig

cfssl gencert \
   -ca=/etc/kubernetes/pki/ca.pem \
   -ca-key=/etc/kubernetes/pki/ca-key.pem \
   -config=ca-config.json \
   -profile=kubernetes \
   scheduler-csr.json | cfssljson -bare /etc/kubernetes/pki/scheduler

kubectl config set-cluster kubernetes \
     --certificate-authority=/etc/kubernetes/pki/ca.pem \
     --embed-certs=true \
     --server=https://192.168.1.88:8443 \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig

kubectl config set-credentials system:kube-scheduler \
     --client-certificate=/etc/kubernetes/pki/scheduler.pem \
     --client-key=/etc/kubernetes/pki/scheduler-key.pem \
     --embed-certs=true \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig

kubectl config set-context system:kube-scheduler@kubernetes \
     --cluster=kubernetes \
     --user=system:kube-scheduler \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig

kubectl config use-context system:kube-scheduler@kubernetes \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig

cfssl gencert \
   -ca=/etc/kubernetes/pki/ca.pem \
   -ca-key=/etc/kubernetes/pki/ca-key.pem \
   -config=ca-config.json \
   -profile=kubernetes \
   admin-csr.json | cfssljson -bare /etc/kubernetes/pki/admin

kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.1.88:8443     --kubeconfig=/etc/kubernetes/admin.kubeconfig

kubectl config set-credentials kubernetes-admin     --client-certificate=/etc/kubernetes/pki/admin.pem     --client-key=/etc/kubernetes/pki/admin-key.pem     --embed-certs=true     --kubeconfig=/etc/kubernetes/admin.kubeconfig

kubectl config set-context kubernetes-admin@kubernetes     --cluster=kubernetes     --user=kubernetes-admin     --kubeconfig=/etc/kubernetes/admin.kubeconfig

kubectl config use-context kubernetes-admin@kubernetes     --kubeconfig=/etc/kubernetes/admin.kubeconfig
```

### 3.2.5创建ServiceAccount Key ——secret

```shell
openssl genrsa -out /etc/kubernetes/pki/sa.key 2048
openssl rsa -in /etc/kubernetes/pki/sa.key -pubout -out /etc/kubernetes/pki/sa.pub
```

### 3.2.6将证书发送到其他master节点

```shell
for NODE in k8s-master02 k8s-master03; do 
for FILE in $(ls /etc/kubernetes/pki | grep -v etcd); do 
scp /etc/kubernetes/pki/${FILE} $NODE:/etc/kubernetes/pki/${FILE};
done; 
for FILE in admin.kubeconfig controller-manager.kubeconfig scheduler.kubeconfig; do 
scp /etc/kubernetes/${FILE} $NODE:/etc/kubernetes/${FILE};
done;
done
```

### 3.2.7查看证书

```shell
ls /etc/kubernetes/pki/
admin.csr      apiserver-key.pem  ca.pem                      front-proxy-ca.csr      front-proxy-client-key.pem  scheduler.csr
admin-key.pem  apiserver.pem      controller-manager.csr      front-proxy-ca-key.pem  front-proxy-client.pem      scheduler-key.pem
admin.pem      ca.csr             controller-manager-key.pem  front-proxy-ca.pem      sa.key                      scheduler.pem
apiserver.csr  ca-key.pem         controller-manager.pem      front-proxy-client.csr  sa.pub

# 一共23个就对了

ls /etc/kubernetes/pki/ |wc -l
23
```

# 4.k8s系统组件配置

## 4.1.etcd配置

### 4.1.1master01配置

```shell
cat > /etc/etcd/etcd.config.yml << EOF 
name: 'k8s-master01'
data-dir: /var/lib/etcd
wal-dir: /var/lib/etcd/wal
snapshot-count: 5000
heartbeat-interval: 100
election-timeout: 1000
quota-backend-bytes: 0
listen-peer-urls: 'https://192.168.1.30:2380'
listen-client-urls: 'https://192.168.1.30:2379,http://127.0.0.1:2379'
max-snapshots: 3
max-wals: 5
cors:
initial-advertise-peer-urls: 'https://192.168.1.30:2380'
advertise-client-urls: 'https://192.168.1.30:2379'
discovery:
discovery-fallback: 'proxy'
discovery-proxy:
discovery-srv:
initial-cluster: 'k8s-master01=https://192.168.1.30:2380,k8s-master02=https://192.168.1.31:2380,k8s-master03=https://192.168.1.32:2380'
initial-cluster-token: 'etcd-k8s-cluster'
initial-cluster-state: 'new'
strict-reconfig-check: false
enable-v2: true
enable-pprof: true
proxy: 'off'
proxy-failure-wait: 5000
proxy-refresh-interval: 30000
proxy-dial-timeout: 1000
proxy-write-timeout: 5000
proxy-read-timeout: 0
client-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
peer-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  peer-client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
debug: false
log-package-levels:
log-outputs: [default]
force-new-cluster: false
EOF
```

### 4.1.2master02配置

```shell
cat > /etc/etcd/etcd.config.yml << EOF 
name: 'k8s-master02'
data-dir: /var/lib/etcd
wal-dir: /var/lib/etcd/wal
snapshot-count: 5000
heartbeat-interval: 100
election-timeout: 1000
quota-backend-bytes: 0
listen-peer-urls: 'https://192.168.1.31:2380'
listen-client-urls: 'https://192.168.1.31:2379,http://127.0.0.1:2379'
max-snapshots: 3
max-wals: 5
cors:
initial-advertise-peer-urls: 'https://192.168.1.31:2380'
advertise-client-urls: 'https://192.168.1.31:2379'
discovery:
discovery-fallback: 'proxy'
discovery-proxy:
discovery-srv:
initial-cluster: 'k8s-master01=https://192.168.1.30:2380,k8s-master02=https://192.168.1.31:2380,k8s-master03=https://192.168.1.32:2380'
initial-cluster-token: 'etcd-k8s-cluster'
initial-cluster-state: 'new'
strict-reconfig-check: false
enable-v2: true
enable-pprof: true
proxy: 'off'
proxy-failure-wait: 5000
proxy-refresh-interval: 30000
proxy-dial-timeout: 1000
proxy-write-timeout: 5000
proxy-read-timeout: 0
client-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
peer-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  peer-client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
debug: false
log-package-levels:
log-outputs: [default]
force-new-cluster: false
EOF
```

### 4.1.3master03配置

```shell
cat > /etc/etcd/etcd.config.yml << EOF 
name: 'k8s-master03'
data-dir: /var/lib/etcd
wal-dir: /var/lib/etcd/wal
snapshot-count: 5000
heartbeat-interval: 100
election-timeout: 1000
quota-backend-bytes: 0
listen-peer-urls: 'https://192.168.1.32:2380'
listen-client-urls: 'https://192.168.1.32:2379,http://127.0.0.1:2379'
max-snapshots: 3
max-wals: 5
cors:
initial-advertise-peer-urls: 'https://192.168.1.32:2380'
advertise-client-urls: 'https://192.168.1.32:2379'
discovery:
discovery-fallback: 'proxy'
discovery-proxy:
discovery-srv:
initial-cluster: 'k8s-master01=https://192.168.1.30:2380,k8s-master02=https://192.168.1.31:2380,k8s-master03=https://192.168.1.32:2380'
initial-cluster-token: 'etcd-k8s-cluster'
initial-cluster-state: 'new'
strict-reconfig-check: false
enable-v2: true
enable-pprof: true
proxy: 'off'
proxy-failure-wait: 5000
proxy-refresh-interval: 30000
proxy-dial-timeout: 1000
proxy-write-timeout: 5000
proxy-read-timeout: 0
client-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
peer-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  peer-client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
debug: false
log-package-levels:
log-outputs: [default]
force-new-cluster: false
EOF
```

## 4.2.创建service（所有master节点操作）

### 4.2.1创建etcd.service并启动

```shell
cat > /usr/lib/systemd/system/etcd.service << EOF

[Unit]
Description=Etcd Service
Documentation=https://coreos.com/etcd/docs/latest/
After=network.target

[Service]
Type=notify
ExecStart=/usr/local/bin/etcd --config-file=/etc/etcd/etcd.config.yml
Restart=on-failure
RestartSec=10
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
Alias=etcd3.service

EOF
```

### 4.2.2创建etcd证书目录

```shell
mkdir /etc/kubernetes/pki/etcd
ln -s /etc/etcd/ssl/* /etc/kubernetes/pki/etcd/
systemctl daemon-reload
systemctl enable --now etcd
```

### 4.2.3查看etcd状态

```shell
export ETCDCTL_API=3
etcdctl --endpoints="192.168.1.32:2379,192.168.1.31:2379,192.168.1.30:2379" --cacert=/etc/kubernetes/pki/etcd/etcd-ca.pem --cert=/etc/kubernetes/pki/etcd/etcd.pem --key=/etc/kubernetes/pki/etcd/etcd-key.pem  endpoint status --write-out=table
+-------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
|     ENDPOINT      |        ID        | VERSION | DB SIZE | IS LEADER | IS LEARNER | RAFT TERM | RAFT INDEX | RAFT APPLIED INDEX | ERRORS |
+-------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
| 192.168.1.32:2379 | 56875ab4a12c94e8 |   3.5.1 |   25 kB |     false |      false |         2 |          8 |                  8 |        |
| 192.168.1.31:2379 | 33df6a8fe708d3fd |   3.5.1 |   25 kB |      true |      false |         2 |          8 |                  8 |        |
| 192.168.1.30:2379 | 58fbe5ec9743048f |   3.5.1 |   20 kB |     false |      false |         2 |          8 |                  8 |        |
+-------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
```

# 5.高可用配置

## 5.1在lb01和lb02两台服务器上操作

### 5.1.1安装keepalived和haproxy服务

```shell

systemctl disable --now firewalld

setenforce 0
sed -i 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/sysconfig/selinux


yum -y install keepalived haproxy
```

### 5.1.2修改haproxy配置文件（两台配置文件一样）

```shell
# cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak

cat >/etc/haproxy/haproxy.cfg<<"EOF"
global
 maxconn 2000
 ulimit-n 16384
 log 127.0.0.1 local0 err
 stats timeout 30s

defaults
 log global
 mode http
 option httplog
 timeout connect 5000
 timeout client 50000
 timeout server 50000
 timeout http-request 15s
 timeout http-keep-alive 15s


frontend monitor-in
 bind *:33305
 mode http
 option httplog
 monitor-uri /monitor

frontend k8s-master
 bind 0.0.0.0:8443
 bind 127.0.0.1:8443
 mode tcp
 option tcplog
 tcp-request inspect-delay 5s
 default_backend k8s-master


backend k8s-master
 mode tcp
 option tcplog
 option tcp-check
 balance roundrobin
 default-server inter 10s downinter 5s rise 2 fall 2 slowstart 60s maxconn 250 maxqueue 256 weight 100
 server  k8s-master01  192.168.1.30:6443 check
 server  k8s-master02  192.168.1.31:6443 check
 server  k8s-master03  192.168.1.32:6443 check
EOF
```

### 5.1.3lb01配置keepalived master节点

```shell
#cp /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.bak

cat > /etc/keepalived/keepalived.conf << EOF
! Configuration File for keepalived

global_defs {
    router_id LVS_DEVEL
}
vrrp_script chk_apiserver {
    script "/etc/keepalived/check_apiserver.sh"
    interval 5 
    weight -5
    fall 2
    rise 1
}
vrrp_instance VI_1 {
    state MASTER
    interface ens18
    mcast_src_ip 192.168.1.38
    virtual_router_id 51
    priority 100
    nopreempt
    advert_int 2
    authentication {
        auth_type PASS
        auth_pass K8SHA_KA_AUTH
    }
    virtual_ipaddress {
        192.168.1.88
    }
    track_script {
      chk_apiserver 
} }

EOF
```

### 5.1.4lb02配置keepalived backup节点

```shell
# cp /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.bak

cat > /etc/keepalived/keepalived.conf << EOF
! Configuration File for keepalived

global_defs {
    router_id LVS_DEVEL
}
vrrp_script chk_apiserver {
    script "/etc/keepalived/check_apiserver.sh"
    interval 5 
    weight -5
    fall 2
    rise 1

}
vrrp_instance VI_1 {
    state BACKUP
    interface ens18
    mcast_src_ip 192.168.1.39
    virtual_router_id 51
    priority 50
    nopreempt
    advert_int 2
    authentication {
        auth_type PASS
        auth_pass K8SHA_KA_AUTH
    }
    virtual_ipaddress {
        192.168.1.88
    }
    track_script {
      chk_apiserver 
} }

EOF
```

### 5.1.5健康检查脚本配置（两台lb主机）

```shell
cat >  /etc/keepalived/check_apiserver.sh << EOF
#!/bin/bash

err=0
for k in \$(seq 1 3)
do
    check_code=\$(pgrep haproxy)
    if [[ \$check_code == "" ]]; then
        err=\$(expr \$err + 1)
        sleep 1
        continue
    else
        err=0
        break
    fi
done

if [[ \$err != "0" ]]; then
    echo "systemctl stop keepalived"
    /usr/bin/systemctl stop keepalived
    exit 1
else
    exit 0
fi
EOF

# 给脚本授权

chmod +x /etc/keepalived/check_apiserver.sh
```

### 5.1.6启动服务

```shell
systemctl daemon-reload
systemctl enable --now haproxy
systemctl enable --now keepalived
```

### 5.1.7测试高可用

```shell
# 能ping同

[root@k8s-node02 ~]# ping 192.168.1.88

# 能telnet访问

[root@k8s-node02 ~]# telnet 192.168.1.88 8443

# 关闭主节点，看vip是否漂移到备节点
```

# 6.k8s组件配置（区别于第4点）

所有k8s节点创建以下目录

```shell
mkdir -p /etc/kubernetes/manifests/ /etc/systemd/system/kubelet.service.d /var/lib/kubelet /var/log/kubernetes
```

## 6.1.创建apiserver（所有master节点）

### 6.1.1master01节点配置

```shell
cat > /usr/lib/systemd/system/kube-apiserver.service << EOF

[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
ExecStart=/usr/local/bin/kube-apiserver \
      --v=2  \
      --logtostderr=true  \
      --allow-privileged=true  \
      --bind-address=0.0.0.0  \
      --secure-port=6443  \
      --insecure-port=0  \
      --advertise-address=192.168.1.30 \
      --service-cluster-ip-range=10.96.0.0/12  \
      --service-node-port-range=30000-32767  \
      --etcd-servers=https://192.168.1.30:2379,https://192.168.1.31:2379,https://192.168.1.32:2379 \
      --etcd-cafile=/etc/etcd/ssl/etcd-ca.pem  \
      --etcd-certfile=/etc/etcd/ssl/etcd.pem  \
      --etcd-keyfile=/etc/etcd/ssl/etcd-key.pem  \
      --client-ca-file=/etc/kubernetes/pki/ca.pem  \
      --tls-cert-file=/etc/kubernetes/pki/apiserver.pem  \
      --tls-private-key-file=/etc/kubernetes/pki/apiserver-key.pem  \
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver.pem  \
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-key.pem  \
      --service-account-key-file=/etc/kubernetes/pki/sa.pub  \
      --service-account-signing-key-file=/etc/kubernetes/pki/sa.key  \
      --service-account-issuer=https://kubernetes.default.svc.cluster.local \
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname  \
      --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,ResourceQuota  \
      --authorization-mode=Node,RBAC  \
      --enable-bootstrap-token-auth=true  \
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.pem  \
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.pem  \
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client-key.pem  \
      --requestheader-allowed-names=aggregator  \
      --requestheader-group-headers=X-Remote-Group  \
      --requestheader-extra-headers-prefix=X-Remote-Extra-  \
      --requestheader-username-headers=X-Remote-User
      # --token-auth-file=/etc/kubernetes/token.csv

Restart=on-failure
RestartSec=10s
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target

EOF
```

### 6.1.2master02节点配置

```shell
cat > /usr/lib/systemd/system/kube-apiserver.service << EOF
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
ExecStart=/usr/local/bin/kube-apiserver \
      --v=2  \
      --logtostderr=true  \
      --allow-privileged=true  \
      --bind-address=0.0.0.0  \
      --secure-port=6443  \
      --insecure-port=0  \
      --advertise-address=192.168.1.31 \
      --service-cluster-ip-range=10.96.0.0/12  \
      --service-node-port-range=30000-32767  \
      --etcd-servers=https://192.168.1.30:2379,https://192.168.1.31:2379,https://192.168.1.32:2379 \
      --etcd-cafile=/etc/etcd/ssl/etcd-ca.pem  \
      --etcd-certfile=/etc/etcd/ssl/etcd.pem  \
      --etcd-keyfile=/etc/etcd/ssl/etcd-key.pem  \
      --client-ca-file=/etc/kubernetes/pki/ca.pem  \
      --tls-cert-file=/etc/kubernetes/pki/apiserver.pem  \
      --tls-private-key-file=/etc/kubernetes/pki/apiserver-key.pem  \
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver.pem  \
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-key.pem  \
      --service-account-key-file=/etc/kubernetes/pki/sa.pub  \
      --service-account-signing-key-file=/etc/kubernetes/pki/sa.key  \
      --service-account-issuer=https://kubernetes.default.svc.cluster.local \
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname  \
      --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,ResourceQuota  \
      --authorization-mode=Node,RBAC  \
      --enable-bootstrap-token-auth=true  \
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.pem  \
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.pem  \
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client-key.pem  \
      --requestheader-allowed-names=aggregator  \
      --requestheader-group-headers=X-Remote-Group  \
      --requestheader-extra-headers-prefix=X-Remote-Extra-  \
      --requestheader-username-headers=X-Remote-User
      # --token-auth-file=/etc/kubernetes/token.csv

Restart=on-failure
RestartSec=10s
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target

EOF
```

### 6.1.3master03节点配置

```shell
cat > /usr/lib/systemd/system/kube-apiserver.service  << EOF

[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
ExecStart=/usr/local/bin/kube-apiserver \
      --v=2  \
      --logtostderr=true  \
      --allow-privileged=true  \
      --bind-address=0.0.0.0  \
      --secure-port=6443  \
      --insecure-port=0  \
      --advertise-address=192.168.1.32 \
      --service-cluster-ip-range=10.96.0.0/12  \
      --service-node-port-range=30000-32767  \
      --etcd-servers=https://192.168.1.30:2379,https://192.168.1.31:2379,https://192.168.1.32:2379 \
      --etcd-cafile=/etc/etcd/ssl/etcd-ca.pem  \
      --etcd-certfile=/etc/etcd/ssl/etcd.pem  \
      --etcd-keyfile=/etc/etcd/ssl/etcd-key.pem  \
      --client-ca-file=/etc/kubernetes/pki/ca.pem  \
      --tls-cert-file=/etc/kubernetes/pki/apiserver.pem  \
      --tls-private-key-file=/etc/kubernetes/pki/apiserver-key.pem  \
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver.pem  \
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-key.pem  \
      --service-account-key-file=/etc/kubernetes/pki/sa.pub  \
      --service-account-signing-key-file=/etc/kubernetes/pki/sa.key  \
      --service-account-issuer=https://kubernetes.default.svc.cluster.local \
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname  \
      --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,ResourceQuota  \
      --authorization-mode=Node,RBAC  \
      --enable-bootstrap-token-auth=true  \
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.pem  \
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.pem  \
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client-key.pem  \
      --requestheader-allowed-names=aggregator  \
      --requestheader-group-headers=X-Remote-Group  \
      --requestheader-extra-headers-prefix=X-Remote-Extra-  \
      --requestheader-username-headers=X-Remote-User
      # --token-auth-file=/etc/kubernetes/token.csv

Restart=on-failure
RestartSec=10s
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target

EOF
```

### 6.1.4启动apiserver（所有master节点）

```shell
systemctl daemon-reload && systemctl enable --now kube-apiserver

# 注意查看状态是否启动正常

systemctl status kube-apiserver
```

## 6.2.配置kube-controller-manager service

```shell
所有master节点配置，且配置相同
172.16.0.0/12为pod网段，按需求设置你自己的网段

cat > /usr/lib/systemd/system/kube-controller-manager.service << EOF

[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
ExecStart=/usr/local/bin/kube-controller-manager \
      --v=2 \
      --logtostderr=true \
      --address=127.0.0.1 \
      --root-ca-file=/etc/kubernetes/pki/ca.pem \
      --cluster-signing-cert-file=/etc/kubernetes/pki/ca.pem \
      --cluster-signing-key-file=/etc/kubernetes/pki/ca-key.pem \
      --service-account-private-key-file=/etc/kubernetes/pki/sa.key \
      --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig \
      --leader-elect=true \
      --use-service-account-credentials=true \
      --node-monitor-grace-period=40s \
      --node-monitor-period=5s \
      --pod-eviction-timeout=2m0s \
      --controllers=*,bootstrapsigner,tokencleaner \
      --allocate-node-cidrs=true \
      --cluster-cidr=172.16.0.0/12 \
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.pem \
      --node-cidr-mask-size=24

Restart=always
RestartSec=10s

[Install]
WantedBy=multi-user.target

EOF
```

### 6.2.1启动kube-controller-manager，并查看状态

```shell
systemctl daemon-reload
systemctl enable --now kube-controller-manager
systemctl  status kube-controller-manager
```

## 6.3.配置kube-scheduler service

### 6.3.1所有master节点配置，且配置相同

```shell
cat > /usr/lib/systemd/system/kube-scheduler.service << EOF

[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
ExecStart=/usr/local/bin/kube-scheduler \
      --v=2 \
      --logtostderr=true \
      --address=127.0.0.1 \
      --leader-elect=true \
      --kubeconfig=/etc/kubernetes/scheduler.kubeconfig

Restart=always
RestartSec=10s

[Install]
WantedBy=multi-user.target

EOF
```

### 6.3.2启动并查看服务状态

```shell
systemctl daemon-reload
systemctl enable --now kube-scheduler
systemctl status kube-scheduler
```

# 7.TLS Bootstrapping配置

## 7.1在master01上配置

```shell
cd /root/Kubernetes/bootstrap

kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.1.88:8443     --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig

kubectl config set-credentials tls-bootstrap-token-user     --token=c8ad9c.2e4d610cf3e7426e --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig

kubectl config set-context tls-bootstrap-token-user@kubernetes     --cluster=kubernetes     --user=tls-bootstrap-token-user     --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig

kubectl config use-context tls-bootstrap-token-user@kubernetes     --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig

# token的位置在bootstrap.secret.yaml，如果修改的话到这个文件修改

mkdir -p /root/.kube ; cp /etc/kubernetes/admin.kubeconfig /root/.kube/config
```

## 7.2查看集群状态，没问题的话继续后续操作

```shell
kubectl get cs

Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS    MESSAGE                         ERROR
controller-manager   Healthy   ok                              
etcd-0               Healthy   {"health":"true","reason":""}   
scheduler            Healthy   ok                              
etcd-1               Healthy   {"health":"true","reason":""}   
etcd-2               Healthy   {"health":"true","reason":""}  

kubectl create -f bootstrap.secret.yaml
```

# 8.node节点配置

## 8.1.在master01上将证书复制到node节点

```shell
cd /etc/kubernetes/

for NODE in k8s-master02 k8s-master03 k8s-node01 k8s-node02 k8s-node03 k8s-node04 k8s-node05; do
     ssh $NODE mkdir -p /etc/kubernetes/pki
     for FILE in pki/ca.pem pki/ca-key.pem pki/front-proxy-ca.pem bootstrap-kubelet.kubeconfig; do
       scp /etc/kubernetes/$FILE $NODE:/etc/kubernetes/${FILE}
 done
 done
```

## 8.2.kubelet配置

### 8.2.1所有k8s节点创建相关目录

```shell
mkdir -p /var/lib/kubelet /var/log/kubernetes /etc/systemd/system/kubelet.service.d /etc/kubernetes/manifests/



所有k8s节点配置kubelet service
cat > /usr/lib/systemd/system/kubelet.service << EOF

[Unit]
Description=Kubernetes Kubelet
Documentation=https://github.com/kubernetes/kubernetes
After=docker.service
Requires=docker.service

[Service]
ExecStart=/usr/local/bin/kubelet

Restart=always
StartLimitInterval=0
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF
```

### 8.2.2所有k8s节点配置kubelet service的配置文件

```shell
cat >  /etc/systemd/system/kubelet.service.d/10-kubelet.conf << EOF
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig --kubeconfig=/etc/kubernetes/kubelet.kubeconfig"
Environment="KUBELET_SYSTEM_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin --container-runtime=remote --runtime-request-timeout=15m --container-runtime-endpoint=unix:///run/containerd/containerd.sock --cgroup-driver=systemd"
Environment="KUBELET_CONFIG_ARGS=--config=/etc/kubernetes/kubelet-conf.yml"
Environment="KUBELET_EXTRA_ARGS=--node-labels=node.kubernetes.io/node='' "
ExecStart=
ExecStart=/usr/local/bin/kubelet \$KUBELET_KUBECONFIG_ARGS \$KUBELET_CONFIG_ARGS \$KUBELET_SYSTEM_ARGS \$KUBELET_EXTRA_ARGS

EOF
```

### 8.2.3所有k8s节点创建kubelet的配置文件

```shell
cat > /etc/kubernetes/kubelet-conf.yml <<EOF
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
address: 0.0.0.0
port: 10250
readOnlyPort: 10255
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 2m0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.pem
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 5m0s
    cacheUnauthorizedTTL: 30s
cgroupDriver: systemd
cgroupsPerQOS: true
clusterDNS:
- 10.96.0.10
clusterDomain: cluster.local
containerLogMaxFiles: 5
containerLogMaxSize: 10Mi
contentType: application/vnd.kubernetes.protobuf
cpuCFSQuota: true
cpuManagerPolicy: none
cpuManagerReconcilePeriod: 10s
enableControllerAttachDetach: true
enableDebuggingHandlers: true
enforceNodeAllocatable:
- pods
eventBurst: 10
eventRecordQPS: 5
evictionHard:
  imagefs.available: 15%
  memory.available: 100Mi
  nodefs.available: 10%
  nodefs.inodesFree: 5%
evictionPressureTransitionPeriod: 5m0s
failSwapOn: true
fileCheckFrequency: 20s
hairpinMode: promiscuous-bridge
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 20s
imageGCHighThresholdPercent: 85
imageGCLowThresholdPercent: 80
imageMinimumGCAge: 2m0s
iptablesDropBit: 15
iptablesMasqueradeBit: 14
kubeAPIBurst: 10
kubeAPIQPS: 5
makeIPTablesUtilChains: true
maxOpenFiles: 1000000
maxPods: 110
nodeStatusUpdateFrequency: 10s
oomScoreAdj: -999
podPidsLimit: -1
registryBurst: 10
registryPullQPS: 5
resolvConf: /etc/resolv.conf
rotateCertificates: true
runtimeRequestTimeout: 2m0s
serializeImagePulls: true
staticPodPath: /etc/kubernetes/manifests
streamingConnectionIdleTimeout: 4h0m0s
syncFrequency: 1m0s
volumeStatsAggPeriod: 1m0s
EOF
```

### 8.2.4启动kubelet

```shell
systemctl daemon-reload
systemctl restart kubelet
systemctl enable --now kubelet
```

### 8.2.5查看集群

```shell
kubectl get node
NAME           STATUS   ROLES    AGE   VERSION
k8s-master01   Ready    <none>   80s   v1.23.4
k8s-master02   Ready    <none>   78s   v1.23.4
k8s-master03   Ready    <none>   74s   v1.23.4
k8s-node01     Ready    <none>   86s   v1.23.4
k8s-node02     Ready    <none>   95s   v1.23.4
k8s-node03     Ready    <none>   87s   v1.23.4
k8s-node04     Ready    <none>   65s   v1.23.4
k8s-node05     Ready    <none>   77s   v1.23.4

```

## 8.3.kube-proxy配置

### 8.3.1此配置只在master01操作

```shell
cd /root/Kubernetes/
kubectl -n kube-system create serviceaccount kube-proxy

kubectl create clusterrolebinding system:kube-proxy         --clusterrole system:node-proxier         --serviceaccount kube-system:kube-proxy

SECRET=$(kubectl -n kube-system get sa/kube-proxy \
    --output=jsonpath='{.secrets[0].name}')

JWT_TOKEN=$(kubectl -n kube-system get secret/$SECRET \
--output=jsonpath='{.data.token}' | base64 -d)

PKI_DIR=/etc/kubernetes/pki
K8S_DIR=/etc/kubernetes

kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.1.88:8443     --kubeconfig=${K8S_DIR}/kube-proxy.kubeconfig

kubectl config set-credentials kubernetes     --token=${JWT_TOKEN}     --kubeconfig=/etc/kubernetes/kube-proxy.kubeconfig

kubectl config set-context kubernetes     --cluster=kubernetes     --user=kubernetes     --kubeconfig=/etc/kubernetes/kube-proxy.kubeconfig

kubectl config use-context kubernetes     --kubeconfig=/etc/kubernetes/kube-proxy.kubeconfig
```

### 8.3.2将kubeconfig发送至其他节点

```shell
for NODE in k8s-master02 k8s-master03; do
     scp /etc/kubernetes/kube-proxy.kubeconfig  $NODE:/etc/kubernetes/kube-proxy.kubeconfig
 done

for NODE in k8s-node01 k8s-node02 k8s-node03 k8s-node04 k8s-node05; do
     scp /etc/kubernetes/kube-proxy.kubeconfig $NODE:/etc/kubernetes/kube-proxy.kubeconfig
 done
```

### 8.3.3所有k8s节点添加kube-proxy的配置和service文件

```shell
cat >  /usr/lib/systemd/system/kube-proxy.service << EOF
[Unit]
Description=Kubernetes Kube Proxy
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
ExecStart=/usr/local/bin/kube-proxy \
  --config=/etc/kubernetes/kube-proxy.yaml \
  --v=2

Restart=always
RestartSec=10s

[Install]
WantedBy=multi-user.target

EOF
```

```shell
cat > /etc/kubernetes/kube-proxy.yaml << EOF
apiVersion: kubeproxy.config.k8s.io/v1alpha1
bindAddress: 0.0.0.0
clientConnection:
  acceptContentTypes: ""
  burst: 10
  contentType: application/vnd.kubernetes.protobuf
  kubeconfig: /etc/kubernetes/kube-proxy.kubeconfig
  qps: 5
clusterCIDR: 172.16.0.0/12 
configSyncPeriod: 15m0s
conntrack:
  max: null
  maxPerCore: 32768
  min: 131072
  tcpCloseWaitTimeout: 1h0m0s
  tcpEstablishedTimeout: 24h0m0s
enableProfiling: false
healthzBindAddress: 0.0.0.0:10256
hostnameOverride: ""
iptables:
  masqueradeAll: false
  masqueradeBit: 14
  minSyncPeriod: 0s
  syncPeriod: 30s
ipvs:
  masqueradeAll: true
  minSyncPeriod: 5s
  scheduler: "rr"
  syncPeriod: 30s
kind: KubeProxyConfiguration
metricsBindAddress: 127.0.0.1:10249
mode: "ipvs"
nodePortAddresses: null
oomScoreAdj: -999
portRange: ""
udpIdleTimeout: 250ms

EOF
```

### 8.3.4启动kube-proxy

```shell
 systemctl daemon-reload
 systemctl enable --now kube-proxy
```

# 9.安装Calico

## 9.1以下步骤只在master01操作

### 9.1.1更改calico网段

```shell
cd /root/Kubernetes/calico/
sed -i "s#POD_CIDR#172.16.0.0/12#g" calico.yaml
grep "IPV4POOL_CIDR" calico.yaml  -A 1
            - name: CALICO_IPV4POOL_CIDR
              value: "172.16.0.0/12"

# 创建

kubectl apply -f calico.yaml
```

### 9.1.2查看容器状态

```shell
kubectl  get pod -A
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-6f6595874c-cf2sf   1/1     Running   0          14m
kube-system   calico-node-2xd2q                          1/1     Running   0          2m25s
kube-system   calico-node-jtzfh                          1/1     Running   0          2m3s
kube-system   calico-node-k4jkc                          1/1     Running   0          2m24s
kube-system   calico-node-msxwp                          1/1     Running   0          2m15s
kube-system   calico-node-tv849                          1/1     Running   0          2m12s
kube-system   calico-node-wdbzt                          1/1     Running   0          2m18s
kube-system   calico-node-x9sjr                          1/1     Running   0          2m33s
kube-system   calico-node-z2mz5                          1/1     Running   0          2m16s
kube-system   calico-typha-6b6cf8cbdf-gshvt              1/1     Running   0          14m
```

# 10.安装CoreDNS

## 10.1以下步骤只在master01操作

### 10.1.1修改文件

```shell
cd /root/Kubernetes/CoreDNS/
sed -i "s#KUBEDNS_SERVICE_IP#10.96.0.10#g" coredns.yaml

cat coredns.yaml | grep clusterIP:
  clusterIP: 10.96.0.10 
```

### 10.1.2安装

```shell
kubectl  create -f coredns.yaml 
serviceaccount/coredns created
clusterrole.rbac.authorization.k8s.io/system:coredns created
clusterrolebinding.rbac.authorization.k8s.io/system:coredns created
configmap/coredns created
deployment.apps/coredns created
service/kube-dns created
```

# 11.安装Metrics Server

## 11.1以下步骤只在master01操作

### 11.1.1安装Metrics-server

在新版的Kubernetes中系统资源的采集均使用Metrics-server，可以通过Metrics采集节点和Pod的内存、磁盘、CPU和网络的使用率

```shell
安装metrics server
cd /root/Kubernetes/metrics-server/

kubectl  create -f . 

serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
```

### 11.1.2稍等片刻查看状态

```shell
kubectl  top node
NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
k8s-master01   172m         2%     1307Mi          16%       
k8s-master02   157m         1%     1189Mi          15%       
k8s-master03   155m         1%     1105Mi          14%       
k8s-node01     99m          1%     710Mi           9%        
k8s-node02     79m          0%     585Mi           7%
```

# 12.集群验证

## 12.1部署pod资源

```shell
cat<<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - name: busybox
    image: busybox:1.28
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
  restartPolicy: Always
EOF



# 查看

kubectl  get pod
NAME      READY   STATUS    RESTARTS   AGE
busybox   1/1     Running   0          17s
```

## 12.2用pod解析默认命名空间中的kubernetes

```shell
kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   17h


kubectl exec  busybox -n default -- nslookup kubernetes
3Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      kubernetes
Address 1: 10.96.0.1 kubernetes.default.svc.cluster.local
```

## 12.3测试跨命名空间是否可以解析

```shell
kubectl exec  busybox -n default -- nslookup kube-dns.kube-system
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      kube-dns.kube-system
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local
```

## 12.4每个节点都必须要能访问Kubernetes的kubernetes svc 443和kube-dns的service 53

```shell
telnet 10.96.0.1 443
Trying 10.96.0.1...
Connected to 10.96.0.1.
Escape character is '^]'.

 telnet 10.96.0.10 53
Trying 10.96.0.10...
Connected to 10.96.0.10.
Escape character is '^]'.

curl 10.96.0.10:53
curl: (52) Empty reply from server
```

## 12.5Pod和Pod之前要能通

```shell
kubectl get po -owide
NAME      READY   STATUS    RESTARTS   AGE   IP              NODE         NOMINATED NODE   READINESS GATES
busybox   1/1     Running   0          17m   172.27.14.193   k8s-node02   <none>           <none>

 kubectl get po -n kube-system -owide
NAME                                       READY   STATUS    RESTARTS      AGE   IP               NODE           NOMINATED NODE   READINESS GATES
calico-kube-controllers-5dffd5886b-4blh6   1/1     Running   0             77m   172.25.244.193   k8s-master01   <none>           <none>
calico-node-fvbdq                          1/1     Running   1 (75m ago)   77m   192.168.1.30     k8s-master01   <none>           <none>
calico-node-g8nqd                          1/1     Running   0             77m   192.168.1.33     k8s-node01     <none>           <none>
calico-node-mdps8                          1/1     Running   0             77m   192.168.1.34     k8s-node02     <none>           <none>
calico-node-nf4nt                          1/1     Running   0             77m   192.168.1.32     k8s-master03   <none>           <none>
calico-node-sq2ml                          1/1     Running   0             77m   192.168.1.31     k8s-master02   <none>           <none>
calico-typha-8445487f56-mg6p8              1/1     Running   0             77m   192.168.1.34     k8s-node02     <none>           <none>
calico-typha-8445487f56-pxbpj              1/1     Running   0             77m   192.168.1.30     k8s-master01   <none>           <none>
calico-typha-8445487f56-tnssl              1/1     Running   0             77m   192.168.1.33     k8s-node01     <none>           <none>
coredns-5db5696c7-67h79                    1/1     Running   0             63m   172.25.92.65     k8s-master02   <none>           <none>
metrics-server-6bf7dcd649-5fhrw            1/1     Running   0             61m   172.18.195.1     k8s-master03   <none>           <none>

# 进入busybox ping其他节点上的pod

kubectl exec -ti busybox -- sh
/ # ping 192.168.1.33
PING 192.168.1.33 (192.168.1.33): 56 data bytes
64 bytes from 192.168.1.33: seq=0 ttl=63 time=0.358 ms
64 bytes from 192.168.1.33: seq=1 ttl=63 time=0.668 ms
64 bytes from 192.168.1.33: seq=2 ttl=63 time=0.637 ms
64 bytes from 192.168.1.33: seq=3 ttl=63 time=0.624 ms
64 bytes from 192.168.1.33: seq=4 ttl=63 time=0.907 ms

# 可以连通证明这个pod是可以跨命名空间和跨主机通信的
```

## 12.6创建三个副本，可以看到3个副本分布在不同的节点上（用完可以删了）

```shell
cat > deployments.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

EOF


kubectl  apply -f deployments.yaml 
deployment.apps/nginx-deployment created

kubectl  get pod 
NAME                               READY   STATUS    RESTARTS   AGE
busybox                            1/1     Running   0          6m25s
nginx-deployment-9456bbbf9-4bmvk   1/1     Running   0          8s
nginx-deployment-9456bbbf9-9rcdk   1/1     Running   0          8s
nginx-deployment-9456bbbf9-dqv8s   1/1     Running   0          8s

# 删除nginx

[root@k8s-master01 ~]# kubectl delete -f deployments.yaml 
```

# 13.安装dashboard

```shell
cd /root/Kubernetes/dashboard/

kubectl  create -f .
serviceaccount/admin-user created
clusterrolebinding.rbac.authorization.k8s.io/admin-user created
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
```

## 13.1创建管理员用户

```shell
cat > admin.yaml << EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user

  namespace: kube-system
---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding 
metadata: 
  name: admin-user
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:

- kind: ServiceAccount
  name: admin-user
  namespace: kube-system

EOF
```

## 13.2执行yaml文件

```shell
kubectl apply -f admin.yaml -n kube-system

serviceaccount/admin-user created
clusterrolebinding.rbac.authorization.k8s.io/admin-user created
```

## 13.3更改dashboard的svc为NodePort，如果已是请忽略

```shell
kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
  type: NodePort
```

## 13.4查看端口号

```shell
kubectl get svc kubernetes-dashboard -n kubernetes-dashboard
NAME                   TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)         AGE
kubernetes-dashboard   NodePort   10.98.201.22   <none>        443:31245/TCP   10m
```

## 13.5查看token

```shell
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
Name:         admin-user-token-k545k
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: c308071c-4cf5-4583-83a2-eaf7812512b4

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6InYzV2dzNnQzV3hHb2FQWnYzdnlOSmpudmtpVmNjQW5VM3daRi12SFM4dEEifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWs1NDVrIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJjMzA4MDcxYy00Y2Y1LTQ1ODMtODNhMi1lYWY3ODEyNTEyYjQiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.pshvZPi9ZJkXUWuWilcYs1wawTpzV-nMKesgF3d_l7qyTPaK2N5ofzIThd0SjzU7BFNb4_rOm1dw1Be5kLeHjY_YW5lDnM5TAxVPXmZQ0HJ2pAQ0pjQqCHFnPD0bZFIYkeyz8pZx0Hmwcd3ZdC1yztr0ADpTAmMgI9NC2ZFIeoFFo4Ue9ZM_ulhqJQjmgoAlI_qbyjuKCNsWeEQBwM6HHHAsH1gOQIdVxqQ83OQZUuynDQRpqlHHFIndbK2zVRYFA3GgUnTu2-VRQ-DXBFRjvZR5qArnC1f383jmIjGT6VO7l04QJteG_LFetRbXa-T4mcnbsd8XutSgO0INqwKpjw
ca.crt:     1363 bytes
namespace:  11 bytes
```

## 13.6登录dashboard

https://192.168.1.30:31245/

eyJhbGciOiJSUzI1NiIsImtpZCI6InYzV2dzNnQzV3hHb2FQWnYzdnlOSmpudmtpVmNjQW5VM3daRi12SFM4dEEifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWs1NDVrIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJjMzA4MDcxYy00Y2Y1LTQ1ODMtODNhMi1lYWY3ODEyNTEyYjQiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.pshvZPi9ZJkXUWuWilcYs1wawTpzV-nMKesgF3d_l7qyTPaK2N5ofzIThd0SjzU7BFNb4_rOm1dw1Be5kLeHjY_YW5lDnM5TAxVPXmZQ0HJ2pAQ0pjQqCHFnPD0bZFIYkeyz8pZx0Hmwcd3ZdC1yztr0ADpTAmMgI9NC2ZFIeoFFo4Ue9ZM_ulhqJQjmgoAlI_qbyjuKCNsWeEQBwM6HHHAsH1gOQIdVxqQ83OQZUuynDQRpqlHHFIndbK2zVRYFA3GgUnTu2-VRQ-DXBFRjvZR5qArnC1f383jmIjGT6VO7l04QJteG_LFetRbXa-T4mcnbsd8XutSgO0INqwKpjw

# 14.安装命令行自动补全功能

```shell
yum install bash-completion -y
source /usr/share/bash-completion/bash_completion
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

# 附录：

配置kube-controller-manager有效期100年（能不能生效的先配上再说）

```shell
vim /usr/lib/systemd/system/kube-controller-manager.service

# [Service]下找个地方加上

--cluster-signing-duration=876000h0m0s \


# 重启

systemctl daemon-reload 
systemctl restart kube-controller-manager
```

防止漏洞扫描

```shell
vim /etc/systemd/system/kubelet.service.d/10-kubelet.conf

[Service] 
Environment="KUBELET_KUBECONFIG_ARGS=--kubeconfig=/etc/kubernetes/kubelet.kubeconfig --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig" 
Environment="KUBELET_SYSTEM_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin" 
Environment="KUBELET_CONFIG_ARGS=--config=/etc/kubernetes/kubelet-conf.yml  --pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.6" 
Environment="KUBELET_EXTRA_ARGS=--tls-cipher-suites=TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384    --image-pull-progress-deadline=30m" 
ExecStart= 
ExecStart=/usr/local/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_SYSTEM_ARGS $KUBELET_EXTRA_ARGS 
```

预留空间，按需分配

```shell
vim /etc/kubernetes/kubelet-conf.yml

rotateServerCertificates: true
allowedUnsafeSysctls:

 - "net.core*"
 - "net.ipv4.*"
   kubeReserved:
     cpu: "1"
     memory: 1Gi
     ephemeral-storage: 10Gi
   systemReserved:
     cpu: "1"
     memory: 1Gi
     ephemeral-storage: 10Gi
```

数据盘要与系统盘分开；etcd使用ssd磁盘
