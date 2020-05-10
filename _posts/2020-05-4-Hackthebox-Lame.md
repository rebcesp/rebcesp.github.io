---
layout: post
title: Hackthebox Lame - OSCP
category: htb,oscp,hackthebox
permalink: "blog/lame"
published: yes
---

Este post de la máquina Lame sera una intrusión utilizando metasploit y `sin metasploit`


# Enumeración

Primeramente  tenemos que  comprobar que la máquina este activa y para esto lanzaremos un ping a a  la máquina `ping -c 10.10.10.3`.

```console
root@rebcesp:/home/rebcesp/htb# ping -c 1 10.10.10.3
PING 10.10.10.3 (10.10.10.3) 56(84) bytes of data.
64 bytes from 10.10.10.3: icmp_seq=1 ttl=63 time=38.7 ms

--- 10.10.10.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 38.732/38.732/38.732/0.000 ms
```
Podemos ver que el ttl=63 pertenece a una máquina linux, podemos guiarnos a través de esta  tabla:

<img class="differentSize70" src="/assets/img/ttlTabla.png" alt="Foto1" style="margin:auto; display:block;">

Vamos a proceder a enumerar los puertos `TCP`, utilizando la siguiente sintaxis de nmap:
`nmap -p- -sS --min-rate 5000 --open -T5 -vvv -Pn -n 10.10.10.3`

```console
PORT     STATE SERVICE      REASON
21/tcp   open  ftp          syn-ack ttl 63
22/tcp   open  ssh          syn-ack ttl 63
139/tcp  open  netbios-ssn  syn-ack ttl 63
445/tcp  open  microsoft-ds syn-ack ttl 63
3632/tcp open  distccd      syn-ack ttl 63
```

Ya que tenemos los puertos, vamos a buscar las versiones de cada uno de ellos y analizarlos para ver si alguno es explotable.

```{r, engine='sh', count_lines}
wc -l en_US.Twitter.txt 
```


`nmap -sC -sV -p21,22,139,445,3632`

```console
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.13
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -3d00h57m03s, deviation: 2h49m45s, median: -3d02h57m06s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2020-05-07T04:04:28-04:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)

```
