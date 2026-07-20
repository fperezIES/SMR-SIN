---
icon: material/file-edit
---


# Cifrado de Discos LUKS en AlmaLinux

![LUKS logo](luks-logo.png){:style="width: 40%;" class="center"}



## Introducción

En un mundo donde la seguridad de la información es cada vez más crucial, proteger los datos almacenados en discos duros y dispositivos de almacenamiento es fundamental. El cifrado de discos asegura que, en caso de pérdida o robo del dispositivo, la información contenida no pueda ser accedida por personas no autorizadas.

Linux Unified Key Setup (**LUKS**) es el estándar para el cifrado de discos duros en Linux. Ofrece una interfaz sencilla para gestionar volúmenes cifrados y es compatible con la mayoría de las distribuciones Linux, incluyendo AlmaLinux.

Esta práctica tiene como objetivo guiarte a través del proceso de cifrado de una partición de disco utilizando LUKS en AlmaLinux, resaltando la importancia y los beneficios de proteger tus datos.

## Objetivos de la Práctica

- Comprender la importancia del cifrado de discos en la seguridad de la información.
- Aprender a cifrar una partición de disco utilizando LUKS en AlmaLinux.
- Configurar el sistema para montar automáticamente la partición cifrada al inicio (opcional).
- Familiarizarse con las herramientas y comandos relacionados con LUKS y la gestión de discos en Linux.

## Requisitos Previos

- Conocimientos básicos del sistema operativo Linux y uso de la terminal.
- AlmaLinux instalado en el sistema.
- Acceso con privilegios de superusuario (root) o mediante el comando `sudo`.
- Un disco o partición adicional disponible para el cifrado (preferiblemente sin datos importantes).

## Advertencia Importante

**¡Cuidado!** El cifrado y manipulación de discos puede llevar a la pérdida de datos si no se realiza correctamente. Asegúrate de realizar copias de seguridad de cualquier información importante antes de proceder.



## Pasos de la Práctica

### 1. Preparación del Entorno

Antes de comenzar, actualiza los paquetes de tu sistema para asegurarte de que tienes las últimas versiones:

```bash
sudo dnf update
```

Instala las herramientas necesarias si no las tienes ya:

```bash
sudo dnf install cryptsetup lvm2
```

- **cryptsetup**: Utilidad para configurar cifrado en discos.
- **lvm2**: Herramientas para gestionar volúmenes lógicos (opcional pero útil si trabajas con LVM).

### 2. Identificación del Disco o Partición a Cifrar

Necesitas identificar el dispositivo que vas a cifrar. Puedes utilizar los siguientes comandos:

```bash
sudo fdisk -l
```

o

```bash
lsblk
```

Busca en la lista el disco o partición que deseas cifrar. Por ejemplo, supongamos que es `/dev/sdb1`.

> **Nota:** Asegúrate de elegir el dispositivo correcto. Cifrar el dispositivo equivocado puede resultar en pérdida de datos.

### 3. Copia de Seguridad de Datos

Si el dispositivo que vas a cifrar contiene datos, debes hacer una copia de seguridad, ya que el proceso de cifrado eliminará toda la información existente.

### 4. Cifrado de la Partición con LUKS

#### 4.1. Opcional: Borrado Seguro de la Partición

Para asegurarte de que no queden datos residuales, puedes sobrescribir la partición con datos aleatorios:

```bash
sudo dd if=/dev/urandom of=/dev/sdb1 bs=1M status=progress
```

Este proceso puede tardar un tiempo dependiendo del tamaño de la partición.

#### 4.2. Iniciar el Cifrado

Inicia el proceso de cifrado con el siguiente comando:

```bash
sudo cryptsetup luksFormat /dev/sdb1
```

Se te pedirá confirmar el proceso y luego ingresar una contraseña:

- **Contraseña**: Elige una contraseña robusta. Esta contraseña será necesaria para acceder a los datos cifrados, así que recuérdala bien o almacénala en un lugar seguro.

**Advertencia:** Si pierdes esta contraseña, no podrás recuperar los datos cifrados.

### 5. Abrir la Partición Cifrada

Una vez cifrada, necesitas "abrir" la partición para poder utilizarla:

```bash
sudo cryptsetup luksOpen /dev/sdb1 mi_disco_cifrado
```

- **mi_disco_cifrado**: Es un nombre arbitrario que asignas al dispositivo mapeado.

### 6. Crear un Sistema de Archivos en la Partición Cifrada

Ahora que el dispositivo está abierto, está disponible en `/dev/mapper/mi_disco_cifrado`. Debes crear un sistema de archivos en él:

```bash
sudo mkfs.ext4 /dev/mapper/mi_disco_cifrado
```

Puedes elegir otro sistema de archivos si lo prefieres (`xfs`, `ext3`, etc.).

### 7. Montar la Partición Cifrada

Crea un punto de montaje:

```bash
sudo mkdir /mnt/mi_disco_cifrado
```

Monta el sistema de archivos:

```bash
sudo mount /dev/mapper/mi_disco_cifrado /mnt/mi_disco_cifrado
```

Ahora puedes utilizar la partición cifrada como cualquier otro sistema de archivos.

### 8. Configuración para Montaje Automático (Opcional)

Si deseas que la partición cifrada se monte automáticamente al iniciar el sistema, sigue estos pasos.

#### 8.1. Añadir Entrada en `/etc/crypttab`

Edita el archivo `/etc/crypttab`:

```bash
sudo nano /etc/crypttab
```

Añade la siguiente línea:

```
mi_disco_cifrado    /dev/sdb1    none    luks
```

- **mi_disco_cifrado**: Debe coincidir con el nombre utilizado en `luksOpen`.

#### 8.2. Añadir Entrada en `/etc/fstab`

Edita el archivo `/etc/fstab`:

```bash
sudo nano /etc/fstab
```

Añade la siguiente línea:

```
/dev/mapper/mi_disco_cifrado    /mnt/mi_disco_cifrado    ext4    defaults    0    0
```

Asegúrate de ajustar el tipo de sistema de archivos si no es `ext4`.

**Nota:** Al reiniciar, el sistema te pedirá la contraseña para desbloquear el disco cifrado durante el proceso de arranque.

### 9. Cerrar la Partición Cifrada

Cuando termines de utilizar el disco cifrado, puedes desmontarlo y cerrar el dispositivo:

```bash
sudo umount /mnt/mi_disco_cifrado
sudo cryptsetup luksClose mi_disco_cifrado
```



## Conclusión

En esta práctica, has aprendido a cifrar una partición de disco utilizando LUKS en AlmaLinux. El cifrado de discos es una medida de seguridad esencial para proteger datos sensibles, especialmente en dispositivos que pueden ser extraviados o robados.

**Puntos clave:**

- **Seguridad de Datos:** El cifrado protege tus datos en caso de acceso físico no autorizado.
- **Contraseñas Robustas:** Utiliza contraseñas fuertes y mantenlas en un lugar seguro.
- **Respaldos:** Siempre realiza copias de seguridad antes de manipular particiones y sistemas de archivos.



## Consideraciones Adicionales

- **Impacto en el Rendimiento:** El cifrado puede tener un impacto mínimo en el rendimiento del sistema debido a la sobrecarga de cifrar y descifrar datos.
- **Gestión de Claves:** LUKS permite gestionar múltiples claves, lo que facilita la rotación de contraseñas sin necesidad de volver a cifrar los datos.
- **Seguridad Física:** Aunque el cifrado protege los datos, es importante también asegurar físicamente los dispositivos y limitar el acceso a usuarios no autorizados.

---

## Preguntas para Reflexionar

1. **¿Por qué es importante el cifrado de discos en entornos empresariales y personales?**
2. **¿Qué podría ocurrir si se pierde la contraseña de un volumen cifrado con LUKS?**
3. **¿Cómo afecta el cifrado al rendimiento del sistema y cómo se puede mitigar este impacto?**
4. **Investiga cómo añadir claves adicionales a un volumen LUKS ya existente. ¿Por qué podría ser útil esta funcionalidad?**

---

## Actividades Adicionales

- **Cifrado de Directorios Específicos:** Explora herramientas como `ecryptfs` para cifrar directorios en lugar de particiones completas.
- **Cifrado del Sistema Completo:** Investiga cómo cifrar todo el disco durante la instalación de AlmaLinux.
- **Integración con TPM:** Averigua cómo integrar LUKS con un Módulo de Plataforma Segura (TPM) para mejorar la seguridad.



## Recursos

- [Documentación de cryptsetup](https://gitlab.com/cryptsetup/cryptsetup/)
- [Guía de Seguridad de AlmaLinux](https://wiki.almalinux.org/security-guide/)
- [Best Practices for LUKS Full Disk Encryption](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/security_hardening/assembly_using-luks-for-full-disk-encryption_security-hardening)
- [Tutorial RedesZone LUKS](https://www.redeszone.net/tutoriales/seguridad/cifrar-discos-particiones-archivos-luks-linux/)
- [Instalación de Almalinux con partición de sistema cifrada](https://blog.alphavps.com/how-to-manually-install-alma-linux-9-with-full-disk-encryption/)
