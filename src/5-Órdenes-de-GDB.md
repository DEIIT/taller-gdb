# 5 - Órdenes de GDB

## Consultar el manual: `help`
Antes de empezar es imperativo saber cómo acceder a información de apoyo en caso de que no sepamos hacer algo.
La orden `help` nos permite consultar información sobre todas las órdenes de GDB y su uso.
Podemos utilizarla pasándole aquello que queremos consultar como argumento o sin pasarle ninguno, en cuyo caso nos mostrará un menú:

```gdb
help
help list
```

## Consultar información: `info`

La orden `info` es una orden genérica utilizada para consultar todo tipo de información, desde CPUs, información sobre la memoria del sistema, las macros disponibles, etc.
Al igual que con `help` podemos pasarle como argumento aquello que queremos consultar o no pasarle ninguno y que nos muestre un menú:

```gdb
info
info os cpus
```

Aunque a simple vista parezca poco útil, esta orden nos permitirá más tarde obtener información sobre algunos parámetros de depuración que activemos manualmente.

## Mostrar el código fuente: `list`

A la hora de depurar es muy importante consultar el código sobre el que se está trabajando.
La forma más básica de hacer esto es usando la orden `list`, que muestra las diez primeras líneas de la función `main`:

```gdb
list
```

Sucesivas llamadas a esta orden mostrarán las diez siguientes líneas tras el último bloque mostrado.
Para mostrar una línea y su contexto, podemos pasarle como argumento el número de línea que queremos consultar.
De esta forma, se mostrará la línea pasada como argumento acompañada de las cinco líneas anteriores y las cuatro posteriores:

```gdb
list 20
```

Para consultar un rango de líneas, podemos pasarle como argumento las líneas entre las cuales queremos leer (ambas inclusive) separadas por una coma.
Por ejemplo, podemos consultar el código desde la línea 20 a la 40:

```gdb
list 20,40
```

También podemos prefijar el fichero del cual queremos leer las líneas seguido de dos puntos (`:`) para especificar que queremos leer las líneas de ese fichero en específico.
Por ejemplo, así mostraríamos las líneas 20 a 40 del fichero `demo.cpp`:

```gdb
list demo.cpp:20,40
```

Si, en lugar de líneas, queremos mostrar funciones del programa, podemos ejecutar la orden pasando como argumento el nombre de dicha función.
Es importante tener en cuenta que, debido a la existencia de la sobrecarga de funciones, es obligatorio indicar los argumentos que recibe la misma.

```gdb
list Factorial(unsigned int)
list operator<<(std::ostream&, ingrediente const&)
```

Para abreviar, podemos escribir simplemente `l` en lugar de `list`:

```gdb
l 20,40
l Factorial(unsigned int)
```

## Ejecutar el programa: `run` y `set args`

Para ejecutar un programa usamos la orden `run`, que puede abreviarse como `r` que lo ejecuta de forma normal pidiendo entrada al usuario si fuese necesario.
La orden se puede ejecutar de forma normal o con argumentos (al igual que en el ejemplo `es_primo` del punto 4):

```gdb
run
run 5 // Ejecutado pasando "5" como argumento
```

Si queremos redireccionar la entrada o salida estándar del programa a ficheros, podemos hacerlo con los operadores de redicción `<`, `>` ó `>>`:

```gdb
run > salida.log
```

Si queremos ejecutar siempre el programa con los mismos argumentos, podemos usar la orden `set args`[^setargs] para indicarlos una única vez y que se ejecute siempre con ellos.

```gdb
set args arg1 arg2 ... argN
```

## Establecer puntos de parada (*breakpoints*): `break`

A la hora de depurar un programa querremos ver el estado de las variables en un punto concreto o centrarnos en una función en concreto.
Para esto existen los *breakpoints* o puntos de parada, que son marcas en la secuencia de sentencias que forman el código en las que el depurador pone en pausa su ejecución nada más llegar.
Esto no significa que el programa deja de ejecutarse, existen órdenes para continuar la ejecución de programa, sino que frena su ejecución para poder evaluarlo en un punto en concreto.

Los *breakpoints* se crean con la orden `break`, que puede abreviarse como `br`.
Al igual que con la orden `list`, podemos especificar la línea o función exacta en la que queremos parar, así como el fichero al que pertenecen éstas:

```gdb
break 12
break operator<<(std::ostream&, receta const&)
break fichero2.cpp:prueba
```

Una vez establecidos los *breakpoints*, podemos ejecutar el programa con `run`, que ejecutará el programa normalmente hasta encontrarse con un punto de parada.
Al llegar a él, GDB frenará la ejecución del programa y nos comunicará el número de veces que se ha alcanzado este punto en la ejecución actual.
Una vez frenada la ejecución, podemos establecer más *breakpoints*, analizar el programa en ese mismo punto y controlar manualmente el avance del programa.

Otra opción que nos ofrecen los *breakpoints* es establecer un punto de parada si se cumple cierta condición. Por ejemplo con el siguiente código:

```c++
#include <iostream>

using namespace std;

int main (int argc, char ** argv){

	int entero = 1;

	cin >> entero;

	if (entero > 0){
		cout << "El número introducido es positivo." << endl;
	} else if (entero < 0){
		cout << "El número introducido es negativo." << endl;
	}


}

```

Podemos establecer el siguiente punto de parada:

```gdb
break 11 if entero == 0
```

Esto hará que al ejecutar el programa solo se detenga si el entero introducido vale 0.

## Controlar el manualmente el avance del programa: `step`, `next`, `finish` y `continue`

Para controlar manualmente el avance del programa tras alcanzar un *breakpoint* podemos usar `step` y `next`, que ejecutan el programa línea a línea con algunas diferencias, y `finish`, que ejecuta el resto de la función.

### `step`

Esta orden, abreviable como `s`, nos permite continuar la ejecución del programa línea a línea, es decir, cada ejecución de `step` equivale a la ejecución de una línea de código.
Si el siguiente paso es una llamada a una subrutina, `step` entrará en ella y continuará la ejecución en su interior hasta volver a la original.

Podemos usarla sin argumentos para avanzar una única línea de código cada vez o pasándole un enetero, con lo que avanzará tantas líneas como le indiquemos:

```gdb
step
step 4
```

### `next`

Al igual que con `step`, continuaremos la ejecución línea a línea, con la diferencia de que `next`, abreviable como `n`, no entrará en las llamadas a subrutinas.
En su lugar, las ejecutará sin entrar en ella y devolverá el control del programa cuando acabe.
Al igual que a `step`, también podemos pasarle un entero como argumento para avanzar tantas líneas como le indiquemos.

```gdb
next
next 4
```

### `finish`

Para ejecutar el programa hasta que la rutina en la que nos encontramos finalice utilizamos la orden `finish`.
Esta orden continúa la ejecución del programa hasta que la rutina devuelva un valor (o finalize, en caso de `void`) y frena la ejecución en ese punto independientemente de la existencia de un *breakpoint* al final de la misma.
Al llegar al final, nos mostrará el valor devuelto si lo hubiera.
Esta orden no recibe argumentos:

```gdb
finish
```

### `continue`

Con esta orden, abreviable como `c` reanudamos la ejecución del programa para que continúe ejecutándose normalmente hasta encontrar el siguiente punto de parada o llegar al final.

```gdb
continue
```

Si esperamos llegar a un punto de parada más de una vez (por ejemplo, si estamos dentro de un bucle o de una función que prevemos que será llamada varias veces) y nos interesa detener la ejecución cuando llegue a la *n*-ésima vez a dicho punto de parada, podemos pasarle como parámetro el número de *breakpoints* que queremos que ignorede forma que continuará la ejecución del programa sin parar en este punto de parada hasta la N-ésima vez que lo alcance.
El *breakpoint* que se ignora es únicamente el que generó la última parada, por lo que GDB podrá parar en otros que se encuentre a lo largo de la ejecución.

```gdb
continue 5
```

## Consultar el valor de variables: `print`

Una vez hemos ejecutado el programa y hemos llegado a un punto de parada, podemos consultar el valor de las distintas variables con el comando `print`, abreviable como `p`.
Tomemos como ejemplo el siguiente programa:

```c++
#include <iostream>

int main (int argc, char** argv) {
	int   entero  = 7;
	float decimal = 23.45f;

	std::cout << "¡Hola, mundo!" << std::endl;
	std::cout << "Mostramos un entero:  " << entero << std::endl;
	std::cout << "Mostramos un decimal: " << decimal << std::endl;

	int entero_inutil = 2;

	return 0;
}
```

Establzcamos un punto de parada en la línea en la que mostramos un entero y ejecutemos el programa:

```gdb
break 8
run
```

El programa se detendrá en la línea 8 sin ejecutar su contenido.
En este punto, podemos preguntar a GDB el valor de las distintas variables del programa con `print`:

```gdb
print entero
```

Lo cual nos devolverá lo siguiente:

```gdb
$1 = 50
```

El valor `$1` indica que es la primera variable sobre la cual hemos ejecutado `print`.
Si, estando en la misma línea, ejecutamos lo siguiente:

```gdb
print entero_inutil
```

GDB nos devolverá un error, ya que la variable `entero_inutil` no ha sido creada todavía:

```gdb
No symbol "entero_inutil" in current context.
```

## Llevar registro de variables: `display` y `undisplay`

En muchas ocasiones necesitamos consultar el valor de ciertas variables y expresiones de forma regular.
GDB nos ofrece la orden `display`, que nos muestra el valor de las variables o expresiones sobre las que hemos ejecutado este comando cada vez que el programa llega a una parada.
Siguiendo el ejemplo anterior, podemos consultar la variable `entero` o compararla con `decimal`

```gdb
display entero
display entero > decimal
```

GDB nos mostrará la siguiente información:

```gdb
1: entero = 7
2: entero > decimal = false
```

Si no queremos mostrar más información sobre una variable o expresión usamos la orden `undisplay` seguida de la variable que queremos dejar de mostrar o su índice:

```gdb
undisplay entero
undisplay 2
```

## Modificar el valor de variables: set

GDB también nos permite modificar el valor de las distintas variables con la orden `set`.
Siguiendo el ejemplo expuesto en el apartado de la orden `print`, con la ejecución parada en la línea 8, ejecutamos lo siguiente:

```gdb
set decimal=99.2f
continue
```

El `std::cout` de `decimal` nos mostrará que éste vale `99.2`.

## Gestionar los puntos de parada y *displays*: `enable` y `disable`

Mediante la orden `info` podemos obtener la siguiente información sobre los puntos de parada activos al ejecutarla:

- El índice de parada.
- El tipo de parada.
- Si el punto de parada debe permanecer.
- Si el punto de parada está activado.
- La dirección de memoria (en hexadecimal) del punto de parada[^brasm].
- En que función y línea está este punto de parada.

```gdb
info br
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00005555555551d4 in main(int, char**) at test.cpp:8
```

Por defecto, los puntos de parada están activados (`Enb=y`).
Esto significa que GDB frenará su ejecución siempre que se encuentre con ellos.
Para desactivarlos sin eliminarlos usamos la orden `disable` y `enable` para reactivarlos:

```gdb
disable breakpoint 1
enable breakpoint 1
```

Estas órdenes también pueden usarse para activar o desactivar *displays*:

```gdb
disable display entero
enable display entero
```

Además, el comando `enable` nos ofrece mayor flexibilidad a la hora de activar los puntos de parada.
Por ejemplo, podemos activar un *breakpoint* para un número `N` de paradas con la sintaxis `enable count N <numero_breakpoint>`.
Por ejemplo, para activar el punto de parada 2 durante las tres siguientes paradas:

```gdb
enable count 3 2
```

También podemos activar un punto y eliminarlo cuando GDB llegue a él con la sintaxis `enable delete <num_breakpoint>`:

```gdb
enable delete 1
```

## Limpiar puntos de parada y *displays*: `clear` y `delete`

Para eliminar puntos de parada podemos usar las órdenes `clear` y `delete`.

### `clear`

La orden `clear` elimnina el *breakpoint* de la línea pasada como argumento o todos los puntos de parada si no se le pasa ninguna.
Por ejemplo, para eliminar el punto de parada en la línea 20 del fichero `prueba.cpp`:

```gdb
clear prueba.cpp:20
clear
```

Es importante tener en cuenta que `clear` no funciona con el índice del punto de parada, sino que es necesario especificar la línea donde éste se encuentra.

### delete

Con `delete` sí podremos eliminar puntos de parada especificando su índice.
Por ejemplo para eliminar el punto de parada número 2:

```gdb
delete 2
```

La orden `delete` también nos permitirá eliminar expresiones o variables que estén afectadas por `display`:

```gdb
delete display entero
```

## Consultar pila de llamadas: `where`

Otra de las funciones más interesantes que nos ofrece GDB es la orden `where`, que nos ofrece información sobre la pila de llamadas y sobre dónde nos encontramos en la ejecución del programa.
Por ejemplo, si `funcion1` es llamada desde dos lugares, `main` y `prueba`, este comando nos permite saber desde cuál de los dos ha sido llamada.
Si establecemos un punto de parada en `funcion1` y, cuando estemos en este punto de parada, ejecutamos `where`,  podemos consultar desde que función ha sido llamado.

```gdb
br funcion1
r
where
```

## Salir de GDB: `quit`

Por último, salir de GDB es muy simple.
Basta con enviar un `EOF` o fin de fichero, que en UNIX y GNU/Linux es `^D` (`Ctrl+D`), o escribir la orden `quit`, que puede abreviarse como `q`.

[^brasm]: Para depurar en ensamblador.
[^setargs]: Éste es un caso particular de la orden `set`, que veremos más adelante.
