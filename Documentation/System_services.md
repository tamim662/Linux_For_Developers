# Managing System Services

**Every operating system has services which are usually started on system initialization and often remain running until shutdown.**

> **systemd** is a new init system and system manager, that was adopted by most of Linux distributions now over the traditional **SysVinit** manager.

### With systemd, all service management is done with the systemctl utility.

### To show the status of everything systemd controls, do:

```bash
    $ systemctl
```

### Show all the devices:

```bash
     $ systemctl list-units -t service --all
    
```

### Show only active services:

```bash
    $ systemctl list-units -t service
```

### To start(active) one or more units:
```bash
    $ sudo systemctl start foo
    $ sudo systemctl start foo.service
    $ sudo systemctl start /path/to/foo.service
```

### To stop (deactivate):
```bash
    $ sudo systemctl stop foo.service
```
These commands are equivalent to **$ sudo service foo start|stop**

### Enable/disable service:
```bash
    $ sudo systemctl enable sshd.service
    $ sudo systemctl disable sshd.service
```
