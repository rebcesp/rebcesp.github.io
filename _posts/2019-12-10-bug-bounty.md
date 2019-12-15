---
layout: post
title: Notas Bug Bounty
category: bugbounty
permalink: "blog/bug-bounty"
published: yes
---

<img class="differentSize40" src="/assets/img/fb.png" alt="antsBUGBOUNTY" style="margin:auto; display:block;">

# TL;DR

Bug Bounty viene desde `Hunter & Ready` fue el primer programa conocido de recompensas de erores en 1983 para su sistema operativo `Versátil Real-Time Exclusive`. Se trataba que cualquiera que encontrar y reportara un error recibiría un `Volkswagen Bettle` (_también conocido como Bug_). Un poco más de una década más tarde, en 1995, Jarret Ridlinghafer , ingenerio soporte técnico de `Netscape Communications Corporations` acuño la frase "BUG BOUNTY".


En la actualidad estos programas permiten a los desarrolladores descubrir y resolver errores antes de que el público en general los conozcan, evitando incidentes de abuso generalizado. los programas de errores han sido implementados por una gran cantidad de organizaciones, incluyendo `Mozilla`, `Facebook`, `Yahoo`, `Google`, `Reddit`, `Square`, `Microsoft`, a estas personas se las denomina como _**WHITE HATS**_.

# Entramos en materia 



## HTTP
### Se basa en:

> Solicitudes y Respuestas<br>
> Distintas acciones<br>
> Protocolo sin estado

Nuestro navegador se basa en `Internet`, no es más que una red de computadoras que se envían mensajes entre sí. Estos mensajes se les denomina _paquetes_. Los paquetes incluyen los datos que se está enviando e información de dónde provienen esos datos y hacian dónde van.

Las **Solicitudes(Requests)** de inicio de la computadora generalmente se conocen como el `cliente`, esto es independiente de si la solicitud la inicia un navegador, una linea de comando, etc.
```
+----------+ +----------+
|Aplicación| |Aplicación|
+----------+ +----------+
|             |
+-----+       +-----+
| TCP |  ···  | TCP |
+-----+       +-----+
|             |
+-----+       +-----+
|  IP |  ···  |  IP |
+-----+       +-----+
|             |
+-----------------------+
|     Enlace físico     |
+-----------------------+
```


Los _servidores_ de los sitios web y las aplicaciones son los que **RECIBEN** las solicitudes. Unas de las pautas a tomar  sobre cómo las computadoras se comunican a traves de internet es por ejemplo la `Solicitud de comentarios` (RFC), esto define sobre cómo deben comportarse las computadoras.

El `Protocolo de transferencia de hipertexto (HTTP)`, define cómo su navegador de Internet se comunica con un servidor remoto mediante el `Protocolo de Internet (IP)`. En este escenario, tanto el cliente como el servidor deben acordar implementar los mismos estándares para que puedan entender los paquetes que cada uno envía y recibe.

## Sitio Web 

Empezaremos viendo un poco técnicamente de lo que se trata cuando accedemos a un sitio web y centrandonos en el protocolo `HTTP`

---
* ## **0x01»** Extracción del nombre de dominio

Vamos a probar ingresando a la página donde se consultan preguntas de programación: [StackOverflow](https://stackoverflow.com)  , hablando con argumentos **stakoverflow** seria el nombre de dominio que estamos intentando visitar y debe cumplir con las reglas específicas definidas por los RFC, por ejemplo un nombre de dominio solo puede contener caracteres alfanúmericos y guiones bajos.
El dominio sirve como una forma de encontrar la dirección del servidor.

* ## **0x02 »** Resolver una dirección IP

Al insertar un dominio en el navegador "ese dominio resolvera el dominio por la IP" y cada dominio en Internet debe resolver una dirección IP para funcionar.

Partimos de que existen dos tipos de direcciones IP: `Protcolo de Internet Version 4(Ipv4)` y `Protocolo de Internet Version 6 (Ipv6)`. 

Las direcciones Ipv4 están estructuradas de 4 numeros conectados por puntos, y cada número tiene un rango de `0 a 255`.
<img class="differentSize60" src="https://i.ytimg.com/vi/izJEQ1b6WM0/maxresdefault.jpg" alt="ipv4" style="margin:auto; display:block;">

Las direcciones Ipv6 es la versión mas nueva del Protocolo de Internet, tiene 128 bits, 16 octetos, hay muchas para el agotamiento de las `direcciones IPv4`.
<img class="differentSize60" src="https://i.ytimg.com/vi/FsieCcOSWn0/maxresdefault.jpg" alt="ipv6" style="margin:auto; display:block;">

Si queremos buscar una dirección IP de un sitio web podemos usar el comando: `dig A www.example.com`  y pasamos el sitio web que queremos bucar.

* ## **0x03** Establecer una conexión TCP

La computadora intentara estabelcer una conexion de Protocolo de Control de Transmision  con la direccion IP en el puerto 80 porque visito un sitio usando `http://` realmente este protocolo ayuda a que las computadoras se comuniquen entre si. TCP proporciona comunicacion bidireccional para que los destinatarios del mensaje puedan verificar la informacion que reciben y no se pierde nada en la transmision.

El servidor al que esta enviando una solicitud podria estar ejecutando multiples servicios. Hay que pensar que un servicio es como un programa de computadora,  por lo tanto se necesita usar `puertos` para identificar procesos especificos para recibir solicitudes.

Por ejemplo el _puerto 80_ es el puerto estandar para enviar y recibir solicitudes HTTP SIN CIFRAR. 
Otro puerto comun es el _puerto 443_ que se utiliza para solicitudes HTTPS CIFRADAS.

Puedes probarlo usando netcat y estableciendo una conexion TCP a un sitio web en el puerto 80 con el comando `nc <DIRECCION IP>80`

* ## **0x04** Enviando una solicitud HTTP











---














