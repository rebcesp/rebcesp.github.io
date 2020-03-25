---
layout: post
title: Paradigmas de programación
category: code,java,reuse,patron,paradigmas
permalink: "blog/code"
published: yes
---

# Aprende a escribir código de calidad

Existen muchos patrones de diseño al desarrollar un software/aplicación, fundamentos a tomar en cuenta al empezar, metodologías de desarrollo. El objetivo de este post es construir mejores aplicaciones teniendo en cuenta una: Claridad, Agilidad, Precisión, Mantenibilidad en tu `código`, entre todo eso más flexible.

# POO / Programación Orientada a Objetos

Algunos de los temas que explicaré y aplica a la Programación orientada a objetos son los siguientes:

* Abstracción
* Poliformismo
* Herencia
* Encapsulación
* Composición
* Asociación
* Agregamiento
* Constructores
* Destructores
* Singleton
* Chain-Of-Responsability
* Class-Responsability-Collaboration

Muchas de las veces que nosotros escuchamos o veos este título `"Orientado a objetos"`, frecuentemente nos asusta, nos cuesta procesarlo o pasamos simplemente de él, pues todo eso no hace falta, solo tenemos que tener en cuenta 3 pasos simples que son:

* Comprender nuestro problema a desarrollar
* Planificar nuestra solución
* Construir nuestra pieza de software para impelementar nuestra solución

Antes de empezar a programar no podemos saltarnos pasos `primordiales` como son el análisis y diseó de nuestro programa y aplicación, esto nos ayudará a comprender y planificar muchisimo mejor nuestra pieza de software que implementaremos.

## ¿Qué es un objeto?

En la programación orientada a objetos se pretende hacer que la máquina piense lo mas parecido a como pensamos nosotros en el mundo real.
Entonces tenemos que preguntarnos que es un bojeto en el mundo real?

Por ejemplo;
* ¿Una manzana es un objeto en la vida real? » Si, es un objeto
* ¿Una taza es un objeto en la vida real? » Si, también es un objeto

Eso si, los objetos están separados uno de otros y cada uno tiene su propia existencia, su propia identidad que es independiente uno de otro.
Por ejemplo tu puedes tener 2 manzanas diferentes o 2 tazas diferentes, esto quiere decir que cada uno de estos objetos tiene diferentes características y su propia identidad.
`Un objeto también puede contener otros objetos`.

Los objetos tienen: características, propiedades inherentes que los describen.

* _Una taza puede estar vacía o llena. --> Atributos de cualquier Objeto_
* _Una manzana puede ser de color verde o rojo. --> Atributos de cualquier Objeto_

Sin apagamos una `lámpara`, esto no quiere decir que se apaguen todas las lamparas del resto del mundo.

### Tomar en cuenta para aplicar a la programación y lo que define un OBJETO.

* ENTIDAD 
* ATRIBUTOS / DATOS / PROPIEDADES 
* COMPORTAMIENTOS / MÉTODOS

## Ejemplo de Objetos 
```
ObjetoCuentaBanco
----------------
saldo: 500 $sus
número: A7238
----------------
depositar()
retirar()
```

```
ObjetoPersona
-------------
nombre: rebcesp
género: masc
--------------
programar()
diseñar()
```

Podemos ver claramente que cada uno de los ejemplos de los objetos tienen sus porpias propiedades como `nombre` y `género` y métodos como `programar()` y `diseñar`.




