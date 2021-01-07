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

```bash
openvpn rebcesp-startingpoint.ovpn
...
2021-01-07 23:22:18 Incoming Data Channel: Cipher 'AES-256-GCM' initialized with 256 bit key
2021-01-07 23:22:18 Preserving previous TUN/TAP instance: tun0
2021-01-07 23:22:18 Initialization Sequence Completed
```


## Enumeracion

HTB nos proporciona una IP para empezar a hackear que en esta ocasión es `10.10.10.27`
