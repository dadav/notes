# iptables

## clear

```bash
# set default policy
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
# flush everyting
iptables -F
iptables -t nat -F
iptables -t mangle -F
iptables -t raw -F
iptables -Z
# delete everything
iptables -X
iptables -t nat -X
iptables -t mangle -X
iptables -t raw -X
```

## list

```bash
# [L]ist rules [n]umeric in [v]erbose-mode with [line-numbers]
iptables -L -n -v --line-numbers
```

## delete

```bash
# run list first; then do this:
iptables -D $CHAIN $NUMBER
```

## natting

```bash
# eth1 = no internet; eth0 = internet
# be sure to: echo 1 > /proc/sys/net/ipv4/ip_forward
# change source ip to ours
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
# allow forwarding for eth1
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
# forward pakets for established connections
iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

## snippets

```bash
# dont accept new connections (because you want to go into maintenance mode)
iptables -A INPUT  -p tcp -m tcp --dport 443 -m state --state NEW -j REJECT

# log connection attempts
iptables -A INPUT -p tcp --syn -j LOG --log-prefix "[NEW_CONNECTION (IN)]: "
```

## firewall

```bash

#!/bin/bash

# Vars
INTERFACE="eth0"

# Clear
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP
iptables -t nat -F
iptables -t mangle -F
iptables -F
iptables -X

# Accept any related or established connections
iptables -I INPUT  1 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -I OUTPUT 1 -m state --state RELATED,ESTABLISHED -j ACCEPT

# Allow all traffic on the loopback interface
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# Allow outbound DHCP request
iptables -A OUTPUT -o "$INTERFACE" -p udp --dport 67:68 --sport 67:68 -j ACCEPT

# Allow inbound SSH
iptables -A INPUT -i "$INTERFACE" -p tcp -m tcp --dport 22 -m state --state NEW  -j ACCEPT

# Allow outbound SSH
iptables -A OUTPUT -o "$INTERFACE" -p tcp -m tcp --dport 22 -m state --state NEW  -j ACCEPT

# Outbound DNS lookups
iptables -A OUTPUT -o "$INTERFACE" -p udp -m udp --dport 53 -j ACCEPT

# Outbound icmp
iptables -A OUTPUT -o "$INTERFACE" -p icmp -j ACCEPT

# Outbound Network Time Protocol (NTP) requests
iptables -A OUTPUT -o "$INTERFACE" -p udp --dport 123 --sport 123 -j ACCEPT

# Outbound HTTP
iptables -A OUTPUT -o "$INTERFACE" -p tcp -m tcp --dport 80 -m state --state NEW -j ACCEPT
iptables -A OUTPUT -o "$INTERFACE" -p tcp -m tcp --dport 443 -m state --state NEW -j ACCEPT
```
