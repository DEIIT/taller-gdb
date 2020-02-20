# 2 - Instalación de GDB

Puedes instalar GDB en prácticamente todos los sistemas basados en UNIX, como GNU/Linux, \*BSD o MacOS y en Microsoft Windows.
Nosotros recomendamos instalarlo a través de un instalador de paquetes:

```sh
# Distribuciones basadas en Debian
sudo apt update
sudo apt install gdb

# Distribuciones basadas en Red Hat/Fedora
sudo dnf install gdb

# Distribuciones basadas en Arch Linux
sudo pacman -S gdb
```

## Otros sistemas

Si utilizas cualquier otra distribución de Linux o sistema operativo en cuyos repositorios no esté disponible GDB, siempre puedes compilarlo e instalarlo a mano.
Para ello, [descarga el código](https://ftp.gnu.org/gnu/gdb/) directamente desde el servidor `ftp` de GNU.
Una vez esté descargado, sigue estos pasos:

```sh
# Extracción del paquete
tar -xvzf gdb-<version>.tar.gz
# Configurarción y compilación
cd gdb-<version>
./configure
make
# Instalación
sudo make install
```

Por defecto, GDB se instalará en `/usr/local/bin` y `/usr/local/lib`.
Puedes comprobar que está instalado mediante una llamada a tu gestor de paquetes ocomprobando la versión de gdb con el argumento `--version`:

```sh
gdb --version
```

