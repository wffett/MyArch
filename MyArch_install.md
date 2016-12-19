# `ArchLinux`?`VirtualBox`
------

#  一、环境
> 
<u>Win10 + VirtualBox5.1.10 + Xshell5
`archlinux-2016.12.01-dual.iso`  
**联网**</u>

# 二、安装准备

##### 新建虚拟机
名称：`MyArch`
网络配置选择`桥接网卡`模式，其余按照默认即可。
挂载`archlinux-2016.12.01-dual.iso`。
启动后选择`Boot Arch Linux(x86_64)`登陆。

##### SSH远程登陆（可省略）
    systemctl start sshd.service #启动sshd服务
    ifconfig   #查看本机的IP地址——我的IP是192.168.1.100。 
    passwd  #为root用户设置密码——这一步是必须的
    #在win10中打开Xshell，输入:
    ssh root@192.168.1.100 #按照提示操作，输入密码,即可登陆。

# 三、安装系统
##### 对硬盘进行分区
    ls /dev  #查看硬盘设备—sda。
    fdisk /dev/sda

#####分区参考方案：

    sda1??/boot??200MB??类型是 Linux
    sda2??swap? ?1GB? ??类型是 Linux swap
    sda3??/?  ???5GB? ??类型是 Linux

输入`m`查看menu：
`n` : 新建分区，
`t` : 分区类型转换，`l` 类型列表。
`p` : 打印分区信息。
`w` : 写入分区表。

##### 格式化分区
    mkfs.ext4 /dev/sda1
    mkfs.ext4 /dev/sda3
    mkswap /dev/sda2
##### 开启交换分区：
    swapon /dev/sda2
输入`lsblk`或`blkid`查看设备分区信息。

##### 挂载（注意命令的顺序）
    mount /dev/sda3 /mnt
    mkdir -p /mnt/boot
    mount /dev/sda1 /mnt/boot
    df #查看磁盘挂载情况
    
*交换分区不用加载*

##### 配置安装包的下载源。
    vim /etc/pacman.d/mirrorlist
只保留：`Server=http://mirrors.163.com/archlinux/$repo/os/$arch`

#### <u>开始安装系统</u>：
     pacstrap /mnt base base-devel

##### 生成fstab文件
    genfstab -U -p /mnt >> /mnt/etc/fstab
检查一下`/mnt/etc/fstab`文件中的内容是否正确

    cat /mnt/etc/fstab

##### 切换系统的根位置
	arch-chroot /mnt

##### 设置Locale
	vi /etc/locale.gen
取消以下对应行的注释符号或直接在首行添加：
> en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8

并执行：
    
    locale-gen
创建并增加内容至`locale.conf`：

    echo LANG=en_US.UTF-8 > /etc/locale.conf

##### 设置时区
	ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
设置时间标准为UTC，并调整时间漂移：

    hwclock --systohc --utc

##### 设置主机名
	echo MyArch > /etc/hostname
编辑`/etc/hosts`：

    vi /etc/hosts
追加：
     
    127.0.0.1 MyArch.localdomain  MyArch
    # End of file

##### 网络配置

	ip addr #查看当前网络连接,这里没有ifconfig命令。
记下当前网络接口名称`enp0s3`
编辑网络配置：

	cp /etc/netctl/examples/ethernet-dhcp /etc/netctl/my_network
	vi /etc/netctl/my_network
使得`Interface`字段等于*enp0s3*
> ..上面省略..
> Interface=enp0s3
> ..下面省略..

把`my_network`作为默认网络配置

    cd /etc/netctl #如果下一步使用完整路径，会产生错误。	
    netctl enable my_network

##### 安装Boot Loader
	pacman -Sy grub
	grub-install --target=i386-pc --recheck /dev/sda
	grub-mkconfig -o /boot/grub/grub.cfg

# 四、安装完成，按照以下步骤退出：
	exit
	umount /mnt/boot
	umount /mnt
	poweroff
---
