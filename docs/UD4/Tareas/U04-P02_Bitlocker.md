---
icon: material/file-edit
title: Cifrado de disco en Windows 10
---

<!-- Tomada de curso seguridad del CEFIRE 2020 -->

# Introducción

El objetivo de esta práctica es concienciar de la importancia del cifrado del disco como herramienta para garantizar la confidencialidad de los datos de nuestro sistema informático.

![Logo Bitlocker](img/bitlocker/bitlocker.jpg){width=50%}

En esta práctica vamos a ver lo sencillo que es iniciar sesión en un ordenador con Windows 10 instalado, sin conocer la contraseña del usuario Administrador.

> Estas técnicas pueden usarse en caso de haber olvidado la contraseña del usuario administrador de nuestro ordenador y con fines educativos. En ningún caso deben utilizarse para acceder ilegalmente en un ordenador o servidor al cual no estamos autorizados.

El objetivo de esta práctica es que el alumno se conciencie de la importancia de la seguridad física como primer  nivel de protección de un sistema informático. Si permitimos que un intruso acceda físicamente a nuestro ordenador, es muy probable que pueda acceder a la información guardada en el mismo, pese a estar protegido con usuario/contraseña y permisos.


## Requisitos
Para esta práctica necesitaremos:

* Una máquina virtual con Windows 10 **Pro** funcionando (no funciona BitLocker en Windows 10 Home)
* La ISO Live de la distribución de seguridad informática Kali Linux.

> Se recomienda crear una instantánea (*snapshot*) de la máquina virtual antes de comenzar para poder restaurarla a su estado inicial al terminar la práctica

En caso de no disponer ya de una máquina virtual de Windows 10, se puede descargar la ISO de Windows 10 Pro desde la propia página de descargas de Microsoft y posteriormente realizar la instalación en una máquina virtual nueva. No hace falta activar la licencia puesto que es una máquina de pruebas y la instalación será completamente funcional para la práctica.

La ISO de Kali Linux Live se puede obtener desde la página de descargas de Kali Linux ([https://www.kali.org/](https://www.kali.org/)).

## EFS vs Bitlocker

EFS y BitLocker son sistemas de cifrado de disco que vienen de serie en los sistemas operativos Windows. Tienen características muy diferentes. Por ejemplo BitLocker se usa para cifrar todo el disco en unidades fijas de disco duro o bien en externas, mediante BitLocker To Go. Sin embargo EFS, se usa para proteger los archivos individuales de las unidades de forma individual para cada usuario.

En siguiente enlace puedes ver una comparativa entre las características de ambos y elegir el más adecuado según las necesidades: 

[https://www.redeszone.net/2015/12/25/diferencias-entre-el-cifrado-de-bitlocker-y-efs-en-windows/](https://www.redeszone.net/2015/12/25/diferencias-entre-el-cifrado-de-bitlocker-y-efs-en-windows/])


# Enunciado

> Realiza una memoria tomando capturas del proceso realizado.

### A) Resetear contraseña de usuario con chntpw

En la máquina virtual Windows, debes crear un usuario en el grupo Administradores y asígnale una contraseña. Este usuario es el que vamos a resetear su contraseña desde Kali Linux con la herramienta **chntpw**, disponible en cualquier otra distribución Linux, pero que en Kali ya viene instalada.

Apaga la máquina virtual Windows y en la configuración de almacenamiento de la máquina virtual, inserta la iso de Kali para que arranque con Kali Live:

![Montar ISO en DVD de máquina virtual](img/bitlocker/img0.1.png){width=70%}

Inicia de nuevo la máquina virtual Windows pero al tener insertada en la unidad CD/DVD virtual la iso de Kali, arrancará con éste al pulsar Enter en la primera entrada del menú:

![Menú de inicio de Kali](img/bitlocker/img1.png){width=70%}


Inicia un terminal de línea de comandos y conviértete en root (`sudo -i`):

![Nos convertimos en root en Kali](img/bitlocker/img2.png){width=70%}

Lista los discos de la máquina con `fdisk -l` y monta en `/mnt` el disco ntfs con la partición de sistema de Windows 10. Si sale un error de que se monta en sólo lectura porque la partición está en un estado inseguro, debes reiniciar la máquina en Windows 10 (quitando la iso de Kali), intenta entrar en una cuenta de usuario con contraseña incorrecta y apaga la máquina correctamente (desde el icono de apagado en la ventana de login de Windows) y vuelve a iniciar en Kali. Ya te dejará montar la partición, que en la captura siguiente es `/dev/sda2` (en tu caso podría ser diferente):

![Montando partición de Windows](img/bitlocker/img3.png){width=70%}

Como puedes ver en la anterior captura, se ha montado la partición y después desde el directorio `/mnt/Windows/System32/config` podemos editar la base de datos **SAM** (donde se encuentran los usuarios locales del sistema) con la herramienta **chntpw**. Pulsamos 1 y nos muestra los usuarios y sus RID (Relative ID). En el ejemplo, elegimos el usuario fulano que es administrador, escribiendo su RID y le reseteamos la contraseña (también podemos convertir en Administrador a cualquier usuario o añadirlo a un grupo):

![En este ejemplo elegimos usuario fulano](img/bitlocker/img4.png){width=70%}

![Eliminamos el password de fulano](img/bitlocker/img5.png){width=70%}

Finalmente salimos con `q` y le damos a guardar:

![Montando partición de Windows](img/bitlocker/img6.png){width=70%}

Reiniciamos la máquina en Windows 10 (quitando la ISO de Kali de la unidad CD/DVD) y comprobamos como podemos acceder como el usuario fulano sin contraseña, siendo administrador de la máquina.


## B) Activar Bitlocker

En este punto veremos como activar BitLocker como Herramienta de cifrado del disco para evitar que puedan usarse cualquiera de las técnicas vistas anteriormente. Para activar BitLocker es tan sencillo como desde el botón derecho sobre la unidad de disco que queremos cifrar (normalmente C:/), seleccionar Activar BitLocker:

![Activar Bitlocker](img/bitlocker/img12.png){width=70%}

Si se nos informa de que no es posible activarlo ya que no hay un **chip TPM **en nuestro sistema, hay que ejecutar como Administrador, el editor de directivas de grupo local (gpedit.msc):

![Editor de directivas de grupo local](img/bitlocker/img13.png){width=30%}

A continuación abrimos la rama `Configuración del equipo -> Plantillas administrativas -> Componentes de Windows -> Cifrado de unidad BitLocker -> Unidades del sistema operativo` y en el panel de directivas hacemos doble clic sobre "Requerir autenticación adicional al iniciar":

![Requerir autenticación adicional al iniciar](img/bitlocker/img13.1.png){width=100%}

Habilitamos la directiva y dejamos el resto de opciones como están por defecto y pulsamos Aceptar. Ya podemos usar BitLocker en la unidad C:

![Bitlocker](img/bitlocker/img11.png){width=70%}

Al activar BitLocker desde el explorador de archivos con el botón derecho sobre la unidad C:, se nos abre el asistente. Le indicamos que para desbloquear la unidad usaremos una contraseña (¡¡¡**importante no olvidarla para que pueda arrancar Windows**!!!):


![Método de desbloqueo](img/bitlocker/img14.png){width=70%}

![Escribir contraseña](img/bitlocker/img15.png){width=70%}

Una vez establecida la contraseña, debido a criticidad de la misma, se nos pide un sistema para guardar una copia de la misma en caso de que la olvidemos y poder arrancar el sistema. En este caso elegimos en un archivo, siempre en una unidad que no esté cifrada obviamente. Pero cualquier otra opción también es válida, como por ejemplo guardarla en una cuenta de Microsoft:


![Clave de recuperación](img/bitlocker/img16.png){width=70%}

Una vez guardada en una unidad no cifrada o bien en una unidad USB (o cualquiera de las otras opciones), nos pide que parte del disco cifrar. Elegimos toda la unidad para garantizar la confidencialidad de todos los datos que hay en la misma:

![Espacio a cifrar](img/bitlocker/img17.png){width=70%}

En el siguiente paso, como el disco de esta máquina no vamos a usarlo en otro Windows versión anterior, elegimos el modo de cifrado nuevo (XTS-AES):

![Modo de cifrado](img/bitlocker/img18.png){width=70%}

Finalmente aceptamos en el último paso donde nos pide confirmación para realizar la comprobación de lectura de las claves de BitLocker:


![Confirmación del cifrado](img/bitlocker/img19.png){width=70%}

Nos avisa de que se debe reiniciar el equipo para activar el cifrado. Una vez reiniciado el equipo, el cifrado del disco entero puede tardar bastantes minutos en función de la información que haya en el disco.

Una vez arranque el ordenador con Windows y la unidad cifrada, nos pedirá introducir la contraseña (es el método que hemos elegido) para poder arrancar el sistema:


![Inicio de ordenador](img/bitlocker/img20.png){width=70%}

En caso de no recordar la contraseña, pulsando Esc se inicia el proceso de recuperación de BitLocker donde se nos pedirá la información de la clave que guardamos en el paso 11.

Si intentamos resetear la contraseña de un usuario administrador o crear uno  nuevo con cualquiera de los sistemas anteriores sin conocer la contraseña de BitLocker, ya no vamos a poder porque los datos del disco se encuentran cifrados:

![Intento de reseteo de contraseña](img/bitlocker/img21.png){width=70%}

Anteriormente a Windows 10 build 1709, se podía utilizar la utilidad syskey para proporcionar una capa adicional de cifrado al fichero SAM que además permitía guardar esta contraseña fuera del sistema y pedirnosla cada vez que arranca Windows. Actualmente se ha deshabilitado y se recomienda usar BitLocker porque syskey usa un sistema de cifrado débil y no se considera seguro:




# Bibliografía

https://www.kali.org/
https://www.microsoft.com/es-es/software-download/windows10ISO

https://unaaldia.hispasec.com/2008/04/mitos-y-leyendas-las-contrasenas-en-windows-ii-syskey.html
https://docs.microsoft.com/en-us/troubleshoot/windows-server/identity/syskey-exe-utility-is-no-longer-supported



