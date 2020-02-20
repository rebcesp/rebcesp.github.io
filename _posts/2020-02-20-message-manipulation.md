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

<img class="differentSize40" src="/assets/img/Foto1.PNG" alt="Foto1" style="margin:auto; display:block;">

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

# Breakpoint

Luego que estamos en la línea de la función `Promise.callSynchronously(function ()`
