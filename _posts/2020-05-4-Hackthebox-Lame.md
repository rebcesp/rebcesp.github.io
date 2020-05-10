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
