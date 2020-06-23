# System Boot
* initramfs --> This is the initial ram filesystem, which contains essential drivers for hardware and filesystems.

* vmlinuz --> This is the compressed Linux kernel.

The first program which runs on the system is called init.

**/sbin/init**


### New method of controlling system startup is "Upstart" developed by Ubuntu.

### Grub to start graphical login screen;

```bash
    $ sudo systemclt start gdm
```
# Killing the Graphical User Interface 

#### First, we will bring down the GUI;

```bash
    $ sudo systemctl stop gdm
    $ sudo telinit 3
```

#### Now, we restart the GUI from the text console with one of the following commands;

```bash
    $ sudo systemctl start gdm
    $ sudo telinit 5
```
telinit are used to change the runlevel of the system. 

# System Runlevels

#### Here is a table summarizing the levels:

| Runlevel   |      Meaning      |
|----------|:-------------:|
| 0|  Shutdown system and turn power off |
| 1|    Single user mode   |
| 2| Multiple user, no NFS, only text login|
| 3|  Multiple user, with NFS and network, only text login|
| 4|    Not used   |
| 5| Multiple user, with NFS and network, graphical login with X |
| 6| Reboot |
    
