# Memory

### The free utility gives a very terse report on free and used memory in your system:

```bash
    $ free -mt
```

where, **m** for megbibiytes and **t** for total


### A more detailed look can be obtained by looking at /proc/meminfo;

```bash
    $ cat /proc/meminfo
```

# Swap

```bash
    $ cat /proc/swaps
```

> **The only commands involving swap are mkswap for formatting a swap file or partition, swapon for enabling one (or all) swap area, and swapoff for disabling one (or all) swap area.**

