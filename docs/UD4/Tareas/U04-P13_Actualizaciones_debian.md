---
icon: material/file-edit
title: Actualizaciones de software Debian
---



<!-- 

Referencias:



-->

# Introducción

Las actualizaciones de los sistemas son principalmente correcciones de vulnerabilidades.
Todo el tiempo que transcurra desde que una vulnerabilidad ha sido detectada y corregida en una actualización hasta que dicha acutalización sea desplegada en nuestro sistema es tiempo que estamos corriendo riesgos innecesarios.

Las herramientas necesarias para la actualización del sistema de forma eficiente variará dependiendo del sistema operativo.

En estas práctica vamos a configurar un servidor Debian.


![Logo Greenbone](img/debupdate/debian-automatic-security-updates.png){width=70%}



# Enunciado

## Por seguridad, actualizar con frecuencia

Es imprescindible que el servidor esté siempre al día con sus actualizaciones. La comunidad _debian_ está actualizando constantemente el software para corregir _bugs_ y fallos de seguridad. Un sistema no actualizado es una puerta abierta a los _hackers_ y los _crackers_. La mejor fuente para buscar software actualizado es internet. **Hay que escoger cuidadosamente las fuentes de software o _repositórios_** y hacer actualizaciones frecuentemente.

También es importante seguir de cerca todas las informaciones referentes a _bugs_ (conflictos o problemas que producen algunos paquetes) y fallos de seguridad, así como estar atentos a cuál es la mejor forma de corregir esos fallos. La distribución edita listas con los anuncios de seguridad y con sus respectivas correcciones en la página de la distribución o si prefiere en las listas de distribución.


## Gestión de paquetes: APT

Descripción completa de la funcionalidad del gestor de paquetes APT usado en debian: [https://servidordebian.org/es/buster/config/software/apt](https://servidordebian.org/es/buster/config/software/apt)

### Actualización de la lista de paquetes

```sh
apt-get update
```

Actualiza en el equipo la lista de paquetes que hay en los repositorios configurados en el archivo `/etc/apt/sources.list`. Este comando hay que ejecutarlo antes de instalar, desinstalar o gestionar paquetes .

 
### Instalación de paquetes

```sh
apt-get install <paquete>
```

Instala un paquete de software y todos los paquetes de los que depende el paquete instalado. 
  
```sh
apt install –reinstall <paquete>
```

Re-instala un paquete, sustituyendo los archivos. Bastante útil, cuando se quiere reponer archivos que han sido cambiados  
 

### Actualización del sistema

```sh
apt-get upgrade
```

Actualiza todos los paquetes instalados en el sistema a la última versión que haya en el momento de ejecutar el comando. Si alguna actualización necesita la instalación de un nuevo paquete, esa actualización no se realiza. **Actualiza sólo los paquetes que no requieran nuevas instalaciones.**  

```sh
apt-get dist-upgrade
```

 Actualiza todos los paquetes instalados en el sistema a la última versión que haya en el momento de ejecutar el comando. Si alguna actualización necesita la instalación de un nuevo paquete, el nuevo paquete se instala y se completa la actualización. **Actualiza todos los paquetes, y si hay que instalar nuevos paquetes para la actualización se instalan**.  


# Comprobación de vulnerabilidades de paquetes instalados


## debsecan

Esta utilidad permite encontrar paquetes que tenemos instalados y que tienen vulnerabilidades

```sh
sudo apt-get install debsecan
man debsecan
```

### Ejemplos

Este comando imprime los nombres de los paquetes que tienen vulnerabilidades c**on parches de seguridad disponible** (sustituir suite por el nombre de la distribución usada):
   
```sh
debsecan --suite suite --format packages --only-fixed
```
  
![Lista de paquetes con parches de seguridad](img/debupdate/debsecan-bullseye2.png){width=70%}

Con el siguiente comando, se descargan los nuevos paquetes que contienen parches de seguridad:

```sh
sudo apt-get install \
$(debsecan --suite suite --format packages --only-fixed)
```

![Reinicio de servicios de forma interactiva](img/debupdate/debsecan-bullseye2.png){width=70%}


Para obtener notificaciones, se podría ejecutar periodicamente (mediante cron) el siguiente comando:

```sh
debsecan --suite suite --format report --update-history --mailto root
```

También se puede usar el comando `debsecan-create-cron` para crear una tarea en cron.

```sh
sudo debsecan-create-cron
```

Podemos ver el contenido de la tarea creada imprimiendo el fichero:
```sh
cat  /etc/cron.d/debsecan
```

```sh
# cron entry for debsecan
MAILTO=root

31 * * * * daemon test -x /usr/bin/debsecan && /usr/bin/debsecan --cron
# (Note: debsecan delays actual processing past 2:00 AM, and runs only
# once per day.)
```

<!--
# Comprobación de paquetes modificados

Vamos a utilizar la utilidad debsums para asegurar que no han sido modificados los paquetes instalados en el equipo. Estas modificaciones podrían haber sido realizadas por algún malware.


```sh
sudo apt-get install debsums
```

Muestra los archivos modificados de paquetes de todos los paquetes  instalados  con
sumas de control.

```sh
sudo debsums -ca
```
-->

# Actualizaciones automáticas: unattended‐upgrades

## Instalación de unnatended-upgrades

La utilizad **unattended‐upgrades** permite tener el sistema actualizado de forma automática sin necesidad de intervención del usuario. De esta forma minimizaremos el tiempo necesario para que se apliquen las actualizaciones a nuestro sistema.

```sh
sudo apt-get install unattended-upgrades 
```

<!--
apt-listchanges
-->

Para editar la configuración de unattended-upgrades usaremos el siguiente comando

```sh
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Asegurate de que las siguientes líneas no están comentadas en el fichero de configuración.

```
"origin=Debian,codename=${distro_codename}-updates";
"origin=Debian,codename=${distro_codename}-proposed-updates";
"origin=Debian,codename=${distro_codename},label=Debian";
"origin=Debian,codename=${distro_codename},label=Debian-Security";
"origin=Debian,codename=${distro_codename}-security,label=Debian-Security";
```

## Activar las actualizaciones automáticas

A continuación activamos las actualizaciones automáticas ejecutando:

```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

Aparecerá la siguente ventana, y elegiremos sí. (Si más tarde quisieramso deseactivar las actualizaciones automáticas ejecutaríamos el mismo comando, pero elegiríamos no.)

![Configuración de unattended-upgrades](img/debupdate/unattended-upgrades-cfg.png){width=90%}


Esto genera un nuevo fiehcor de configuración, que vamos a leer ejecutando

```bash
cat /etc/apt/apt.conf.d/20auto-upgrades
```


El contenido del fichero es el siguiente:

```sh
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

Para comprobar si el servicio se está ejecutando podemos usar el comando:

```bash
sudo systemctl status unattended-upgrades.service
```


## Forzar ejecución

Para tareas de depuración es posible forzar manualmente la ejecución mediante el comando:

```bash
sudo unattended-upgrade --debug --dry-run
```

## Revisar cambios realizados

Cuando se realicen actualizaciones desatentidas, la actividad realizada se almacenará en logs dentro de ficheros contenidos en la carpeta **`/var/log/unattended-upgrades/`** 



# Reinicio de servicios tras actualizaciones

Actualizar el sistema es necesario, pero no suficiente. Los servicios y sesiones en ejecución seguirán usando los binarios anteriores hasta que no se reinicien. Para minimizar los servicios a reiniciar podemos utilizar la utilidad `needrestart` que nos permite identificar y reiniciar los elementos actualizados que sigan utilizando los binarios antiguos.

Para instalar needrestart:

```sh
sudo apt-get install needrestart
```

Es posible cambiar el comportamiento por defecto editantando la configuración:

```sh
sudo nano /etc/needrestart/needrestart.conf
```

Por ejemplo, podemos activar el mod interactivo:

```sh
# Restart services (l)ist only, (i)nteractive or (a)utomatically.  
$nrconf{restart} = 'i';
```

Podemos veer las opciones de ejecución mediante:

```sh
sudo needrestart --help
```


## Pluggin Nagios

Needrestart ofrece un modo plugin de `nagios` que puede ser integrado en la monitorización del sistema. Con este modo puede que quieras omitir las comprobaciones de kernel añadiendo la opción si el la actualización del kernel está siendo realidad mediante `Livepatch` (característica de actualización de kernel sin reiniciar introducida en Ubuntu).


```sh
$ sudo needrestart -p -l  

CRIT — Services: 4 (!), Containers: none, Sessions: 1 (!)|Services=4;;0;0 Containers=0;;0;0 Sessions=1;0;;0
```

![Ejemplo de visualización de nagios](img/debupdate/needrestart-nagios.png){width=90%}

## Ejemplos de funcionamiento:


![Sesión en ejecución con binarios obsoletos](img/debupdate/needrestart-sessionoutdated.png){width=70%}


![Reinicio de servicios de forma interactiva](img/debupdate/needrestart-interactive.png){width=70%}




# Bibliografía

APT


* [https://servidordebian.org/es/buster/config/software/apt](https://servidordebian.org/es/buster/config/software/apt)


Unnatended-upgrades


* [https://wiki.debian.org/UnattendedUpgrades](https://wiki.debian.org/UnattendedUpgrades)
* [https://linuxiac.com/how-to-set-up-automatic-updates-on-debian/#h-install-unattended-upgrades-package-on-debian](https://linuxiac.com/how-to-set-up-automatic-updates-on-debian/#h-install-unattended-upgrades-package-on-debian)
* [https://guides.wp-bullet.com/configure-automatic-security-updates-debian/](https://guides.wp-bullet.com/configure-automatic-security-updates-debian/)
* [https://www.richud.com/wiki/Ubuntu_Enable_Automatic_Updates_Unattended_Upgrades](https://www.richud.com/wiki/Ubuntu_Enable_Automatic_Updates_Unattended_Upgrades)


Needrestart


* [https://github.com/liske/needrestart](https://github.com/liske/needrestart)
* [https://medium.com/@nobuto_m/knowing-what-services-need-restart-with-needrestart-37419f44ed46](https://medium.com/@nobuto_m/knowing-what-services-need-restart-with-needrestart-37419f44ed46)

Debsecan


* [https://wiki.debian.org/DebianSecurity/debsecan](https://wiki.debian.org/DebianSecurity/debsecan)
* [https://gitlab.com/fweimer/debsecan](https://gitlab.com/fweimer/debsecan)

<!-- Debsums no sirve como herramienta de seguridad, véase otras herramientas tales como: **aide**, **integrit**, **samhain**, **tripwire**.
Debsums


* [https://manpages.ubuntu.com/manpages/trusty/es/man1/debsums.1.html](https://manpages.ubuntu.com/manpages/trusty/es/man1/debsums.1.html)
-->