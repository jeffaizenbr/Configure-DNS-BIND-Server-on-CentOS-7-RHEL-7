# Configure-DNS-BIND-Server-on-CentOS-7-RHEL-7

# 1 - Enviroment

```bash
MASTER - 192.168.122.13
SLAVE  - 192.168.122.14
```

# 2 - Install DNS Bind

```bash
yum install bind bind-utils
```

# 3 - configure DNS configuration file /etc/named.conf

```bash
// listen-on port 53 { 127.0.0.1; 192.168.122.13; };
// listen-on-v6 port 53 { ::1; };

listen-on port 53 { 127.0.0.1; 192.168.0.10; };
allow-query     { localhost; 192.168.122.0/24; };
allow-transfer { 192.168.122.14; };
also-notify { 192.168.122.14; };
```

# 4 - Creating zone "SouJeff.Local in file /etc/named.conf"

```bash
zone "soujeff.local" IN {

         type master;

         file "/var/named/soujeff.local.db";

         allow-update { none; };
};


zone "122.168.192.in-addr.arpa" IN {

          type master;

          file "/var/named/192.168.122.db";

          allow-update { none; };
};
```

# 5 - Creating forward-zone vim /var/named/soujeff.local.db

```bash
@   IN  SOA     ns1.soujeff.local. root.soujeff.local. (
                                                1001    ;Serial
                                                3H      ;Refresh
                                                15M     ;Retry
                                                1W      ;Expire
                                                1D      ;Minimum TTL
                                                )

;Name Server Information
@      IN  NS      ns1.soujeff.local.

;IP address of Name Server
ns1 IN  A       192.168.122.13

;Mail exchanger
itzgeek.local. IN  MX 10   mail.soujeff.local.

;A - Record HostName To IP Address
www     IN  A       192.168.122.100
mail    IN  A       192.168.122.150
jeff    IN  A       192.168.122.166
webmin  IN  A       192.168.122.167
;CNAME record
ftp     IN CNAME        www.soujeff.local.

```

# 6 - Creating reverse-zone vim /var/named/192.168.122.db

```bash
@   IN  SOA     ns1.soujeff.local. root.soujeff.local. (
                                                1001    ;Serial
                                                3H      ;Refresh
                                                15M     ;Retry
                                                1W      ;Expire
                                                1D      ;Minimum TTL
                                                )

;Name Server Information
@ IN  NS      ns1.soujeff.local.

;Reverse lookup for Name Server
10        IN  PTR     ns1.soujeff.local.

;PTR Record IP address to HostName
100      IN  PTR     www.soujeff.local.
150      IN  PTR     mail.soujeff.local.
```

# 7 - Change resolv.conf and primary DNS

```bash
vim /etc/resolv.conf
vim /etc/sysconfig/network-scripts/enp0s8
```
# 8 - Adding new entrance on DNS zone

```bash
dnssec-signzone -S -z -o dominio.com.br db.dominio.com.br
rndc reload
```
