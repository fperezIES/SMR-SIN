---
icon: material/file-edit
---

---
title: SSH port forwarding
---

En esta práctica vamos a trabajar con las funcionalidades relacionadas con el reenvío de puertos de SSH: Renvío de X11, que nos permite ejecutar aplicaciones con interfaz gráfica sobre máquinas remotas; y reenvío de puertos que nos permiten reenviar conexiones encapsuladas en la conexión SSH.

![SSH](img/vpn_ssh/img.jpg){width=80%}


@. En la máquina Linux en la que vamos a habilitar el servicio SSH debemos ejecutar:

```sh
sudo apt-get install openssh-server
```
# Renvío X11 (X11 Forwarding)

Para el reenvío de ventanas de aplicaciones debemos conectarnos del siguiente modo desde un sistema operativo con entorno X11 (en nuestro caso desde un Linux).
El reenvío de X11 está habilitado por defecto en Ubuntu 14.04, puedes comprobarlo abriendo los ficheros `/etc/ssh/sshd_config` y `etc/ssh/ssh_config` y nos aseguramos que las líneas que las reenvían están activadas. En caso de que no lo estén basta con añadir la sentencia `X11Forwarding yes` en el primer fichero y `ForwardAgent yes` y `ForwardX11 yes` en el segundo.

```sh
ssh -X usuario@servidor
```

Ahora podemos ejecutar una aplicación con entorno gráfico en la máquina remota desde la línea de comandos, para ello escribimos el nombre del ejecutable de la aplicación seguido de `&`para que se ejecute en background. Por ejemplo: gedit, nautilus o wireshark

@. Comprueba el funcionamiento de varias de estas aplicaciones funcionando de forma remota.

```sh
libreoffice&
```

@. Comprueba que los procesos se están ejecutando en el servidor, para ello utiliza el comando ps en el servidor:

```sh
ps aux
```

@. ¿Qué utilidad puede tener este tipo de ejecución de tareas remotas?

# Reenvío de puertos (Tunneling)

Esta funcionalidad permite reenviar tráfico a través del servidor remoto. Permitiendo que todo el tráfico vaya cifrado al ser enviado a través de una conexión SSH.
Por ejemplo, podríamos usarlo para cifrar conexiones a servicios que transmiten datos en claro, como IMAP, VNC, o IRC.
Existen varios tipos de reenvío de puertos: Local, Remoto y Dinámico

## Reenvío de puertos locales (Local port forwarding)

@. En esta modalidad de reenvío, usaremos un túnel SSH para conectarnos a un servidor remoto a través de un servidor SSH intermedio. Esta modalidad puede resultar útil para saltar las limitaciones impuestas por un firewall que limite las conexiones a determinados puertos.

```sh
ssh -L 8080:www.google.es:80 usuario@servidorssh
```

`-L local-port:host:remote-port`, permite mapear un puerto local a un host:puerto
remoto

@. Para probar que funciona abrimos en un navegador la siguiente URL:

```sh
http://localhost:8181
```

Estaremos conectando a la URL indicada usando el servidor SSH como intermediario, y cifrando el tráfico entre el servidor SSH y el ordenador desde el que me conecto. Este tipo de redireccionamiento permite ocultar mis conexiones cuando estamos usando redes poco seguras.

@. Se pueden redirigir incluso puertos de la propia máquina que hace de servidor SSH, vamos a conectarnos de forma segura a un servidor web:


En el servidor instalaremos apache:

```sh
sudo apt update
sudo apt install apache2
```

En el cliente vamos a redirigir el puerto local para poder conectar al servidor en máquina remota:

```sh
sudo ssh -L 80:localhost:80 usuario@servidorssh
```

Para comprobarlo desde el cliente abriremos un navegador y cargaremos la página `http://localhost`


## Reenvío de puertos remotos (Remote port forwarding)
Primero, Instalamos openssh-server en lal máquina cliente:

```sh
sudo apt-get install openssh-server
```

@. El reenvío de puertos remotos, permite exponer un puerto de la máquina local en la máquina remota, por ejemplo, el siguiente comando permitiría conectar a la máquina cliente por SSH desde la máquina servidor.

```sh
ssh -R 19999:localhost:22 usuario@servidorssh
```

@. Ahora conecta desde la máquina servidor SSH a la máquina cliente usando:

```sh
ssh –p 19999 usuario@localhost
```

## Dynamic port forwarding

Nos permite crear un proxy SOCKS para reenviar el tráfico de nuestro ordenador por la conexión SSH. Esto nos permite cifrar todo nuestro tráfico.

@. Para habilitar un servidor proxy a través de SSH, usando el protocolo SOCKS, ejecutamos:

```sh
ssh -C -D 1080 usuario@servidor
```

`-C`, habilita la compresión
`-D 1080`, indica el uso del modo dinámico que corresponde con el proxy SOCKS en el puerto 1080

@. Ahora configuramos nuestra máquina cliente para que use un proxy, configuramos protocolo SOCKS en el puerto 1080, y todo nuestro tráfico será reenviado por la conexión SHH a través de nuestro servidor remoto.

En las preferencias de red de Ubuntu, configurar el Proxy tal como se muestra en la imagen, y pulsar “Aplicar en todo el sistema”:

TODO: img

Ahora todo tu tráfico de la máquina cliente es reenviado a través de la conexión SSH.

12) Abre varias páginas en un navegador web, sin cerrar el túnel, ejecuta en otro terminal el comando:

```sh
netstat -atunp
```

Fíjate que todas las conexiones pasan a través del puerto 1080 y no hay conexiones con el puerto 80, ni con el puerto 443


@. Cierra el túnel e intenta navegar de nuevo, no deberías poder hacerlo. Verás un mensaje de que está fallando el servidor proxy.

@. Elimina la configuración del proxy, y vuelve a navegar. Ahora te estás conectando directamente a la red.

Compruébalo usando el comando ps. Ahora sí que verás conexiones establecidas en los puertos 80 y 443.

```sh
netstat -atunp
```