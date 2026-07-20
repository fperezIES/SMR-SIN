---
icon: material/file-edit
---

# Instalación y configuración de un cortafuegos perimetral

<!-- Tomada de curso seguridad del CEFIRE 2020 -->

## Introducción

En esta práctica vamos a usar OPNSense para implementar un cortafuegos perimetral. Simularemos toda la configuración de red con VirtualBox.

![Logo OPNSense](docs/UD6/img/opnsense/logo.png){:style="width: 50%;" class="center"}


### Preparación

Para realizar la práctica se necesita:

* Un PC con al menos 4 GB de RAM libre para arrancar **3 máquinas virtuales** con VirtualBox: cortafuegos, equipo cliente y servidor en DMZ. 
* ISO de OPNsense ([https://opnsense.org/download/](https://opnsense.org/download/)), un cortafuegos libre basado en FreeBSD. En la página oficial de descarga, seleccionar arquitectura **amd64**, tipo de imagen **dvd** y un mirror de Europa
* Dos máquinas virtuales que simularán un **cliente** de la red local de la organización, y un **servidor** web en la DMZ. Para el cliente y el servidor se puede usar el SO que prefiera el alumno, pero preferiblemente que no necesite excesiva RAM para funcionar y simular todo en un único PC, se recomienda Debian con escritorio Xfce.


## Enunciado

Para esta práctica vamos a simular el siguiente escenario de la red de una organización con un cortafuegos con tres zonas: interna (LAN), externa (WAN) y DMZ. Esta configuración es habitual en pequeñas organizaciones aunque hay configuraciones más robustas donde existen varias capas de cortafuegos entre las diferentes zonas:

![Esquema de red](Practica_OPNSense.png){:style="width: 70%;" class="center"}

**Tanto el cortafuegos OPNSense, como el cliente en la LAN y el servidor en la DMZ, deben simularse con VirtualBox**. La red LAN de la organización corresponde con la red sólo-anfitrión de VirtualBox, la red DMZ corresponde con una red interna a la que llamaremos DMZ y la red WAN, corresponde con la LAN del centro o domicilio del alumno donde se esté realizando la práctica.

Adjunta una memoria en formato PDF con capturas del proceso realizado y súbela al Moodle en la actividad correspondiente.

### Instalación de OPNSense

Una vez descargada y descomprimida la imagen de OPNSense, creamos una máquina en VirtualBox con las siguientes características:

* Nombre: OPNSense (puede ser cualquier otro que le sugiera al alumno)
* Tipo: BSD
* Versión: FreeBSD (64 bit)
* Memoria: 512 MB (suficiente para OPNSense)
* Disco duro: por defecto todo (Crear disco, tipo VDI, dinámicamente, 16 GB)

Una vez creada la máquina, desde la configuración de la VM, editamos las red y creamos tres adaptadores:

* Adaptador 1: puente con la tarjeta con conexión a Internet del anfitrión. Será la WAN.
* Adaptador 2: sólo-anfitrión (vboxnet0), será la LAN del escenario.
* Adaptador 3: Red interna con nombre DMZ.

> @. Muestra capturas de la configuración de cada uno de los adaptadores de red en VitualBox

En las propiedades del CD, conectamos el fichero ISO con el instalador de OPNSense

Iniciamos la máquina y nos aparece el asistente de instalación en modo texto:

![Asistente de instalación en modo texto](img0.png){:style="width: 70%;" class="center"}

Si no pulsamos nada, inmediatamente arranca la instalación de OPNSense y nos pedirá credenciales para seguir. Introducimos:

> login: **installer** 
> password: **opnsense**

Pulsamos enter en la siguiente ventana:

![Ventana de bienvenida](docs/UD6/img/opnsense/img1.png){:style="width: 70%;" class="center"}


Cambiamos el mapa de teclado y elegimos español:

![Cambio del teclado](docs/UD6/img/opnsense/img2.png){:style="width: 70%;" class="center"}

![Elección del teclado](docs/UD6/img/opnsense/img3.png){:style="width: 70%;" class="center"}

Aceptamos los ajustes:

![Aceptamos los ajustes](docs/UD6/img/opnsense/img4.png){:style="width: 70%;" class="center"}

Elegimos instalación guiada:

![Elegimos instalación guiada](docs/UD6/img/opnsense/img5.png){:style="width: 70%;" class="center"}

Seleccionamos el disco virtual:

![Seleccionamos el disco virtual](docs/UD6/img/opnsense/img6.png){:style="width: 70%;" class="center"}

Seleccionamos GPT/UEFI como modo de instalación:

![Seleccionamos GPT/UEFI como modo de instalación:](img7.png){:style="width: 70%;" class="center"}

Y comienza a realizar la instalación en el disco duro virtual de la máquina:

![Comienza instalación](img8.png){:style="width: 70%;" class="center"}

Nos solicita que establezcamos la contraseña de root:

![Contraseña de root](img9.png){:style="width: 70%;" class="center"}

Reiniciamos y quitamos la ISO del CD/DVD virtual:

![Reiniciamos](img10.png){:style="width: 70%;" class="center"}

### Configuración inicial de OPNSense

Tras reiniciar la máquina e introducir las credenciales de root, nos aparece el menú principal de consola:

![opnsense console menu](docs/UD6/img/opnsense/img11.png){:style="width: 70%;" class="center"}


El primer paso es reasignar las tres interfaces del firewall para que coincidan con el escenario. La primera interfaz (em0), es la WAN porque está puenteada con la tarjeta del PC anfitrión. La segunda (em1) es la LAN, que está en modo sólo-anfitrión y a la que se conectará el cliente de la red local interna de la organización. La tercera (em2) es la DMZ de la organización, que está conectada a una red interna con nombre DMZ.
Por tanto, pulsamos 1 y vamos asignando las interfaces. A la primera pregunta, indicamos que **no vamos a configurar VLANs**:

![No vamos a configurar VLANs](docs/UD6/img/opnsense/img12.png){:style="width: 70%;" class="center"}

Nos pregunta por la interfaz WAN. Escribimos **em0** que es la primera:

![Asignamos WAN](docs/UD6/img/opnsense/img13.png){:style="width: 70%;" class="center"}


Después nos pregunta por la WAN y por la opcional 1, a lo que respondemos **em1** y **em2** respectivamente. Desde la GUI posteriormente cambiaremos el nombre de OPT1 a DMZ:

![Asignamos LAN y OPT1](docs/UD6/img/opnsense/img14.png){:style="width: 70%;" class="center"}

Finalmente indicamos que queremos proceder y se guardará la configuración:

![Guardamos configuración](docs/UD6/img/opnsense/img15.png){:style="width: 70%;" class="center"}

**Seleccionamos 2** en el menú, para **asignar las direcciones IP**. 
En la WAN aparece por DHCP, así es que la tenemos que cambiar y configurar con una IP de aula que tenemos asignada para cada alumno, indicando IP, máscara y gateway. No configures IPv6. Después indicamos que no queremos revertir a HTTP (dejamos HTTPS por seguridad) en la GUI.


Seleccionamos la LAN y le asignamos una IP estática del rango de la interfaz vboxnet0 que siempre es 192.168.56.0/24 (a no ser que se cambie). Elegimos la 192.168.56.100/24, asegurándonos también de haber deshabilitado el DHCP de VirtualBox pues posteriormente usaremos el cortafuegos como servidor DHCP:

![Configuramos IP LAN](docs/UD6/img/opnsense/img16.png){:style="width: 70%;" class="center"}

Después contestamos que no vamos a configurar gateway (lo coge de la WAN por dhcp) y que tampoco va a tener IPv6 en la LAN. **El servicio DHCP en la LAN podemos configurarlo desde la GUI posteriormente**. Después indicamos que no queremos revertir a HTTP (dejamos HTTPS por seguridad) en la GUI.

Procedemos igual con la interfaz OPT1 (DMZ) asignándole la 203.0.113.1/24, sin gateway y sin dirección IPv6. Esta interfaz también puede configurarse después desde la GUI, no es necesario hacerlo ahora. Realmente la única importante es la LAN, para poder acceder desde un navegador a la IP 192.168.56.100 y realizar el resto de configuración.

![Configuramos IP OPT1 (DMZ)](docs/UD6/img/opnsense/img17.png){:style="width: 70%;" class="center"}

A continuación accedemos desde un navegador en el anfitrión, a la IP de la LAN del cortafuegos, que hará las veces de interfaz de gestión en este caso (**aunque en entornos de producción es habitual que estos dispositivos tengan una interfaz dedicada para administración fuera de banda**):


![Interfaz Web](docs/UD6/img/opnsense/img18.png){:style="width: 70%;" class="center"}


Una vez identificados con las credenciales de root, se nos abrirá el asistente. Podemos cancelarlo o dejar que nos guíe en la configuración inicial. En este caso lo utilizaremos pero es prescindible:


![Asistente inicial 1](docs/UD6/img/opnsense/img19.png){:style="width: 70%;" class="center"}

![Asistente inicial 2](docs/UD6/img/opnsense/img20.png){:style="width: 70%;" class="center"}

El resto de opciones pulsamos Next porque confirma la dirección WAN y LAN que hemos configurado desde la consola y finalmente pulsamos Recargar.
A continuación, ya de nuevo en la GUI, seleccionamos “Interfícies” desde el menú de la izquierda para cambiar el nombre de OPT1 a DMZ:


![Cambiamos nombre de OPT1 a DMZ](docs/UD6/img/opnsense/img21.png){:style="width: 70%;" class="center"}

Pulsamos Guardar y Aplicar cambios.


> @. Muestra una captura de la consola de OPNSense una vez configuradas las interfaces según las indicaciones anteriores

### Configuración de reglas de NAT saliente

A continuación vamos a configurar el NAT saliente. Por defecto crea varias reglas pero las vamos a crear desde cero manualmente para aprender como realizar el NAT:

![Configuración Manual del NAT](img22.png){:style="width: 70%;" class="center"}

Le damos a Guardar y Aplicar cambios nos habrá eliminado las reglas automáticas. A continuación creamos dos reglas de NAT, para traducir las direcciones de la LAN y la DMZ respectivamente, cuando se salga a Internet. **Nótese que en la DMZ tenemos direccionamiento IP público, que suele ser lo habitual en DMZ**. En un entorno real no sería necesario hacer NAT para DMZ pero en nuestro caso sí porque es un direccionamiento de pruebas (red de TEST-NET-3) y que nuestro ISP no reconoce.

Pulsamos Añadir:

![NAT saliente LAN](img23.png){:style="width: 70%;" class="center"}

Y Guardar y Aplicar cambios.


Agregamos una nueva para hacer NAT en la DMZ y que nuestro servidor virtual en la DMZ pueda salir a Internet (recordemos que con direccionamiento público real no haría falta):


![NAT saliente DMZ](img24.png){:style="width: 70%;" class="center"}


Y Guardar y Aplicar cambios.

Finalmente nuestras dos reglas de NAT saliente creadas:

![Reglas NAT](img25.png){:style="width: 70%;" class="center"}

> @. Toma una captura de tus reglas NAT una vez configuradas

A continuación vamos a terminar de configurar el servicio DHCP (si no se ha hecho con el asistente) e indicar algunos parámetros que faltan:

![Configuración DHCP](img26.png){:style="width: 70%;" class="center"}


Pulsamos Guardar.

> @. Mostrar captura de configuración de DHCP.

### Máquinas Servidor DMZ y Cliente

#### Cliente

Es el momento de comprobar que tanto el cliente en la LAN como el servidor en la DMZ, pueden salir a Internet a través del firewall haciendo NAT con la IP de la WAN. Para ello puedes crear dos máquinas con las siguientes características (recomendamos un Debian  con escritorio Xfce). Para el cliente en la LAN (Puedes clonar una que ya tengas instalada):

* Nombre: tuapellido_Cliente
* Tipo: Linux
* Versión: Debian (32/64 bit)
* Memoria: 2048 MB
* Disco duro: por defecto todo (Crear disco, tipo VDI, dinámicamente)

Una vez creada la máquina, desde la configuración de la VM, editamos la red y configuramos el **adaptador 1 en la red sólo-anfitrión**. Al arrancar, cogerá una IP de la LAN a través del servidor DHCP que hemos habilitado en OPNSense.

Si todo ha ido bien nuestro Linux cliente estará navegando perfectamente como puede verse en las siguientes capturas:


![Prueba de conectividad desde máquina cliente LAN](img27.png){:style="width: 70%;" class="center"}

> @. Muestra una captura que demuestre que tu cliente tiene conectividad.

#### Servidor DMZ

Comprobamos lo mismo con el servidor en la DMZ, una máquina virtual Debian también (aunque puede ser cualquier otro en función de la RAM disponible en el anfitrión) 
con las siguientes características:

Nombre: tuapellido_Server
Tipo: Linux
Versión: Debian (32/64 bit)
Memoria: 2048 MB
Disco duro: por defecto todo (Crear disco, tipo VDI, dinámicamente)

Una vez creada la máquina, desde la configuración de la VM, editamos la red y configuramos el adaptador 1 en la red interna DMZ. Al arrancar el servidor, es necesario configurarle una IP estática que es lo habitual en servidores, con los siguientes parámetros:

IP: 203.0.113.2/24
Gateway: 203.0.113.1 (el cortafuegos)
DNS: los dns de tu conexión a Internet o cualquier DNS público fiable como Google o Cloudflare (8.8.8.8, 8.8.4.4, 1.1.1.1)

![Configuración de red de servidor DMZ](img28.png){:style="width: 70%;" class="center"}

Si probamos a navegar o hacer una traza a un destino de Internet (comando mtr o traceroute) **veremos que no funciona nada**, ni el ping. Esto es debido a que por defecto, un firewall tiene una política restrictiva y para las redes no confiables como una WAN o una DMZ, tiene todo el tráfico cortado. Puedes comprobar que para la LAN, ha creado una serie de reglas automáticas que permiten que funcione el DHCP y que los clientes de la LAN puedan salir a Internet tanto para IPv4 como para IPv6:

![Reglas creadas automáticamente para LAN](img29.png){:style="width: 70%;" class="center"}

Esto permite incluso que el cliente LAN pueda hacer ping al servidor en la DMZ, y permitir que el ping reply vuelva porque es un cortafuegos de estado. Sin embargo, el servidor en la DMZ no puede hacer ping al cliente en la LAN.

> @. Muestra una captura de la configuración de red tu servidor.

### Configuración de reglas en el cortafuegos

Empezamos creando reglas para la red DMZ. Por seguridad, no se recomienda que desde la DMZ se pueda originar tráfico hacia equipos de la red interna. Esto es así porque un servidor de la DMZ puede haber sido atacado y comprometido desde Internet ya que ofrece servicios a Internet como http, dns, ftp, e-mail, etc que pueden ser vulnerables y ser una vía de entrada para un atacante que pivotando a través del servidor comprometido, podría entrar en la LAN interna de la organización. Por tanto vamos a permitir sólo que desde la DMZ hacia Internet se permita todo el tráfico (de momento sólo IPv4 en esta práctica) pero podría limitarse a determinados servicios también. Nos ubicamos en `DMZ` dentro `Cortafuegos -> Reglas` y pulsamos `Añadir`:

![Añadimos reglas DMZ](img30.png){:style="width: 70%;" class="center"}


Si observas hemos aplicado una regla de permitir desde la DMZ a **cualquier destino que NO SEA la LAN interna** (**Destino/invertir**).

Pulsamos `Guardar` y `Aplicar cambios`.

> 
  
SSH:
```sh
sudo apt-get install openssh-server
```
  
HTTP Y HTTPS:
```sh
sudo apt-get install apache
sudo a2enmod ssl
sudo a2ensite default-ssl
sudo service apache2 reload
```
  
FTP:
```sh
sudo apt-get install vsftpd
```

Para probar la directiva 1, puedes hacerlo desde el cliente de la LAN hacia el servidor DMZ. En la DMZ Para probar la directiva 2, puedes hacerlo desde el propio anfitrión o cualquier otro equipo conectado a la red de tu casa/centro (donde estés haciendo la práctica, que a efectos es como si fuera Internet en el escenario) y comprueba si puedes conectarte al servidor web del servidor en la DMZ por esos servicios, indicando la dirección IP en la WAN que tiene el cortafuegos, puesto que el servidor de la DMZ está enmascarado tras el cortafuegos para los usuarios de Internet.


#### Consideraciones a tener en cuenta:

A. Si en el servidor hay algún firewall por defecto activado (Ubuntu lo tiene) es conveniente a efectos de la práctica, deshabilitarlo (`sudo ufw disable`).

B. A efectos de la práctica, el direccionamiento privado de tu casa/centro es Internet, pero por defecto OPNSense deniega que desde la WAN se permita tráfico con origen direcciones privadas de la RFC 1918, así es que debes desactivar esta característica desde `Interfícies -> WAN -> Bloquear redes privadas` (desmarcar):

![Permitimos tráfico desde redes con IPs privadas](img31.png){:style="width: 70%;" class="center"}


C. Para implementar la directiva 1, se recomienda modificar la regla de IPv4 que crea OPNSense para permitir a la red LAN acceder a todo el tráfico. Esta regla puedes partirla en dos (con la opción de clonar una regla): una afecta al destino DMZ y otra a lo que no sea DMZ (Invertir destino). También puedes crear un alias de puertos para meterlos en una sólo regla, desde `Cortafuegos -> Aliases`):

![Alias de puertos](img32.png){:style="width: 70%;" class="center"}


Las dos reglas que se crearían, después de crear el alias de puertos, tendrían este aspecto:

![Reglas de alias de puertos](img36.png){width=100%}

D. Para implementar la directiva 2, es necesario crear reglas de NAT de redirección de puerto, indicando que la IP de destino de la petición es la IP WAN del cortafuegos y se debe redirigir a la IP del servidor en la DMZ (203.0.113.2) y al puerto correspondiente que podría ser otro:


![Reglas redirección de puertos](img34.png){:style="width: 70%;" class="center"}

En este punto, cuando al cortafuegos le llegue una petición desde Internet a su IP WAN por el servicio indicado (protocolo y puerto), redirigirá la petición al servidor de la DMZ indicado, al puerto indicado, que puede ser el mismo u otro.

Por tanto para implementar la política del puerto ssh 20022, hay que redirigir éste puerto público al 22 del servidor de la DMZ. Cambiar el puerto público de un servicio a uno no estándar puede ayudar a proteger el servicio, si bien es una medida de seguridad débil por ofuscación, ya que en el momento que un atacante haga un barrido de puertos completo, lo puede descubrir.

Cada vez que creamos una redirección de puerto, OPNSense crea automáticamente una regla de entrada en la interfaz WAN. Por ejemplo, se han creado dos redirecciones de puertos de HTTP y 20022 hacia el HTTP y el SSH del servidor de la DMZ y se han creado automáticamente estas dos reglas:

![Reglas automáticas de redirección de puertos](img35.png){width=100%}



## Bibliografía

https://opnsense.org/

https://docs.opnsense.org/manual/install.html



