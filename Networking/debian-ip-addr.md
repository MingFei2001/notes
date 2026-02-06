# Debian Static Network Address
Debian uses the `ifupdown` package to manage network interfaces. Configuration is done through `/etc/network/interfaces`, and changes are applied using `ifup` and `ifdown` commands.

This guide shows how to configure a static IP address for a Debian server.

## Set Static IP Address

Edit `/etc/network/interfaces` using your preferred text editor:

```
iface eth1 inet static
    address <ipv4 addr>
    netmask 255.255.255.0
    gateway <ipv4 gateway>
```

## Set DNS Servers
Need to point to your DNS servers in order to get internet access.

Edit `/etc/resolv.conf`:

```
nameserver 8.8.8.8
nameserver 1.1.1.1
```
