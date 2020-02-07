---
layout: post
title: Java Tips
category: Java, tips
permalink: "blog/java-tips"
published: yes
---

# Tips Java / Código Limpio 


Estas son algunos de los tips que utilizan programdores Seniors, autodictas con mucho años de experiencia,  tienen una estructura de lógica limpia y clara para entender el código, como también consejos a la hora de empezar un proyecto y escribir con coherencia la estructura del código.


## ¿Cómo comparar correctamente Strings y (Objetos) en Java?

Supongamos que tu tienes este código, el cual es un juego que un usuario X tenga que adivinar un nombre. Pero cuando se quiere comparar dos cadenas de texto para ver si son iguales como se tendría que hacer??

###Código Errrado

```java
class Juego{
  public static void main(String[] argv){
    final String miNombre = "rebcesp";
    Scanner input = new Scanner(System.in);
    System.out.println("Adivina el nombre");
    
    while(true){
      String intento = input.next();
      if(intento == miNombre){
        System.out.println("Acertaste");
        break;
       }else{
         System.out.println("Intentalo de nuevo")
       }
    
    
    }
    
    
  }
}

```
En el anterior código hay un error que es comparar ``dos String` con operadores `==`, esto en Java no funciona, los operadores `==` se usa para: _Integer,Char,Object_.
Lo mejor que se puede usar en vez de `if(intento==miNombre){` es --> `if(equals.miNombre(rebcesp))`

**Output**
---
Adivina el nombre:
rebcesp
Acertaste!
---

