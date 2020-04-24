
Documentation of Getting Help:
___
UBuntu(Linux)

# 3 Ways;
man is the workhorse of Linux documentation.
(short for manual)
man command gives you information about the system utilities.

For instance, socket has at least two different man pages, in chapters 2 and 7. You can look at any one of them by specifying the particular chapter, as in:
$ man 7 socket
or you can see all of them in sequence by doing:
$ man -a socket
man has a lot of options (do man man), some of which have utility short hand forms. For example, by doing either of these commands:
$ whatis socket
$ man -f socket
you will get a list of all man pages that have socket in their name. Likewise, if you do either of the commands:
$ apropos socket
$ man -k socket
you will get a list of all man pages that discuss sockets, whether or not it is in their name.




