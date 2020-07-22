---
layout: post
title: Esteroides en un servicio [.onion] sobre un VPS #1
category: onion,tor,anonimato
permalink: "blog/roidsonion"
published: yes
---



<img class="differentSize30" src="/assets/img/torlogo.png" alt="Foto1" style="margin:auto; display:block;">

---
DISCLAIMER
---
El post esta hecho por mera curiosidad y de forma educacional, no incentivo a nadie el `CIBERCRIMEN` ni aconsejo hacerlo.

<details>
  <summary>Resumen</summary>

  
  Voy a escribir una serie de posts sobre el securizado y anonimato(`con esteroides`) a la hora de levantar un servicio onion y aplicar reglas fundamentales como también conocer   los conceptos de lo que vamos haciendo, espero que lo disfruten.
  
</details>

# ¿Qué es un servicio [.onion]?

Los servicios `onion` , anteriormente conocidos como servicios ocultos son servicios como cualquier sitio web con la diferencia que solo se puede acceder a través de la red `TOR`.

Los servicios onion ofrecen algunas ventajas:

* La ubicación y la dirección IP de los servicios onion están ocultos, lo que dificulta a los adversarios censurarlos o identificar a sus operadores.
* Todo el tráfico entre los usuarios de Tor y los servicios onion está cifrado de extremo a extremo por lo que no hace falta preocuparse de `Conectarse a través de HTTPs`
* La dirección de servicio se genera automáticamente, de forma que los operadores no necesitan comprar/registrar un nombre de dominio; la URL `.onion` también ayuda a TOR a asegurarse que estás conectando a la ubicación correcta y que la conexión no esta siendo alterada.

# ¿Como se accede a un servicio [.onion]?

Para poder acceder se necesitará saber la dirección del servicio onion para conectarse. Una dirección onion es una cadena de 16 caracteres formado en su mayoría por letras y números aleatorios, seguidos de la extensión `.onion`

Si queremos visualizar nuestro servicio .onion podemos hacerlo desde el navegador oficial que es el `Tor Browser` o desde el navegador de `Brave` con la opcion `New private whit Tor`. Ahora también lo puedes hacer descargando la aplicación de `Tor` para teléfonos móviles.

# Como funciona el servicio onion en la red TOR

Primero hablaremos sobre los conceptos de que es un servicio onion oculto y como funciona. Un servicio oculto es un servicio que anuncia su existencia en la red `TOR` antes de que los clientes puedan comunicarse con él.

Para iniciar un servicio oculto, se registra un servicio generando un `descriptor` del servicio. Este descriptor contiene la clave pública del servicio y los nodos desde donde hay un circuito de conexión (Introduction Point). Estos se anuncian en base de datos distribuidas que almacenan tablas de descriptores

<img class="differentSize50" src="https://www.error509.com/wp-content/uploads/2017/12/122817_0743_TORPrimeros1.png" alt="Foto1" style="margin:auto; display:block;">

Una vez que esta publicado el servicio oculto `(Hidden service)`, el cliente solicita el descriptor a la base de datos para conocer la clave pública del servicio y el punto de introducción con el que comunicar para acordar una conexión. Se envía un mensaje al punto de introducción, proponiendo al servicio un tercer nodo que actúe de relay `(Rendevous Point)` entre ambos. Además, se envía una cookie que actuará de clave temporal en la comunicación.


<img class="differentSize50" src="https://www.error509.com/wp-content/uploads/2017/12/122817_0743_TORPrimeros2.png" alt="Foto1" style="margin:auto; display:block;">

Ahora en este punto, la conexión entre ambos se realiza utilizando un punto de encuentro intermedio mediante un circuito en la red `Tor`, por tanto, la comunicación entre el servicio y el cliente es totalmente anónima. El punto de encuentro se encarga de repartir y reenviar los mensajes cifrados que han intercambiar cliente y servicio para mantener una comunicación.

De esta forma la conexión entre las partes está establecida y ninguna de las dos partes conocen la ubicación de la otra.

<img class="differentSize50" src="https://www.error509.com/wp-content/uploads/2017/12/122817_0743_TORPrimeros3.png" alt="Foto1" style="margin:auto; display:block;">

Hasta aquí dejo la primera parte, la segunda ya se pondra manos a la práctica levantando un servicio `[.onion]` mediante nuestro VPS que compraremos con BTC utlizando una VPN.
