---
icon: material/file-edit
---

# Configuración WPA empresarial
<!-- Tomada de curso seguridad del CEFIRE 2020 -->

## Introducción

En esta práctica vas a usar un servidor RADIUS para poder utilizar seguridad WPA empresarial al configurar una red WiFi.

Usaremos freeRadius como servidor RADIUS, y la interfaz web daloRADIUS para configurarlo.

<!--
![Escenario a replicar en la práctica](img/radius/octo-signal.svg){width=40%}
-->

![Escenario a replicar en la práctica](img/radius/wordmark.svg){width=70%}


### Requisitos

* Un servidor Linux conectado a la red local del aula. Deberás usar una interfaz de red configurada en modo puente.
* Un punto de acceso para comprobar que funciona la instalación. En clase te lo proporcionará el servidor.
* Un móvil (u otro dispositivo WiFi) para conectar al punto de acceso y comprobar que el punto de acceso y el servidor están funcionando correctamente.

## Preparación

Antes de empezar, actualiza el contenido de tus repositorios con el comando:

```sh
sudo apt update
```


## Instalación del gestor de base de datos


@. Instalamos el gestor de base de datos. En esta práctica usaremos MariaDB.

```sh
sudo apt -y install mariadb-server mariadb-client
```


When prompted to set the root password, provide the password and confirm.

@. Comprobamos el estado del servicio con:

```sh
$ systemctl status mariadb
```

  
@. Configuramos la seguridad del gestor de bases de datos, esto implica:
 
* Configurar una contraseña segura para el usuario root de gestor de bases de datos
	- **Inicialmente root no tiene contraseña**
	- Se recomienda usar `smr2` como contraseña en la práctica para evitar posibles olvidos 
* Eliminar las cuentas anónimas
* Deshabilitar el login remoto para el usuario root
* Eliminar la base de datos de pruebas y el acceso a la misma
* Finalmente, recargar la tabla privilegios

Para realizar estos cambios, ejecuta el siguiente comando, establece la contraseña de root y elige sí en el resto de preguntas:

```sh
$ sudo mysql_secure_installation
```
 
@. Actualiza el plugin de autenticación para que permita el usuario root como un usuario normal: 


```sh
$ sudo mysql -u root
```

Y desde la consola SQL:

```sql
MariaDB [(none)]> UPDATE mysql.user SET plugin = 'mysql_native_password' WHERE User = 'root';
MariaDB [(none)]> FLUSH PRIVILEGES;
MariaDB [(none)]> QUIT;
```


@. Comprobamos la instalaicón del gestor de base de datos MariaDB, primero accederemos al gestor de  base de datos como root con el siguiente comando:

```sh
$ mysql -u root -p
```
 
Después, comprobamos la versión:

```sql
MariaDB [(none)]> SELECT VERSION();
```
Finalmente, salimos con el comando: 

```sql
 MariaDB [(none)]> QUIT 
```



## Configuración de la base de datos

@. Una vez instalado el gestor de la base de datos, procedemos a la creación de una base de datos que será usada por el servidor RADIUS:


```sh
$  mysql -u root -p
```
A continuación, desde la consola de MariaDB, creamos una base de datos llamada `radius` y concedemos privilegios al usuario `radius` desde la máquina local con la contraseña `StrongradIusPass`.

```sql
MariaDB [(none)]> CREATE DATABASE radius;
MariaDB [(none)]> GRANT ALL ON radius.* TO radius@localhost IDENTIFIED BY "StrongradIusPass";
MariaDB [(none)]> FLUSH PRIVILEGES;
MariaDB [(none)]> \q
```



@. Comprobamos que el usuario `radius` puede acceder a la base de datos que acabamos de crear, primero accedemos a la consola de MariaDB:

```sh
$ mysql -u radius -p
```
A continuación, desde la consola de MariaDB, ejecutamos:

```sql
MariaDB [(none)]> SHOW DATABASES;
MariaDB [(none)]> QUIT
```


## Instalación de Apache Web Server y PHP

Usaremos el servidor web Apache y PHP para alojar la interfaz web daloRADIUS. Para instalarlos:

```sh
sudo apt -y install apache2
sudo apt -y install php libapache2-mod-php php-{gd,common,mail,mail-mime,mysql,pear,mbstring,xml,curl}
```
@. Instalamos componente DB a través de pearl

```sh
sudo pear install DB
```

@. Comprobamos la versión PHP para comprobar que todo ha ido bien:

```sh
$ php -v
```

@. Comprobamos el estado del servicio del servidor web.

```sh
systemctl status apache2
```

Si el firewall ufw estuviera habilitado deberíamos permitir el acceso a los puertos `http` y `https`. En la instalaicón por defecto de Debian 10 no está habilitado ufw, pero en Ubuntu sí que lo está.

```sh
sudo ufw allow http
sudo ufw allow https
```


## Instalación de FreeRADIUS 

Los paquetes de FreeRADIUS están disponibles en los repositorios de Debian. Así que para instalarlos es suficiente con ejecutar:

```sh
sudo apt -y install freeradius freeradius-mysql freeradius-utils
```

Inicia el servicio después de la instalación:

```sh
sudo systemctl enable --now freeradius.service 
```

@. Comprueba el estado del servicio:

```sh
systemctl status freeradius
```


## Configuración de FreeRADIUS

Vamos a configurar FreeRADIUS para que use MariaDB, para ello:

@. Primero, debes importar el esquema de la base de datos de Radius para inicializar las tablas:

```sh
sudo su
mysql -u root -p radius < /etc/freeradius/3.0/mods-config/sql/main/mysql/schema.sql
```

Después, configura Radius:


@. Creamos un enlace para el módulo SQL:

```sh
sudo ln -s /etc/freeradius/3.0/mods-available/sql /etc/freeradius/3.0/mods-enabled/
```

@. Configura el módulo SQL, cambia los parámetros de conexión a la base de datos para que se adapten a tu instalación, edita el fichero:

```sh
sudo nano /etc/freeradius/3.0/mods-enabled/sql
```

En la sección sql, debes cambiar los siguientes parámetros:

```sh
sql {
driver = "rlm_sql_mysql"
dialect = "mysql"

# Connection info:

server = "localhost"
port = 3306
login = "radius"
password = "StrongradIusPass"

# Database table configuration for everything except Oracle

radius_db = "radius"
}

# Set to ‘yes’ to read radius clients from the database (‘nas’ table)
# Clients will ONLY be read on server startup.
read_clients = yes

# Table to keep radius client info
client_table = "nas"
```

@. Después, cambia los permisos de grupo de `/etc/freeradius/3.0/mods-enabled/sql`

```sh
sudo chgrp -h freerad /etc/freeradius/3.0/mods-available/sql
sudo chown -R freerad:freerad /etc/freeradius/3.0/mods-enabled/sql
```

@. Ejecuta freeradius en modo depuraicón para comprobar que los cambios son correctos.

```sh
sudo systemctl stop freeradius
sudo freeradius -X
```


@. Reinicia el servico raiusd:

```sh
sudo systemctl restart freeradius
```



## Instalación y configuraicón de Daloradius

Para gestionar RADIUS desde una interfaz web vamos  a usar Daloradius. 

@. Descarga la última release de  daloradius desde Github:


```sh
sudo apt -y install wget unzip
wget https://github.com/lirantal/daloradius/archive/master.zip
```

Descomprime el paquete:

```sh
unzip master.zip
mv daloradius-master/ daloradius
cd daloradius
```
@. Importa las tablas de daloRadius

```sh
mysql -u root -p radius < contrib/db/fr2-mysql-daloradius-and-freeradius.sql 
mysql -u root -p radius < contrib/db/mysql-daloradius.sql
```

@. Mueve el directorio de daloRadius a `/var/www/html`:

```sh
cd ..
sudo mv daloradius /var/www/html/
```

@. Cambia los permisos para el directorio http,  crea el fichero de configuración de daloRadius y configura los permisos adecuados:

```sh
sudo chown -R www-data:www-data /var/www/html/daloradius/
sudo cp /var/www/html/daloradius/library/daloradius.conf.php.sample /var/www/html/daloradius/library/daloradius.conf.php
sudo chmod 664 /var/www/html/daloradius/library/daloradius.conf.php
```
@. Ahora, debes modificar el fichero `daloradius.conf.php` para ajustar los valores de conexión a la base de datos.

```sh
sudo nano /var/www/html/daloradius/library/daloradius.conf.php
```

Configura el nombre, usuario y password para la conexión de la base de datos, deja el resto de parámetros sin modificar:

```sh
$configValues['CONFIG_DB_HOST'] = 'localhost';
$configValues['CONFIG_DB_PORT'] = '3306';
$configValues['CONFIG_DB_USER'] = 'radius';
$configValues['CONFIG_DB_PASS'] = 'StrongradIusPass';
$configValues['CONFIG_DB_NAME'] = 'radius';
```

@. Para estar seguro de que todo funciona, reinicia los servicios `radiusd` y `httpd`:
d

```sh
sudo systemctl restart freeradius.service apache2
```



@. Acceso a la interfaz web de daloRADIUS 

La interfaz de gestión de daloradius estará disponible en la URL:
```
http://server_ip_or_hostname/daloradius.
```

Las credenciales por defecto son:

* Username: administrator
* Password: radius

![Interfaz Web daloRADIUS](img/radius/dolaradius_UI.png){widht=40%}

## Probando FreeRADIUS

En este punto ya deberíamos tener instalad y configurado FreeRADIUS. Para comprobar su funcionamiento vamos a crear un usuario desde la interfaz web y mandar una petición de Autenticación al servidor.


### Configuraicón del punto de acceso (NAS)

Para que un usuario se conecte a nuestro servidor RADIUS, es necesario que lo haga a través de un Punto de acceso que harás las funciones de NAS (Network Access Server).


@. Para añadirlo iremos a dolaRadius, apartado `Management > NAS > New NAS`, indicaremos la IP y los datos de identificación del punto de acceso:

![Configuración NAS de AP](img/radius/NAS.png){widht=40%}




### Configuración del usuario

@. Añadimos un usuario a dolaRADIUS mediante `Management > Users > New Users` y rellenando los datos de usuario de forma similar a la siguiente:

![Añadimos usuario RADIUS](img/radius/user.png){widht=40%}

## Prueba con Punto de Acceso



El profesor te proporcionará indicaciones para acceder a la configuración de un punto de acceso. Si no dispones de un punto de acceso para al siguiente apartado.

@. En la configuración de la seguridad del punto de acceso, usaremos WPA2 enterprise, con cifrado AES, añadiremos la IP del servidor RADIUS y el secreto configurado para su IP en el apartado anterior.

![Configuración de seguridad AP](img/radius/cfg_ap_radius.png){widht=40%}

@. Finalmente, sólo queda probar si es posible conectar desde un teléfono al punto de acceso.


## Prueba sin Punto de Acceso

Si no disponemos de un punto de acceso con soporte de WPA empresarial, podemos probar el servidor RADIUS con la utilidad `NTRadPing` que puedes descargar desde:

Antes de ejecutarlo, pondremos el servicio de RADIUS en modo depuración:

```sh
sudo systemctl stop freeradius
sudo freeradius -X
```

@. Ejecuta la utilidad `NTRadPing` para comprobar el correcto funcionamiento del servidor radius.

[https://draculaservers.com/tutorials/wp-content/uploads/2018/12/ntradping.zip
](https://draculaservers.com/tutorials/wp-content/uploads/2018/12/ntradping.zip)

Captura la salida de la consola de depuración del servidor una vez realizada la petición desde la aplicación `NTRadPing`.



## Bibliografía

https://freeradius.org/

http://daloradius.com/

https://computingforgeeks.com/install-freeradius-and-daloradius-on-debian/

https://draculaservers.com/tutorials/install-freeradius-daloradius-debian-9-mysql/

https://networkradius.com/doc/FreeRADIUS%20Technical%20Guide.pdf

https://computingforgeeks.com/how-to-install-mariadb-on-debian-10-buster/

https://blog.khophi.co/radius-server-inside-daloradius-nginx-dd-wrt-captive-portal-linksys-chillispot/

http://deployingradius.com/book/concepts/nas.html






