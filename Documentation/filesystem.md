# Partitions


### The command line utility for creating and examining hard disk partitions is fdisk;**

```bash
    $ sudo fdisk /deb/sda
```
> /dev/sda is the first hard drive (the primary master), /dev/sdb is the second etc. The numbers refer to partitions, so /dev/sda1 is the first partition of the first drive.


* sda5 = 5th partition on the first HD (‘a’ is the first HD)
* sdc8 = 8th partition on the third HD (‘c’ is third of three active HDs)
* sdb3 = 3rd partition on the second HD (‘b’ is second of two or more active HDs)

### What is Grub?

Grub is a bootloader. A bootloader is a program that is found by the system BIOS in the boot sector of your storage device (hard drive’s Master boot record), and which locates and starts your operating system for you.

Grub has its own format/syntax for finding an HD location that’s different from the syntax the Linux OS uses:

* (hd0,5) = sda5
* (hd2,8) = sdc8
* (hd1,3) = sdb3

## To get know more about  the fdisk;

```bash
    $ fdisk --help

    or

    $ sudo fdisk /deb/sda

    > m  (press m)
    >p  (print the details of storage)
    >n  (create a new partition)
    >q (to quite)
```


### Partitioning Scheme

* /boot is releatively small, typically 100-200 MB , and holds kernels and other boot-related materials; these files are vital and rarely change; and it is safer to keep them on a seperate partition.

* / contains everything else and is as large as you need; depending on your distribution; system files and applications and basic programs and development tools will probably chew up 3-8 GB of space

* The swap partition should not be at least as big as the amount of the memory on your system; you can use swap files instead of partitions, but this is a weaker method due to both efficiecny and stability considerations.

* Often the /home directory is put on a separate partition.

* usr which is relative static may be put on a seperate  partition, as might /var which is quite volatile, and /tmp , which is temporary.

* In addition, one more of these partitions might be available only as a network share ,as in the use NFS, and not even be mountable untill the system is well-booted.