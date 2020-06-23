# Upgrading and Patching

```bash
    $ sudo apt-get update
    $ sudo apt-get dist-upgrade
    
```
> **Debian-based systems require update to first synchronize repository information before updating the entire package system.**
 ### The following table lists the basic packaging operations:

 | Oeartion       |                          Debian      |
|-------------------|:-----------------------------------:|
|install a package|   dpkg --install foo.deb |
|Install a package with dependencies from repository |  apt-get install foo |
|Remove a package| dpkg --remove foo.deb  |
| Remove a package and dependencies using a repository|	apt-get remove foo   |
|Update package to a newer version | dpkg --install foo.deb  |
|Update entire system | apt-get dist-upgrade  |
|Show available packages with “foo” in name | apt-cache search foo|
|What package does a file belong to? | 	dpkg --search file  |
 