---
layout: post
title: Notas Bug Bounty
category: bugbounty
permalink: "blog/bug-bounty"
published: yes
---

<img class="differentSize30" src="/assets/img/ants.png" alt="antsBUGBOUNTY" style="margin:auto; display:block;">

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
* ### **0x01->** Extracción del nombre de dominio

Vamos a probar ingresando a la página donde se consultan preguntas de programación: _[https://stackoverflow.com]_ (https://stackoverflow/)  , hablando con argumentos **stakoverflow** seria el nombre de dominio que estamos intentando visitar y debe cumplir con las reglas específicas definidas por los RFC, por ejemplo un nombre de dominio solo puede contener caracteres alfanúmericos y guiones bajos.
* 
---













