# 3 - Requisitos para depurar con GDB

GDB puede utilizarse para depurar casi cualquier ejecutable.
Sin embargo, si el fichero que se quiere depurar no ha sido compilado con opciones de depuración específicas, GDB únicamente mostrará código ensamblador, ya que es lo único que contiene el ejecutable[^asm].
Para depurar con GDB teniendo acceso al código fuente se debe activar el *flag* de compilación con símbolos de depuración.

Cuando compilamos, el compilador se encarga de eliminar y modificar el código para que sea lo más rápido posible (aún más si utilizamos la opción `-O`).
Sin embargo, estas modificaciones eliminarán el código y las diferentes modificaciones harán que sea más difícil comparar el binario resultante con el código del que procede.
Por ejemplo, un bucle `for` podría optimizarse a una única instrucción de suma si fuese posible.
Compilar con este *flag* nos permite adjuntar el código del programa en el binario para que sea posible consultarlo con una herramienta de depuración.
La compilación con símbolos de depuración es tan fácil como añadir la opción `-g` en un compilador de GNU, como son `gcc` o `g++`.

Supongamos el siguiente programa escrito en C++:

```c++
#include <iostream>

int main (int argc, char** argv) {
	std::cout << "Hola mundo" << std::endl;
	return 0;
}
```

Para compilarlo con símbolos de depuración, ejecutamos `g++` añadiendo con la opción `-g`:

```sh
g++ hola.cpp -o hola -g
```

Hecho esto, obtenemos el ejecutable `hola` que podemos abrir en GDB:

```sh
gdb hola
```

Si nuestro ejecutable debiera recibir argumentos para su correcto funcionamiento, podemos usar la opción `-args` para indicarlo.
Así se abriría en GDB un programa que indica si el número pasado como argumento es primo:

```sh
gdb -args es_primo 5
```

[^asm]: Este tipo de depuración es útil para tareas de ingeniería inversa, como la que se propone en la práctica "Bomba Digital" de Estructura de Computadores.
