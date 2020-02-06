## 1 - Qué es GDB

GDB (GNU Debugger) es un software desarrollado por el proyecto GNU que te permite ver que está pasando dentro de un programa cuando este se esta ejecutando (o cuando explota).

En este PDF explicaremos el uso básico de esta herramienta, desde como instalarlo a como comenzar a usarlo de forma básica, pero que sirva de introducción al uso de depuradores, una herramienta imprescindible en el desarrollo de conocimientos de cualquier persona que se dedique a desarrollar software ayudándote a ser un buen programador (o dejar de ser uno malo).

GDB fue desarrollado por Richard Stallman en 1986, continuando su desarrollo actualmente el proyecto GNU. Como todos los programas de GNU se trata de software libre, y podéis consultar su código [aquí](https://sourceware.org/git/gitweb.cgi?p=binutils-gdb.git)

## 2 - Para qué sirve GDB

GDB tiene cuatro características principales, que en conjunto te permitirán realizar muchas más, para ayudarte a encontrar y resolver errores en tu código:

- Ejecutar tu programa, avisando de cualquier cosa que afecte a su ejecución.
- Detener la ejecución de tu programa en puntos específicos usando condiciones específicas.
- Examinar que ha ocurrido cuando detienes (o se detiene) tu programa.
- Cambiar valores en tu programa para poder experimentar la traza de ejecución.

Con GDB podrás depurar programas escritos en los siguientes lenguajes:

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

Nosotros para realizar los distintos ejemplos usaremos C++, aunque es indiferente el lenguaje usado.

## 3 - Instalación de GDB

Puedes instalar GDB en prácticamente todos los sistemas UNIX, como GNU/Linux, \*BSD o MacOS además de en Microsoft Windows.

Nosotros recomendamos instalarlo a través de un instalador de paquetes:

### Distribuciones basadas en Debian

```sh
sudo apt update
sudo apt install gdb
```

### Distribuciones basadas en Red Hat/Fedora

```sh
sudo dnf install gdb
```

### Distribuciones basadas en Arch Linux

```sh
sudo pacman -S gdb
```

### Otros sistemas

Si utilizas cualquier otro sistema operativo que en sus repositorios no este disponible GDB siempre puedes compilarlo e instalarlo a mano.

Puedes encontrar el código de GDB para compilarlo en este enlace: [https://ftp.gnu.org/gnu/gdb/](https://ftp.gnu.org/gnu/gdb/).

Una vez descargado el código sigue estos pasos:

- Extraer el paquete

```sh
tar -xvzf gdb-<version>.tar.gz
```

- Configurar y compilarlo

```sh
cd gdb-<version>
./configure
make
```

- Instalarlo

```sh
sudo make install
```

Por defecto se instalará en `/usr/local/bin` y `/usr/local/lib`

Puedes comprobar que esta instalado comprobando la versión de gdb con:

```sh
gdb --version
```

## 4 - Requisitos de GDB

El primer paso para usar GDB es activar el flag para compilar con símbolos de depuración.

Cuando compilamos el compilador se encarga de eliminar y modificar el código para que sea lo más rápido posible (aún más si utilizamos la opción `-O`), sin embargo, estas modificaciones eliminarán el código y las diferentes modificaciones harán que sea más difícil comparar el binario resultante con el código del que procede. Compilar con esta flag nos permitirá que el código del programa esté adjunto en el binario para que sea posible consultarlo con una herramienta de depuración.

A este proceso se le llama compilar con símbolos de depuración, y (si usamos un compilador de GNU) es tan facil como añadir el flag `-g`.

Ejemplo compilando un fichero en C++:

```sh
g++ hola.cpp -o hola -g
```

Que contiene el siguiente código en C++:

```c++
#include <iostream>

int main(int argc, char ** argv){

	std::cout << "Hola mundo" << std::endl;

	return 0;

}
```

Una vez obtenemos el binario `hola` abrimos GDB con este fichero:

```sh
gdb hola
```

## 5 - Órdenes GDB

### Salir de GDB: quit

Salir de GDB es muy simple, basta con usar el fin de fichero (EOF, en UNIX y GNU/Linux con Ctrl+D) o escribir el comando:

```gdb
quit
```

### Mostrar código: list

Dentro de GDB podemos consultar el código que estemos usando.

Una forma básica es ejecutar solo el comando `list`

```gdb
list
```

Con esto podemos consultar 10 lineas alrededor del main.

Otra opción es usar un parámetro, que indicará el número de linea a consultar.

```gdb
list 20
```

Con esto consultaremos 10 lineas alrededor de la linea 20.

Si queremos ver un rango de lineas, basta con separarlas con una coma, por ejemplo, para consultar de la linea 20 a la 40 basta con ejecutar:

```gdb
list 20,40
```

Otra opción es usar los nombres de las funciones, por ejemplo, si queremos ver el código de la función `prueba` podemos ejecutar:

```gdb
list prueba
```

En caso de que nuestro proyecto tenga más de un fichero podemos seleccionar el fichero donde consultar si antes de la linea, el rango de lineas o la función especificamos el nombre del fichero seguido de "`:`".

```gdb
list fichero2.cpp:20,40
```

Esto nos mostrará el intervalo de lineas de la 20 a la 40 del fichero `fichero2.cpp`.

Como abreviatura puedes usar `l`.

```gdb
l 20,40
```

Esto es equivalente a:

```gdb
list 20,40
```

### Establecer puntos de parada (breakpoints): break

Para depurar un programa una de las opciones más útiles que nos ofrece GDB son los puntos de parada (breakpoints).

Los puntos de parada nos permiten detener la ejecución del programa en el punto que nosotros queramos y si se cumplen ciertas condiciones.

Para establecer un punto de parada podemos usar el comando `break` seguido del número de la linea o el nombre de la función.

Por ejemplo, para poner un punto de parada en la linea 12:

```gdb
break 12
```

Para poner un punto de parada en la función `prueba`:

```gdb
break prueba
```


Al igual que con la orden `list`, si nuestro proyecto tiene distintos ficheros podemos establecer un punto de parada en una función de otro fichero usando "`:`":

```gdb
break fichero2.cpp:prueba
```


Cada vez que se alcance un punto de parada GDB nos avisará del número de veces que se ha alcanzado es punto.


Como abreviatura puedes usar `br`:

```gdb
br fichero2.cpp:prueba
```

### Ejecutar: run

Con el comando `run` ejecutaremos el programa.

El programa se ejecutará de forma normal, detendrá su ejecución si encuentra un punto de parada y finalizará la ejecución una vez acabe el programa (cuidado, el llegar a un punto de parada no implica finalizar el programa, simplemente detendrá la ejecución de instrucciones, y como veremos en la siguiente sección, continuará desde este punto).

```gdb
run
```

Si tu programa necesita argumentos, estos se pueden pasar como argumentos a `run`.

```gdb
run arg1 arg2 arg3
```

También es posible usar los operadores para redireccionar la salida y entrada("`>`", "`<`" o "`>>`").

```gdb
run > salida.txt
```

Como abreviatura pueder usar `br`:

```gdb
r
```


### Continuar: continue

### Consultar información: info

### Activar y desactivar puntos de parada: enable y desable

### Limpiar puntos de parada: clear y delete

### Consultar el valor de variables: print

### Modificar el valor de variables: set


### Establecer argumentos: set args

set args

o cargar gdb con --args





### Ejecutar por pasos: step, next y finish

step -> entra en subrutinas

next -> no entra en subrutinas

finish -> ejecuta hasta que acabe la función

### Consultar pila de llamadas: where


### Llevar registro de variables: display

## 6 - Malas prácticas

## 7 - Otros programas de depuración
