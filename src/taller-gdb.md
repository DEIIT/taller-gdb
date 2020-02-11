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

Como abreviatura puedes usar `r`:

```gdb
r
```

### Consultar el valor de variables: print

Una vez hemos ejecutado el programa y hemos llegado a un punto de parada podemos consultar el valor de las distintas variables con el comando `print`.

Por ejemplo, en el siguiente código:


```c++
#include <iostream>

int main(int argc, char ** argv){

	int entero = 7;
	float decimal = 23.45f;

	std::cout << "Hola mundo" << std::endl;

	std::cout << "Mostramos un entero: " << entero << std::endl;

	std::cout << "Mostramos un decimal: " << decimal << std::endl;

	int entero_inutil = 2;


	return 0;

}
```

Si establecemos un punto de parada en la línea en la que mostramos un entero:

```gdb
break 10
```

Y ejecutamos el programa:

```gdb
run
```

El programa se detendrá en la linea 10, sin ejecutar el contenido de esta. En este punto podemos preguntar a GDB el valor de las distintas variables del programa con `print`:

```gdb
print entero
```

De parte de GDB obtendremos:

```gdb
$1 = 50
```

Sin embargo, si ejecutamos:

```gdb
print entero_inutil
```

De parte de GDB obtendremos:

```gdb
No symbol "entero_inutil" in current context.
```

Ya que en el contexto actual de la ejecución todavía no se ha creado la variable `entero_inutil`.


### Modificar el valor de variables: set

GDB también nos permite modificar el valor de las distintas variables con la orden `set`.

Siguiendo el ejemplo anterior, con la ejecución parada en la linea 10, ejecutamos:

```gdb
set decimal=99.2f
```

Y continuamos la ejecución con:

```gdb
continue
```

El `cout` de decimal nos mostrará 99.2.


#### Establecer argumentos: set args

\
\
Con la orden `set` podemos establecer los parámetros de ejecución, evitando tener que ponerlos en la orden `run` cada vez que lo ejecutemos.

```gdb
set args arg1 arg2 argN
```


### Continuar: continue

Con el comando `continue` podemos reanudar la ejecución del programa. Se ejecutará con normalidad hasta que encuentre el siguiente punto de parada o finalice.

```gdb
continue
```

Si esperamos llegar a un punto de parada más de una vez (está dentro de un bucle, o es una función que se llamará varias veces) y nos interesa detener la ejecución cuando llegue a la N-ésima vez a dicho punto de parada podemos usar un entero como parámetro de `continue` de forma que continuará la ejecución del programa sin parar en este punto de parada (puede parar en otros) hasta la N-ésima vez que lo alcance.

```gdb
continue 5
```


Como abreviatura puedes usar `c`:

```gdb
c 5
```


### Consultar información: info

El comando `info` nos servirá como un comando genérico para consultar información, con este comando podremos consultar de todo tipo de información, desde CPU's, información sobre la memoria del sistema, las macros disponibles, etc. Uno de los más importantes será la información sobre los puntos de parada (`breakpoints`).


```gdb
info br
```

Vemos como con este comando GDB nos mostrará información sobre los puntos de parada, en orden, el número de parada, el tipo, si el punto de parada debe permanecer, si está activado, la dirección en hexadecimal del punto de parada (para depurar ensamblador) y por último en que función y linea está este punto de parada. Por defecto el punto de parada estará activado.

Otro ejemplo, en el que consultamos las CPU's del sistema:

```gdb
info os cpus
```


### Llevar registro de variables: display y undisplay

En muchas ocasiones necesitamos consultar el valor de ciertas variables y expresiones de forma constante. GDB nos ofrece la opción del comando `display`. Con este comando, cada vez que se detenga la ejecución nos mostrará el valor de las variables o expresiones sobre las que ejecutemos este comando.

```gdb
display entero
```

```gdb
display entero > decimal
```

Y para dejar de mostrar una variable usaremos el comando `undisplay`:

```gdb
undisplay entero
```


### Ejecutar por pasos: step, next y finish

Ya hemos visto como detener la ejecución y como consultar el valor de las distintas variables y estado de la ejecución. GDB también nos permite continuar la ejecución de forma especial, continuando linea a linea, entrando a llamadas a subrutinas o hasta finalizar la ejecución de la función.

#### step

\

Con esta orden continuaremos la ejecución de nuestro programa linea a linea con la característica de que si encuentra una llamada a una subrutina entrará en esta y continuará con su ejecución.

Podemos usarla sin parámetros, lo que avanzará una linea de código, o con un entero como parámetro, con lo que avanzará tantas lineas como le pasemos por parametro.

Por ejemplo, para continuar 5 lineas.

```gdb
step 4
```

#### next

\

Al igual que con `step` continuaremos la ejecución linea a linea, con la diferencia de que usando `next` no entraremos en las llamadas a subrutinas, es decir, se ejecutará la subrutina y continuará en la misma rutina en la que estaba.

```gdb
next
```

Al igual que con `step` le podemos pasar un entero como argumento para avanzar tantas lineas como indiquemos en este entero.

```gdb
next 4
```

#### finish

\

Con esta orden continuaremos la ejecución hasta finalizar la subrutina en la que estemos. Esto quiere decir que aunque no exista un punto de parada al final de dicha subrutina si se detendrá la ejecución.

```gdb
finish
```

Además nos mostrará el valor que devuelve la subrutina.

### Activar y desactivar puntos de parada: enable y disable

Como hemos visto con la orden `info br` los puntos de parada pueden estar activados o desactivados, el comando `enable` y `disable`.

El comando `disable` nos permitirá desactivar puntos de parada o expresiones de `display` sin eliminarlas, es decir, las podremos reactivar usando el comando `enable`.

Algunos ejemplos:

```gdb
disable display entero
```

```gdb
disable breakpoints 2
```

Para volver a activarlos podemos usar el comando `enable`:

```gdb
enable display entero
```

```gdb
enable breakpoints 2
```

Además el comando `enable` nos permite más flexibilidad a la hora de activar los puntos de parada:

- Activar el punto de N paradas: `enable count N <numero_breakpoint>`

Con esto activaremos el punto de parada para las siguientes N paradas. Por ejemplo, para activar el punto de parada 2 durante las 3 siguientes paradas:

```gdb
enable count 3 2
```

- Activar un punto y eliminarlo en la siguiente parada: `enable delete <numero_breakpoint>`

GDB nos permite activar un punto de parada y que este sea borrado la siguiente vez que lleguemos a el:

```gdb
enable delete 1
```

### Limpiar puntos de parada y expresiones de display: clear y delete

Hemos visto como activar y desactivar los puntos de parada, sin embargo no hemos visto como eliminarlos.

Para eliminar puntos de parada podemos usar dos ordenes, `clear` y `delete`.

#### clear

\

El uso de la orden `clear` es muy simple: `clear <linea_breakpoint>`

Por ejemplo, para eliminar el punto de parada en la linea 20 del fichero `prueba.cpp`:

```gdb
clear prueba.cpp:20
```

Si queremos eliminar todos los puntos de parada podemos ejecutar esta orden sin argumentos:

```gdb
clear
```

Hay que tener en cuenta que `clear` no funciona con el número del punto de parada, es necesario especificar la linea donde se encuentra

#### delete

\
\

Con delete si podremos eliminar puntos de parada especificando el número.

Por ejemplo para eliminar el punto de parada número 2:

```gdb
delete 2
```

La orden `delete` también nos permitirá eliminar expresiones o variables que estén afectadas por `display`:

```gdb
delete display entero
```










### Consultar pila de llamadas: where


## 6 - Malas prácticas

## 7 - Otros programas de depuración
