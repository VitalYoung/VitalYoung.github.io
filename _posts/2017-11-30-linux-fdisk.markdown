---
layout: post
title: "Linux系统盘扩容"
date: 2017-11-30 12:58:00 +0800
categories: linux fdisk 扩容
---

1、将/dev/sda上未分配的磁盘空间分区
========

```shell
root $ fdisk /dev/sda
n
p
3
w
```

重启Linux
reboot



2、将新建的分区格式化，建立文件系统
========

```shell
root $ mkfs.ext4 /dev/sda3
```

3、创建物理卷
========

```shell
root $ pvcreate /dev/sda3
```

4、扩展卷组
========

`vgdisplay` 查看卷组， 记住`VG Name`(以centos为例)

```shell
root $ vgextend centos /dev/sda3
```

5、扩展逻辑卷
========

`lvdisplay` 查看逻辑卷， 记住`LV Path`(以/dev/centos/root为例)

```shell
root $ lvextend -L 22G -n /dev/centos/root
```
增加22G的磁盘空间

6、调整根逻辑卷大小
========

```shell
root $  xfs_growfs /dev/centos/root
```

>In Centos 7 default filesystem is xfs.
>xfs file system support only extend not reduce. So if you want to resize the filesystem use xfs_growfs rather than resize2fs.
>`xfs_growfs /dev/root_vg/root`
>
>Note: For ext4 filesystem use
>`resize2fs /dev/root_vg/root`

