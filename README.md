# MOOC IPv6

## Tutorial #1

This is the first tutorial on IPv6. The figure shows the topology of the studied network.

![Network Topology](/images/MoocSession5_act16_topolo_20190605.png)

### `ifconfig` (`ipconfig` for windows)

Typing `ifconfig` in the terminal of PC-1::c1 returns the following output

<pre><code>eth0      Link encap:Ethernet  HWaddr de:5a:00:f0:63:e2  
          <font color="magenta">inet6 addr: fd75:e4d9:cb77:1::c1/64 Scope:Global</font>
          <font color="cyan">inet6 addr: fe80::dc5a:ff:fef0:63e2/64 Scope:Link</font>
          UP BROADCAST RUNNING MULTICAST  <font color="lightgreen">MTU:1500</font>  Metric:1
          RX packets:20 errors:0 dropped:0 overruns:0 frame:0
          TX packets:7 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:2320 (2.2 KiB)  TX bytes:702 (702.0 B)

lo        Link encap:Local Loopback  
          <font color="red">inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host</font>
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)</code></pre>

We can see that PC-1 has two networks. One called **eth0** and another called **lo**. eth0 is simply the ethernet network, while lo is the loopback network. The loopback network has the usual IPv4 address of `127.0.0.1`, which corresponds to the IPv6 address of `::1`.

For eth0, on the other hand, we see two IPv6 addresses (_inet6 addr_). One which has a Scope:Link (local) and another which has a Scope:Global. As in IPv4 (the family of `192.168.0.0` IP addresses), the link IPv6 is used to access a machine on a local area network (LAN), while the global IPv6 is used to access a machine on the internet.

We also see that we have a Maximum Transmission Unit (MTU) of 1500, meaning that the largest data packets that an Internet-connected device can accept is 1500 bytes.

### `ip addr sh`

The ip addresses can also be accessed using the command

```sh
# for all ips
ip addr sh
# for a specific ip, e.g. eth0
ip addr sh eth0
```

On router R2, `ip addr sh` returns

```text
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 0c:49:0a:1c:5a:00 brd ff:ff:ff:ff:ff:ff
    inet6 fd75:e4d9:cb77:2::2/64 scope global
       valid_lft forever preferred_lft forever
    inet6 fe80::e49:aff:fe1c:5a00/64 scope link
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 0c:49:0a:1c:5a:01 brd ff:ff:ff:ff:ff:ff
    inet6 fd75:e4d9:cb77::2/64 scope global
       valid_lft forever preferred_lft forever
    inet6 fe80::e49:aff:fe1c:5a01/64 scope link
       valid_lft forever preferred_lft forever
4: eth2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
    link/ether 0c:49:0a:1c:5a:02 brd ff:ff:ff:ff:ff:ff
5: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 0c:49:0a:1c:5a:03 brd ff:ff:ff:ff:ff:ff
    inet6 fd75:e4d9:cb77:3::3/64 scope global
       valid_lft forever preferred_lft forever
    inet6 fe80::e49:aff:fe1c:5a03/64 scope link
       valid_lft forever preferred_lft forever
```

We observe 5 network. Network 1 being the loopback network, while networks 2 to 5 are all ethernet networks.

- eth0 (`fd75:e4d9:cb77:2::2`) which connects to PC-2 on the network `fd75:e4d9:cb77:2::/64`.
- eth1 (`fd75:e4d9:cb77::2`) which connects to R1 on the network `fd75:e4d9:cb77::/64`.
- eth2 (`<NO-CARRIER>`) indicating that the network jack detects no signal on the line. This is usually because the network cable is unplugged or broken.
- eth3 (`fd75:e4d9:cb77:3::3`) which connects to PC-2 on the network `fd75:e4d9:cb77:3::/64`.

### `ping6`

We can use the `ping6` (or `ping` for IPv4) command to check if PC-2 is reachable from PC-1.

On the terminal of PC-2, we type

```sh
# ping address fd75:e4d9:cb77:2::c2 twice
ping6 -c 2 fd75:e4d9:cb77:2::c2
```

and we received the following outputs

```text
PING fd75:e4d9:cb77:2::c2(fd75:e4d9:cb77:2::c2) 56 data bytes
64 bytes from fd75:e4d9:cb77:2::c2: icmp_seq=1 ttl=62 time=29.2 ms
64 bytes from fd75:e4d9:cb77:2::c2: icmp_seq=2 ttl=62 time=3.03 ms

--- fd75:e4d9:cb77:2::c2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 3.038/16.144/29.251/13.107 ms
```

Which confirms that PC-2 is reachable (0% packet loss).

### `nc` (netcat)

Using netcat, we can do some further investigation.
First, on PC-1, we type:

```sh
nc -6lvu -p 4500
```

The `-6lvu` tags specify that we are using IPv6, listening mode, verbose, and udp, while the -p 4500 specifies that we will be listening on port 4500. The previous command returns:

```sh
Listening on [:::] (family 10, port 4500)
```

which declares that no we are listening to incoming messages on port 4500 on all IPv6 addresses of the machine (`:::` is similar to `0.0.0.0`).

Now on PC-2, we can connet to PC-1 by typing:

```sh
nc -6vu fd75:e4d9:cb77:1::c1 4500
```

specifying the IPv6 address `fd75:e4d9:cb77:1::c1` and the target port `4500` of PC-1.

The terminal returns

```sh
Connection to fd75:e4d9:cb77:1::c1 4500 port [udp/ipsec-nat-t] succeeded!
```

### `curl`

`curl` is a command-line tool and library for transferring data with URLs. In this practice, PC-3, with IPv6 address `fd75:e4d9:cb77:3::c3`, is a server.

We can get the html file from PC-3 by simply typing

```sh
curl [fd75:e4d9:cb77:3::c3]
# a longer way would be
# curl -6 http://[fd75:e4d9:cb77:3::c3]
```

by enclosing the IP address in between brackets, `curl` knows that it is dealing with an IPv6 address.

## References

- [nc Command (Netcat) with Examples](https://phoenixnap.com/kb/nc-command)
- [curl Command in Linux with Examples](https://www.geeksforgeeks.org/curl-command-in-linux-with-examples/)
- [Difference Between ifconfig and ipconfig](https://www.baeldung.com/linux/ifconfig-vs-ipconfig)

```sh
sh interfaces detail #Router
sh ipv6 groups #Router
ip maddr sh #PC
ping6 -c 2 ff02::1%eth0 #PC local network

PING ff02::1%eth0(ff02::1) 56 data bytes
64 bytes from fe80::dc5a:ff:fef0:63e2: icmp_seq=1 ttl=64 time=0.019 ms
64 bytes from fe80::e49:aff:fe42:7b00: icmp_seq=1 ttl=64 time=0.620 ms (DUP!)
64 bytes from fe80::dc5a:ff:fef0:63e2: icmp_seq=2 ttl=64 time=0.034 ms

--- ff02::1%eth0 ping statistics ---
2 packets transmitted, 2 received, +1 duplicates, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.019/0.224/0.620/0.280 ms

ping6 -c 2 ff02::1%eth0 #PC only router multicast

cat /etc/resolv.conf #dns ??

```
