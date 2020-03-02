## Enabling the eth0 Interface in Kali Linux

We need to enable a network interface on kali linux as it doesn't have one enabled by default.

We need to add a configuration for `eth0` interface to the `/etc/network/interfaces` file.

```bash
nano /etc/network/interfaces
```

Add the following lines to the bottom of the file.

>Hint: you can paste in nano with `ctrl` + `shift` + `v`

```
auto eth0
iface eth0 inet dhcp
```

Save the changes with `ctrl` + `o` (think Write **O**ut)

Exit the editor with `ctrl` + `x` (think E**x**it )

Restart the network interface

```
root@kali:~# ifdown eth0
```

```
root@kali:~# ifup eth0

Internet Systems Consortium DHCP Client 4.4.1
Copyright 2004-2018 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/eth0/08:00:27:33:75:72
Sending on   LPF/eth0/08:00:27:33:75:72
Sending on   Socket/fallback
DHCPDISCOVER on eth0 to 255.255.255.255 port 67 interval 7
DHCPOFFER of 10.0.2.15 from 10.0.2.3
DHCPREQUEST for 10.0.2.15 on eth0 to 255.255.255.255 port 67
DHCPACK of 10.0.2.15 from 10.0.2.3     
bound to 10.0.2.15 -- renewal in 506 seconds.      
```

So our IP address should be the IP address shown on the bottom line E.g. `10.0.2.15`

We can confirm by using `ipconfig` to check the IP address assigned to `eth0`

```
root@kali:~# ifconfig eth0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
        inet6 fe80::a00:27ff:fe33:7572  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:33:75:72  txqueuelen 1000  (Ethernet)
        RX packets 6138  bytes 8222095 (7.8 MiB)
        RX errors 2  dropped 0  overruns 0  frame 0
        TX packets 3115  bytes 230609 (225.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 19  base 0xd020    
```

The IP address of our kali machine shown next to `inet` is `10.0.2.15`. Don't worry if yours is different.

