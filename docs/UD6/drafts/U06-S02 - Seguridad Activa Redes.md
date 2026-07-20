
# Seguridad activa en redes: Acceso a redes




## VLAN

### Arquitecturas de LAN

> ¿Qué puedo hacer para separar los equipos en diferentes redes dentro de una empresa?

![Arquitecturas típias de LAN](img/06_2/VLAN_1.png){width=90%}

### Separación de puertos en VLANs

![División de switch en VLANs](img/06_2/VLAN_2.png){height=90%}

### Protocolo VLAN

Protocolo de etiquetado **IEEE 802.1Q**

* Se basa en el etiquetado de tramas
* Cada VLAN es etiquetada con un número
* Cada puerto del switch es asignado a una VLAN
* Existen los puertos **TRUNK** que permiten llevar tráfico de diferentes VLANS, se usan para interconectar switches




### Comunicación entre VLANs

Interconexión VLANs se realiza a nivel 3

* A través de un router
* A través de switches de nivel 3 (o multicapa)

![Router VLAN](img/06_2/VLAN_3.png){width=90%}

### Ventajas VLAN

* Separación de dominios de Broadcast
* Permite aprovechar mejor los switches, no es necesario duplicar equipos para separar redes



## VPN

### VPN: Introducción

Red de empresa, diferenciamos entre:

* **LAN**: parte privada y confiable
* **WAN**: parte externa e insegura

> ¿Es posible que empleados fuera de la oficina se conecten a la red LAN de la empresa? ¿Es posible unir las redes de oficinas separadas?

2 alternativas:

* Líneas dedicadas 
	* Actualmente circuitos MPLS (Multiprotocol Label Switching)
* Red Privada Virtual (**VPN**)


### Funcionamiento VPN

Basado en el concepto de **túnel**. 

> Una **VPN** permite conectar a la red privada de una empresa a través de Internet de forma segura usando conexiones encapsuladas a través de un túnel que oculta el tráfico mediante cifrado.

* Se usa una conexión insegura para establecer una conexión cifrada segura
* El tráfico se envía a través de la conexión cifrada, **encapsulado** en el payload cifrado. (Se denomina túnel)

### Ventajas e Inconvenientes de VPNs

Ventajas VPN:

* Seguridad
* Bajo coste económico
* Escalabilidad: fácil añadir nuevos nodos
* Movilidad de clientes

Inconvenientes VPN:

* Dependencia de acceso a Internet
* Ancho de banda no asegurado
* Pérdida de ancho de banda en encapsulación


### VPN site-to-site

![VPN site-to-site](img/06_2/VPN_site-to-site.png){width=95%}

### VPN dialup

![VPN site-to-site](img/06_2/VPN_dialup.png){width=95%}


### Protocolos VPN

* **PPTP** (Point-to-Point Tunneling Protocol) 
	* Desarrollado entre Microsoft, 3COM y otros. 
	* Proporciona túneles a las tramas de **PPP** (Point to point protocol)

* **L2TP/IPSec**
	* Desarrollado por IETF	 
	* El paquete IP original se encapsula en una trama PPP
	* La trama PPP se encapsula sobre L2TP (Layer 2 Tunneling Protocol)

* **SSH VPN**
	* Utilizan el software SSH ampliamente instalado de serie en muchos sistemas operativos
	* "VPN de pobres"	 


## Redes WiFi

### Redes WiFi

* Redes de tipo infraestructura
* **Medio compartido** y no guiado (fácil interceptación)
* La WLAN se considera una extensión de la red cableada
![](img/06_2/WiFi_AP.png){width=70%}

### Medidas de protección

* Proteger el **access point** físicamente
	 * Complicada, el AP tiene que estar cerca de los usuarios
* Proteger el access point lógicamente (usuario/contraseña)
* Controlar qué clientes pueden conectarse a él
	* Autenticación
	* Filtrado por MAC
* Usar **varios SSIDs** para separar tipos de usuarios
	* Cada SSID suele estar asociado a una VLAN distinta
* **Cifrar la transmisión equipos y el AP**


### Cifrados en WiFi

* WiFi Abierta: sin contraseña ni cifrado

* **WEP**: Wired Equivalent Privacy 
	* Primer mecanismo de protección WiFi. **Actualmente roto**.

* **WPA** (1999): Wi-Fi Protected Access. **Actualmente roto**

* **WPA2** (2004): **Usado Actualmente **
	* Modo personal (PSK) y modo empresarial

* **WPA3** (Anunciado en 2018)
	* Introduce mejoras de seguridad respecto WPA2
	* Usuarios PSK no pueden leer tráfico de otros (**sí podían con WPA2**)
	* Implantación lenta. **Requiere actualización HW** (caro)


### Tipos de cifrado en WPA

Existen dos :

* **TKIP** (protocolo de integridad de clave temporal)
	* Protocolo que partiendo de una clave compartida (que no es la precompartida) entre el punto de acceso y todas las estaciones, genera nuevas claves diferentes para cada cliente renovables cada cierto tiempo
	* **TKIP es vulnerable** a un ataque de recuperación de keystream, esto es, sería posible reinyectar tráfico en una red que utilizara WPA TKIP

* **AES** (cifrado avanzado estándar) 
	* Algoritmo más robusto y complejo que TKIP
	* Es preferible utilizar AES que TKIP. Requiere HW más potente.
	* **Algunos dispositivos W-Fi antiguos no son compatibles**

### Tipos de cifrado en WPA

![Tipos de cifrado WPA](img/06_2/wpa_top.png){width=90%}

### Autenticación en WPA

* WPA **personal**
	* PSK [Pre-Shared Key] 
	* Todos los clientes usan la misma clave
		* Cambiar la clave supone reconfigurar todos los clientes
		* Cuantos más clientes, más fácil es que la clave caiga en manos equivocadas
	* **En WPA2 cualquiera con la clave puede observar el tráfico ajeno**
	
* WPA **empresarial**
	* Servidor **RADIUS** (Remote Authentication Dial-In User Service)
	* Cada usuario tiene sus credenciales de acceso
	* Es posible quitar acceso a una persona concreta


### WPA empresarial

![WPA empresarial](img/06_2/wifi_raidus.png){width=80%}



## Bibliografía

https://www.networkworld.com/article/3316567/what-is-wpa3-wi-fi-security-protocol-strengthens-connections.html

https://www.howtogeek.com/204697/wi-fi-security-should-you-use-wpa2-aes-wpa2-tkip-or-both/

