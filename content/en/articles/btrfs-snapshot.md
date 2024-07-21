+++
title = 'Take a Btrfs Snapshot'
date = 2024-07-20T04:49:57-04:00
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
lsblk

```
2. **Mount**: 
```
sudo mount -o subvol=/ /dev/sda3 /mnt
ls /mnt
. .. @rootfs
```
3. **Snapshot**:
```
sudo btrfs subvolume snapshot /mnt/@rootfs /mnt/@20240721
ls /mnt
. .. @rootfs @20240721
sudo btrfs subvolume list /

```
3. **Delete**:
```
sudo btrfs subvolume get-default /
sudo btrfs subvolume set-default /
sudo btrfs subvolume delete /mnt/@rootfs
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
update-grub
mount -av
reboot
```
## Links
1. https://blog.fernvenue.com/archives/how-to-make-btrfs-snapshot/
{{< giscus >}}
