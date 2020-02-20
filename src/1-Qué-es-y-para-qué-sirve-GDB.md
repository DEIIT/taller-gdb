# 1 - Qué es y para qué sirve GDB

GDB (GNU Debugger) es un software desarrollado por el proyecto GNU que permite ver que está pasando dentro de un programa cuando éste se está ejecutando (o cuando explote inevitablemente).
A lo largo de este documento explicaremos el uso básico de esta herramienta, desde cómo instalarlo a cómo comenzar a usarlo de forma básica de forma que sirva como introducción a la depuración de programas para usuarios cualquier nivel.
Ésta es una herramienta imprescindible en el abanico de recursos de cualquier persona que se dedique a desarrollar software, ya que el usuario adquiere una eficiencia abismal a la hora de programar con respecto a aquellos que depuran con mensaje por salida estándar.

GDB fue desarrollado por Richard Stallman en 1986, continuando su desarrollo actualmente el proyecto GNU.
Como todos los programas de GNU, GDB es software libre.
Su código puede consultarse [aquí](https://sourceware.org/git/gitweb.cgi?p=binutils-gdb.git).

GDB ofrece cuatro funcionalidades básicas que, usadas en conjunto, permiten encontrar y resolver errores en tu código:

- Ejecutar el programa avisando de cualquier cosa que afecte a su ejecución.
- Detener la ejecución del programa en puntos específicos usando condiciones específicas.
- Examinar qué ha ocurrido al detener el programa o cuando éste se detiene.
- Cambiar valores en el programa para poder experimentar la traza de su ejecución.

Con GDB se pueden depurar programas escritos en los siguientes lenguajes:

- Ada
- Ensamblador
- C
- C++
- D
- Fortran
- Go
- Objetive-C
- OpenCL
- Modula-2
- Pascal
- Rust

Nosotros para realizar los distintos ejemplos usaremos C++, pero cualquier lenguaje de los especificados en la lista valdría igualmente.

