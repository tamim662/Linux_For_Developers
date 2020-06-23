# Network and Network Interfaces

### Show information for all network interfaces(new):
```bash
    ip link
```
### Show with statistics:

```bash
    $ ip -s link
```

### Show information for the <eth_name> network interface:
```bash
    $ ip -s link show eth0/enp1s0
```

### Set the IP address for eth0/enp1s0:
```bash
    $ sudo ip addr add 192.168.1.7 dev eth0
```

### Bring eth0 down:
```bash
    $ sudo ip link set eth0 down
```

### Set the networking route:
```bash
    $ sudo ip route add 172.16.1.0/24 via 192.168.1.5
```
