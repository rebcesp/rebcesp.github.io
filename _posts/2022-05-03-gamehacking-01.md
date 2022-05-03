---
layout: post
title: 0x01~GameHacking
category: exploit,asm,programming
permalink: "blog/0x01-gamehacking"
published: yes
---

# Introducción

El hacking de videojuegos es una mezcla entre lo exótico y complejo, no creas que vas aprender a escribir trucos desde una consola como en  `GTA` Vice City o Call Of Duty para tener vida infinita o todas las armas, ser inmortal, NO!  aqui aprenderas como un hacker! desde como funciona una computadora, las partes del hardware que tiene y que hace cada una de ella, como procesa la información a bajo nivel, como interactuar con el código maquina y poder reversear esto, modificar funciones depurando nuestro codigo, inyectando DLLs, crear POC multihacking. No se diga mas y empecemos.

## Configuraciones Mitigaciones / Herramientas de Análisis

A la hora de introducirse en el tema, es recomendable deshabilitar las técnicas de mitigación presentes por defecto en los sistemas modernos. A medida que se avanza con ataques más sofisticados es posible habilitar una a una estas mitigaciones para poner en práctica como subvertirlas y trabajar en el marco de escenarios mas `"reales"`. Por lo tanto se reocmienda compilar los programas vulnerables con ciertos flags específicos que harán la tarea más fácil y se recomiendan ciertas configuraciones extras en el sistema.

## Entorno sin mitigaciones

Por ejemplo si hablamos de desbordamiento de búfer(overflow) es una vulnerabilidad de larga data, los sistemas operativos desarrollaron prevenciones para este tipo de ataques, entre otras la randomización de espacio de direcciones y la implementación de `canaries` de protección de pila. Por eso para introducir estos ataques es necesario deshabilitar una serie de configuraciones para, en segunda instancia, explicar cómo superarlas. A continuación se hace un repaso de las mitigaciones y se usa el script `checksec` para detectar cuando se encuentran habilitadas o no

## ASLR 
El ASLR("Address Space Layout Randomization" o mapa de espacio de direciones aleatorio) es una tecnología diseñada para impedir la explotación de ciertas vulnerabilidades. El ASLR vuelve aleaotrias las direcciones de memoria de un proceso y por ende, dificulta la tarea de adivinar direcciones de la pila. Esta información es necesaria para la lectura y reescritura de sectores claves de la pila, para la inyección y ejecución de código malicioso y en el caso de técnicas de explotación más avanzadas, para la creación de `ROP gadgets`.

Para desactivar temporalmente esta protección desde la terminal es configuración el espacio de memoria como `no random`

```bash
rebcesp@rebcesp:~$ sudo sysctl -w kernel.randomize_va_space=0 #no random
rebcesp@rebcesp:~$ sudo sysctl -w kernel.randomize_va_space=1 #random
#Comprobamos que este en 0
rebcesp@rebcesp:~$ cat /proc/sys/kernel/randomize_va_space
0
```
## Mitigación W^X
Es una política en relación a la memoria que indica que nunca se debe tener una página de memoria escribible y ejecutable al mismo tiempo`(representado como write XOR execute o W^X)`. Es por ello que se marcan los sectores escribibles dentro de las direcciones de un proceso como no ejecutables. La pila entonces deviene en un área de memoria no ejecutable.
Los ataques iniciales consistiran en parte en inyectar un código arbitrario en la pila(escribir en la pila) y ejecutarlo(ejecutar en la pila). Para ser capaces de escribir y ejecutar en la pila, es necesario deshabilitar esta mitigación

Por defecto `gcc` compila con esta mitigación habilitada. Para deshabilitar protección de ejecución en la pila, al compilar con `gcc` agregamos el flag `-z execstack`

```bash
rebcesp@rebcesp:~$ gcc -o no-exec programa.c
rebcesp@rebcesp:~$ ./checksec.sh --file no-exec
NX          ... FILE
NX enabled  ... no-exec

rebcesp@rebcesp:~$ gcc -z execstack -o exec programa.c
rebcesp@rebcesp:~$ ./checksec.sh --file exec
NX          ... FILE
NX disabled ... exec
```
## Deshabilitar RELRO

Para prevenir técnicas de explotación que sobreescriben la `Global Offset Table`, se obliga al linker a resolver todas las funciomnes linkeadas dinámicamente al iniciarse el programa y, una vez actualizada la tabla GOT, volverala de sólo lectura de modo que no pueda ser sobreescrita de manera arbitaria. Es por ello que en exploits que involucran la GOT es necesario que la mitigación RELRO`(RELocation Read-Only)` se encuentre `deshabilitada o habilitada parcialmente con la variante "RELRO parcial`. Esta última opción es usada por defecto en la mayoría de distribuciones de Linux modernas, por lo que no es necesaria ninguna configuración extra.
No obstante en casos donde un ataque utilice la sección DTORS serña necesario deshabilitarlo completamente con el flag `-Wl,-z,norelro`.

```bash
rebcesp@rebcesp:~$ gcc -o con-relro programa.c
rebcesp@rebcesp:~$ ./checksec.sh --file con-relro
RELRO           ...    FILE
Partial RELRO   ...    con-relro

rebcesp@rebcesp:~$ gcc -Wl,-z,norelro -o sin-relro programa.c
rebcesp@rebcesp:~$ ./checksec.sh --file sin-relro
RELRO      ...    FILE
No RELRO   ...    sin-relro
```

## _canary_ de protección de pila

En esta técnica de mitigación el compilador inserta una marca o canario en la pila cuando detecta una función que accede a variables locales por referencia. En estos casos inmediatamente después de almacenar la dirección de retorno, se almacena a su vez un valor(el canario o la cookie) entre la variable local(datos) y la dirección de retorno(información de control).
Frente a un ataque de reesccritura de la dirección  de retorno, el valor del canario se verá modificado levantando alertas de que se produjo un desbordamiento de búffer. Y se detiene la ejecucióon del programa antes de que la función retorne a la dirección vulnerada por ejemplo con una excepción de violación de segmento. En una primera isntancia, de esta manera se evitaría una reescritura de la dirección de retorno de una función y la consecuente ejecución de código malicioso.

Dado que inicialmente se trabajará con técnicas de corrupción de la dirección de retorno en memoria es necesario deshabilitar esta protección de la pila compilando con `gcc` con el `flag -fno-stack`

```bash
rebcesp@rebcesp:~$ gcc -o con-canary programa.c
rebcesp@rebcesp:~$ ./checksec.sh --file con-canary
STACK CANARY      ...    FILE
Canary found      ...    con-canary

rebcesp@rebcesp:~$ gcc -fno-stack-protector -o sin-canary programa.c
rebcesp@rebcesp:~$ ./checksec.sh --file sin-canary
STACK CANARY      ...    FILE
No canary found   ...    sin-canary
```

### Flags de GCC útiles


* -m32: Para compilar ejecutables de 32 bits
* -mpreferred-stack-boundary=2 para el alineamiento de la pila
* -ggdb habilita la informaciín de debugging
* -no-pie para evitar compilar el binario como _Position Independent Executable_, sino cambiaría en cada ejecución la ubicación del código en el espacio de memoria virtual.


