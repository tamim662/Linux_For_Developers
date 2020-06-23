# Using dpkg

### dpkg is a debian packaging system.

> d-pkg  --->  debian package

### To get list the all packages of the system:

```bash
    $ dpkg --list | less
```
typed **less** because its a long list

### To get see information of particular package "bzip2"
```bash
    $ dpkg --list | grep bzip2
```

### To see what contains in that package actually:
```bash
    $ dpkg --listfiles bzip2 | less
```
### To remove the package:
```bash
    $ sudo dpkg --remove bzip2
```
Sometimes its may not removed for dependency issue.