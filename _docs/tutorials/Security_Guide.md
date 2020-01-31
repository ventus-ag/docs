---
title: Security Guide
description: Provides information about security incidents and how to fix problems. the issues.
tags: [ featured, tutorial ]
# permalink: /Security_Guide/
---
# Security Guide  
{: .no_toc }
---
Our Security Guide provide further information on security incidents reported with e-mail Reports to Users and how to fix the issues.

The open services can be abused – or have already been actively abused – for DDoS reflection attacks against third parties. 
Currently,  incidents on the following openly accessible services are taken into account:

## Table of contents  
{: .no_toc .text-delta }

1. TOC
{:toc}

## DNS Open-resolver  
---
DNS Open-resolvers are DNS servers responding to recursive queries for arbitrary domain names from anywhere on the Internet.

### Problem  
DNS Open-resolvers can be abused for DDoS reflection attacks against third parties.

### Verification  
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a DNS server is configured as an open resolver allowing recursive queries, you can use the 'dig' tool for sending a DNS request for an arbitrary domain name (the server is not authoritative for) to the IP address of the DNS server in question:
```
$ dig cert-bund.de @192.168.45.67
```

An open resolver allowing recursive queries will return a response like this, followed by a set of DNS records:
```console
; <<>> DiG 9.8.1-P1 <<>> cert-bund.de @192.168.45.67
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43941
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
```

A DNS server not allowing recursive queries will instead respond with an error message like this:
```console
; <<>> DiG 9.8.1-P1 <<>> cert-bund.de @192.168.45.67
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: REFUSED, id: 42022
;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available
```

### Solution  
Disable recursion or limit recursion to trusted clients in the DNS server's configuration.
For instructions on how to disable/limit recursion with other DNS servers, please consult the manual.

**Further Information**      
- Wikipedia: [Domain Name System](<https://en.wikipedia.org/wiki/Domain_Name_System>)  
- US-CERT: [DNS Amplification Attacks (TA13-088A)](<https://www.us-cert.gov/ncas/alerts/TA13-088A>)  


## NTP servers with 'monlist' enabled  
---
The Network Time Protocol (NTP) is a networking protocol for clock synchronization between IT systems. NTP supports a monitoring service that allows administrators to query the server for traffic counts of connected clients via the 'monlist' command.

### Problem  
The NTP 'monlist' feature can be abused for DDoS reflection attacks against third parties.

### Verification  
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if an NTP server is openly accessible from the Internet and has the 'monlist' feature enabled, you can use 'ntpdc' like this:
```
# ntpdc -n -c monlist 192.168.45.67
```

An NTP server with the 'monlist' feature enabled will return a list of clients that recently queried the NTP server:
```console
remote address          port local address      count m ver rstr avgint  lstint
===============================================================================
123.56.67.89             123 192.168.45.67       3559 4 4      0    446     119
34.56.78.90              123 192.168.45.67      16992 4 4      0    137     207
98.76.54.32              123 192.168.45.67      17005 4 4      0    137     232
111.22.33.44           58708 192.168.45.67          3 3 4      0 423826   72192
222.33.44.55           35560 192.168.45.67          8 3 4      0 180029  236607
33.44.55.66            59053 192.168.45.67          1 3 3      0 615565  615565
44.55.66.77            59040 192.168.45.67          2 3 4      0 637297  664374
```

If the list of clients is currently empty, it will instead return:
```
***Server reports data not found
```

If there is no openly accessible NTP server with the 'monlist' feature enabled, the request will run into a timeout: Timeout:
```
192.168.45.67: timed out, nothing received

***Request timed out
```

### Solution  
Update to ntpd version 4.2.7p26 or later. If an update is not possible, disable status queries in the NTP server's configuration or restrict access to trusted clients.

**Further Information**     
- Wikipedia: [Network Time Protocol](<https://en.wikipedia.org/wiki/Network_Time_Protocol>)  
- US-CERT: [NTP Amplification Attacks Using CVE-2013-5211 (TA14-013A)](<https://www.us-cert.gov/ncas/alerts/TA14-013A>)  
- CERT.org: [NTP can be abused to amplify denial-of-service attack traffic (VU#348126)](<https://www.kb.cert.org/vuls/id/348126/>)  
- NTP.org: [DRDoS / Amplification Attack using ntpdc monlist command](<http://support.ntp.org/bin/view/Main/SecurityNotice#April_2010_DRDoS_Amplification_A>)  


## Openly accessible Elasticsearch servers  
---
Elasticsearch is a popular search engine based on Apache Lucene, often used with web applications.

### Problem  
If an Elasticsearch server is openly accessible from the Internet and not protected by any forms of authentification, anyone who can connect to the server has unrestricted access to the data stored with it. This allows attackers to modify or delete any data or potentially steal sensitive information. In addition, prior to versions 1.2.x an attacker can use dynamic scripting to perform arbitrary code execution on the machine that Elasticsearch is hosted on.

### Verification  
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if an Elasticsearch server is openly accessible from the Internet, you can use 'netcat' as follows:
```
$ printf "GET / HTTP/1.0\r\n\r\n" | netcat 192.168.45.67 9200
```

An openly accessible Elasticsearch server will return information like this:
```console
{
  "status" : 200,
  "name" : "My Database",
  "cluster_name" : "My Cluster",
  "version" : {
    "number" : "1.7.5",
    "build_hash" : "00f95f4ffca6de89d68b7ccaf80d148f1f70e4d4",
    "build_timestamp" : "2016-02-02T09:55:30Z",
    "build_snapshot" : false,
    "lucene_version" : "4.10.4"
  },
  "tagline" : "You Know, for Search"
}
```

Otherwise, netcat will return an error message:
```
netcat: connect to 192.168.45.67 port 9200 (tcp) failed: Connection refused
```
or
```
netcat: connect to 192.168.45.67 port 9200 (tcp) failed: Connection timed out
```

### Solution  
* Do not expose your Elasticsearch server to the Internet!  
* Restrict access to the Elasticsearch server to trusted systems (e. g., the web application server) in the server's configuration and/or by blocking incoming connections from the Internet to port 9200/tcp on the firewall.  
* Check the security best practices provided by the Elasticsearch developers.  
* Keep your Elasticsearch installation up-to-date. Install available security updates asap.  

**Further Information**    
- AlienVault: [ElasticZombie Botnet - Exploiting Elasticsearch Vulnerabilities](<https://cybersecurity.att.com/blogs/labs-research/elasticzombie-botnet-exploiting-elasticsearch-vulnerabilities>)  
- BleepingComputer: [MongoDB Hijackers Move on to ElasticSearch Servers](<https://www.bleepingcomputer.com/news/security/mongodb-hijackers-move-on-to-elasticsearch-servers/>)  


## Openly accessible LDAP servers
---
The Lightweight Directory Access Protocol (LDAP) is a networking protocol for accessing and maintaining distributed directory information services.

### Problem
Openly accessible LDAP servers can be abused for DDoS reflection attacks against third parties. Furthermore, they allow potential attackers to gather information on the server or network for preparation of further attacks.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if an LDAP server is openly accessible from the Internet, you can use the 'ldapsearch' tool:
```
$ ldapsearch -x -h 192.168.45.67 -s base
```

An openly accessible LDAP server will return information like this:
```console
dn:
currentTime: 20161227101121.0Z
subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=MYDOMAIN,DC=DE
dsServiceName: CN=NTDS Settings,CN=SRV01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=MYDOMAIN,DC=DE
namingContexts: DC=MYDOMAIN,DC=de
namingContexts: CN=Configuration,DC=MYDOMAIN,DC=de
namingContexts: CN=Schema,CN=Configuration,DC=MYDOMAIN,DC=de
defaultNamingContext: DC=MYDOMAIN,DC=de
schemaNamingContext: CN=Schema,CN=Configuration,DC=MYDOMAIN,DC=de
configurationNamingContext: CN=Configuration,DC=MYDOMAIN,DC=de
rootDomainNamingContext: DC=MYDOMAIN,DC=de
```

Otherwise, ldapsearch will run into a timeout.

### Solution  
* Restrict access to the LDAP server to trusted clients, e. g. by blocking incoming connections to ports 389/tcp and 389/udp on the firewall.   
* Use LDAP with StartTLS or LDAPS (LDAP over TLS/SSL) which provides secure and encrypted communication between clients and the LDAP server.   

**Further Information**    
- Wikipedia: [Lightweight Directory Access Protocol](<https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol>)  
- f5: [Old Protocols, New Exploits: LDAP Unwittingly Serves DDoS Amplification Attacks](<https://www.f5.com/labs/articles/threat-intelligence/old-protocols-new-exploits-ldap-unwittingly-serves-ddos-amplification-attacks-22609>)   
- 360.com: [CLDAP is Now the No.3 Reflection Amplified DDoS Attack Vector, Surpassing SSDP and CharGen](<https://blog.netlab.360.com/cldap-is-now-the-3rd-reflection-amplified-ddos-attack-vector-surpassing-ssdp-and-chargen-en/>)


## Openly accessible mDNS services
---
Multicast DNS (mDNS) is used for resolving host names to IP addresses within small networks that do not include a local DNS server. It is implemented e. g. by the Apple 'Bonjour' and Linux/BSD 'Avahi' (nss-mdns) services. mDNS uses port 5353/udp.

### Problem
Openly accessible mDNS services can be abused for DDoS reflection attacks against third parties. Furthermore, they allow potential attackers to gather information on the system or network the service is running on for preparation of further attacks.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if an mDNS service is openly accessible from the Internet, the 'dig' tool can be used like this:
```
$ dig +short -p 5353 -t ptr _services._dns-sd._udp.local @192.168.45.67
```

An openly accessible mDNS service will return a response like this:
```console
_workstation._tcp.local.
_udisks-ssh._tcp.local.
```

Otherwise, the request will run into a timeout:
```console
;; connection timed out; no servers could be reached
```

### Solution
If the mDNS service is not required, disable or deinstall it. Otherwise, restrict access to trusted clients, for example by blocking incoming connections to port 5353/udp on the firewall.

On Debian/Ubuntu based Linux systems, the mDNS service can be removed using the following command:
```
# apt-get remove avahi-daemon
```

**Further Information**   
- Wikipedia: [Multicast DNS](<https://en.wikipedia.org/wiki/Multicast_DNS>)  
- CERT.org: [Multicast DNS (mDNS) implementations may respond to unicast queries originating outside the local link (VU#550620)](<https://www.kb.cert.org/vuls/id/550620/>)


## Openly accessible Memcached servers
---
Memcached is an open-source distributed memory object caching system which is generic in nature but often used for speeding up dynamic web applications. In the default configuration, memcached listens on port 11211/tcp and (up to including version 1.5.5) also on port 11211/udp.

### Problem
memcached servers openly accessible from anywhere on the Internet via UDP are abused for DDoS reflection attacks against third parties on a regular basis. This way, extremely high amplification factors can be achieved which poses a serious security threat.

If a memcached server is openly accessible from the Internet via TCP or UDP and no SASL authentification has been configured, anyone who can connect to the server has unrestricted access to the data stored with it. This allows attackers to modify or delete any data or potentially steal sensitive information like login credentials for web applications or customer data from online shops.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

**TCP**   
To check if a Memcached server is openly accessible from the Internet via TCP, you can use 'netcat' as follows:
```
$ echo "stats" | netcat 192.168.45.67 11211
```

An openly accessible Memcached server will return information like this:
```console
STAT pid 1090
STAT uptime 1808125
STAT time 1483622758
STAT version 1.4.14 (Ubuntu)
STAT libevent 2.0.21-stable
STAT pointer_size 64
STAT rusage_user 57.424253
STAT rusage_system 54.322505
STAT curr_connections 5
STAT total_connections 643
STAT connection_structures 9
STAT reserved_fds 20
```

Otherwise, netcat will return an error message:
```
netcat: connect to 192.168.45.67 port 11211 (tcp) failed: Connection refused
```
or
```
netcat: connect to 192.168.45.67 port 11211 (tcp) failed: Connection timed out
```

**UDP**     
To check if a Memcached server is openly accessible from the Internet via UDP, you can use 'netcat' as follows:
```
$ echo -en "\x00\x00\x00\x00\x00\x01\x00\x00stats\r\n" | netcat -u 192.168.45.67 11211
```

An openly accessible Memcached server will return information like shown above.

### Solution   
* Do not expose your Memcached server to the Internet!   
* Restrict access to the Memcached server to trusted systems (e. g., the web application server) in the server's configuration and/or by blocking incoming connections from the Internet to ports 11211/tcp and 11211/udp on the firewall.   
* The UDP port is usually not required. Start memcached with option '-U 0' to disable it.   
* Keep your Memcached installation up-to-date. Install available security updates asap.   

**Further Information**     
- Cloudflare: [Memcrashed - Major amplification attacks from UDP port 11211](<https://blog.cloudflare.com/memcrashed-major-amplification-attacks-from-port-11211/>)   
- Couchbase: [Memcached security](<https://blog.couchbase.com/memcached-security/>) 
- Cisco Talos Blog: [Remotely Exploitable Bugs in Memcached Identified and Patched](<https://blog.talosintelligence.com/2016/10/memcached-vulnerabilities.html>)   


## Openly accessible MongoDB servers
---
MongoDB is a popular NoSQL database system commonly used as a backend for web applications.

### Problem
Access to a MongoDB server should be restricted to trusted systems. If a MongoDB server is openly accessible from the Internet, an attacker can take advantage of this to access the server and modify or delete data, or even obtain sensitive information like customer data from an online shop.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a MongoDB server is openly accessible from the Internet, the MongoDB client tool 'mongo' can be used as follows:
```
$ mongo --host 192.168.45.67
```

If a connection to the MongoDB server was successfully established, the output looks like:
```console
MongoDB shell version: 2.4.10
connecting to: 192.168.45.67:27017/test
>
```

Otherwise the connection will run into a timeout:
```console
Fri Feb  5 10:25:42 Error: couldn't connect to server 192.168.45.67:27017 shell/mongo.js:86
exception: connect failed
```

### Solution   
* Do not expose your MongoDB server to the Internet!   
* Restrict access to the MongoDB server to trusted systems (e. g., the web application server) in the server's configuration and/or by blocking incoming connections from the Internet to port 27017/tcp on the firewall.   
* Check the security best practices provided by the MongoDB developers.   
* Keep your MongoDB installation up-to-date. Install available security updates asap.   

**Further Information**      
- SecurityWeek: [Thousands of MongoDB Databases Found Exposed on the Internet](<https://www.securityweek.com/thousands-mongodb-databases-found-exposed-internet>)
- BleepingComputer: [MongoDB Databases Held for Ransom by Mysterious Attacker](<https://www.bleepingcomputer.com/news/security/mongodb-databases-held-for-ransom-by-mysterious-attacker/>)


## Openly accessible NetBIOS name services
---
NetBIOS defines a software interface and a naming convention. NetBIOS-over-TCP/IP provides the NetBIOS programming interface over the TCP/IP protocol. NetBIOS includes a name service, often called WINS on Microsoft Windows operating systems. The NetBIOS name service uses port 137/udp.

The NetBIOS name service is only needed within local networks and with systems before Microsoft Windows 2000 which require name resolution through WINS. Otherwise,in particular on the Internet, name resolution is done via DNS. Thus, it does not make sense to expose a NetBIOS name service to the Internet.

### Problem
Openly accessible NetBIOS name services can be abused for DDoS reflection attacks against third parties. Furthermore, they allow potential attackers to gather information on the server or network for preparation of further attacks.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a NetBIOS name service is openly accessible from the Internet, you can use the 'nmblookup' tool:
```
$ nmblookup -A 192.168.45.67
```

An openly accessible NetBIOS name service will return information like this:
```console
Looking up status of 192.168.45.67
        HOSTNAME        <00> -         B <ACTIVE>
        WORKGROUP       <00> - <GROUP> B <ACTIVE>
        HOSTNAME        <20> -         B <ACTIVE>
        WORKGROUP       <1e> - <GROUP> B <ACTIVE>
        MAC Address = 12-34-56-78-90-AB
```

Otherwise, nmblookup will run into a timeout:
```console
Looking up status of 192.168.45.67
No reply from 192.168.45.67
```

### Solution
If NetBIOS-over-TCP/IP is not needed, disable it. Otherwise, restrict access to the NetBIOS name service to trusted clients, e. g. by blocking incoming connections to port 137 tcp/udp on the firewall. For security reasons, you should consider blocking access to ports 135, 138, 139 and 445 from anywhere on the Internet as well.

On Linux/Unix systems, the NetBIOS name service is usually provided by 'nmbd' included with Samba. If you don't need Samba, disable or deinstall it. Otherwise, NetBIOS support can be disabled by setting `disable netbios = Yes` in the Samba configuration.

**Further Information**   
- Wikipedia: [NetBIOS](<https://en.wikipedia.org/wiki/NetBIOS>)  
- Akamai: [NetBIOS, RPC Portmap and Sentinel Reflection DDoS Attacks](<https://blogs.akamai.com/2015/10/netbios-rpc-portmap-and-sentinel-reflection-ddos-attacks.html>)   


## Openly accessible MSSQL browser services
---
Microsoft SQL-Server (MSSQL) includes a 'browser service' which lets users connect to instances of the database engine that are not listening on the default port, without knowing the port number.

### Problem
In addition to disclosing information about the network the SQL-Server is running on (which can be used by potential attackers for preparation of further attacks), openly accessible MSSQL browser services can be abused for DDoS reflection attacks against third parties.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if an MSSQL browser service is openly accessible from the Internet, connect to the MS-SQL server using 'netcat' as follows:
```
$ netcat -u 192.168.45.67 1434
```

Then, press <Ctrl+B> followed by <Enter>.
An openly accessible MSSQL browser service will return a response like this:
```console
ServerName;S16362421;InstanceName;MSSQLSERVER2012;
IsClustered;No;Version;11.0.2100.60;tcp;49511;np;
\\S16462341\pipe\MSSQL$MSSQLSERVER2012\sql\query;;
```

Otherwise, there will be no response.

### Solution
If the MSSQL browser service is not needed, disable it. Otherwise, restrict access to trusted clients, for example by blocking incoming connections to port 1434/udp on the firewall.
Microsoft recommends:
*"The SQL Server Browser service lets users connect to instances of the Database Engine that are not listening on port 1433, without knowing the port number. To use SQL Server Browser, you must open UDP port 1434. To promote the most secure environment, leave the SQL Server Browser service stopped, and configure clients to connect using the port number."*
(<https://msdn.microsoft.com/library/ms175043.aspx>)

**Further Information**     
- Akamai: [Attackers Using New MS SQL Reflection Techniques](<https://blogs.akamai.com/2015/02/plxsert-warns-of-ms-sql-reflection-attacks.html>)    
- Microsoft: [SQL Server-Browserdienst](<https://docs.microsoft.com/de-de/sql/tools/configuration-manager/sql-server-browser-service?redirectedfrom=MSDN&view=sql-server-ver15>)


## Openly accessible Portmapper services
---
The Portmapper (portmap, rpcbind) is required for mapping RPC requests (remote procedure calls) to a network service. It is needed e. g. for mounting network shares using the Network File System (NFS).

### Problem
Openly accessible Portmapper services can be abused for DDoS reflection attacks against third parties.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a Portmapper service is openly accessible from the Internet, the tool 'rpcinfo' can be used:
```
$ rpcinfo -T udp -p 192.168.45.67
```

An openly accessible Portmapper service will return a response like this:
```console
program vers proto   port  service
100000    4   tcp    111  portmapper
100000    3   tcp    111  portmapper
100000    2   tcp    111  portmapper
100000    4   udp    111  portmapper
100000    3   udp    111  portmapper
100000    2   udp    111  portmapper
100024    1   udp  48035  status
100024    1   tcp  52605  status
```

Otherwise, rpcinfo will run into a timeout:
```console
rpcinfo: can't contact portmapper: RPC: Remote system error - Connection timed out
```

### Solution
If the Portmapper service (portmap, rpcbind) is not required, disable or deinstall it. Otherwise, restrict access to trusted clients, for example by blocking incoming connections to port 111/tcp and 111/udp on the firewall.

On Debian/Ubuntu based Linux systems, the portmapper service can be removed using the following command:
```
# apt-get remove rpcbind
```

**Further Information**    
- Wikipedia: [Portmap](<https://en.wikipedia.org/wiki/Portmap>)   
- Securing Debian Manual: [Securing RPC services](<https://www.debian.org/doc/manuals/securing-debian-howto/ch-sec-services.en.html#s-rpc>)   



## Openly accessible Redis servers
---
Redis is an open-source in-memory database server with a simple key-value data structure often used with dynamic web applications.

### Problem
If a Redis server is openly accessible from the Internet and no SASL authentification has been configured, anyone who can connect to the server has unrestricted access to the data stored with it. This allows attackers to modify or delete any data or potentially steal sensitive information like login credentials for web applications or customer data from online shops.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a Redis server is openly accessible from the Internet, you can use 'netcat' as follows:
```
$ (printf "info\r\n"; sleep 1) | netcat 192.168.45.67 6379
```

An openly accessible Redis server will return information like this:
```console
# Server
redis_version:2.8.17
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:4c1d5710660b9479
redis_mode:standalone
os:Linux 3.16.0-4-amd64 x86_64
arch_bits:64
multiplexing_api:epoll
gcc_version:4.9.2
process_id:12738
run_id:178e1ca5be355158cabdb51aa848b4cdd68a5d54
tcp_port:6379
uptime_in_seconds:8785215
uptime_in_days:101
hz:10
lru_clock:7298172
config_file:/etc/redis/redis.conf
```

Otherwise, netcat will return an error message:
```console
netcat: connect to 192.168.45.67 port 6379 (tcp) failed: Connection refused
```
or
```
netcat: connect to 192.168.45.67 port 6379 (tcp) failed: Connection timed out
```

### Solution   
* Do not expose your Redis server to the Internet!   
* Restrict access to the Redis server to trusted systems (e. g., the web application server) in the server's configuration and/or by blocking incoming connections from the Internet to port 6379/tcp on the firewall.   
* Check the [security best practices](<https://redis.io/topics/security>) provided by the Redis developers.   
* Keep your Redis installation up-to-date. Install available security updates asap.   

**Further Information**    
- RiskBasedSecurity: [Redis: Over 6,000 Installations Compromised](<https://www.riskbasedsecurity.com/2016/07/06/redis-over-6000-installations-compromised/>)   
- Duo Labs: [Over 18,000 Redis Instances Targeted by Fake Ransomware](<https://duo.com/decipher/over-18000-redis-instances-targeted-by-fake-ransomware>)


## Openly accessible SNMP servers
---
The Simple Network Management Protocol (SNMP) is a networking protocol for device management and monitoring.

### Problem
Openly accessible SNMP servers using the default 'public' community can be abused for DDoS reflection attacks against third parties. Furthermore, they allow potential attackers to gather information on the server or network for preparation of further attacks.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if an SNMP server is openly accessible from the Internet, you can use the 'snmpget' tool:
```
$ snmpget -c public -v 2c 192.168.45.67 1.3.6.1.2.1.1.1.0
```

An openly accessible SNMP server will return a 'System Description' string like this:
```console
iso.3.6.1.2.1.1.1.0 =
STRING: "Linux easy.box 2.6.32.32 #1 Fri Jun 12 11:16:35 CST 2015 mips"
```

Otherwise, snmpget will run into a timeout:
```console
Timeout: No Response from 192.168.45.67.
```

### Solution
Configure a 'private' community with mandatory authentication instead of using the default 'public' community. Restrict access to the SNMP server to trusted clients in the server's configuration and/or by blocking incoming connections to port 161/udp on the firewall.

**Further Information**   
- Wikipedia: [Simple Network Management Protocol](<https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol>)


## Openly accessible Sphinx servers
---
Sphinx is an open-source search server commonly used as a backend for web applications.

### Problem
In the default configuration, the Sphinx server listens on ports 9306/tcp and 9312/tcp on all network interfaces. Sphinx does not provide any authentication mechanisms. If a Sphinx server is openly accessible from the Internet, an attacker can take advantage of this to read, modify or delete any data stored in the Sphinx database.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a Sphinx server is openly accessible from the Internet, you can use 'netcat' as follows:
```
$ netcat 192.168.45.67 9306
```

If a connection to the Sphinx server was successfully established, it will return the version information:
```console
2.2.11-id64-release (95ae9a)
```

### Solution   
* Do not expose your Sphinx server to the Internet!   
* Restrict access to the Sphinx server to trusted systems, e. g. by blocking incoming connections from the Internet to ports 9306/tcp and 9312/tcp on the firewall.    
* If both the Web and Sphinx servers are running on the same system, the Sphinx server should only listen on the localhost interface. To achieve this, change the following lines in the configuration file    
```
listen       = 9312
listen       = 9306:mysql41
```
to:
```
listen       = localhost:9312
listen       = localhost:9306:mysql41
```
* Keep your Sphinx installation up-to-date. Install available security updates asap.   

## Openly accessible SSDP servers
---
The Simple Service Discovery Protocol (SSDP) is a network protocol for advertisement and discovery of network services and presence information. SSDP is the basis of the discovery protocol of Universal Plug and Play (UPnP). SSDP uses port 1900/udp.

### Problem
Openly accessible SSDP servers can be abused for DDoS reflection attacks against third parties.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if an SSDP server is openly accessible from the Internet, run 'tcpdump' in a first terminal:
```
# tcpdump -n -A host 192.168.45.67
```

Then, in a second terminal, use the Bash shell to send an SSDP request:
```console
$ perl -e 'print "M-SEARCH * HTTP/1.1\r\nHost:239.255.255.250:1900\r\n
ST:upnp:rootdevice\r\nMan:\"ssdp:discover\"\r\nMX:3\r\n\r\n"'
> /dev/udp/192.168.45.67/1900
```

If the SSDP server is openly accessible from the Internet, you will see a response like this in the first terminal:
```console
HTTP/1.1 200 OK
Location: http://192.168.45.67:32469/DeviceDescription.xml
Cache-Control: max-age=1800
Server: UPnP/1.0 DLNADOC/1.50 Platinum/1.0.4.11
EXT: 
USN: uuid:abcdb3c3-eada-b308-2e21-6edbab9cf4ed::upnp:rootdevice
ST: upnp:rootdevice
Date: Fri, 01 Apr 2016 11:15:08 GMT
```

### Solution
If the SSDP server is not required, disable or deinstall it. Otherwise, restrict access to trusted clients, for example by blocking incoming connections to port 1900/udp on the firewall.

**Further Information**   
- Wikipedia: [Simple Service Discovery Protocol](<https://en.wikipedia.org/wiki/Simple_Service_Discovery_Protocol>)    
- Sucuri: [Quick Analysis of a DDoS Attack Using SSDP](<https://blog.sucuri.net/2014/09/quick-analysis-of-a-ddos-attack-using-ssdp.html>)   


## Openly accessible Telnet servers
---
Telnet is an outdated network protocol for text-oriented command-line access to remote hosts.

### Problem
With Telnet, all communication including username and password is transmitted unencrypted in clear text and is therefore susceptible to eavesdropping.

Many IoT devices (routers, network cameras, etc.) are running Telnet servers by default. If the devices are openly accessible from the Internet and standard login credentials have not been changed, an attacker can easily gain full control of the devices. Malware like Mirai automatically exploits insecure Telnet servers openly accessible from the Internet using to compromise devices and connect them to a botnet.

### Solution
If the Telnet server is not required, disable or deinstall it. Otherwise, restrict access to trusted local networks. In particular for IoT devices: Check if default login credentials have been changed.

CERT-Bund recommends using (Open)SSH with key-based authentication for secure access to remote hosts.

**Further Information**   
- Wikipedia: [Telnet](<https://en.wikipedia.org/wiki/Telnet>)


## Openly accessible Ubiquiti Device Discovery services
---
Ubiquiti network devices come with a 'Device Discovery' service which is enabled by default and listening on port 10001/udp.

### Problem
Ubiquiti Device Discovery services openly accessible from the Internet disclose potentially sensitive information about the network device running the service and can be abused for performing DDoS reflection/amplification attacks against third parties.

### Verification
In this section, we show how to check a host for an openly accessible service. All tests are performed using tools commonly included with standard Linux/Unix distributions. To verify the service is openly accessible from the Internet, the test should not be run on the host itself or the local network but instead from a different node on the Internet, for example a host on a cable/DSL line. In all examples, replace 192.168.45.67 with the IP address of the host to check.

To check if a Ubiquiti Device Discovery service is openly accessible from the Internet, you can use 'netcat' as follows:
```
$ echo -ne "\x01\x00\x00\x00" | netcat -u 192.168.45.67 10001 | hexdump -C
```

An openly accessible Device Discovery service will return information like this:
```console
00000000  01 00 00 8e 02 00 0a XX  XX XX XX XX XX 50 95 fb  |.......MACADR...|
00000010  67 02 00 0a 44 d9 e7 XX  XX XX c0 a8 37 01 01 00  |g...D.......7...|
00000020  06 44 d9 e7 XX XX XX 0a  00 04 00 28 f5 ca 0b 00  |.D.........(....|
00000030  0f XX XX XX XX XX XX XX  XX XX XX XX XX XX XX XX  |...DEVICE_NAME..|
00000040  0c 00 03 4c 4d 35 0d 00  XX XX XX XX XX XX XX XX  |...LM5....ESSID.|
00000050  0e 00 01 02 03 00 20 58  XX XX XX XX XX XX XX XX  |.......FIRMWARE.|
```

### Solution
Disable the Device Discovery service if not required.
Otherwise, restrict access to trusted clients, for example by blocking incoming connections to port 10001/udp on the firewall.

**Further Information**    
- Rapid7: [Understanding Ubiquiti Discovery Service Exposures](<https://blog.rapid7.com/2019/02/01/ubiquiti-discovery-service-exposures/>)   
- Ubiquiti: [EdgeRouter - Ubiquiti Device Discovery](<https://help.ubnt.com/hc/en-us/articles/204976244-EdgeRouter-UBNT-Device-Discovery>)

