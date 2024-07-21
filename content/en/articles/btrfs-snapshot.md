+++
title = 'Take a Btrfs Snapshot'
date = 2024-07-21T04:49:57-04:00
description = ""
tags = ["COW","btrfs"]
draft = true
+++

System snapshot is a crucial tool for data protection and system recovery. Today, I will start with COW file system, introduce four key features of Btrfs, and explain how to use Btrfs to take snapshots of your system.
## Copy-On-Write
Copy-On-Write (COW) is an efficient storage management technique that ensures data integrity and optimizes storage utilization. When two processes simultaneously read the same data block in a COW file system, the system ensures that both processes access the same data block. This is because COW file systems maintain pointers to the data blocks.However, The behavior changes when a process attempts to modify the data block. Instead of directly overwriting the existing block, the COW system creates a new copy of the data block for the modification. Once the new copy is written, the system updates the relevant metadata to point to this new block, ensuring that subsequent reads and writes are directed to the updated data. For example, Btrfs, or B-tree file system, is a modern COW file system designed to address the limitations of traditional file systems like ext4.
## Btrfs vs Ext4
While ext4 is a mature and reliable filesystem with widespread use, Btrfs offers several advanced features:
1. **Snapshots**: Btrfs supports efficient snapshots, allowing users to create point-in-time copies of the file system or individual subvolumes.These snapshots are instantaneous and space-efficient, making them ideal for backups and system recovery.
2. **Data checksumming**: Btrfs includes built-in data checksumming for both data and metadata, ensuring data integrity.When corruption is detected, Btrfs can automatically repair data using redundant copies (in RAID configurations).
3. **Integrated RAID Support**: Btrfs supports various RAID configurations (RAID 0, 1, 10, 5, 6) natively within the file system, and allows dynamic adjustment of RAID levels and devices.
4. **Dynamic Disk Management**: Btrfs allows for online resizing and adding/removing devices from the file system.This flexibility is particularly useful for scaling storage capacity without downtime.
## Take a Btrfs Snapshot
1. **Prerequisites**: 
```
$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 465.8G  0 disk
├─sda1   8:1    0   976M  0 part /boot/efi
├─sda2   8:2    0   977M  0 part /boot
└─sda3   8:3    0 463.9G  0 part /
zram0  253:0    0   1.8G  0 disk [SWAP]
$ df -T
文件系统        类型         1K的块      已用       可用  已用% 挂载点
/dev/sda3      btrfs    486384640 25510416 459792496    6% /
/dev/sda2      ext2        983476   291508    641948   32% /boot
/dev/sda1      vfat        997432     5972    991460    1% /boot/efi
```
2. **Mount and Snapshot**: 
```
$ sudo mount -o subvol=/ /dev/sda3 /mnt
$ ls /mnt -a
. .. @rootfs
$ sudo btrfs subvolume snapshot /mnt/@rootfs /mnt/@20240721
$ ls /mnt -a
. .. @rootfs @20240721
$ sudo btrfs subvolume list /
ID 265 gen 29588 top level 5 path @rootfs
ID 266 gen 26787 top level 5 path @20240717
```
3. **Delete**:
```
$ sudo btrfs subvolume get-default /
ID 265 gen 29588 top level 5 path @rootfs
$ sudo btrfs subvolume set-default 266 /
$ sudo btrfs subvolume delete /mnt/@rootfs
```
4. **Recovery**:
```
mkdir /mnt
mount /dev/sda3 /mnt
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys
chroot /mnt
mount -o subvol=/ /dev/sda3 /mnt
mount -av
update-grub
reboot
```
## Links
1. https://blog.fernvenue.com/archives/how-to-make-btrfs-snapshot/

{{< giscus >}}
