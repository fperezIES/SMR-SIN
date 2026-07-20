---
icon: material/file-edit
---


# Guía paso a paso para configurar RAID 5 y RAID 6 en AlmaLinux

En esta práctica, aprenderás a configurar RAID 5 y RAID 6 utilizando `mdadm` en AlmaLinux. RAID (Redundant Array of Independent Disks) permite combinar múltiples discos duros en una unidad lógica para mejorar el rendimiento y la redundancia de datos.

## Prerrequisitos

- **Privilegios de superusuario**: Asegúrate de tener acceso root o privilegios de sudo.
- **Múltiples discos duros**: Necesitarás al menos:
  - **RAID 5**: Un mínimo de **3 discos**.
  - **RAID 6**: Un mínimo de **4 discos**.
- **AlmaLinux instalado**: Esta guía asume que estás utilizando AlmaLinux.

**Nota**: Todos los datos en los discos seleccionados serán eliminados. Asegúrate de hacer copias de seguridad si es necesario.

---

## Configuración de RAID 5

### Paso 1: Instalación de mdadm

Primero, instala la herramienta `mdadm` si aún no lo has hecho.

```bash
sudo dnf install mdadm -y
```

### Paso 2: Identificación de los discos

Identifica los discos que utilizarás para el RAID.

```bash
lsblk
```

Supongamos que los discos son `/dev/sdb`, `/dev/sdc` y `/dev/sdd`.

### Paso 3: Borrado de particiones existentes (Opcional)

Si los discos tienen particiones o datos previos, es recomendable limpiarlos.

```bash
sudo wipefs -a /dev/sdb
sudo wipefs -a /dev/sdc
sudo wipefs -a /dev/sdd
```

### Paso 4: Creación del RAID 5

Crea el arreglo RAID 5 usando `mdadm`.

```bash
sudo mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/sdb /dev/sdc /dev/sdd
```

- **/dev/md0**: Dispositivo RAID a crear.
- **--level=5**: Tipo de RAID 5.
- **--raid-devices=3**: Número de dispositivos en el RAID.

### Paso 5: Verificación del estado del RAID

Puedes verificar el progreso de la sincronización:

```bash
watch -n 1 cat /proc/mdstat
```

Presiona `Ctrl+C` para salir.

### Paso 6: Crear un sistema de archivos

Una vez sincronizado, formatea el RAID con el sistema de archivos deseado, por ejemplo, ext4.

```bash
sudo mkfs.ext4 /dev/md0
```

### Paso 7: Crear un punto de montaje y montar el RAID

Crea un directorio para montar el RAID y luego móntalo.

```bash
sudo mkdir -p /mnt/raid5
sudo mount /dev/md0 /mnt/raid5
```

### Paso 8: Configurar montaje automático en el arranque

Primero, agrega la configuración del RAID al archivo mdadm.conf.

```bash
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
```

Actualiza el initramfs.

```bash
sudo dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
```

Luego, agrega la entrada al archivo `/etc/fstab` para montar el RAID automáticamente.

```bash
echo '/dev/md0 /mnt/raid5 ext4 defaults 0 0' | sudo tee -a /etc/fstab
```

### Paso 9: Verificación final

Reinicia el sistema y verifica que el RAID se monte correctamente.

```bash
sudo reboot
```

Después del reinicio:

```bash
df -h | grep /mnt/raid5
```

---

## Configuración de RAID 6

### Paso 1: Instalación de mdadm (si no se hizo antes)

```bash
sudo dnf install mdadm -y
```

### Paso 2: Identificación de los discos

Identifica los discos para el RAID 6, por ejemplo, `/dev/sdb`, `/dev/sdc`, `/dev/sdd`, `/dev/sde`.

### Paso 3: Borrado de particiones existentes (Opcional)

```bash
sudo wipefs -a /dev/sdb
sudo wipefs -a /dev/sdc
sudo wipefs -a /dev/sdd
sudo wipefs -a /dev/sde
```

### Paso 4: Creación del RAID 6

```bash
sudo mdadm --create --verbose /dev/md0 --level=6 --raid-devices=4 /dev/sdb /dev/sdc /dev/sdd /dev/sde
```

### Paso 5: Verificación del estado del RAID

```bash
watch -n 1 cat /proc/mdstat
```

### Paso 6: Crear un sistema de archivos

```bash
sudo mkfs.ext4 /dev/md0
```

### Paso 7: Crear un punto de montaje y montar el RAID

```bash
sudo mkdir -p /mnt/raid6
sudo mount /dev/md0 /mnt/raid6
```

### Paso 8: Configurar montaje automático en el arranque

Agregar configuración al archivo mdadm.conf:

```bash
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
```

Actualizar initramfs:

```bash
sudo dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
```

Agregar entrada a `/etc/fstab`:

```bash
echo '/dev/md0 /mnt/raid6 ext4 defaults 0 0' | sudo tee -a /etc/fstab
```

### Paso 9: Verificación final

Reinicia el sistema y verifica el montaje:

```bash
sudo reboot
```

Después del reinicio:

```bash
df -h | grep /mnt/raid6
```

---

## Comprobación y Administración del RAID

### Ver detalles del RAID

```bash
sudo mdadm --detail /dev/md0
```

### Simular fallo de un disco

Para probar la resiliencia del RAID, puedes simular el fallo de un disco.

```bash
sudo mdadm /dev/md0 --fail /dev/sdb
sudo mdadm /dev/md0 --remove /dev/sdb
```

Luego, agregar un nuevo disco (supongamos `/dev/sdf`):

```bash
sudo mdadm /dev/md0 --add /dev/sdf
```

### Monitoreo del RAID

Puedes configurar alertas de correo electrónico o utilizar herramientas de monitoreo para mantener el seguimiento del estado del RAID.

---



# Cómo Añadir un Disco de Repuesto (Spare Disk) a un Arreglo RAID en AlmaLinux

Un disco de repuesto (spare disk) es un disco adicional que no participa activamente en el arreglo RAID pero está disponible para reemplazar automáticamente a un disco fallido. Esto permite que el arreglo se reconstruya sin intervención manual, minimizando el tiempo de inactividad y aumentando la fiabilidad del sistema.

A continuación, te mostraré cómo añadir un disco de repuesto a tu arreglo RAID utilizando `mdadm` en AlmaLinux.

---

## Prerrequisitos

- **Arreglo RAID existente**: Debes tener un arreglo RAID configurado, como se describió en los pasos anteriores.
- **Disco(s) adicional(es)**: Necesitarás uno o más discos adicionales para actuar como discos de repuesto.
- **Privilegios de superusuario**: Asegúrate de tener acceso root o privilegios de sudo.

---

## Paso 1: Identificar el Disco de Repuesto

Primero, identifica el disco que deseas agregar como disco de repuesto. Puedes utilizar `lsblk` para listar los discos disponibles.

```bash
lsblk
```

Supongamos que el disco que deseas usar como repuesto es `/dev/sde`.

---

## Paso 2: Limpiar el Disco (Opcional)

Si el disco tiene particiones o datos previos, es recomendable limpiarlo.

```bash
sudo wipefs -a /dev/sde
```

---

## Paso 3: Añadir el Disco de Repuesto al Arreglo RAID

### Opción 1: Durante la Creación del Arreglo RAID

Si estás creando un nuevo arreglo RAID y deseas incluir un disco de repuesto desde el inicio, puedes especificarlo en el comando `mdadm --create`.

Por ejemplo, para crear un RAID 5 con un disco de repuesto:

```bash
sudo mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/sdb /dev/sdc /dev/sdd --spare-devices=1 /dev/sde
```

- **--spare-devices=1**: Indica que se añadirá un disco de repuesto.
- **/dev/sde**: Especifica el disco de repuesto.

### Opción 2: Añadir un Disco de Repuesto a un Arreglo RAID Existente

Si ya tienes un arreglo RAID funcionando y deseas añadir un disco de repuesto, utiliza el comando `mdadm --manage --add` con la opción `--spare`.

```bash
sudo mdadm --manage /dev/md0 --add /dev/sde
```

`mdadm` detectará automáticamente que el arreglo está completo y agregará el disco como repuesto.

Puedes verificar que el disco ha sido añadido como repuesto:

```bash
sudo mdadm --detail /dev/md0
```

Deberías ver una sección que indica `Number of Spares : 1` y el disco `/dev/sde` listado como `spare`.

---

## Paso 4: Verificar el Estado del Arreglo RAID

Para confirmar que el disco de repuesto ha sido añadido correctamente, puedes utilizar:

```bash
sudo mdadm --detail /dev/md0
```

La salida debería mostrar información similar a:

```
/dev/md0:
        Version : 1.2
  Creation Time : ...
     Raid Level : raid5
     Array Size : ...
  Used Dev Size : ...
   Raid Devices : 3
  Total Devices : 4
    Persistence : Superblock is persistent

  Intent Bitmap : Internal

    Update Time : ...
          State : clean
 Active Devices : 3
Working Devices : 4
 Failed Devices : 0
  Spare Devices : 1

         Layout : left-symmetric
     Chunk Size : 512K

...

    Number   Major   Minor   RaidDevice State
       0       8       16        0      active sync   /dev/sdb
       1       8       32        1      active sync   /dev/sdc
       2       8       48        2      active sync   /dev/sdd

       4       8       64        -      spare   /dev/sde
```

Aquí puedes ver que `/dev/sde` está listado como `spare`.

---

## Paso 5: Probar el Disco de Repuesto

Para asegurarte de que el disco de repuesto funcionará correctamente en caso de fallo de un disco activo, puedes simular un fallo y observar cómo el disco de repuesto entra en acción.

### Simular un Fallo en un Disco Activo

Supongamos que deseas simular un fallo en `/dev/sdb`:

```bash
sudo mdadm --manage /dev/md0 --fail /dev/sdb
```

Esto marcará el disco `/dev/sdb` como fallido. `mdadm` detectará el fallo y automáticamente utilizará el disco de repuesto `/dev/sde` para reconstruir el arreglo.

### Verificar el Proceso de Reemplazo

Puedes monitorear el estado del arreglo y el proceso de reconstrucción con:

```bash
watch -n 1 cat /proc/mdstat
```

La salida mostrará el progreso de la reconstrucción.

### Verificar el Estado Actual del Arreglo

Después de la reconstrucción, verifica el estado:

```bash
sudo mdadm --detail /dev/md0
```

Deberías ver que `/dev/sde` ahora es un disco activo y `/dev/sdb` está marcado como fallido.

---

## Paso 6: Reemplazar el Disco Fallido (Opcional)

Una vez que hayas reemplazado el disco físico fallido (en este caso `/dev/sdb`), puedes remover el disco fallido del arreglo y agregar el nuevo disco como repuesto o como disco activo.

### Remover el Disco Fallido

```bash
sudo mdadm --manage /dev/md0 --remove /dev/sdb
```

### Agregar el Nuevo Disco como Repuesto

Si deseas agregar un nuevo disco como repuesto:

```bash
sudo mdadm --manage /dev/md0 --add /dev/sdb
```

El nuevo disco `/dev/sdb` se añadirá como repuesto, listo para ser utilizado en caso de futuros fallos.

---

## Paso 7: Actualizar la Configuración de `mdadm`

Es importante actualizar la configuración de `mdadm` para reflejar los cambios en el arreglo, especialmente si has añadido nuevos discos o cambiado la estructura.

Ejecuta el siguiente comando para actualizar el archivo de configuración:

```bash
sudo mdadm --detail --scan | sudo tee /etc/mdadm.conf
```

Luego, actualiza el `initramfs`:

```bash
sudo dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
```

---

## Paso 8: Configurar Montaje Automático (si es necesario)

Si el arreglo RAID ha cambiado de configuración o dispositivo, verifica que la entrada correspondiente en `/etc/fstab` siga siendo válida.

---

## Consejos Adicionales

- **Múltiples Discos de Repuesto**: Puedes agregar más de un disco de repuesto si lo deseas, simplemente añádelos utilizando el comando `mdadm --manage /dev/md0 --add /dev/sdf`.

- **Monitoreo Activo**: Mantén monitoreado el estado de tus arreglos RAID y los discos de repuesto utilizando `mdadm --detail` y revisando `/proc/mdstat`.

- **Alertas de Correo Electrónico**: Asegúrate de tener configuradas las alertas de correo electrónico para ser notificado en caso de fallos y reemplazos automáticos.




---

Si tienes más preguntas o necesitas asistencia adicional, no dudes en preguntar.

# (Opcional) Cómo Configurar Alertas de Correo Electrónico para el Monitoreo de RAID en AlmaLinux

Configurar alertas de correo electrónico es fundamental para monitorear el estado de tus arreglos RAID y ser notificado inmediatamente en caso de fallos o problemas. A continuación, te mostraré cómo configurar `mdadm` para enviar alertas por correo electrónico en AlmaLinux.

## Prerrequisitos

- **Configuración previa del RAID**: Asegúrate de haber configurado tu RAID como se describió anteriormente.
- **Servidor de correo electrónico**: Necesitas tener un agente de transferencia de correo (MTA) instalado y configurado para enviar correos electrónicos desde tu sistema. Puedes utilizar Postfix, Sendmail, SSMTP o cualquier otro MTA de tu preferencia.

---

## Paso 1: Instalar un Agente de Transferencia de Correo (MTA)

### Opción 1: Usar Postfix

Instala Postfix si aún no lo tienes:

```bash
sudo dnf install postfix -y
```

Configura Postfix para enviar correos electrónicos. Durante la instalación, se te pedirá que elijas el tipo de configuración. Selecciona **"Sitio de Internet"** y proporciona el nombre de dominio adecuado para tu servidor.

Inicia y habilita el servicio de Postfix:

```bash
sudo systemctl start postfix
sudo systemctl enable postfix
```

### Opción 2: Usar SSMTP (Más sencillo)

Si prefieres una configuración más simple y quieres usar un servidor SMTP externo (como Gmail), puedes usar `ssmtp`.

Instala SSMTP:

```bash
sudo dnf install ssmtp -y
```

Configura SSMTP editando el archivo `/etc/ssmtp/ssmtp.conf`:

```bash
sudo nano /etc/ssmtp/ssmtp.conf
```

Agrega la siguiente configuración (ejemplo para Gmail):

```
root=tu_correo@gmail.com
mailhub=smtp.gmail.com:587
AuthUser=tu_correo@gmail.com
AuthPass=tu_contraseña
UseSTARTTLS=YES
```

**Nota**: Si usas Gmail, es posible que necesites habilitar el acceso de aplicaciones menos seguras o utilizar una contraseña de aplicación.

---

## Paso 2: Configurar `mdadm` para Enviar Alertas por Correo Electrónico

Edita el archivo de configuración de `mdadm`:

```bash
sudo nano /etc/mdadm.conf
```

Agrega o modifica la línea que especifica la dirección de correo electrónico para las alertas:

```
MAILADDR tu_correo@ejemplo.com
```

Guarda y cierra el archivo.

---

## Paso 3: Iniciar el Servicio de Monitoreo de `mdadm`

El servicio de monitoreo de `mdadm` debe estar en ejecución para que se envíen las alertas. Inicia y habilita el servicio:

```bash
sudo systemctl start mdmonitor
sudo systemctl enable mdmonitor
```

Verifica el estado del servicio:

```bash
sudo systemctl status mdmonitor
```

---

## Paso 4: Probar la Configuración de Alertas

Para asegurarte de que las alertas funcionan correctamente, puedes simular un fallo en uno de los discos del RAID.

### Simular un Fallo en un Disco

Supongamos que tienes un RAID con `/dev/sdb` como uno de los discos. Puedes simular un fallo así:

```bash
sudo mdadm --manage /dev/md0 --fail /dev/sdb
```

Esto debería desencadenar una alerta por correo electrónico.

### Verificar los Correos Electrónicos

Revisa la bandeja de entrada del correo electrónico que configuraste para recibir alertas. Deberías haber recibido un mensaje de `mdadm` notificándote del fallo en el disco.

### Revertir el Fallo Simulado

Después de la prueba, puedes remover el disco fallido y agregarlo nuevamente:

```bash
sudo mdadm --manage /dev/md0 --remove /dev/sdb
sudo mdadm --manage /dev/md0 --add /dev/sdb
```

---

## Paso 5: Configuración Adicional (Opcional)

### Personalizar los Parámetros de Monitoreo

Puedes ajustar cómo `mdadm` monitorea los arreglos RAID editando el archivo de configuración o modificando el servicio de monitoreo.

Edita el archivo `/etc/sysconfig/mdmonitor` (en algunas distribuciones puede ser `/etc/default/mdadm`):

```bash
sudo nano /etc/sysconfig/mdmonitor
```

Asegúrate de que las opciones reflejen tus necesidades.

### Configurar Reintentos de Envío de Correo

Si estás usando Postfix u otro MTA, puedes configurar reintentos en caso de que el correo electrónico no se pueda enviar en el primer intento.

---

## Solución de Problemas

### No Se Envía el Correo Electrónico

- **Verifica el MTA**: Asegúrate de que el agente de correo electrónico esté correctamente configurado y en ejecución.
- **Logs del Sistema**: Revisa los registros en `/var/log/maillog` o `/var/log/mail.log` para identificar posibles errores.
- **Configuración de `mdadm`**: Confirma que la dirección de correo electrónico en `/etc/mdadm.conf` es correcta y que no hay errores tipográficos.

### Problemas con Gmail y SSMTP

Si estás utilizando Gmail y SSMTP, recuerda que Google ha deshabilitado el acceso de aplicaciones menos seguras. Deberás utilizar una contraseña de aplicación:

1. Habilita la autenticación de dos factores en tu cuenta de Google.
2. Genera una contraseña de aplicación para SSMTP.
3. Utiliza esta contraseña en el campo `AuthPass` en `/etc/ssmtp/ssmtp.conf`.

---

## Consejos Adicionales

- **Usa un Correo Corporativo**: Para sistemas de producción, es recomendable utilizar una dirección de correo electrónico corporativa o un servidor SMTP dedicado.
- **Monitoreo Centralizado**: Considera integrar el monitoreo de RAID con herramientas como Nagios, Zabbix o Prometheus para un monitoreo más completo.
- **Pruebas Regulares**: Programa pruebas periódicas para asegurar que las alertas funcionan correctamente.

---

# (Opcional) Ampliar el número de discos de un RAID 5

Para aumentar el número de discos en un RAID 5 con `mdadm`, es posible añadir discos adicionales al arreglo existente y expandir la capacidad del RAID. A continuación, te presento una guía paso a paso para lograrlo:

### Prerrequisitos

- **Arreglo RAID 5 existente**: Debe haber un RAID 5 ya configurado con `mdadm`.
- **Disco(s) adicional(es)**: Necesitarás uno o más discos nuevos para añadir al RAID.
- **Copias de seguridad**: Es recomendable tener una copia de seguridad actualizada de los datos antes de modificar el RAID.

### Pasos para añadir un disco a un RAID 5 con `mdadm`

1. **Verifica el estado actual del RAID**  
   Antes de hacer cualquier cambio, comprueba que el RAID esté en buen estado.

   ```bash
   sudo mdadm --detail /dev/md0
   ```

   Sustituye `/dev/md0` con el nombre de tu dispositivo RAID si es diferente.

2. **Añade el nuevo disco al RAID**  
   Supongamos que el disco que deseas añadir es `/dev/sde`.

   ```bash
   sudo mdadm --manage /dev/md0 --add /dev/sde
   ```

   Esto agregará el disco al arreglo, pero todavía no formará parte activa del RAID 5. Para integrarlo completamente, es necesario expandir el RAID.

3. **Expandir el arreglo RAID**  
   Para ampliar el arreglo RAID para que incluya el nuevo disco, utiliza el siguiente comando:

   ```bash
   sudo mdadm --grow /dev/md0 --raid-devices=4
   ```

   Aquí, `--raid-devices=4` indica el nuevo número total de discos en el RAID. Ajusta el número según la cantidad total de discos que deseas tener en el arreglo.

4. **Ampliar el sistema de archivos**  
   Después de expandir el arreglo, también es necesario ampliar el sistema de archivos para utilizar el nuevo espacio disponible. Si estás utilizando `ext4`, puedes hacer lo siguiente:

   ```bash
   sudo resize2fs /dev/md0
   ```

   Si estás utilizando otro sistema de archivos, usa el comando adecuado para ampliar su tamaño.

5. **Verificar el estado del RAID**  
   Es recomendable monitorizar el progreso de la expansión del RAID, que puede tardar algún tiempo dependiendo del tamaño de los discos y de la cantidad de datos.

   ```bash
   watch -n 1 cat /proc/mdstat
   ```

   Presiona `Ctrl+C` para salir una vez que el proceso de expansión haya finalizado.

6. **Confirmar la nueva configuración**  
   Una vez completado el proceso, verifica que el RAID ha sido ampliado correctamente.

   ```bash
   sudo mdadm --detail /dev/md0
   ```

   Deberías ver el nuevo disco añadido como parte activa del arreglo y el espacio total disponible reflejando la capacidad adicional.

### Consideraciones adicionales

- **Tiempo de reconstrucción**: La expansión del RAID puede tardar bastante tiempo, durante el cual el rendimiento del sistema podría verse afectado.
- **Pruebas regulares**: Después de cualquier modificación en el RAID, asegúrate de hacer pruebas periódicas para verificar la integridad del arreglo.
- **Copia de seguridad**: Siempre es recomendable tener una copia de seguridad antes de realizar cambios en un RAID.


---

## Conclusión

Has aprendido a configurar RAID 5 y RAID 6 en AlmaLinux utilizando `mdadm`. Esta configuración proporciona redundancia y mejora la integridad de los datos. Siempre es importante monitorear el estado de tus arreglos RAID y tener planes de respaldo adecuados.

Has configurado con éxito alertas de correo electrónico para el monitoreo de tus arreglos RAID en AlmaLinux. Esta configuración te permitirá recibir notificaciones inmediatas en caso de fallos, lo que es crucial para mantener la integridad y disponibilidad de tus datos.

Añadir un disco de repuesto a tu arreglo RAID es una práctica recomendada para mejorar la resiliencia y disponibilidad de tus datos. Con `mdadm`, puedes gestionar de forma flexible los discos activos y de repuesto en tus arreglos RAID, permitiendo una respuesta automática ante fallos y minimizando el tiempo de inactividad.

---

# Bibliografía

- [Guía RAID de RedHat 9](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/managing_storage_devices/managing-raid_managing-storage-devices#creating-a-software-raid-on-an-installed-system_managing-raid)

- [Guía RAID mdadm Debian](https://www.digitalocean.com/community/tutorials/how-to-create-raid-arrays-with-mdadm-on-debian-9#creating-a-complex-raid-10-array)