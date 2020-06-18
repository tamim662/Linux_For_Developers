# Path

### 
PATH is an environmental variable in Linux and other Unix-like operating systems that tells the shell which directories to search for executable files (i.e., ready-to-run programs) in response to commands issued by a user.


### View your PATH:
```bash
    echo $PATH
```

### Set your PATH:

Simply add /place/with/the/file to the $PATH variable with the following command:

```bash
    export PATH=$PATH:/place/with/the/file
```

> An addition to a user's PATH variable can be made permanent by adding it to that user's .bash_profile file. .bash_profile is a hidden file in each user's home directory that defines any specific environmental variables and startup programs for that user.

### Adding to the Path
Create a simple executable file with the name ls in your current directory, which we will assume to be /tmp:

```bash
    $ cd /tmp
    $ echo echo Hello, This is MY ls program > ls
    $ chmod +x ls
```
You can run this directly by doing:

```bash
    $ ./ls
```

but just typing ls will bring up the normal /bin/ls, which can be verified by typing which ls.

If you do:

```bash
    $ export PATH=/tmp:$PATH
```

then typing ls will bring up your program no matter where you are sitting on the filesystem.



