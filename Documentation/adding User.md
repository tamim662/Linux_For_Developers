### A file control under /etc/default/useradd appropriately named.

```bash
    $ less /etc/default/useradd
```


### Creating an account:

```bash
    $ sudo useradd -m -c "Tamim Boss" -s bin/bash tamim
```

> Here, m for make sure to craete newuser directoru in /home and c for account name , s for specify the default shell to bin/bash.

Then add password:
```bash
    $ sudo passwd tamim
```

### Verify the account:
```bash
    $ grep tamim /etc/passwd /etc/group
```
Then log in to run:
```bash
    $ ssh tamim@localhost
```

### To remove the acoount:
```bash
    $ sudo userdel -r tamim
```
