---
icon: material/file-edit
---



# Clonado masivo por red con Clonezilla


![Diagrama red Clonezilla](diagrama_clonezilla.jpg){width=80%}

En la práctica anterior creamos una imagen de un equipo y la almacenamos en un servidor SSH, para después restaurarla en otro equipo diferente.

En esta práctica vamos a volver a utilizar Clonezilla, esta vez para restaurar varias máquinas a la vez a través de la red. 

En los casos en que tenemos que restaurar una gran cantidad de ordenadores, Clonezilla nos ofrece la posibilidad de distribuir la imagen por red mediante varias opciones:

* Multicast: para los casos en que todos los ordenadores están conectados al mismo segmento de red.
* Broadcast: para casos en que los ordenadores están distribuidos en diferentes redes.


> Tomaremos como punto de partida la práctica anterior: utilizaremos la imagen que ya habíamos guardado para inicializar nuevas máquinas virtuales. 

La restauración de múltiples equipos de forma simultánea a través de la red es un proceso que nos puede ahorrar mucho tiempo si el número de ordenadores a restaurar es grande. 

Por ejemplo, los ordenadores de nuestra clase del instituto podrían volverse a dejar igual que estaban el primer día de clase de una forma sencilla usando Clonezilla de la manera que vamos a ver a continuación.

## Configuración de red de las máquinas

Para evitar conflictos de red, vamos a utilizar la red sólo-anfitrión, **pero deshabilitaremos el servicio DHCP**. Para ello en VirtualBox debes ir al menú `Archivo->Administrador de red de anfitrión...` y desmarcar la casilla que pone DHCP de la red utilizada.

![Deshabilitamos servicio DHCP de red anfitrión](clone.red.no.dhcp.png)

> Configuraremos la red `sólo-anfitrión en todas las máquinas que usemos en la práctica.

## Preparación de servidor

Vamos a aprovechar la imagen de Clonezilla que ya habíamos hecho en la anterior práctica. 

Para ahorrar tráfico de red, **vamos a lanzar el servidor Clonezilla usando la máquina que anteriormente usamos como destino de copia **(la que tenía el servidor SSH instalado). Esta máquina tiene una copia de la imagen que vamos a restaurar en su disco duro. Para ello:

* Apagamos la máquina con el servidor SSH
* Montamos la ISO en el DVD de esta máquina 
* Arrancamos con Clonezilla


Continuamos con los siguientes pasos:

Arrancamos con la primera opción que aparece en el  menú:

![Menú de arranque de Clonezilla](clone.red.01.png){width=70%}

Dejamos la configuración por defecto del teclador (No vamos a escribir nada):

![Configuración de teclado](clone.red.02.png){width=70%}

Elegimos la opción `Start_Clonezilla`:

![Menú Start Clonezilla](clone.red.03.png){width=70%}

Esta vez vamos a arrancar en el modo `lite-server` que es el que nos va a permitir restaurar varios equipos simultáneamente a través de la red:

![Menú de modo de trabajo](clone.red.04.png){width=70%}

A continuación, elegimos `start` para iniciar el servidor:

![Menú de arranque de Lite server](clone.red.05.png){width=70%}

Elegimos la opción `netboot` para que los clientes arranquen por red, de este modo no necesitaremos un CD o un USB para arrancar los clientes:

![Mecanismo de inicio de clientes](clone.red.06.png){width=70%}

Elegimos la opción `start-new-dhcdp`, esta opción nos evita tener que configurar interfaces de red:

![Servicio DHCP](clone.red.07.png){width=70%}

Ahora tenemos que indicar la imagen que vamos a restaurar, hay una gran variedad de opciones. En nuestro caso, vamos a tomar la imagen del disco duro de la máquina que estamos usando para arrancar con Clonezilla. Elegiremos `local_dev` que significa dispositivo local:

![Directorio de imágenes](clone.red.08.png){width=70%}

Nos sandrá una serie de indicaciones para conectar algún dispositivo USB, en el caso de ser necesario. No es nuestro caso, pulsamos intro:

![Posibilidad de conectar USB con imágenes](clone.red.09.png){width=70%}

Clonezilla queda a la espera, detectando nuevos dispositivos USB. Para continuar debemos pulsar `Ctrl+C`:

![A la espera de USB](clone.red.10.png){width=70%}

Elegimos el único disco duro que tiene la máquina:

![Elección de dispositivo con imágenes](clone.red.11.png){width=70%}

Ahora debemos seleccionar el directorio donde guardamos la imagen en la práctica anterior, en primer lugar elegimos `home`:

![Elección de directorio con imágenes 1](clone.red.12.png){width=70%}

Y después elegimos `partimag` que es el directorio con las imágenes:

![Elección de directorio con imágenes 2](clone.red.13.png){width=70%}

Finalmente pulsamos tabulador hasta seleccionar el botón `Done`

![Elección de directorio con imágenes 3](clone.red.14.png){width=70%}

Nos aparece un mensaje indicando que ha montado el directorio indicado. Pulsaremos intro:

![Directorio de imágenes montado](clone.red.15.png){width=70%}

Ahora debemos elegir el modo de configuración, elegiremos `Beginner` que es el modo principiante:

![Modo ce configuración de restauración](clone.red.16.png){width=70%}

En la siguiente pantalla, elegimos `massive-deployment`, que significa despliegue masivo:

![Modo de despliegue](clone.red.17.png){width=70%}

Ahora nos pregunta si queremos que tome los datos de un disco o de una imagen, elegimos `from-image`:

![Desde disco o desde imagen](clone.red.18.png){width=70%}

A continuación nos da la opción de restaurar un disco completo o solamente una partición, elegimos `restoredisk`:

![Restaurar disco o partición](clone.red.19.png){width=70%}

@. Nos muestra una lista de las imágenes disponibles en la carpeta elegida anteriormente. Toma una captura de esta pantalla, y después elige la única imagen que tenemos:

![Selección de imagen a restaurar](clone.red.20.png){width=70%}

Nos pregunta que elijamos la unidad de disco a restaurar, elegimos `sda`:

![Unidad de disco a restaurar](clone.red.21.png){width=70%}

Ahora debemos indicar si queremos comprobar la imagen antes de restaurarla, como ya la habíamos comprobado después de guardarla vamos a elegir `No` para ahorrar tiempo. Aunque suele ser una buena política comprobar las imágenes antes de la restauración:

![Comprobación de imagen antes de restauración](clone.red.22.png){width=70%}

La siguiente pregunta, requiere elegir qué hacer con los clientes una vez restaurados, vamos a elegir `poweroff`, pero en un caso real la elección dependería de lo que fuera más conveniente en cada caso.

![Acción tras restauración](clone.red.23.png){width=70%}

A continuación nos pregunta el modo de transmisión, vamos a elegir `multicast` que es el más adecuado cuando todos los clientes están en la misma red:

![Modo de transmisión de la imagen](clone.red.24.png){width=70%}

La siguiente pantalla nos da a elegir varios modos de espera antes de empezar el envío multicast de la imagen: puede ser por tiempo, por número de clientes o combinación de ambos. Elegiremos `clients-to-wait` porque tenemos la seguridad de que serán exactamente 2 clientes, y no queremos que comience si no se van a actualizar los dos clientes.

![Método para inicio de multicast](clone.red.25.png){width=70%}

Nos pregunta cuántos clientes vamos a restaurar, inicaremos `2`:

![Número de clientes a restaurar](clone.red.26.png){width=70%}

Finalmente, el servidor queda a la espera de que los clientes se conecten. El envío de la imagen no comenzará hasta que no estén todos conectados, ya que la imagen se envía a todos a la vez para reducir el ancho de banda consumido.

![Servidor esperando conexión de clientes](clone.red.27.png){width=70%}

## Restauramos los clientes (por red)

Crearemos dos máquinas virtuales, con un disco duro del mismo tamaño que el de la máquina de la que sacamos la imagen. Después arrancaremos las máquinas por red para comience la restauraicón.

Para restaurar los clientes, sólo nos falta conectarlos a la misma red `sólo-afitrión` a la que está conectado el servidor y cambiar el modo de arranque de las máquinas para que inicien por red, tal como se muestra en la siguiente imagen:


![Inicio de clientes por red](clone.red.inicioClientes.png){width=70%}

Al arrancar la máquina comenzará a buscar el servidor, se verá una imagen similar a la siguiente:

![Conectando con servidor](clone.red.28.png){width=70%}

> @. Toma una captura del menú de selección del cliente antes de comenzar la restauración. (Imagen similar a la que se muestra a continuación)

![Menú de selección en cliente](clone.red.29.png){width=70%}


Mientras no estén conectados todos los clientes, quedarán a la espera. El servidor comenzará a enviar la imagen cuando todos los clientes que hemos dicho que se conectarían se conecten.

![Cliente a la espera del resto de clientes](clone.red.30.png){width=70%}

Una vez conectados todos los clientes, comienza el envío de la imagen:

![Cliente en proceso de restauración](clone.red.31.png){width=70%}
	
Al terminar los clientes se apagarán automáticamente (tal como habíamos seleccionado al configurar el servidor).
Antes de arrancarlos debemos volver a configurar el inicio de la máquina desde el disco duro.

@. Arranca las máquinas restauradas para comprobar que funcionan.

## Bibliografía:

https://clonezilla.org/show-live-doc-content.php?topic=clonezilla-live/doc/11_lite_server
