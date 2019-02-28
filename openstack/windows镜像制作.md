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

#### 下载virtio驱动：

win默认不支持virtio驱动，而通过openstack管理虚拟机是需要virtio驱动需要下载两个virtio驱动

virtio-win.vfd是软盘格式驱动里面包含两个驱动一个是硬盘的，一个是网卡的

virtios-win.iso里面包含qemu跟pci驱动进入系统后会用到

https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.141-1/

![](https://upload-images.jianshu.io/upload_images/15199265-6705acafc67fac79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 创建镜像：

1、建立一个硬盘映射，用来存放我们将来生成的镜像文件，qcow2格式，大小10G

```shell
qemu-img create -f qcow2 en_windows7.qcow2 10G
```
2、下面创建镜像
如果需要从远程连接并继续执行安装，需要修改qemu配置文件

```shell
sudo vim /etc/libvirt/qemu.conf
#spice_listen = "0.0.0.0"
取消注释
```

```shell
virt-install \
  --name windows7 --ram 2048 --vcpus 2 \
  --network netwlork=default,model=virtio \
  --disk path=en_windows7.qcow2,format=qcow2,device=disk,bus=virtio \
  --cdrom en_windows_7_ultimate_with_sp1_x64_dvd_u_677332.iso \
  --disk path=virtio-win-0.1.141_amd64.vfd,device=floppy \
  --os-type windows --os-variant win7
  
备注：
  --disk path=*.qcow2，device=disk 表示挂载为硬盘
  --cdrom  把iso镜像装载到光驱
  --disk path=*.vfd,device=floppy 表示将virtio驱动挂载为软盘
  --os-type --os-variant分别为系统类型和版本
```
注意事项：

Ubuntu系统语言为中文时，可能会出现编码的错误

![1543463410697](https://upload-images.jianshu.io/upload_images/15199265-e435a1d43015e8b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、打开Remote Viewer 安装windows

输入：spice://localhost:5900  进入图形化安装界面

4、继续安装

这一步选择加载驱动程序，选择对应版本的驱动程序

![1543464126718](https://upload-images.jianshu.io/upload_images/15199265-e0afc65030836323.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后下一步继续安装

#### 安装必要软件

进入系统后需要下载并且安装

1、WinRAR：

http://cdn7.mydown.com/5bfcbc3a/6ed5c81b0bb419f2bf01c2bb4053a7bc/newsoft/winrar_x64_561scp.exe

2、cloud-init：

https://www.cloudbase.it/downloads/CloudbaseInitSetup_0_9_11_x64.msi

![1543473212700](https://upload-images.jianshu.io/upload_images/15199265-ae2e6f62da96ee2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装完成cloud-init后需要修改配置文件

默认安装路径C:\Program Files\Cloudbase Solutions\Cloudbase-Init\conf\cloudbase-init.conf

cloudbase-init.ini文件末尾加一行

```
allow_reboot=false
注：需要先给当前用户修改权限
```

3、安装curl

#### 安装PCI、qemu驱动

在宿主机上将之前下载的virtio-win驱动作为硬盘/光驱挂载到虚拟机

```shell
virsh attach-disk windows7 /home/Download/virtio-win-0.1.141.iso hda --type cdrom --mode readonly
```

##### PCI：

![1543474074949](https://upload-images.jianshu.io/upload_images/15199265-5baae4dd99c867ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

PIC设备---→更新驱动程序----→找到挂载virtio-win路径下的Balloon--->选择系统对应的驱动文件

PCI简易通讯控制器---→更新驱动程序----→找到挂载virtio-win路径下的vioserial--->选择系统对应的驱动文件

##### qemu：

找到挂载virtio-win的盘符--->guest-agent--->qemu-ga-x64

执行安装程序，等待安装完成后，在cmd运行**services.msc**,找到**QEMU Guest Agent** 
**Vss Provider** 将这个服务设为自动

![1543473938710](https://upload-images.jianshu.io/upload_images/15199265-a94cd47f58fd2dfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 其他设置：

1、设置是否允许空密码登录（按需设置）

![1543475818053](https://upload-images.jianshu.io/upload_images/15199265-c74846f32eb66e51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果允许空密码登录：

![](https://upload-images.jianshu.io/upload_images/15199265-c90d4f92db2d309b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2、设置远程桌面（按需设置）

- 允许任意版本请选择 **RDP** 模式
- 网络级别身份认证请选择 **NLA** 模式

![1543476018951](https://upload-images.jianshu.io/upload_images/15199265-5017d4ac47e15d6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、删除虚拟内存

进入控制面板----→系统和安全--->系统--->高级--->性能，点击更改

4、关闭系统防火墙

5、关闭系统更新

6、关闭电源计划中屏幕锁定计划

7、关闭个性化设置-屏幕保护程序

8、释放ip 

​    打开CMD

```powershell
ipconfig /release
```

镜像压缩:

制作完的镜像文件可以进行压缩后在上传至服务器以节省空间

qemu-img convert -c -O qcow2  img_name.qcow2  new_name.qcow2 

#### 镜像导入：

将制作好的镜像上传至openstack所在的服务器

```shell
source ~/admin.openrc
# 加载环境变量（openstack用户名密码之类）
openstack image create --container-format bare --disk-format qcow2 --public --file img_file  img_name
```

#### 镜像拷贝：

```shell
ls /var/lib/glance/image/img_id
scp /var/lib/glance/image/img_id root@server_ip:/tmp
source ~/admin.openrc
openstack image create --container-format bare --disk-format qcow2 --public --file img_file  img_name
```

**docker save&load**
```
docker save img -o  file_path

docker load -i file_path

openstack image create --file FILE_PATH --disk-format raw --container-format docker --public NAME
```

#### 注意点：

1、对于从virtual-box或VMware拷贝过来Windows类型的镜像

镜像-编辑元数据 增加 

hw_disk_bus  ide

hw_vif_model rtl8139

2、Linux类型的镜像SSH登录慢的问题：

关闭UseDns 加速SSH登录

```shell
vim /etc/ssh/sshd_config
UseDNS no
GSSAPIAuthentication no
service sshd restart
```

10.10.86.252:9000/dashboard :        default        admin              ADMIN_PASS

10.10.62.252:9000/dashboard:		default        admin               L5uCdcjQQuyY9DLs

10.10.62.252 ssh:    root    gMqrS8HRNorzs9tL