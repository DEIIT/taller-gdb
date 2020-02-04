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


## 5 - Órdenes GDB

## 6 - Malas prácticas
