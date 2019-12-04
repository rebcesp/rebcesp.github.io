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

<img class="differenteSize65" src="/assets/img/xmlLineas.png" alt="LineasXml" style="margin:auto; display:block;">

Necesito trabajar con un nodo de etiquetas , específicamente la etiqueta `</draw>` donde aquí dentro integraremos las otras etiquetas de `tipo complejo` , esto va a ser necesario para que cuando el formulario el cual estoy trabajando se visualize en un PDF , cabe decir que son formularios con accesibilidad para personas con discapacidades , volviendo al tema cuando se visualize el formulario al apretar la tecla `TAB` el **setFocus** que es el método que esta en el Backend del formulario programado en Javascript , debería enfocar los campos secuencialmente y gracias a la herramienta [NVDA](https://nvda.es/) qué es un lector de pantalla la persona con la discapacidad de visión debería escuchar los nombres de los campos. 

## Primeramente necesitamos usar lo que vamos a necesitar:

```xml
 <draw h="5mm" name="sectionTitle" w="189.999mm" y="0mm" x="0mm"> <!--Nodo Padre -->
                  <font typeface="BdE Neue Helvetica 55 Roman" baselineShift="0pt" size="9pt" weight="bold"/>
                  <margin bottomInset="0mm" leftInset="0mm" rightInset="0mm" topInset="0mm"/>
                  <para marginLeft="0pt" marginRight="0pt" spaceAbove="0pt" spaceBelow="0pt" textIndent="0pt"/>
                  <ui>
                     <textEdit/>
                  </ui>
                  <value>
                     <exData contentType="text/html">
                        <body xmlns="http://www.w3.org/1999/xhtml" xmlns:xfa="http://www.xfa.org/schema/xfa-data/1.0/"><p style="letter-spacing:0in">Encabezado1<span style="font-size:8pt;color:#ff0000">_</span></p></body>
                     </exData>
                  </value>
                  <!--Etiquetas para visualizar en el PDF -->
                  <extras name="bookmark">
                     <text name="name">tituloEncabezado</text>
                     <text name="color">0,0,0</text>
                     <text name="style">normal</text>
                     <text name="action">runScript</text>
                     <text name="script">xfa.host.setFocus(xfa.form.FrmDefault.FormContent.BDESection);</text>
                  </extras>
               </draw>

```
