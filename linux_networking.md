
## Networking commands

- `ip link` : list network interface
- `ip addr`: to list the address
- `ip addr add 192.168.1.10/24 dev eth0` : add ip to a network, to persist /etc/network/interfaces 
- `ip route` OR `route`: to view the routing table
- `ip route add <ip> via <ip>`: to add ip in the routing table
- `cat /proc/sys/net/ipv4/ip_forward`: to check ip_forward in host ( configured as router )

## Gateway & router

- `route` : list route 
- `ip route add 192.168.2.0/24 via 192.168.1.1<router>`: set the gateway
- `ip route add 172.217.194.0/24 via 192.168.2.1<internet>`
- `ip route add default via 192.168.2.1`: this set 192.168.2.1 as default gateway

### use linux host as router

A(192.168.1.5)  ------192.168.1.0 ---------- B(192.168.1.6, 192.168.2.5) --------------192.168.2.0------C(192.168.2.5)
How to reach from A to C?
- `ip route add 192.168.2.0/24 via 192.168.1.6`: for A
- `ip route add 192.168.1.0/24 via 192.168.2.6`: for C
- `echo 1 > /proc/sys/net/ipv4/ip_forward`: set ip_forward to 1 on B
- `/etc/sysctl.conf`: set net.ipv4.ip_forward to 1 so that it persist and not change after rebooting


# DNS
- `/etc/hosts`: name the ip , db -> 192.168.1.11
- `/etc/resolv.conf`: dns server, nameserver
- `/etc/nsswitch.conf`: order for dns, files dns
