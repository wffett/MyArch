# `ArchLinux`?`VirtualBox`
------

#  һ������
> 
<u>Win10 + VirtualBox5.1.10 + Xshell5
`archlinux-2016.12.01-dual.iso`  
**����**</u>

# ������װ׼��

##### �½������
���ƣ�`MyArch`
��������ѡ��`�Ž�����`ģʽ�����ఴ��Ĭ�ϼ��ɡ�
����`archlinux-2016.12.01-dual.iso`��
������ѡ��`Boot Arch Linux(x86_64)`��½��

##### SSHԶ�̵�½����ʡ�ԣ�
    systemctl start sshd.service #����sshd����
    ifconfig   #�鿴������IP��ַ�����ҵ�IP��192.168.1.100�� 
    passwd  #Ϊroot�û��������롪����һ���Ǳ����
    #��win10�д�Xshell������:
    ssh root@192.168.1.100 #������ʾ��������������,���ɵ�½��

# ������װϵͳ
##### ��Ӳ�̽��з���
    ls /dev  #�鿴Ӳ���豸��sda��
    fdisk /dev/sda

#####�����ο�������

    sda1??/boot??200MB??������ Linux
    sda2??swap? ?1GB? ??������ Linux swap
    sda3??/?  ???5GB? ??������ Linux

����`m`�鿴menu��
`n` : �½�������
`t` : ��������ת����`l` �����б�
`p` : ��ӡ������Ϣ��
`w` : д�������

##### ��ʽ������
    mkfs.ext4 /dev/sda1
    mkfs.ext4 /dev/sda3
    mkswap /dev/sda2
##### ��������������
    swapon /dev/sda2
����`lsblk`��`blkid`�鿴�豸������Ϣ��

##### ���أ�ע�������˳��
    mount /dev/sda3 /mnt
    mkdir -p /mnt/boot
    mount /dev/sda1 /mnt/boot
    df #�鿴���̹������
    
*�����������ü���*

##### ���ð�װ��������Դ��
    vim /etc/pacman.d/mirrorlist
ֻ������`Server=http://mirrors.163.com/archlinux/$repo/os/$arch`

#### <u>��ʼ��װϵͳ</u>��
     pacstrap /mnt base base-devel

##### ����fstab�ļ�
    genfstab -U -p /mnt >> /mnt/etc/fstab
���һ��`/mnt/etc/fstab`�ļ��е������Ƿ���ȷ

    cat /mnt/etc/fstab

##### �л�ϵͳ�ĸ�λ��
	arch-chroot /mnt

##### ����Locale
	vi /etc/locale.gen
ȡ�����¶�Ӧ�е�ע�ͷ��Ż�ֱ����������ӣ�
> en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8

��ִ�У�
    
    locale-gen
����������������`locale.conf`��

    echo LANG=en_US.UTF-8 > /etc/locale.conf

##### ����ʱ��
	ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
����ʱ���׼ΪUTC��������ʱ��Ư�ƣ�

    hwclock --systohc --utc

##### ����������
	echo MyArch > /etc/hostname
�༭`/etc/hosts`��

    vi /etc/hosts
׷�ӣ�
     
    127.0.0.1 MyArch.localdomain  MyArch
    # End of file

##### ��������

	ip addr #�鿴��ǰ��������,����û��ifconfig���
���µ�ǰ����ӿ�����`enp0s3`
�༭�������ã�

	cp /etc/netctl/examples/ethernet-dhcp /etc/netctl/my_network
	vi /etc/netctl/my_network
ʹ��`Interface`�ֶε���*enp0s3*
> ..����ʡ��..
> Interface=enp0s3
> ..����ʡ��..

��`my_network`��ΪĬ����������

    cd /etc/netctl #�����һ��ʹ������·�������������	
    netctl enable my_network

##### ��װBoot Loader
	pacman -Sy grub
	grub-install --target=i386-pc --recheck /dev/sda
	grub-mkconfig -o /boot/grub/grub.cfg

# �ġ���װ��ɣ��������²����˳���
	exit
	umount /mnt/boot
	umount /mnt
	poweroff
---
