---
layout: post
title: Manipulación de mensajes en WhatsApp / WhatsApp Message Manipulation
category: exploit,bug,javascript
permalink: "blog/wp-message-manipulation"
published: yes
---

# Explotando el bug

Este bug consiste en manipular un mensaje de la víctima haciendo creer a él/ella que ha escrito un texto el cual no lo hizo. Para reproducir este bug se necesita estar primeramente desde `WhatsAppWeb` https://web.whatsapp.com/.

Comenzamos entrando a `WhatsAppWeb`, seleccionamos algun chat aleotario donde tengamos una conversación y apretamos `F12` para abrir la consola de desarrollador, esta parte vamos a Sources o Fuentes depende el idioma de tu consola de desarrollador.

<img class="differentSize70" src="/assets/img/Foto1.PNG" alt="Foto1" style="margin:auto; display:block;">

Una vez estamos ahi vamos a buscar la función `Promise.callSynchronously(function ()`

## ¿Qué hace esta función?

Esta función es `asincróna` lo que hace es escribir un código basado en promesas como si fuere sincrónico, pero sin bloquear el hilo principal. Es decir hacen a tu código menos "inteligente" y más legible. 

Las funciones asincrónicasfunciona de la siguiente manera:

```js
async function myFirstAsyncFunction() {
  try {
    const fulfilledValue = await promise;
  }
  catch (rejectedValue) {
    // …
  }
}
```
Dejare el link de referencia al final del post, para que lo puedas leer mas detallademente como funciona, sigamos.

##  Breakpoint

Luego que estamos en la línea de la función `Promise.callSynchronously(function ()` clickeamos en la `línea` para asignar un punto de interrupción, antes de seguir cabe decir que para que puedas ver toda el código formateado (PrettyPrint), selecciona en la barra debajo el ícono de las llaves `{}`

<img class="differentSize70" src="/assets/img/Foto2.PNG" alt="Foto1" style="margin:auto; display:block;">

##  Respondiendo el mensaje

Bueno en este punto selecciona un mensaje de la víctima y dale `Responder`, primeramente tienes que escribir una respuesta ya sabiendo el mensaje que manipularas en la víctima.

<img class="differentSize70" src="/assets/img/responder.PNG" alt="Foto1" style="margin:auto; display:block;">


<img class="differentSize70" src="/assets/img/muchasgraciasbro.PNG" alt="Foto1" style="margin:auto; display:block;">


Cuando tengas esto, intenta enviar, veras que no se enviará ya que el punto de interrupción esta antes que se envíe en este punto es donde cambiaras el mensaje por el que tu quieras.

Dírigite a la `Consola` de la consola de desarrollador valga la redundancia y escribe esta sentencia con tu mensaje a manipular:

<img class="differentSize70" src="/assets/img/foto3.PNG" alt="Foto1" style="margin:auto; display:block;">


## Manipulando el mensaje

Vuelve a la sección de `Depurador` o `Debug` de la consola de desarrollador y dale a `Play` para que el mensaje siga su flujo y salgamos del `Breakpoint`.

Una vez hecho esto, podrás ver que el mensaje que yo he escrito es como si él/ella lo hubiera enviado.

<img class="differentSize70" src="/assets/img/foto4.PNG" alt="Foto1" style="margin:auto; display:block;">

Es decir hemos falsificado el mensaje el cual hacemos mención, si miras tu télefono móvil tambien podras comprobarlo desde allí.

<img class="differentSize70" src="/assets/img/movil.jpeg" alt="Foto1" style="margin:auto; display:block;">

Eso sería todo, muchas gracias.

### Referencias
https://research.checkpoint.com/2019/black-hat-2019-whatsapp-protocol-decryption-for-chat-manipulation-and-more/
https://developers.google.com/web/fundamentals/primers/async-functions




