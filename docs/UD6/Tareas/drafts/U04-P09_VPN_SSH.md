---
icon: material/file-edit
title: VPN site-to-site con SSH
---

<!-- Tomada de curso seguridad del CEFIRE 2020 -->

# Introducción

En esta práctica vamos a conectar dos sedes remotas de nuestra empresa usando un túnel **PPP sobre SSH**. Este tipo de VPN es conocida como la VPN del hombre pobre, pero es sencilla de configurar en cualquier servidor Linux. El escenario sobre el que vamos a trabajar es el siguiente:

![Escenario a replicar en la práctica](img/vpn_ssh/escenario.png){width=80%}

* Tenemos **dos sedes** distantes, cada una con su propia red LAN independiente. 
* Cada sede tiene un Router que permite a los clientes de la red interna conectarse a internet mediante **NAT**
* En nuestro caso Internet será la red de la propia clase

Vamos a permitir la comunicación entre equipos de ambas sedes conectándolas mediante una VPN de manera que los equipos de ambas sedes sean capaces de comunicarse.


# Preparación

Vamos a necesitar **4 máquinas virtuales** arrancadas simultáneamente (2 clientes y 2 routers), así que debemos configurarlas para consumir poca **RAM**. Se recomienda encarecidamente usar Debian con interfaz Xfce que puede funcionar con **1GB de RAM**.

Si en la preparación de la práctica se clonan las máquinas, se debe:

* **Cambiar las MACs** de las interfaces de red para evitar conflictos.
* **Cambiar el nombre** de la máquina para facilitar reconocer dónde estamos trabajando. Para
ello se debe editar: `/etc/hostname` y `/etc/hosts` y reemplazar el antiguo nombre por el nuevo. También es posible cambiar el nombre de la máquina usando la herramienta `hostnamectl`.

> Nombres a utilizar:
> 
> * **R1**: apellido+R1 (ej: perezR1)
> * **R2**: apellido+R2 (ej: perezR2)
> * **CLIENTE SEDE 1**: apellido+C1 (ej: perezC1)
> * **CLIENTE SEDE 2**: apellido+C2 (ej: perezC2)
> 
> **Durante la realización de la práctica toma una captura en cada uno de los apartados que están numerados**.



## Configuración de interfaces 

Máquinas R1 y R2, tendrán dos interfaces:

* Interfaz 1: Red externa (WAN) -> **Adaptador puente** 
* Interfaz 2: Red interna (LAN): -> **Red interna** 
	* Usa el nombre **LAN1** para la Sede 1 y **LAN2** para la Sede 2

Máquinas Cliente1 y Cliente2:

* Interfaz 1: Red interna (LAN): -> **red interna** (**LAN1** para Sede 1 y **LAN2** para Sede 2

> **Todas** las interfaces se configurarán con **IPs estáticas**. 

* Las IPs pertenecientes a la red externa (En nuestro caso la red de la clase) tendrán una ip en el rango 192.168.0.XX
* El resto de IPs se tomarán del esquema de red mostrado al principio de la práctica 


# Configuración de routers

Antes de comenzar, debes haber configurado las IPs de forma estática tanto de routers como clientes (ver apartado anterior).

## Comprobación de interfaces

Instala net-tools en todas las máquinas:

```sh
sudo apt-get install net-tools
```

@. Muestra el estado de las interfaces de R1:

```sh
R1:$ sudo ifconfig
```

@. Muestra el estado de las interfaces de R2:

```sh
R2:$ sudo ifconfig
```

@. Muestra el estado de las interfaces de C1:

```sh
C1:$ sudo ifconfig
```


@. Muestra el estado de las interfaces de C2:

```sh
C2:$ sudo ifconfig
```


## Configuración como router

Comenzamos configurando los routers para que los clientes puedan acceder a Internet mediante NAT.

Habilita el enrutado **en los dos routers**. Para ello, edita como root /etc/sysctl.conf y pon `net.ipv4.ip_forward=1`
 

```sh
sudo nano /etc/sysctl.conf
```

@. Guarda, y para aplicar los cambios ejecuta (muestra una captura desde R1 y otra desde R2): 

```sh
sudo sysctl -p
```


## Configuración de NAT

**Iptables** es un proyecto de Linux que proporciona funcionalidades de filtrado y reenvío de paquetes. Permite implementar *firewalls* y enrutamiento NAT. En esta práctica lo usaremos para implementar un router NAT. Puedes aprender más sobre este proyecto en [http://www.netfilter.org/](http://www.netfilter.org/)

Para habilitar NAT en los routers con IP tables:


En R1 (debes cambiar `enp0s3` por el nombre de tu adaptador de red):

```sh
R1:$ iptables -t nat -A POSTROUTING -s 172.20.0.0/24 -o enp0s3 -j MASQUERADE
```


En R2 (debes cambiar `enp0s3 ` por el nombre de tu adaptador de red):

```sh
R2:$ iptables -t nat -A POSTROUTING -s 172.30.0.0/24 -o enp0s3 -j MASQUERADE
```


@. Comprueba que el cliente 1 puede salir a Internet

```sh
C1:$ traceroute 1.1.1.1
```

@. Comprueba que el cliente 2 puede salir a Internet

```sh
C2:$ traceroute 1.1.1.1
```


# Configuración SSH

R1 y R2 se conectarán a través de SSH, para ello es necesario que uno de ellos haga de servidor y otro de cliente. En esta práctica **R2 será el servidor SSH y R1 será el cliente SSH**. Además, configuraremos que R1 se pueda conectar mediante criptografía de clave pública para evitar usar contraseñas.

## Instalamos servidor SSH

@. Primero, debemos instalar el servidor SSH en R2:

```sh
R2:$ sudo apt-get install openssh-server
```

Edita el fichero de configuración de SSH mediante el comando:

```sh
R2:$ sudo nano /etc/ssh/sshd_config
```

@ Para que funcione el establecimiento del túnel tenemos que permitir la conexión por SSH del usuario `root`. Para ello, busca la línea que contiene `PermitRootLogi` y asegurate que tenga el valor `yes` y que no esté comentada

```sh
...
PermitRootLogin yes
...
```
Guarda el fichero y finalmente reinicia el servicio SSH:

```sh
R2:$sudo systemctl restart ssh
```



## Configuramos R2 para conexión sin contraseñas

Lo que haremos es configurar R2 para que nos podamos conectar desde R1 con identificación basada en clave pública.

@. Comenzaremos creando un par de claves **desde R1**:

* Te preguntará dónde quieres guardar la clave, pulsa `Enter` para que se guarde en la ruta por defecto.
* Después te preguntará una contraseña para proteger la clave privada, no introduzcas nada. Simplemente pulsa `Enter` de nuevo. De este modo se podrán iniciar conexiones sin necesidad de teclear ninguna contraseña.

```sh
R1:$ ssh-keygen
```



@. A continuación, debemos copiar la clave pública del usuario de R1 al usuario root del servidor SSH (R2), para ello usaremos el comando `ssh-copy-id` que tiene la siguiente sintaxis (debes cambiar ip_remote_host por la IP de R2):

```sh
R1:$ ssh-copy-id root@ip_remote_host
```
Te preguntará el password del usuario remoto para conectar y después copiará la clave pública.

@. Prueba la conexión SSH desde R1 a R2 usando criptografía de clave pública:

```sh
R1:$ ssh username@remote_host
```

Si todo ha ido bien, te deberías poder conectar sin introducir ninguna contraseña.

@. Por último, vamos a deshabilitar la identificación por contraseña en el servicio SSH de R2. Algo imprescindible en equipos conectados a Internet.

Edita el fichero de configuración de SSH mediante el comando:

```sh
R2:$ sudo nano /etc/ssh/sshd_config
```

Busca la línea que contiene `PasswordAuthentication y asegurate que tenga el valor `no` y que no esté comentada

```sh
...
PasswordAuthentication no
...
```

Guarda el fichero y finalmente reinicia el servicio SSH:

```sh
R2:$sudo systemctl restart ssh
```


# Configuración de VPN

En este caso vamos a utilizar PPP sobre SSH, puedes encontrar ejemplos de su utilización en:

[https://wiki.archlinux.org/index.php/VPN_over_SSH](https://wiki.archlinux.org/index.php/VPN_over_SSH)

El comando a utilizar será similar al siguiente:

```sh
sudo pppd updetach noauth silent nodeflate pty "/usr/bin/ssh root@192.168.0.34 /usr/sbin/pppd nodetach notty noauth" ipparam vpn 10.1.1.1:10.1.1.2
```

Debes cambiar la IP 192.168.0.34 por la de tu R2


Si los has hecho bien, aparecerán nuevas interfaces en los dos extremos llamadas **ppp0**.

@. Muestra el estado de las interfaces de R1:

```sh
R1:$ sudo ifconfig
```

@. Muestra el estado de las interfaces de R2:

```sh
R2:$ sudo ifconfig
```


## Rutas 

En el apartado anterior hemos creado el tunel, sólo nos falta configurar los routers para que el tráfico dirigido a las respectivas LAN sea enviado a través del router:


@. En R1:

```sh
R1:$ sudo route add -net 172.30.0.0 netmask 255.255.255.0 gw 10.1.1.1
```

@. En R2:

```sh
R2:$ sudo route add -net 172.20.0.0 netmask 255.255.255.0 gw 10.1.1.2
```

# Comprueba el resultado

@. Realiza un `traceroute` desde el cliente 1 al cliente 2.

@. Realiza un `traceroute` desde el cliente 2 al cliente 1.

# Bibliografía

https://www.cyberciti.biz/faq/how-to-change-hostname-on-debian-10-linux/

http://www.netfilter.org/

https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-debian-10

https://wiki.archlinux.org/index.php/VPN_over_SSH





