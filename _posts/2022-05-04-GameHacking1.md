---
layout: post
title: 0x01/GameHacking
category: hacking,gaming,programming
permalink: "blog/0x01-gamehacking"
published: no
---
# Introducción

El hacking de videojuegos es una mezcla entre lo exótico y complejo, no creas que escribiendo trucos desde una consola para  `GTA` Vice City, Call Of Duty o Medall Of Honor donde puedes tener vida infinita, todas las armas, ser inormtal, te convierte en  "`Hacker`", NOOOO!, este articulo se trata justamente de eso; de profundizar como es que funcionan los videojuegos a bajo nivel y crear programas, Inyecciones Dlls, depurar codigo, localizar direcciones de memoria. Par asi mismo poder alterar el funcionamiento de los videojuegos y desarrollar tus propios Hacks como un verdadero H4X0R. Empecemos.

## Componentes de una computadora

Antes de nada necesitamos saber los componentes importantes de una computadora:

* CPU
* DISCO DURO
* TARJETA DE VIDEO
* TARJETA MADRE
* RAM

DISCOS DUROS: Almacenan la informacion, ejecutables, archivos del sistema.
RAM: Contiene datos a los que se debe acceder rápidamente. Los datos se cargan en la RAM desde el disco duro.
TARJETA DE VIDEO: Son responsables de mostrar graficos al monitor.
TARJETA MADRE: Une todos estos componentes y les permiten comunicarse.


### CPU
Es el cerebro de la computadora, también llamado `procesador`. `Es el responsable de ejecutar instrucciones`. Estas instrucciones son simples y varían según la arquitectura. Por ejemplo, una instrucción podría sumar dos números. Para acelerar el tiempo de ejecución, la CPU tiene varias áreas especiales donde puede almacenar y modificar datos. Esto se llaman registros.

Algunos de estos registros son: 
```lua
eax
ebx
ecx
edx
edi
esi
ebp
esp
```

Una instrucción de ejemplo podría ser: 
```lua
add eax, 2  //almacenamos en eax el valor 2
```


