---
layout: post
title: Tratando con el DOM de un XML Avanzado
category: XML
permalink: "blog/xml-Avanzado"
published: yes
---

# _0x01 - Introdución_

Una de las cosas interesantes que necesitaba hacer en este gran XML como yo lo llamo `XML - Avanzado` por el hecho que se trata de un XML compuesto que contiene más XML valga la redudancia y scripts en `Javascript` suele ser complejo al parsearlo y trabajar con los Nodos que necesito.

Entonces nos pusimos a diseccionarlo y trabajar con el XML que realmente necesitabamos , el cual era el `Xml-Template`


# _0x02 - Diseccionando_

Una de las cosas mas difícil en estas ocasiones es diseccionarlo y trabajar justamente con lo que necesito , con las etiquetas de `Tipo Complejo` ya que este XML tiene 2887 líneas !!!

<img class="differenteSize65" src="/assets/img/xmlEtiquetas.png" alt="LineasXml" style="margin:auto; display:block;">

Necesito trabajar con un nodo de etiquetas , específicamente la etiqueta `</draw>` donde aquí dentro integraremos las otras etiquetas de `tipo complejo` , esto va a ser necesario para que cuando el formulario el cual estoy trabajando se visualize en un PDF , cabe decir que son formularios con accesibilidad para personas con discapacidades , volviendo al tema cuando se visualize el formulario al apretar la tecla `TAB` el **setFocus** que es el método que esta en el Backend del formulario programado en Javascript , debería enfocar los campos secuencialmente y gracias a la herramienta [NVDA](https://nvda.es/) qué es un lector de pantalla la persona con la discapacidad de visión debería escuchar los nombres de los campos. 




