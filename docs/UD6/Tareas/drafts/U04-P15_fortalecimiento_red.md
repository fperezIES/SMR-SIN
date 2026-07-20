---
icon: material/file-edit
title: Hardening de TCP IP
---



<!-- 

Referencias:



-->

# Introducción

En esta práctica vamos a ver algunas configuraciones para fortalecer sistemas ante ataques basados en vulnerabilidades de red comunes.

![Hardening TCP IP](img/tcp/tcpip.png){width=40%}



# Configuración de parámetros TCP del kernel

## sysctl 

Es una utilidad que permite cambiar los parámetros del kernel en tiempo de ejecución.

Para conocer algo más sobre esta utilidad:

```sh
man sysctl
```


Los parámetros que se pueden cambiar están listados en el directorio `/proc/sys/`

Tamibién es posible listar los parámetros mediante el comando:

```sh
sysctl -a
```

### Fichero de configuración

Se puede modificar el valor de los distintos parámetros usando el propio comando sysctl, por ejemplo:

```sh
sysctl -w net.ipv4.conf.all.send_redirects=0
```

Y también es posible cambiar los parámetros  editando el fichero de configuración:

sudo cat /etc/sysctl.conf 

```sh 
#
# /etc/sysctl.conf - Configuration file for setting system variables
# See /etc/sysctl.d/ for additional system variables.
# See sysctl.conf (5) for information.
#

#kernel.domainname = example.com

# Uncomment the following to stop low-level messages on console
#kernel.printk = 3 4 1 3

###################################################################
# Functions previously found in netbase
#

# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
# Turn on Source Address Verification in all interfaces to
# prevent some spoofing attacks
#net.ipv4.conf.default.rp_filter=1
#net.ipv4.conf.all.rp_filter=1

# Uncomment the next line to enable TCP/IP SYN cookies
# See http://lwn.net/Articles/277146/
# Note: This may impact IPv6 TCP sessions too
#net.ipv4.tcp_syncookies=1

# Uncomment the next line to enable packet forwarding for IPv4
#net.ipv4.ip_forward=1

# Uncomment the next line to enable packet forwarding for IPv6
#  Enabling this option disables Stateless Address Autoconfiguration
#  based on Router Advertisements for this host
#net.ipv6.conf.all.forwarding=1


###################################################################
# Additional settings - these settings can improve the network
# security of the host and prevent against some network attacks
# including spoofing attacks and man in the middle attacks through
# redirection. Some network environments, however, require that these
# settings are disabled so review and enable them as needed.
#
# Do not accept ICMP redirects (prevent MITM attacks)
#net.ipv4.conf.all.accept_redirects = 0
#net.ipv6.conf.all.accept_redirects = 0
# _or_
# Accept ICMP redirects only for gateways listed in our default
# gateway list (enabled by default)
# net.ipv4.conf.all.secure_redirects = 1
#
# Do not send ICMP redirects (we are not a router)
#net.ipv4.conf.all.send_redirects = 0
#
# Do not accept IP source route packets (we are not a router)
#net.ipv4.conf.all.accept_source_route = 0
#net.ipv6.conf.all.accept_source_route = 0
#
# Log Martian Packets
#net.ipv4.conf.all.log_martians = 1
#

###################################################################
# Magic system request Key
# 0=disable, 1=enable all, >1 bitmask of sysrq functions
# See https://www.kernel.org/doc/html/latest/admin-guide/sysrq.html
# for what other values do
#kernel.sysrq=438

```

Tal como se puede observar se sugieren algunas posibles configuraciones para fortalecer la configuraicón de la pila TCP/IP ante ataques comunes.

Una vez cambiada la configuración podemos aplicar los cambiosn con: 

```sh
sysctl -p /etc/sysctl.conf
```

## Deshabilitar IPv6

Suele venir activado por defecto, tienen implicaciones de seguridad que requieren configuraciones específicas de firewalls.

Riesgos por desconocimiento de IPv6: [https://nira.com/ipv6-security-risks/](https://nira.com/ipv6-security-risks/)


```sh
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1
```




## Evitar ataques Man in the Middle

Es importante establecer unas políticas de seguridad a nivel de red, para ello deberemos bloquear lel redireccionamiento ICMP, IP forwarding y las respuestas ICMP Broadcast. De esta manera evitaremos que un atacante realice ataques "Man in the Middle", evitando que obtuvieran datos confidenciales.


###  ICMP redirects  (prevent MITM attacks)

```sh
# Do not accept ICMP redirects (prevent MITM attacks)
sysctl -w net.ipv4.conf.all.accept_redirects = 0
sysctl -w net.ipv6.conf.all.accept_redirects = 0

#  Do not send ICMP redirects (we are not a router)
sysctl -w net.ipv4.conf.all.send_redirects = 0

```


### IP forwarding

Deshabilitar IP Forwarding, que no es necesario si el equipo no actúa como firewall, router o NAT

```sh
sysctl -w net.ipv4.ip_forward=0
```

No aceptar IP source route packets, ya que no somos un router.

```sh
sysctl -w net.ipv4.conf.all.accept_source_route = 0
sysctl -w net.ipv6.conf.all.accept_source_route = 0
```

### Deshabilitar broadcast ICMP

Es importante eliminar también el broadcast ICMP, que puede ser utilizado para saturar la red:

```sh
sysctl -w net.ipv4.icmp_echo_ignore_broadcasts=1

```

<!--
https://www.tenable.com/audits/items/CIS_Distribution_Independent_Linux_Server_L1_v2.0.0.audit:0bd887213fb6fe5c4159a04e7278af2e
-->

Aceptar solicitudes de marca de tiempo y eco ICMP con destinos de difusión o multidifusión para
su red podría usarse para engañar a su anfitrión para que inicie (o participe) en un ataque Smurf.

Un ataque Smurf se basa en un atacante que envía grandes cantidades de transmisión mensajes ICMP  con una dirección de origen falsificada. Todos los anfitriones que reciben este mensaje y responden enviaría mensajes de respuesta de eco a la dirección falsificada, que probablemente no sea enrutable. Si muchos hosts responden a los paquetes, la cantidad de tráfico en la red podría
multiplicarse significativamente.


## Paquetes con errores

También debemos definir que registre solo aquellos paquetes que cumplan los estándares.

Así, escribimos lo siguiente:

```sh
sysctl -w net.ipv4.icmp_ignore_bogus_error_responses=1

sysctl -w net.ipv4.conf.all.log_martians = 1

sysctl -w net.ipv4.conf.default.log_martians = 1
```

`icmp_ignore_bogus_error_responses`: Algunos routers (y algunos atancates) pueden enviar respuestas que violan la RFC-1122 intentando llenar el sistema de log con numerosos mensajes de error inútiles

Los `Martian packets` [https://en.wikipedia.org/wiki/Martian_packet ](https://en.wikipedia.org/wiki/Martian_packet) (Paquetes con direcciones reservadas para usos especiales) aparecen generalmente como IP suplantadas en ataques de denegación de servicio, pero también pueden aparecer a causa de equipos de red mal configurados o con problemas de funcionamiento.


## Evitar Suplantación de IP (Spoofing)

La verificación de la dirección de origen es una función a nivel de kernel que descarta paquetes que parecen provenir de su red interna, pero no lo hacen.   El **Source Address Verification** (SAV), es un standard formalizado en la RVC 2827 que tiene el objetivo de descartar paquetes con una dirección IP origen suplantada (spoofed). 

Para evitar el spoofing, es decir, para asegurar que el origen de los paquetes es el correcto, escribiremos los siguientes comandos para activar

```sh
sysctl -w net.ipv4.conf.all.rp_filter=1

sysctl -w net.ipv4.conf.default.rp_filter=1
```

rp_filter significa reverse path filtering, el mecanismo consiste en buscar en la tabla de rutas la dirección de origen y si el adaptador por el que ha llegado no se corresponde con el que aparece en la tabla se descartará el paquete.

## Ataque de inundación SYN

Un ataque "SYN" es un ataque de denegación de servicio que consume todos los recursos de una máquina. Cualquier servidor conectado a una red es potencialmente susceptible de ser atacado.

[https://www.cloudflare.com/es-es/learning/ddos/syn-flood-ddos-attack/](https://www.cloudflare.com/es-es/learning/ddos/syn-flood-ddos-attack/)


Se basa en el uso de conexiones sin terminar todo el handshake, de esta manera el servidor deja una cola de conexiones en espera, pudiendo llegar a realizar una denegación de servicio. Para evitarlo podemos utilizar la siguiente configuración:

```sh
sysctl -w net.ipv4.tcp_syncookies=1
```


Alternativiamente podríamos cambiar los párametros que controlan el tamaño máximo de conexiones abiertas y el tiempo que se permitirá que permanezcan en dicho estado.

```sh
sysctl -w net.ipv4.tcp_max_syn_backlog=8192  
sysctl -w net.ipv4.tcp_fin_timeout=3
```

 `net.ipv4.tcp_max_syn_backlog` representa la longitud de la cola SYN, el valor predeterminado es 1024, se recomienda aumentar la longitud de la cola a 8192 o más, lo que puede acomodar más conexiones de red esperando ser conectadas.  
Este parámetro es el valor máximo que usa el servidor para registrar aquellas solicitudes de conexión que no han recibido confirmación del cliente.

`net.ipv4.tcp_fin_timeout`  significa que el socket está cerrado por la solicitud local. Este parámetro determina el tiempo que permanece en el estado FIN-WAIT-2. El valor predeterminado es 60 segundos. En el ejemplo lo reducimos a 3 segundos.

---


<!--
## Enable TCP SYN Cookie Protection



Disable IP Source Routing

Source Routing is used to specify a path or route through the network from source to destination. This feature can be used by network people for diagnosing problems. However, if an intruder was able to send a source routed packet into the network, then he could intercept the replies and your server might not know that it's not communicating with a trusted server.

To enable Source Route Verification, edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.conf.all.accept_source_route = 0
To enable TCP SYN Cookie Protection, edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.tcp_syncookies = 1
Disable ICMP Redirect Acceptance
ICMP redirects are used by routers to tell the server that there is a better path to other networks than the one chosen by the server. However, an intruder could potentially use ICMP redirect packets to alter the hosts's routing table by causing traffic to use a path you didn't intend.

To disable ICMP Redirect Acceptance, edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.conf.all.accept_redirects = 0
Enable IP Spoofing Protection
IP spoofing is a technique where an intruder sends out packets which claim to be from another host by manipulating the source address. IP spoofing is very often used for denial of service attacks. For more information on IP Spoofing, I recommend the article IP Spoofing: Understanding the basics.

To enable IP Spoofing Protection, turn on Source Address Verification. Edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.conf.all.rp_filter = 1
Enable Ignoring to ICMP Requests
If you want or need Linux to ignore ping requests, edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.icmp_echo_ignore_all = 1
This cannot be done in many environments.
Enable Ignoring Broadcasts Request

If you want or need Linux to ignore broadcast requests, edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.icmp_echo_ignore_broadcasts = 1
Enable Bad Error Message Protection
To alert you about bad error messages in the network, edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.icmp_ignore_bogus_error_responses = 1
Enable Logging of Spoofed Packets, Source Routed Packets, Redirect Packets
To turn on logging for Spoofed Packets, Source Routed Packets, and Redirect Packets, edit the /etc/sysctl.conf file and add the following line:

  net.ipv4.conf.all.log_martians = 1
-->


# Bibliografía

* Deshabilitar IPv6
	* https://nira.com/ipv6-security-risks/ 
	* https://linuxhint.com/debian-disable-ipv6-on-interface/ 

https://www.cyberciti.biz/faq/linux-kernel-etcsysctl-conf-security-hardening/

https://cromwell-intl.com/cybersecurity/stack-hardening.html

https://softpanorama.org/Net/Transport_layer/hardening_tcp_stack_in_linux.shtml

https://www.techrepublic.com/article/how-to-disable-ipv6-on-linux/

https://linuxhint.com/debian-disable-ipv6-on-interface/

https://linuxhint.com/debian-disable-ipv6-on-interface/

https://en.wikipedia.org/wiki/Martian_packet

[https://mkorczynski.com/Korczynski-Nosyk2021_Source_Address_Validation.pdf](https://mkorczynski.com/Korczynski-Nosyk2021_Source_Address_Validation.pdf)

* UFW
	* https://wiki.debian.org/es/Uncomplicated%20Firewall%20%28ufw%29