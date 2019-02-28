#### 前提：

1、查看cpu是否支持kvm虚拟化

```shell
egrep -c '(vmx|svm)' /proc/cpuinfo
```

返回值为0说明CPU不支持硬件虚拟化，返回1或者更高说明cpu支持。通常情况，硬件虚拟化扩展是默认不启用的，要通过进入BIOS设置启用。

开机F12->BootMenu-><EnterSetup>->Config->CPU->

将Intel(R) Virtualization Technology选为Enabled

2、安装KVM及相关依赖

```shell
sudo apt-get install qemu-kvm
sudo apt-get install qemu
sudo apt-get install virt-manager
sudo apt-get install virt-viewer 
sudo apt-get install libvirt-bin 
sudo apt-get install bridge-utils
```

3、验证KVM是否安装成功

```shell
virsh list --all 
```

没有错误提示则表示安装成功

### 开始安装
创建虚拟磁盘文件
```shell
qemu-img create -f qcow2 ubuntu1604.qcow2 10G
```

```shell
virt-install \
  --virt-type kvm \
  --name ubuntu1204 \
  --ram 1024 \
  --cdrom=ubuntu-12.04.5-server-amd64.iso \
  --disk ubuntu1204.qcow2 \
  --network network=default \
  --graphics vnc,listen=0.0.0.0 \
  --noautoconsole \
  --os-type=linux \
  --os-variant=ubuntu12.04[rhel7]
```

## **安装后设置**

系统安装后会自动关机，通过virsh命令查看

$ virsh list --all

 Id    Name                           State

\----------------------------------------------------

 \-     ubuntu1604                     shut off

```shell
virsh start ubuntu1604
```
Domain ubuntu1604 started


使用vnc客户端连接，输入用户名密码登录系统

### 配置SSH

###  配置允许密码登录
```
vim /etc/ssh/sshd_config
PasswordAuthentication yes
### 配置允许root登录
PermitRootLogin yes
```

安装场景依赖软件
```shell
sudo apt install wget unzip
```
安装qemu-guest-agent
```shell
sudo apt install qemu-guest-agent
```
安装cloudinit
```shell
sudo apt install cloud-init
dpkg-reconfigure cloud-init
选择ConfigDrive   OpenStack    Ec2
```

```shell
vim /etc/cloud/cloud.cfg
Enable root
删除system_info下面的default_user 和package_mirrors项
```
保存后关机
清理镜像
```shell
virt-sysprep -d ubuntu1604
```

### 压缩镜像
```shell
qemu-img convert -c -O qcow2 ubuntu1604.qcow2 ubuntu1604.qcow2c
ls -lh
-rw-r--r-- 1 root         root  2.1G 3月  11 11:26 ubuntu1604.qcow2
-rw-r--r-- 1 root         root  707M 3月  11 11:38 ubuntu1604.qcow2c
```

### 上传镜像到openstack
```
openstack image create "Ubuntu16.04" \
  --file ubuntu1604.qcow2 \
  --disk-format qcow2 --container-format bare \
  --public
```