---
Section: post
Url: linux-grub-fix
Title: Fixing GRUB not loading
Description: Fixing GRUB not loading
Keywords:
- linux
- grub
- dual boot
Topics:
- Tutorial
date: 2017-01-10
disqus_title: Fixing GRUB not loading
disqus_url: https://maunium.net/blog/linux-grub-fix
---

While GRUB usually works just fine, there can be cases where everything doesn't
work as expected and you might just find yourself in a situation where you
can't get GRUB to load, especially if you have more than one OS installed.
These instructions have helped me fix most that problem in most cases.

Capital letters in commands in the following instructions are variables which
you should replace with appropriate values for your setup.

1.  Boot into a live environment

2.  Mount your root partition into `/mnt`: `sudo mount /dev/sdXY /mnt` where X
    is the number of the disk and Y is the lowercase letter of the partition.

    You can use [`lsblk`](https://linux.die.net/man/8/lsblk),
    [`sudo fdisk -l`](https://linux.die.net/man/8/fdisk) or GParted to view what
    partitions are available. Your root partition is generally the largest
    partition (probably over 10GB) and in `ext4` format.

    If your disk is encrypted, you can use the
    [`cryptsetup`](https://linux.die.net/man/8/cryptsetup)
    command to decrypt it: `sudo cryptsetup luksOpen /dev/sdXY sdXY--crypt`.
    After decrypting, the decrypted data is available at
    `/dev/mapper/sdXY--crypt`.

    If the encrypted partition is using [LVM](https://wiki.ubuntu.com/Lvm), the
    LVM partitions will be available under `/dev/mapper` with the partition
    names provided during setup. On Ubuntu, the main partition should be
    something like `ubuntu-FLAVOR--vg_root`

3.  Check if there are any files in `/mnt/boot`. If you can find files with
    names containing `initrd` or `vmlinuz`, you can skip the next step since you
    most likely don't have a separate boot partition.

4.  Mount your boot partition into `/mnt/boot`: `sudo mount /dev/sdXY /mnt`. X
    (the disk number) is probably the same as in step 2, but Y (the partition
    letter) will not be the same. The boot partition is usually in `ext2`
    format.

5.  If you have an EFI partition, mount it into `/mnt/boot/efi`:
    `sudo mount /dev/sdXY /mnt/boot/efi`.

    To determine if you have an EFI partition, simply use
    `sudo fdisk -l | grep -i efi`.
    If you have an EFI partition, the first column in the output will be the
    full path to the EFI partition (i.e. what you replace `/dev/sdXY` in the
    command with)

6.  Mount the special Linux directories to `mnt`:

    ```bash
    for i in proc sys dev dev/pts run
    do
      sudo mount -o bind /$i /mnt/$i
    done
    ```

    You may replace newlines with semicolons (`;`) to write the script in one
    line:

        for i in proc sys dev dev/pts run; do sudo mount -o bind /$i /mnt/$i; done

7.  [`chroot`](https://linux.die.net/man/1/chroot) into `/mnt`:
    `sudo chroot /mnt`

8.  Finally fix the GRUB installation: `sudo grub-install /dev/sdX` where `X` is
    the same as `X` in step 4 or step 2.
