---
layout: post
title: Esteroides en un servicio onion sobre un VPS #1
category: onion,tor,anonimato
permalink: "blog/roidsonion"
published: yes
---

Voy a escribir una serie de posts sobre el securizado y anonimato(`con esteroides`) a la hora de levantar un servicio onion y aplicar reglas fundamentales como también conocer los conceptos de lo que vamos haciendo, espero que lo disfruten.

# ¿Qué es un servicio Onion?

Primero hablaremos sobre los conceptos de que es un servicio onion oculto y como funciona. Un servicio oculto es un servicio que anuncia su existencia en la red `TOR` antes de que los clientes puedan comunicarse con él.

Para iniciar un servicio oculto, se registra un servicio generando un `descriptor` del servicio. Este descriptor contiene la clave pública del servicio y los nodos desde donde hay un circuito de conexión
