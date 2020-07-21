---
layout: post
title: Esteroides en un servicio [.onion] sobre un VPS #1
category: onion,tor,anonimato
permalink: "blog/roidsonion"
published: yes
---

<img class="differentSize50" src="/assets/img/torlogo.png" alt="Foto1" style="margin:auto; display:block;">

Voy a escribir una serie de posts sobre el securizado y anonimato(`con esteroides`) a la hora de levantar un servicio onion y aplicar reglas fundamentales como también conocer los conceptos de lo que vamos haciendo, espero que lo disfruten.

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

Para iniciar un servicio oculto, se registra un servicio generando un `descriptor` del servicio. Este descriptor contiene la clave pública del servicio y los nodos desde donde hay un circuito de conexión
