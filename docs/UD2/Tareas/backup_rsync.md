---
icon: material/file-edit
---


# Copias de seguridad con rsync

![Logo rsync](synclogo.jpg){:style="width: 50%;" class="center"}

La utilidad `rsync` es una herramienta muy versátil para hacer copias de archivos. Permite mantener directorios duplicados transmitiendo solamente los cambios que se han producido desde la última copia. Cuando un archivo ha sido modificado, solamente se transmiten los bloques de datos que contienen los cambios, no el archivo entero. De esta forma, se ahorran recursos de red y se disminuye el tiempo de copia.

`rsync` permite hacer copias en local, similar a como funciona el comando `cp`. También permite hacer copias en máquinas remotas a través del protocolo SSH o mediante su propio protocolo, de forma similar a como hace `scp` (secure copy). Además, incluye opciones para mantener copias antiguas de los archivos modificados, permitiendo configurar un sistema de copias incrementales.

## Preparación de máquinas virtuales para la práctica

Para esta práctica, se necesitan **dos máquinas con Linux**. Recomendamos utilizar la distribución **AlmaLinux 9**. La primera máquina, llamada **cliente**, contendrá los archivos de los que queremos hacer copia de seguridad. La segunda, llamada **servidor**, será el destino de las copias.

Si clonas las máquinas virtuales, recuerda cambiar la dirección MAC del adaptador de red para evitar problemas de conectividad.

### Cambiar el nombre de las máquinas

El nombre de las máquinas será cambiado a **cliente+tu_apellido** y **servidor+tu_apellido**. Para cambiar el nombre de una máquina en Linux, sigue estos pasos:

1. Cambia el nombre de la máquina con el siguiente comando:

   ```sh
   sudo hostnamectl set-hostname nuevo_nombre
   ```

2. Edita el archivo `/etc/hosts` para reemplazar el viejo nombre por el nuevo:

   ```sh
   sudo nano /etc/hosts
   ```

3. Comprueba el cambio de nombre con el siguiente comando:

   ```sh
   hostname
   ```

> **Muestra una captura del terminal con el `prompt` de cada máquina para verificar que el cambio de nombre se ha realizado correctamente.**

## Instalación de rsync

Para que la práctica funcione, instala `rsync` en ambas máquinas:

```sh
sudo dnf install rsync -y
```

### Funcionamiento básico de rsync

Copias locales:

```sh
rsync [OPTION]... SRC [SRC]... DEST
```

Copias a través de SSH:

```sh
rsync [OPTION]... SRC [SRC]... [USER@]HOST:DEST
```

Copias a través del protocolo rsync:

```sh
rsync [OPTION]... SRC [SRC]... rsync://[USER@]HOST[:PORT]/DEST
```

### Opciones más comunes

| Opción      | Descripción                                                                                       |
|-------------|---------------------------------------------------------------------------------------------------|
| `-a`        | Modo archivo: copia recursivamente y preserva permisos, enlaces, propietarios, etc.                |
| `-b`        | Hace copias de seguridad de los archivos modificados.                                              |
| `--backup-dir=DIR` | Guarda las copias de los archivos modificados en el directorio DIR indicado.                        |
| `--suffix=SUFFIX`  | Añade un sufijo a los archivos modificados.                                                  |
| `--delete`  | Elimina de la carpeta destino los archivos que se han borrado en la carpeta origen.                |
| `-v`        | Modo detallado: muestra el progreso de la copia.                                                   |
| `-z`        | Comprime los datos durante la transferencia.                                                       |
| `--exclude=PATTERN`| Excluye archivos o carpetas que coincidan con el patrón especificado (PATTERN).             |
| `-P`        | Muestra el progreso de la copia y permite reiniciar transferencias interrumpidas.                  |

Consulta el manual de `rsync` con:

```sh
man rsync
```

También lo tienes disponible en [esta web](https://man7.org/linux/man-pages/man1/rsync.1.html).


### Importancia de la barra `/` en `rsync`

El comando `rsync` distingue si la ruta del directorio origen termina o no con una barra `/`:

- **Sin barra final**: 
   ```sh
   rsync -avz foo:src/bar /data/tmp
   ```
   Esto copia todos los archivos de `src/bar` (en la máquina `foo`) a un nuevo directorio `bar` dentro de `/data/tmp` en la máquina local.

- **Con barra final**: 
   ```sh
   rsync -avz foo:src/bar/ /data/tmp
   ```
   Aquí, **no se crea el directorio `bar` dentro de `/data/tmp`**, solo se copia el contenido de `bar` directamente a `/data/tmp`.

En resumen, la barra `/` al final indica que se copiará el contenido del directorio, no el directorio en sí, pero los atributos del directorio se conservan en ambos casos.


### Prueba básica de rsync

1. En la máquina cliente, crea dos directorios, `origen` y `destino`, dentro de tu carpeta personal:

   ```sh
   cd ~
   mkdir origen
   mkdir destino
   ```

2. Crea tres archivos vacíos y una carpeta llamada `datos` dentro de `origen`:

   ```sh
   cd origen
   touch 1 2 3
   mkdir datos
   cd datos
   touch 4 5 6
   ```

3. Copia los archivos localmente con `rsync`:

   ```sh
   rsync -av ~/origen ~/destino
   ```

> **Muestra una captura del comando ejecutado y del contenido de la carpeta destino usando `ls`.**

4. Si no quisieras que se cree una carpeta `origen` dentro de `destino`, ¿qué comando deberías haber ejecutado?

5. Borra uno de los archivos en `origen` y ejecuta de nuevo el comando. ¿Se ha eliminado el archivo en destino? Usa la opción `--delete` y verifica su funcionamiento.

6. Modifica uno de los archivos en `origen`, añade algo de texto y copia de nuevo usando `--backup`. Observa los cambios y explica cómo funciona la opción `--backup`.

### Uso de rsync a través de SSH

Instala el **servidor SSH** en la máquina servidor:

```sh
sudo dnf install openssh-server -y
sudo systemctl start sshd
```

Copia la carpeta `/home/origen` de la máquina cliente al servidor mediante SSH:

```sh
rsync -av ~/origen usuario@192.168.0.37:~/destino
```

> **Muestra una captura del comando utilizado.**

### Configuración de autenticación SSH sin contraseña

Genera claves SSH en la máquina cliente:

```sh
ssh-keygen
```

- El proceso te preguntará dónde guardar la clave, pulsa enter para guardarla en la ubicación sugerida por defecto.
- Después, te preguntará una clave para proteger tu clave privada. Habitualmente escribiríamos una contraseña, pero en este caso queremos poder usarla sin intervención humana. Así que dejaremos esta contraseña en blanco, pulsando enter.

Copia la clave pública al servidor:

```sh
ssh-copy-id usuario@192.168.0.37
```

> **Muestra una captura del comando ejecutado.**

## Automatización de copias con un script

Crea un script llamado `copia_seguridad.sh` en la máquina cliente:

```sh
nano copia_seguridad.sh
```

Añade el siguiente contenido:

```sh
#!/bin/bash

DAY=$(date +%A)

ssh usuario@192.168.0.37 "if [ -e ~/destino/$DAY ]; then rm -r ~/destino/$DAY ; fi"
rsync -a --delete --quiet --backup --backup-dir=~/destino/$DAY ~/origen usuario@192.168.0.37:~/destino
```

Concede permisos de ejecución al script:

```sh
chmod +x copia_seguridad.sh
```

Ejecuta el script manualmente para verificar que funciona:

```sh
./copia_seguridad.sh
```

#### Explicación del script

1. **`DAY=$(date +%A)`**:
    
    - Almacena el día de la semana actual (por ejemplo, lunes, martes, etc.) en la variable `DAY`. El comando `date +%A` devuelve el nombre completo del día de la semana.
2. **`ssh usuario@192.168.0.37 "if [ -e ~/destino/$DAY ]; then rm -r ~/destino/$DAY ; fi"`**:
    
    - A través de una conexión SSH, accede al servidor con la IP `192.168.0.37` usando el usuario `usuario`.
    - Una vez dentro, verifica si existe una carpeta llamada como el día actual dentro de `~/destino`. Si esa carpeta existe (`if [ -e ~/destino/$DAY ];`), la elimina con `rm -r` (borrado recursivo de directorios).
3. **`rsync -a --delete --quiet --backup --backup-dir=~/destino/$DAY ~/origen usuario@192.168.0.37:~/destino`**:
    
    - **`rsync`** es una herramienta para sincronizar archivos y directorios de manera eficiente.
    - **`-a`**: Activa el modo archivo, lo que preserva los permisos, fechas y enlaces simbólicos.
    - **`--delete`**: Elimina en el destino cualquier archivo que ya no exista en el directorio de origen.
    - **`--quiet`**: Ejecuta el comando en modo silencioso (sin mostrar detalles en la terminal).
    - **`--backup`**: Hace una copia de seguridad de los archivos que van a ser sobrescritos o eliminados.
    - **`--backup-dir=~/destino/$DAY`**: Los archivos que van a ser reemplazados o eliminados se almacenan en un directorio llamado como el día de la semana (`~/destino/$DAY`) en el servidor remoto.

### Automatización con cron


Ahora que tenemos un script capaz de realizar copias de seguridad, vamos a configurar nuestro servidor Linux para que lo ejecute periódicamente y, de este modo, automatizar el proceso de copias de seguridad.

#### ¿Qué es Cron?

El nombre _cron_ proviene del griego _chronos_, que significa "tiempo". En el sistema operativo Linux, cron es un administrador de procesos en segundo plano **(demonio)** que ejecuta scripts o procesos a intervalos regulares (por ejemplo, cada minuto, día, semana o mes). Los procesos que deben ejecutarse y la hora en que deben hacerlo se especifican en el fichero _crontab_.

#### ¿Qué es Crontab?

_Crontab_ es un archivo de texto simple que almacena una lista de comandos que se ejecutarán en un momento especificado por el usuario. _Crontab_ verifica la fecha y hora en que se debe ejecutar el script o comando, comprueba los permisos de ejecución y lo ejecuta en segundo plano. Cada usuario puede tener su propio archivo _crontab_. De hecho, el archivo `/etc/crontab` es el _crontab_ del usuario root. Cuando los usuarios normales (o incluso root) desean generar su propio archivo _crontab_, se utiliza el comando _crontab_.

_Crontab_ es la forma más sencilla de gestionar tareas en cron en sistemas multiusuario, tanto para usuarios comunes como para el administrador (root).

#### Automatización de la ejecución del script

Configura cron para que el script se ejecute automáticamente cada día a las 12 de la noche. Para ello, edita el crontab:

```sh
crontab -e
```

Se abrirá un editor 'vi', puedes consultar cómo usarlo [aquí](https://www.dc.fi.udc.es/~afyanez/info-vi/fun.html)

Añade la siguiente línea al final del archivo:

```sh
0 0 * * * /home/usuario/copia_seguridad.sh
```

> **Muestra la crontab modificada para que se ejecute cada minuto y verifica que las copias se realizan automáticamente.**

### Preguntas adicionales

- ¿Qué configuración necesitas para que la tarea se ejecute el primer día de cada mes a las 00:30?

Para configurar la periodicidad de la tarea puedes usar [esta página](https://crontab.guru/examples.html)

- ¿Cuántas copias diferenciales se guardan en este sistema? ¿Qué hace la opción `--backup-dir`?


## Bibliografía

Página del manual:

* [Manual rsync](https://man7.org/linux/man-pages/man1/rsync.1.html)

Actualizar nombre de máquina:

* [Cambio de nombre de máquina RedHat](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_managing_networking/assembly_changing-a-hostname_configuring-and-managing-networking#proc_changing-a-hostname-using-nmcli_assembly_changing-a-hostname)

Uso de SSH



Uso de Rsync:

* [Manual Rsync Arch](https://wiki.archlinux.org/index.php/Rsync)
* [Manual Rsync LinuxTotal](https://www.linuxtotal.com.mx/index.php?cont=rsync-manual-de-uso)
* [Usando rsync y cron para automatizar respaldos incrementales](https://www.jveweb.net/archivo/2011/02/usando-rsync-y-cron-para-automatizar-respaldos-incrementales.html)
* [Guía DigitalOcean Rsync](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories-es)

Configuración de SSH con certificados:

* [Uso de SSH](https://www.server-world.info/en/note?os=AlmaLinux_9&p=ssh&f=4)
* [SSH con certificados en Debian 9](https://www.digitalocean.com/community/tutorials/como-configurar-claves-de-ssh-en-debian-9-es)

Cron y crontab:

* [Cron y crontab:](https://www.redeszone.net/2017/01/09/utilizar-cron-crontab-linux-programar-tareas/)
* [Crontab guru](https://crontab.guru/)

Uso de vi:

- [Uso del editor vi](https://www.dc.fi.udc.es/~afyanez/info-vi/fun.html)

Formato de fechas:

* [Formato de fechas](https://www.cyberciti.biz/faq/linux-unix-formatting-dates-for-display/)