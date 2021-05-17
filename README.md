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


options {
        listen-on port 53 { 127.0.0.1; 192.168.122.13; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file  "/var/named/data/named.recursing";
        secroots-file   "/var/named/data/named.secroots";
        allow-query     { localhost; 192.168.122.0/24; };
        allow-transfer { 192.168.122.14; };
        also-notify { 192.168.122.14; };

        recursion yes;







zone "soujeff.local" {
     type master;
     file "soujeff.local"; #zone file path
     allow-update { none; };
};
zone "122.168.192.in-addr.arpa" {
     type master;
     file "rev.122.168.192"; #192.168.168.0/24 subnet
     allow-update { none; };
};


```

# 4 - Creating zone "SouJeff.Local in file /etc/named.conf"

```bash

$TTL 86400
@                  IN     SOA      ns1.soujeff.local. root.soujeff.local. (
                       000000005   ;Serial
                       3600        ;Refresh
                       1800        ;Retry
                       604800      ;Expire
                       86400       ;Minimum TTL
)
;; Name Servers forwarder
@                  IN      NS      ns1.soujeff.local.
@                  IN      NS      ns2.soujeff.local.
@                  IN      A       192.168.122.13
@                  IN      A       192.168.122.14
ns1                IN      A       192.168.122.13
ns2                IN      A       192.168.122.14
jeff               IN      A       10.0.0.4
jeff2              IN      A       10.0.0.5
jeff3              IN      A       10.0.0.6
pega               IN      A       10.0.0.7
lula               IN      A       10.0.0.166


```

# 5 - Creating forward-zone vim /var/named/

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

# 6 - Creating reverse-zone vim /var/named/rev.122.168.192

```bash

$TTL 86400
@                IN     SOA      ns1.soujeff.local. root.soujeff.local. (
                    000000002    ;Serial
                    3600         ;Refresh
                    1800         ;Retry
                    604800       ;Expire
                    86400        ;Minimum TTL
)
;; Name Servers resolver
@                IN      NS      ns1.vlearning.com.kh.
@                IN      NS      ns2.vlearning.com.kh.
@                IN      PTR     vlearning.com.kh.
ns1              IN      A       192.168.122.13
ns2              IN      A       192.168.122.14
10               IN      PTR     ns1.vlearning.com.kh.
20               IN      PTR     ns2.vlearning.com.kh.
~


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


Obs: to generating key tag and Digest key 
dnssec-keygen -a NSEC3RSASHA1 -b 2048 -n ZONE example.com
dnssec-keygen -f KSK -a NSEC3RSASHA1 -b 4096 -n ZONE example.com

example by registro.br

GERA = dnssec-keygen -r /dev/urandom -f KSK dominio.com.br
ASSINA = dnssec-signzone -S -z -o dominio.com.br db.dominio.com.br
CAPTURA KEYTAG E DIGEST = cat dsset-dominio.com.br. | head -1
```
