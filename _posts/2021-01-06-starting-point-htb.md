---
layout: post
title: Starting Point HTB
category: hacking,programming,htb
permalink: "blog/starting-point"
published: yes
---

## Introducción

Bueno todos sabemos que HackTheBox es una plataforma ahora mismo muy pulida para aprender a hackear máquinas de diferentes sistemas operativos, tanto como `Linux`,`Windows` o `FreeBSD`, en este post se explicará de una forma mas interactiva para empezar a hackear una máquina y algunos pasos fundamentales que hay que seguir, por supuesto el tutorial esta en la misma plataforma de `HackTheBox` pero hay muchos conceptos que dan por hecho que el usuario conoce, en mi caso explicaré cada uno de ellos para profundizar y tener un aprendizaje más óptimo de lo que estamos haciendo.


Antes de nada es muy necesario contar con la conexión VPN para poder empezar a analizar las máquinas normalmente el archivo se descarga desde la misma web y tiene un aspecto mas o menos asi: `rebcesp-startingpoint.ovpn`, nos conectamos mediante la terminal con los comandos:

```lua
openvpn rebcesp-startingpoint.ovpn
...
2021-01-07 23:22:18 Incoming Data Channel: Cipher 'AES-256-GCM' initialized with 256 bit key
2021-01-07 23:22:18 Preserving previous TUN/TAP instance: tun0
2021-01-07 23:22:18 Initialization Sequence Completed
```


## Enumeracion

HTB nos proporciona una IP para empezar a hackear que en esta ocasión es `10.10.10.27`, lo primero que tenemos que hacer es el reconocimiento de esta máquina es decir, listar sus puertos abiertos junto a los servicios que estan corriendo en cada uno de ellos, para eso se utiliza una herramienta que es `navaja suiza` llamada `Nmap` lo cual es `escaner de puertos MUY potente`. Vamos a usar los parámetros de nmap para poder enumerar los puertos que estan abiertos y servicios que estan corriendo junto a scriptss que contiene la misma herramienta para automatizar el proceso.

```lua
─[rebcesp@parrot]─[~]
└──╼ $nmap -sC -sV 10.10.10.27
Starting Nmap 7.80 ( https://nmap.org ) at 2021-01-07 23:28 CET
Nmap scan report for 10.10.10.27
Host is up (0.044s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2019 Standard 17763 microsoft-ds
1433/tcp open  ms-sql-s      Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: ARCHETYPE
|   NetBIOS_Domain_Name: ARCHETYPE
|   NetBIOS_Computer_Name: ARCHETYPE
|   DNS_Domain_Name: Archetype
|   DNS_Computer_Name: Archetype
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2021-01-01T23:54:25
|_Not valid after:  2051-01-01T23:54:25
|_ssl-date: 2021-01-07T23:45:16+00:00; +1h16m45s from scanner time.
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: ARCHETYPE
|   NetBIOS_Domain_Name: ARCHETYPE
|   NetBIOS_Computer_Name: ARCHETYPE
|   DNS_Domain_Name: Archetype
|   DNS_Computer_Name: Archetype
|   Product_Version: 10.0.17763
|_  System_Time: 2021-01-07T23:45:07+00:00
| ssl-cert: Subject: commonName=Archetype
| Not valid before: 2021-01-02T19:54:24
|_Not valid after:  2021-07-04T19:54:24
|_ssl-date: 2021-01-07T23:45:16+00:00; +1h16m45s from scanner time.
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h25m20s, deviation: 3h01m27s, median: 1h16m45s
| ms-sql-info: 
|   10.10.10.27:1433: 
|     Version: 
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| smb-os-discovery: 
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: Archetype
|   NetBIOS computer name: ARCHETYPE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-01-07T15:45:11-08:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-01-07T23:45:08
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.20 seconds
```
En este punto podemos ver que tenemos dos puertos relevantes que nos interesan son el `445` y el `1433` los cuales estan abiertos y sus servicios que corren son `SMB` y `Microsoft SQL Server 2017`

