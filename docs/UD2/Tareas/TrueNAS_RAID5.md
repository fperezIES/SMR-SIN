---
icon: material/file-edit
---


# Práctica de RAID 5 con TrueNAS, NAS y SAN usando VirtualBox

![](TrueNAS_logo.svg){:class="center"}

## Objetivo de la práctica:

El objetivo de esta práctica es que los estudiantes configuren un sistema RAID 5 (RAID-Z1) utilizando TrueNAS, simulen un fallo de disco y comprueben la integridad y disponibilidad de los datos durante la reconstrucción del RAID. Además, se configurará un sistema de almacenamiento compartido (NAS) y almacenamiento por bloques (SAN) para comprobar su accesibilidad antes, durante y después del fallo.

### Informe Escrito

Debes entregar un fichero PDF que contenga un informe sobre la realización de la práctica con los siguientes apartados:

- **Introducción**: Descripción breve de los conceptos que han aplicado durante la práctica (RAID-Z, NAS, SAN, TrueNAS, etc.).
    
- **Descripción de la configuración**: Explicación detallada de cómo configuraron cada parte de la práctica:
    
    - **Máquina virtual en VirtualBox**: Especificaciones de la VM (memoria, discos, red).
    - **Configuración de RAID-Z1**: Cómo crearon el RAID-Z1 (RAID 5) y el pool de discos en TrueNAS.
    - **Configuración de NAS y SAN**: Cómo crearon el almacenamiento compartido (carpeta SMB) y por bloques (iSCSI), incluyendo los servicios que habilitaron (SMB e iSCSI).
- **Simulación de fallo y recuperación**:
    
    - Explicación sobre cómo simularon el fallo de un disco, el estado del RAID tras el fallo, y el proceso de recuperación (añadir un nuevo disco y reconstruir el RAID).
    - Comprobación de la accesibilidad a los datos durante la reconstrucción.

### Capturas de Pantalla

Se deben incluir en la  capturas de pantalla que documenten cada paso del proceso:

1. **Configuración de la máquina virtual en VirtualBox**:
    
    - Muestra los discos añadidos y la memoria asignada.
2. **Configuración de RAID-Z1 en TrueNAS**:
    
    - Captura de la creación del pool RAID-Z1 con los discos seleccionados.
3. **Configuración de NAS y SAN**:
    
    - Captura del panel de TrueNAS mostrando la creación de los Datasets para NAS (carpeta compartida) y SAN (iSCSI).
    - Captura de la pantalla donde habilitan los servicios SMB e iSCSI.
4. **Simulación del fallo de disco**:
    
    - Captura del estado degradado del RAID en TrueNAS, donde se muestra el fallo de un disco.
5. **Proceso de reconstrucción del RAID**:
    
    - Captura del proceso de sustitución del disco y reconstrucción del RAID.
6. **Verificación de accesibilidad de los datos**:
    
    - Captura de la carpeta compartida (NAS) accesible desde el explorador de archivos en su equipo, con los archivos creados antes del fallo.
    - Captura de la unidad iSCSI montada en el cliente iSCSI (Windows o Linux), mostrando que el almacenamiento por bloques es accesible.



---
### Introducción a TrueNAS

[**TrueNAS**](https://www.truenas.com) es una plataforma de almacenamiento en red (NAS) y almacenamiento por bloques (SAN) basada en el sistema de archivos ZFS, diseñada para ofrecer una gestión de datos robusta, segura y eficiente. Es una solución de código abierto que puede implementarse en redes locales para compartir archivos, gestionar copias de seguridad, crear volúmenes virtuales y ofrecer servicios iSCSI. Gracias a su enfoque en la redundancia y la protección de datos, TrueNAS es ideal tanto para entornos domésticos como empresariales.

TrueNAS es conocido por su capacidad para gestionar grandes volúmenes de datos con alta fiabilidad, lo que lo convierte en una solución popular para administradores de sistemas que necesitan proteger y gestionar el almacenamiento de manera eficaz. Entre sus características destacan:

- **Compatibilidad con RAID-Z**: Implementación avanzada de RAID que ofrece tolerancia a fallos y recuperación rápida.
- **Almacenamiento unificado**: Soporta tanto sistemas NAS como SAN, permitiendo la gestión centralizada de múltiples tipos de almacenamiento.
- **Snapshots y replicación**: Funcionalidades para proteger los datos contra errores humanos, corrupciones o ciberataques.

### Versiones de TrueNAS

TrueNAS se presenta en dos versiones principales:

1. **TrueNAS CORE**:
    
    - Esta es la versión más conocida y está basada en FreeBSD. Está diseñada para soluciones de almacenamiento unificado, combinando NAS y SAN, con soporte completo para ZFS, RAID-Z y otros mecanismos avanzados de protección de datos.
    - **Uso recomendado**: Ideal para pequeñas y medianas empresas, así como para usuarios domésticos avanzados que necesitan un sistema de almacenamiento robusto y eficiente.
    - **Características destacadas**:
        - Soporte para plugins y virtualización ligera.
        - Enfoque en sistemas NAS tradicionales.
        - Comunidad activa de usuarios y soporte gratuito.
2. **TrueNAS SCALE**:
    
    - Basado en Linux, TrueNAS SCALE es una versión más reciente que incorpora características avanzadas como escalabilidad en clúster y soporte nativo para virtualización y contenedores (Docker y Kubernetes).
    - **Uso recomendado**: Empresas y entornos que requieren mayor flexibilidad y escalabilidad, especialmente en almacenamiento distribuido.
    - **Características destacadas**:
        - Soporte nativo para máquinas virtuales y contenedores.
        - Escalabilidad en clústeres.
        - Flexibilidad para almacenamiento híbrido y entornos distribuidos.

Ambas versiones permiten a los usuarios aprovechar las fortalezas del sistema de archivos ZFS, con su enfoque en la integridad de los datos y la protección contra fallos de hardware. Además, TrueNAS SCALE introduce nuevas posibilidades para empresas que buscan soluciones de almacenamiento escalables y flexibles en entornos modernos.

### RAID-Z

**RAID-Z** es una implementación de RAID (Redundant Array of Independent Disks) que utiliza el sistema de archivos **ZFS**. Fue diseñado por Sun Microsystems como una mejora sobre los niveles tradicionales de RAID, ofreciendo mayor integridad y protección de datos. RAID-Z se usa comúnmente en sistemas como **TrueNAS** debido a su capacidad para gestionar grandes volúmenes de datos con alta fiabilidad y eficiencia.

### Características de RAID-Z

1. **Sin escritura de paridad desalineada (write hole)**:
   A diferencia de los niveles tradicionales de RAID, como RAID 5, RAID-Z está diseñado para evitar el problema conocido como "escritura desalineada" o "write hole". Este problema ocurre cuando una escritura interrumpida (por ejemplo, debido a un apagón) deja la paridad inconsistente, lo que puede corromper los datos. RAID-Z maneja las escrituras de manera transaccional, lo que garantiza la coherencia entre los datos y la paridad en todo momento.

2. **Integridad de los datos**:
   RAID-Z usa el sistema de archivos ZFS, que implementa una verificación de los datos con **sumas de verificación** en cada bloque almacenado. Esto permite detectar y corregir automáticamente errores de datos en caso de corrupción, lo que ofrece una protección adicional sobre los RAID tradicionales.

3. **Redundancia y tolerancia a fallos**:
   RAID-Z permite la pérdida de uno o más discos sin pérdida de datos, dependiendo de la configuración específica de RAID-Z:
   - **RAID-Z1**: Similar a RAID 5, permite el fallo de **un solo disco**.
   - **RAID-Z2**: Similar a RAID 6, permite el fallo de **hasta dos discos**.
   - **RAID-Z3**: Permite  el fallo de **hasta tres discos**, proporcionando un nivel muy alto de tolerancia a fallos.

4. **Gestión de espacio eficiente**:
   RAID-Z utiliza un enfoque más eficiente en la gestión del espacio, optimizando la distribución de la paridad y los datos, lo que minimiza la sobrecarga.

### Ventajas de RAID-Z frente a RAID tradicional

- **Recuperación rápida**: RAID-Z está diseñado para maximizar el rendimiento de la reconstrucción de discos fallidos, reduciendo los tiempos de recuperación.
- **Gestión de bloques variable**: A diferencia de RAID 5, que utiliza un tamaño de bloque fijo para los datos y la paridad, RAID-Z ajusta dinámicamente el tamaño de los bloques de datos, mejorando el uso del espacio y el rendimiento.
- **No requiere escritura previa a la paridad**: RAID-Z escribe los datos y la paridad de una sola vez, eliminando la necesidad de realizar cálculos adicionales antes de la escritura.

### Usos comunes de RAID-Z

RAID-Z se utiliza comúnmente en entornos de almacenamiento donde la **seguridad e integridad de los datos** son fundamentales, como en servidores NAS (almacenamiento en red), servidores empresariales y centros de datos. Su implementación en sistemas como TrueNAS lo hace ideal para administrar grandes volúmenes de datos con un alto nivel de protección, siendo una elección popular para configuraciones de almacenamiento críticas.

En resumen, RAID-Z es una solución avanzada de RAID que combina la flexibilidad y eficiencia del sistema de archivos ZFS con la redundancia y protección de datos, superando algunas de las limitaciones presentes en los RAID tradicionales.


---

### Parte 1: Preparación del entorno

#### 1. Instalación de VirtualBox y creación de una máquina virtual para TrueNAS

1. **Descargar VirtualBox**:
    
    - Descargar e instalar VirtualBox desde [virtualbox.org](https://www.virtualbox.org).
2. **Crear una nueva máquina virtual**:
    
    - Abre VirtualBox y selecciona `Nueva`.
    - Asigna un nombre descriptivo, por ejemplo, "TrueNAS RAID".
    - Tipo: Selecciona **BSD**.
    - Versión: Selecciona **FreeBSD (64-bit)**.
    - Asigna **2 GB de memoria RAM** ( 8 GB mínimo recomendado en producción) y continúa.
3. **Configuración de disco virtual**:
    
    - Selecciona "Crear un disco duro virtual ahora" y luego "VDI (VirtualBox Disk Image)".
    - Tipo de almacenamiento: "Dinamicamente asignado".
    - Tamaño del disco: Asegura al menos 20 GB para el sistema operativo TrueNAS.
4. **Añadir discos adicionales**:
    
    - Selecciona la máquina virtual creada, ve a "Configuración" y selecciona "Almacenamiento".
    - Añade tres discos adicionales para crear el RAID 5:
        - Haciendo clic en el ícono "Añadir disco duro". (Es posible que necesites añadir un nuevo controlador también.)
        - Tamaño de los discos adicionales: Al menos **5 GB cada uno** (puedes usar discos de mayor capacidad si lo deseas).
5. **Configurar red**:
    
    - Ve a la sección "Red" en la configuración de la máquina virtual.
    - Asegúrate de seleccionar "Adaptador puente" para conectar TrueNAS a la red local.
6. **Instalación del sistema TrueNAS**:
    
    - Descarga la ISO de **TrueNAS CORE** desde [truenas.com](https://www.truenas.com).
    - En "Almacenamiento", selecciona la ISO de TrueNAS como unidad de arranque (óptica).
    - Inicia la máquina virtual y sigue las instrucciones de instalación de TrueNAS.
    - Durante la instalación, selecciona el disco de 20 GB para instalar el sistema.

---

### Parte 2: Configuración de TrueNAS y creación del RAID 5

#### 1. Configuración inicial de TrueNAS

1. **Acceso a la interfaz de TrueNAS**:
    
    - Después de la instalación, TrueNAS mostrará una dirección IP.
    - Conéctate a esa IP desde un navegador en tu equipo local.
    - Inicia sesión con las credenciales que configuraste durante la instalación. (usr: root, pass: especificado durante instalación)
2. **Configuración de discos y creación del RAID 5**:
    
    - Ve a la pestaña "Storage" (almacenamiento) y selecciona "Pools".
    - Haz clic en "Add" para crear un nuevo pool.
    - Selecciona los tres discos adicionales que añadiste en VirtualBox.
    - Elige la opción **RAID-Z1** (RAID 5) para distribuir los datos con paridad entre los discos.
    - Asigna un nombre al pool, como "RAID5_Pool", y haz clic en "Create".

---

### Parte 3: Creación de carpetas compartidas (NAS) y almacenamiento por bloques (SAN)

#### 1. Configuración de carpetas compartidas (NAS)

1. **Crear Dataset para NAS**:
    
    - En "Storage" > "Pools", selecciona tu pool recién creado y haz clic en "Add Dataset".
    - Asigna un nombre, como "Compartido_NAS".
    - Deja las configuraciones por defecto y haz clic en "Save".
2. **Habilitar servicio SMB para compartir archivos**:
    
    - Ve a "Services" en el menú principal y activa el servicio **SMB** (Samba).
    - Crea un usuario para asignarle permisos a la carpeta compartida.
    -  Configura las credenciales (Edit Share ACL) para poder leer y escribir en el recurso compartido (tendrás que crear un nuevo usuario previamente).
    - Ve a "Sharing" > "Windows Shares (SMB)". Haz clic en "Add" y selecciona el Dataset creado previamente, "Compartido_NAS", como el directorio compartido.
    -
3. **Acceso desde el equipo local**:
    
    - En el explorador de archivos de tu equipo local, accede a la carpeta compartida ingresando la dirección IP de TrueNAS precedida por dos barras inclinadas (por ejemplo, `\\192.168.1.100\Compartido_NAS`).
    - Crea un archivo o carpeta dentro del recurso compartido para probar el acceso.

#### 2. Configuración de almacenamiento por bloques (SAN)

1. **Crear Dataset para SAN**:
    
    - En "Storage" > "Pools", selecciona tu pool y añade otro Dataset, llamado "Compartido_SAN".
2. **Configurar iSCSI (SAN)**:  
    - Ve a "Sharing" > "Block Shares (iSCSI)" y usa el botón "Wizard" que te guiará por los diferentes pasos:
    -
		1. "Create or Choose BlockDevice": Asigna un nombre y en "Device" usa "Create New" para elegir el dataset creado previamente (Compartido_SAN). En "Size" especifica el tamaño del disco. En "Sharin Platform" selecciona "Modern OS".
		2. "Portal": usa "Create New", añade un nombre y Authentication "NONE". Elige la IP en la que se anuncirá. Puedes usar 0.0.0.0 para que se anuncie en todas las interfaces). 
		3. (Opcional) Alternativamente puedes usar autenticación CHAP: crea un nuevo "Discovery Authentication Group", "usa Group ID" 1, añade un nombre de usuario y un "Secret", luego tendrás que usar estas credenciales al descubrir el volumen desde el cliente.
		4. En "Initiators" puedes limitar los hosts y redes desde los que se conectará el dispositivo. En nuestro caso lo dejaremos sin cambiar.
		5. Confirma los ajustes.
        
3. **Acceso desde un cliente iSCSI**:
    
    - Desde otro sistema, configura un cliente iSCSI para conectarte al Target y verifica que puedas acceder y escribir en el almacenamiento por bloques. Tienes instrucciones de cómo hacerlo en AlmaLinux, Windows y Windows Server más adelante.

---

### Parte 4: Simulación de fallo y reconstrucción de RAID

#### 1. Simular un fallo de disco

1. **Apagar la máquina virtual**:
    
    - Apaga TrueNAS y ve a la configuración de la máquina virtual en VirtualBox.
    - Ve a la pestaña "Almacenamiento" y elimina uno de los discos adicionales (simulando un fallo).
2. **Encender la máquina virtual y comprobar el estado del RAID**:
    
    - Inicia TrueNAS nuevamente.
    - Ve a "Storage" > "Pools", y deberías ver el pool en estado "Degraded" (degradado).
    - TrueNAS te notificará del fallo en uno de los discos.


#### 2. Verificar la accesibilidad durante la reconstrucción

Aunque el RAID esté  en estado degradado es posible seguir usándolo, mientras se reconstruye. Esto aumentará el tiempo de reconstrucción.

1. **Comprobar el acceso a la carpeta compartida (NAS)**:
    
    - Accede nuevamente a la carpeta compartida desde tu equipo local.
    - Intenta crear o modificar archivos para comprobar la integridad de los datos durante la reconstrucción.
2. **Comprobar el acceso al almacenamiento por bloques (SAN)**:
    
    - Accede al almacenamiento iSCSI desde el cliente configurado.
    - Verifica que los datos siguen siendo accesibles.
#### 3. Reconstruir el RAID

1. **Añadir un nuevo disco**:
    
    - Apaga nuevamente la máquina virtual.
    - Añade un nuevo disco en VirtualBox como sustituto del que falló.
2. **Reemplazar el disco fallido en TrueNAS**:
    
    - Inicia TrueNAS y accede a "Storage" > "Pools".
    - Selecciona el pool degradado y elige la opción "Status" desde el menú de la rueda dentada. Busca el disco duro que falla y usa la opción "Replace" en el disco fallido.
    - Selecciona el disco nuevo añadido para iniciar el proceso de reconstrucción.



---

## Configurar un **Spare Disk** en TrueNAS (Opcional)

Un **Spare Disk** es un disco de reserva que se configura en un sistema RAID para reemplazar automáticamente a un disco que falle. Cuando uno de los discos en el conjunto RAID deja de funcionar, el disco de repuesto (Spare Disk) se activa y se utiliza para reemplazar el disco fallido, iniciando de manera automática el proceso de reconstrucción sin intervención manual. A continuación, te explico cómo configurar un **Spare Disk** en **TrueNAS**.

#### **Pasos para configurar un Spare Disk en TrueNAS**

### 1. Preparar el entorno de almacenamiento

Antes de configurar un Spare Disk, es importante que ya tengas un pool configurado con algún tipo de redundancia, como RAID-Z1, RAID-Z2 o RAID-Z3, donde se pueda añadir el disco de repuesto.

### 2. Añadir un disco de repuesto (Spare Disk)

1. **Acceder a la interfaz de administración de TrueNAS**:
    
    - Inicia sesión en la interfaz web de TrueNAS utilizando tu navegador.
2. **Ir a la sección de almacenamiento**:
    
    - Ve a **"Storage" (Almacenamiento)** en el menú principal y selecciona la opción **"Pools"**.
3. **Seleccionar el pool de almacenamiento**:
    
    - Elige el pool de discos RAID-Z ya existente en el que deseas añadir el Spare Disk.
    - Haz clic en la rueda dentada al lado del nombre del pool y selecciona **"Status" (Estado)** para ver los detalles del pool.
4. **Añadir un disco de repuesto**:
    
    - En la parte superior derecha de la pantalla de estado del pool haz click en la rueda dentada, luego, haz clic en **"Add Vdevs"**.
    - Aparecerá una ventana emergente donde puedes añadir diferentes tipos de VDEVs (Virtual Device) al pool.
    - Selecciona la opción **"Hot Spare"** en el menú de la izquierda.
5. **Seleccionar el disco a utilizar como Spare Disk**:
    
    - En la lista de discos disponibles, selecciona el disco que quieres usar como Spare Disk. Este disco no debe estar actualmente en uso en el pool.
    - Asegúrate de que el disco tiene suficiente capacidad y está sin formatear, ya que se usará para reemplazar un disco en caso de fallo.
6. **Confirmar la operación**:
    
    - Haz clic en **"Add" (Añadir)** para confirmar la adición del Spare Disk al pool.
7. **Verificar el Spare Disk**:
    
    - Una vez añadido, deberías poder ver el disco de repuesto (Spare Disk) listado en el estado del pool. Aparecerá como un disco en reserva que no está siendo utilizado hasta que sea necesario.

### 3. Probar el Spare Disk

Para comprobar que el Spare Disk funciona correctamente, puedes simular un fallo de disco en el pool (como ya hiciste en la práctica de RAID-Z). Cuando un disco del pool falle, el sistema debería automáticamente activar el Spare Disk para reemplazar al disco defectuoso y comenzar el proceso de reconstrucción.

1. **Simular un fallo de disco**:
    
    - Apaga la máquina virtual (o el servidor físico) y desconecta uno de los discos que forman parte del pool RAID-Z.
    - Inicia TrueNAS de nuevo y observa el estado del pool. El sistema debería detectar el disco fallido y automáticamente empezar a utilizar el Spare Disk.
2. **Verificar la reconstrucción**:
    
    - Ve a la sección de **"Status"** del pool en TrueNAS y deberías ver que el Spare Disk está activo y el sistema está reconstruyendo los datos desde los discos restantes.
    - Puedes monitorizar el proceso de reconstrucción y ver cómo el Spare Disk asume el rol del disco fallido.

### 4. Consideraciones adicionales

- **Sustituir el disco fallido**: Aunque el Spare Disk reemplaza automáticamente al disco defectuoso, es importante que reemplaces el disco fallido lo antes posible por otro nuevo para mantener una configuración de redundancia adecuada. Una vez que añadas el nuevo disco, puedes volver a configurar un Spare Disk para futuras fallos.
    
- **Notificaciones**: Asegúrate de tener configuradas las notificaciones en TrueNAS (a través de correo electrónico, por ejemplo) para que te avise en caso de fallos de discos o la activación del Spare Disk.

---

## Conectar iSCSI en AlmaLinux (Opcional)

Conectar un almacenamiento iSCSI en **AlmaLinux** implica algunos pasos clave que incluyen la instalación de los paquetes necesarios, la configuración del cliente iSCSI (iniciador), y la conexión al dispositivo de almacenamiento iSCSI (objetivo). Aquí te explico los pasos detallados para hacerlo:

### Paso 1: Instalar el paquete iSCSI

1. **Actualizar el sistema**:
   Asegúrate de que tu sistema está actualizado ejecutando los siguientes comandos:
   
   ```bash
   sudo dnf update
   ```

2. **Instalar los paquetes necesarios para iSCSI**:
   Instala los paquetes que te permitirán conectarte a un dispositivo iSCSI:

   ```bash
   sudo dnf install iscsi-initiator-utils
   ```

### Paso 2: Configurar el iniciador iSCSI


1. **Configurar el servicio iSCSI**:
   Asegúrate de que el servicio iSCSI esté habilitado para iniciarse automáticamente al arrancar el sistema:

   ```bash
   sudo systemctl enable iscsid
   sudo systemctl start iscsid
   ```

2. **Ver el nombre del iniciador iSCSI**:
   El iniciador iSCSI tiene un nombre único (IQN) que se utiliza para identificar el cliente iSCSI. Para ver el IQN de tu sistema, utiliza el siguiente comando:

   ```bash
   sudo cat /etc/iscsi/initiatorname.iscsi
   ```

   Esto debería mostrar algo como:

```bash
   InitiatorName=iqn.1994-05.com.redhat:xxxxxxx
```

### Paso 3.1 Configurar la autenticación CHAP (Si es necesario)

Una vez que hayas identificado el destino iSCSI correcto, configura la autenticación CHAP (Si es necesario). Necesitarás editar el archivo de configuración iSCSI para agregar las credenciales CHAP.

1. **Editar la configuración de iSCSI:**
	Abre el archivo de configuración del objetivo iSCSI:

``` bash
    `sudo nano /etc/iscsi/iscsid.conf
```

2. **Habilitar la autenticación CHAP** comentando o descomentando las siguientes líneas (dependiendo de tu configuración actual):
    
```bash 
node.session.auth.authmethod = CHAP 
node.session.auth.chap_algs = SHA3-256,SHA256,SHA1,MD5
node.session.auth.username = <tu_usuario_CHAP> node.session.auth.password = <tu_password_CHAP
```

3. **Guardar los cambios** y cerrar el editor. (Ctrl+O, Ctrl+X)
### Paso 3.2: Descubrir el objetivo iSCSI (Target)

1. **Descubrir los dispositivos iSCSI disponibles**:
   Para conectarte a un servidor iSCSI (Target), primero debes descubrir los objetivos disponibles. Utiliza el siguiente comando, donde `<IP_del_servidor>` es la dirección IP del servidor TrueNAS u otro dispositivo que esté actuando como servidor iSCSI.

   ```bash
   sudo iscsiadm --mode discovery --type sendtargets --portal <IP_del_servidor>
   ```

   Esto debería devolver una lista de objetivos iSCSI disponibles en el servidor.


### Paso 4: Iniciar sesión en el objetivo iSCSI

1. **Iniciar sesión en el dispositivo iSCSI**:
   Después de descubrir los objetivos, puedes conectarte al objetivo deseado utilizando su IQN (identificador único del objetivo iSCSI) y el portal de destino:

   ```bash
   sudo iscsiadm --mode node --targetname <nombre_del_objetivo> --portal <IP_del_servidor> --login
   ```

   Ejemplo:
   ```bash
   sudo iscsiadm --mode node --targetname iqn.2005-03.com.example:storage.target01 --portal 192.168.1.100 --login
   ```

2. **Verificar la conexión**:
   Para comprobar que la conexión se realizó correctamente, puedes utilizar el siguiente comando para listar las sesiones iSCSI activas:

   ```bash
   sudo iscsiadm --mode session
   ```

### Paso 5: Formatear y montar el dispositivo iSCSI

1. **Listar los nuevos dispositivos**:
   Una vez conectado, el sistema debería haber detectado el nuevo dispositivo de bloque asociado con el objetivo iSCSI. Para verlo, puedes utilizar `lsblk` o `fdisk`:

   ```bash
   sudo lsblk
   ```

   El nuevo dispositivo probablemente será algo como `/dev/sdb` o similar.

2. **Formatear el dispositivo**:
   Si el dispositivo iSCSI es nuevo o no tiene un sistema de archivos, puedes formatearlo con el sistema de archivos deseado, por ejemplo, ext4:

   ```bash
   sudo mkfs.ext4 /dev/sdX
   ```

   Asegúrate de reemplazar `sdX` con el identificador correcto de tu dispositivo.

3. **Montar el dispositivo**:
   Crea un punto de montaje y monta el dispositivo iSCSI:

   ```bash
   sudo mkdir /mnt/iscsi
   sudo mount /dev/sdX /mnt/iscsi
   ```

4. **Verificar el montaje**:
   Puedes verificar que el dispositivo esté correctamente montado con el comando `df`:

   ```bash
   df -h
   ```

   Deberías ver el nuevo dispositivo iSCSI montado en el punto de montaje que elegiste.

### Paso 6: Desconectar el objetivo iSCSI

Cuando ya no necesites usar el dispositivo iSCSI, puedes desconectar el objetivo de la siguiente manera:

1. **Desmontar el dispositivo**:
   Primero, desmonta el dispositivo de tu sistema:

   ```bash
   sudo umount /mnt/iscsi
   ```

2. **Cerrar la sesión del objetivo iSCSI**:
   Luego, desconéctate del servidor iSCSI:

   ```bash
   sudo iscsiadm --mode node --targetname <nombre_del_objetivo> --portal <IP_del_servidor> --logout
   ```


Con estos pasos, habrás configurado un cliente iSCSI en AlmaLinux y conectado a un dispositivo iSCSI para acceder a un almacenamiento remoto. Esta técnica es útil para integrar sistemas Linux con servidores de almacenamiento en red como TrueNAS u otros sistemas SAN, permitiendo aprovechar al máximo las ventajas del almacenamiento distribuido.


## Conectar un volumen iSCSI en Windows 10 o 11 (Opcional):

### 1. **Acceder al iniciador iSCSI**

- Abre el **Panel de control** y selecciona la opción **Herramientas administrativas**.
- Haz clic en **Iniciador iSCSI**. Si es la primera vez que lo abres, te pedirá que inicies el servicio de iSCSI. Haz clic en **Sí** para activarlo.

### 2. **Conectar a un destino iSCSI**

* En la pestaña "Detección", pulsa el botón "Detectar portal", introduce la ip y las credenciales CHAP (si lo has configurado) usando el botón "Opciones avanzadas..."

- En la pestaña **Destinos** pulsa "Actualizar", debería aparecer el disco compartido.
- Seleccionalo y haz click **Conectar**. Si todo está bien configurado en el servidor, el cliente se conectará al destino iSCSI.

### 3. **Configurar el disco iSCSI**

- Después de establecer la conexión, abre **Administración de discos** en Windows (puedes buscar "Crear y formatear particiones del disco duro" en el menú de inicio).
- Ahí verás el nuevo disco iSCSI. Deberás inicializarlo, formatearlo y asignarle una letra de unidad, como harías con cualquier disco local.

### 4. **Opciones avanzadas**

- En el iniciador iSCSI, puedes configurar opciones avanzadas como autenticación CHAP, persistencia de la conexión y ajuste de las configuraciones de red, en caso de que lo necesites.

---

## Conectar volumen iSCSI desde Windows Server (Opcional)

Para conectar un almacenamiento iSCSI desde **Windows Server**, sigue los pasos detallados a continuación. La conexión iSCSI te permitirá acceder a un dispositivo de almacenamiento remoto como si fuera un disco local.

### Paso 1: Acceder al iniciador iSCSI en Windows Server

1. **Abrir el iniciador iSCSI**:
   - En el **Administrador del Servidor**, ve a **Herramientas** y selecciona **Iniciador iSCSI**.
   - También puedes acceder al Iniciador iSCSI desde el menú de inicio buscando "Iniciador iSCSI".

2. **Iniciar el servicio iSCSI**:
   - Si es la primera vez que inicias el Iniciador iSCSI, te pedirá habilitar el servicio. Haz clic en **Sí** para que se inicie automáticamente cada vez que se arranque el servidor.

### Paso 2: Descubrir el objetivo iSCSI (Target)

1. **Configurar el portal de destino**:
   - En la pestaña **Destinos**, haz clic en **Descubrir portal**.
   - Introduce la **dirección IP** o el **nombre de dominio** del servidor iSCSI al que te quieres conectar (por ejemplo, el servidor TrueNAS).
   - Deja el puerto por defecto en **3260**, a menos que hayas configurado un puerto diferente en el servidor iSCSI.

2. **Descubrir los objetivos iSCSI**:
   - Una vez añadido el portal, debería aparecer el objetivo (Target) iSCSI en la lista de destinos disponibles. Si no aparece de inmediato, puedes hacer clic en **Actualizar**.
   - Verás algo similar a `iqn.2005-03.com.example:storage.target01` con el estado "Desconectado".

### Paso 3: Conectarse al objetivo iSCSI

1. **Conectar al objetivo**:
   - Selecciona el objetivo iSCSI descubierto y haz clic en **Conectar**.
   - Si no necesitas autenticación adicional (CHAP), puedes conectarte directamente. Si tu servidor iSCSI requiere autenticación, selecciona **Habilitar CHAP** e introduce las credenciales correspondientes.

2. **Configuración avanzada (opcional)**:
   - Si necesitas configurar opciones adicionales como multipathing o configurar un adaptador NIC específico, haz clic en **Avanzado** y ajusta las configuraciones según sea necesario.

3. **Verificar la conexión**:
   - Una vez conectado, el estado del objetivo debería cambiar a **Conectado**.

### Paso 4: Inicializar y formatear el disco iSCSI

1. **Acceder al Administrador de discos**:
   - Después de conectar el objetivo, abre **Administrador de discos**. Puedes hacerlo haciendo clic derecho en el botón de inicio y seleccionando **Administración de discos**.
   - Verás el nuevo disco iSCSI en la lista de discos como un dispositivo no asignado.

2. **Inicializar el disco**:
   - Si el disco no está inicializado, Windows te pedirá que lo inicialices. Selecciona **MBR** o **GPT**, dependiendo de tus necesidades.
   
3. **Crear un nuevo volumen**:
   - Haz clic derecho sobre el espacio no asignado del nuevo disco iSCSI y selecciona **Nuevo volumen simple**.
   - Sigue el asistente para formatear el disco con el sistema de archivos que prefieras (normalmente **NTFS**) y asignar una letra de unidad.

4. **Verificar el montaje del disco**:
   - Una vez formateado y montado, el disco iSCSI aparecerá en **Este equipo** como un nuevo disco, y podrás utilizarlo como cualquier otro disco local.

### Paso 5: Desconectar el objetivo iSCSI

1. **Desconectar el objetivo iSCSI**:
   - Si necesitas desconectar el objetivo iSCSI en el futuro, abre nuevamente el **Iniciador iSCSI**.
   - Selecciona el objetivo al que estás conectado y haz clic en **Desconectar**.

2. **Detener el servicio iSCSI (opcional)**:
   - Si no necesitas más la conexión iSCSI, puedes detener el servicio haciendo clic en **Detener** dentro del **Administrador de servicios**.

---

## Conclusiones de la práctica:

- Durante esta práctica, habrás configurado un sistema RAID 5 utilizando TrueNAS, simulado un fallo de disco y verificado que los datos siguen siendo accesibles durante la reconstrucción del RAID. Opcionalmente puedes haber configurado un "Spare disk" para que la reconstrucción del RAID sea lo más rápida posible.
- El acceso continuo a los datos tanto en NAS como en SAN demuestra la robustez de los sistemas RAID en entornos reales.

# Bibliografía

- [Documentación TrueNAS Core](https://www.truenas.com/docs/core/)
	- [Configuración de almacenamiento](https://www.truenas.com/docs/core/gettingstarted/storingdata/)
	- *[Cómo compartir almacenamiento](https://www.truenas.com/docs/core/gettingstarted/sharingstorage/)*
	- [Cómo configurar iSCSI](https://www.truenas.com/docs/core/coretutorials/sharing/iscsi/addingiscsishare/)
- [RedHat 9 iSCSI initiator](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/managing_storage_devices/configuring-an-iscsi-initiator_managing-storage-devices)
- [AlmaLinux 9 iSCI initiator](https://www.server-world.info/en/note?os=AlmaLinux_9&p=iscsi&f=2#google_vignette)
- [Iniciador iSCSI Windows Server 2016](https://www.sqlshack.com/how-to-configure-an-iscsi-initiator-on-windows-server-2016/)