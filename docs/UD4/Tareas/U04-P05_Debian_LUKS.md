---
icon: material/file-edit
title: Cifrado de discos en Linux (LUKS)
---

<!-- Tomada de curso seguridad del CEFIRE 2020 -->

# Introducción

El objetivo de esta práctica es instalar una máquina con sistema operativo Linux configurando particiones separadas para `/home`, `/var`, y `/etc cifradas para almacenar los datos.

![LUKS Logo](img/DebianLUKS/luks-logo.png){width=50%}

# Enunciado

En esta práctica vamos a instalar un Linux configurando el cifrado de la partición de datos. Usaremos las opciones de instalación guiada de Debian.

Usaremos máquinas virtuales sobre VirtualBox ([https://www.virtualbox.org/](https://www.virtualbox.org/))

Usaremos la versión de Debian más reciente que esté disponible ([https://www.debian.org/download](https://www.debian.org/download))

Para la instalación con el cifrado LUKS se puede seguir el siguiente tutorial, pero haremos algunas cosas diferentes:

> 1. Pondremos como nombre a la máquina nuestra inicial y apellido. Ej: `Juan García` llamaría a la máquina `jgarcia`

> 2. Usaremos la instalación guiada, pero separando `/var`, `/tmp` y `/home` en particiones separadas


[[TUTORIAL: https://www.redeszone.net/tutoriales/seguridad/cifrar-discos-particiones-archivos-luks-linux/]](https://www.redeszone.net/tutoriales/seguridad/cifrar-discos-particiones-archivos-luks-linux/)

![Instalación Debian cifrando y separando particiones](img/DebianLUKS/1-particiones.png){width=70%}

Durante la instalación se podrá elegir un escritorio, se recomienda instalar uno, yo elijo `Xfce` por ser muy ligero 

![Instalación Debian cifrando y separando particiones](img/DebianLUKS/2-escritorio.png){width=70%}





## Comprobación de particiones

Si queremos comprobar las particiones que tenemos creadas, podemos usar el comando siguiente.

```sh
lsblk --fs
```

(@) Toma una captura del resultado del comando anterior

Debería parecerse a:

![Listado de particiones](img/DebianLUKS/3-particiones.png){width=70%}



Si queremos ver las características de LUKS (algoritmo de cifrado simétrico usado, longitud de clave etc), podemos usar el siguiente comando:

```sh
cryptsetup luksDump /dev/sda5
```

(@) Toma una captura del resultado del comando anterior


![Detalles del cifrado LUKS usado](img/DebianLUKS/4-detallesLUKS.png){width=70%}


El `/dev/sda5` es en nuestro caso, si has realizado una instalación diferente es posible que cambie este dato. Tal y como podéis ver, `LUKS` cifra todos los datos con `AES-XTS` en su versión de 512 bits, utiliza un *PBKDF* (Password-Based Key Derivation Function) `argon2i`, y un *hash* `SHA256`.

## Comprobamos protección

Finalmente, intentaremos cambiar la contraseña de root modificando la entrada de GRUB, tal como se hizo en la práctica de restauración de contraseñas.

(@) ¿Es posible el cambio de la contraseña?

# Bibliografía

* [https://www.redeszone.net/tutoriales/seguridad/cifrar-discos-particiones-archivos-luks-linux/](https://www.redeszone.net/tutoriales/seguridad/cifrar-discos-particiones-archivos-luks-linux/)

* [https://gitlab.com/cryptsetup/cryptsetup](https://gitlab.com/cryptsetup/cryptsetup)

- https://wiki.archlinux.org/title/Dm-crypt_(Espa%C3%B1ol)/Encrypting_an_entire_system_(Espa%C3%B1ol)