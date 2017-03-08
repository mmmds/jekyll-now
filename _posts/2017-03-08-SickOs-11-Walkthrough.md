---
published: false
---
[SickOs: 1.1](https://www.vulnhub.com/entry/sickos-11,132/) is a vulnerable machine with an objective to get root and read file /root/a0216ea4d51874464078c618298b1367.txt


To get VM's IP I used nmap
	
    # nmap -sn 192.168.175.*

	Starting Nmap 7.01 ( https://nmap.org ) at 2017-03-08 22:47 CET
	Nmap scan report for 192.168.175.131
	Host is up (0.00017s latency).
	MAC Address: 00:50:56:26:E0:9E (VMware)
	Nmap scan report for 192.168.175.254
	Host is up (-0.10s latency).
	MAC Address: 00:50:56:F2:26:25 (VMware)
	Nmap scan report for 192.168.175.1
	Host is up.
	Nmap done: 256 IP addresses (3 hosts up) scanned in 4.59 seconds
	
#   
    
    # nmap 192.168.175.131
	
	Starting Nmap 7.01 ( https://nmap.org ) at 2017-03-08 22:47 CET
	Nmap scan report for 192.168.175.131
	Host is up (0.00019s latency).
	Not shown: 997 filtered ports
	PORT     STATE  SERVICE
	22/tcp   open   ssh
	3128	/tcp open   squid-http
	8080/tcp closed http-proxy
	MAC Address: 00:50:56:26:E0:9E (VMware)
	
	Nmap done: 1 IP address (1 host up) scanned in 11.06 seconds
