
# Vulnerabilidades y ataques en redes informáticas

## Introducción

### Introducción

* El **software malicioso o malware** es una de las principales amenazas	 
	* Suelen ser **bandas y mafias** que buscan beneficios
* Protección a nivel de Sistema:
	* Actualizaciones 
	* Antivirus
	* Cortafuegos
	* Formación y concienciación de usuarios

* La exposición aumenta al usar redes
	* Redes corporativas
	* Redes públicas
		* WAN	
		* WiFi abiertas: hoteles, aeropuertos, cafeterías...

### La seguridad heredada

* Internet nace en 1969 (ARPANET)
* Poco después, la pila de protocolos TCP/IP y ethernet se establecieron como estándares de facto a nivel mundial
* Se desarrollaron en **ámbito académico y militar**
	* Pocos usuarios, conocidos y confiables
	* No había motivos para preocuparse por usos malintencionados
* **En el desarrollo de los protocolos de Internet no se contempló la seguridad como una necesidad** 



### Ataques comunes en redes locales


Ataques más comunes en redes:

* Man In The Middle (**MITM**)
* **ARP** Spoofing
* **DNS** Spoofing
* Ataques al **DHCP**
* Ataques a **SSL/TLS**

> Los ataques citados se pueden aplicar tanto en redes cableadas como inalámbrica

### Ataque: Man In The Middle

Los ataques **MITM** (Man In The Middle) son un conjunto de técnicas que permiten a un atacante colocarse de forma inadvertida entre emisor y receptor. Así puede interceptar, modificar, o realizar DoS.

![Ataque MITM](img/06/mitm-attack1.png){width=60%}

> Es habitual interpornerse entre el router de acceso a Internet y la víctima

## ARP Spoofing

### Ataque: ARP Spoofing

> El **ARP** es un protocolo de IPv4:
> 
> * Permite obtener la dirección de nivel 2 (MAC) de un destino a partir de su IP
> * Está basado en peticiones broadcast
> * La respuesta se guardada durante un tiempo en una memoria caché 

El ataque de **envenenamiento ARP o ARP Poison o ARP Spoofing**:

* Es uno de los ataques MITM más habituales
* Afecta a cualquier red IPv4, sino se implementan defensas

### Ataque: ARP Spoofing - Funcionamiento

* Se realiza la falsificación de la dirección MAC de ambas partes mediante el protocolo ARP
	* De esta forma se recibe el tráfico de la víctima y del destino
* Es habitual falsificar la dirección MAC del router para la víctima y la dirección de la víctima para el router 
* Herramientas para ARP spoofing: arpspoof, ettercap, Caín & Abel...

![Ataque MITM](img/06/mitm_attack2.jpg){width=90%}



### Ataque: ARP Spoofing - Contramedidas {.allowframebreaks}


* **Entradas ARP estáticas** en todos los equipos
	* Asociación IP-MAC que realiza ARP en la tabla o caché arp es fijada por el administrador
	* Este procedimiento es eficaz pero **muy laborioso **


* **Inspección ARP en conmutadores/AP**: 
	* **Buena solución**, conmutadores monitorizan el protocolo ARP y rechazan las tramas maliciosas
	* Necesitamos conmutadores o puntos de acceso que soporten estas tecnologías 
	* EJ: Cisco DAI, D-Link ARP spoofing prevention, arp-patrol 
	

* **Monitorización pasiva** con herramientas como detectores de intrusos, arpwatch, arpalert...

* **Monitorización activa** con herramientas como Marmita, Xarp, Patriot-NG, ettercap ...

* **Aislamiento de clientes** en puntos de acceso Wifi: 
	* Además de evitar el ataque impiden la comunicación directa entre dispositivos en la red wifi, lo cual puede ser una funcionalidad requerida

* **Secure ARP - MACSec** (802.1AE)
	* Estándar de firma digital similar a IPSec que se está usando en los servidores de Data Centers
	* Todas las tramas ethernet van firmadas de forma que no es posible suplantar la MAC de nadie

## Ataques al DNS

### Ataques al DNS

> El protocolo **DNS** (Domain Name System) es un servicio que permite conocer la dirección IP asociada a un nombre de dominio. Otras funciones son:
> 
> * Resolución inversa de nombres
> * Información de un dominio
> * servidores de correo o de nombres a asociados a un dominio, 
> * Iegistros de verificación de servidores de correo, etc.

* El DNS es otro de los servicios críticos de Internet y candidato a sufrir muchos ataques.
* Si la respuesta del servidor DNS es manipulada, nos puede **redirigir a servidores fraudulentos**

### Ataque: DNS Spoofing

El **DNS Spoofing** es una técnica que permite manipular las respuestas de un servidor DNS legítimo.

* Usa un ataque MITM previo para dirigir el tráfico DNS de la víctima hacia el atacante. 
* Se evita mediante las contramedidas de MITM ARP

![DNS spoofing reaizado por un atacante con un MITM](img/06/dns_spoof.jpg){width=55%}

### Ataque:DNS Poison

El **envenenamiento DNS** es un ataque más peligroso,  ataca directamente los servidores DNS.  Atacante engaña a servidor para modificar su cache.

* Sevidor DNS proporcionará información errónea a sus clientes
* Es fundamental mantener actualizados y seguros servidores DNS


![DNS Poison](img/06/dns_poison.jpg){width=40%}


### DNSSEC

**DNSSEC** son unas extensiones de seguridad para el DNS 

* Usa firma digital entre los servidores DNS, y así garantiza la autenticidad de la respuesta
* Impide corromper la caché de un servidor DNS
   
* Es totalmente efectivo contra el DNS Poison

DNSSEC no está implantándose lo que debiera debida a su complejidad técnica, que requiere que toda la cadena de respuestas de servidores estén firmadas. 
Poco a poco, los dominios de primer nivel están implantando DNSSEC, pero a un ritmo muy lento. (similar a la implantación de IPv6) 



## Ataques al DHCP

### Ataques al DHCP

> El protocolo **DHCP** (Dynamic Host Configuration Protocol) permite asignar de manera automática la dirección IP, la máscara, la puerta de enlace o router, los DNS, el nombre de dominio y muchas más opciones a los equipos cliente de una LAN

Es un protocolo fundamental en las redes actuales ya que **evita la configuración manual**, con los posibles problema de duplicidad de direcciones.

Este protocolo, que funciona tanto para IPv4 e IPv6, también **fue creado sin tener en mente la seguridad**. 

### Ataque: DHCP Starvation

**DHCP starvation o agotamiento DHCP** 

* Consiste en una denegación de servicio (DoS) al DHCP.
* El ataque consiste en realizar sucesivas peticiones con direcciones MAC falseadas desde el ordenador del atacante
* El servidor asigna todas las direcciones IP disponibles y se agota el rango de IPs
* Los clientes de la red dejan de funcionar por no poder obtener IP


### Ataque: Rogue DHCP

**Rogue DHCP o falso DHCP**, 

* Consiste en montar un servidor DHCP no autorizado en la red
* Su objetivo es modificar la configuración IP de los equipos de la red, ya que éstos no validan al servidor DHCP
* Principalmente se cambia la **puerta de enlace** y los servidores **DNS** para que apunten al atacante, con lo que se consigue hacer: 
	* **MITM** 
	* **y DNS Spoofing**
	* También puede usarse para DoS.

> El ataque rogue DHCP se suele realizar después de un DHCP Starvation, para asegurarse de que el servidor DHCP legítimo no funciona al tener agotado el rango de IPs.


### Ataque: DHCP ACK Injection

En vez de montar un servidor DHCP falso, se realiza un seguimiento de las peticiones DHCP (DHCP Request) de los clientes de la red y **se contesta con una confirmación (DHCP ACK) con los parámetros de configuración** (gateway, dns, etc) manipulados.

Sin embargo:

* La respuesta ACK del servidor legítimo podría llegar antes que la del atacante 
* **No tiene un 100% de fiabilidad desde el punto de vista del atacante**

### Ataques DHCP - Contramedidas {.allowframebreaks}

* Habilitar **port-security en los puertos del switch**: 
	* Esta técnica permite asociar una MAC o un grupo de MAC conocidas, de forma que ante un intento de falsificar la MAC para solicitar una nueva dirección IP, se bloquea el puerto. 
	* Esta técnica sin embargo no sirve para todos los ataques y además debemos tener conmutadores gestionables con esta función

* Habilitar **protección DHCP en los conmutadores y routers**: 
	*  Es necesario tener equipamiento que soporte esta funcionalidad como por ejemplo:
         - Cisco: DHCP Snooping
         - D-Link: DHCP Server Screening

- - -

* **Monitorización activa**: 
	* La solución más viable por coste
	* Detectar y alertar ante un servidor DHCP no autorizado con herramientas software como **dhcp_probe**

## Ataques por eMail

### Correo electrónico

Servicio de mensajería en red más usados desde los **orígenes de Internet**. Se usaba en el año 1961 en el MIT, anteriormente a la creación de Internet. (Entre usuarios de una misma máquina)
  
* El correo electrónico es uno de los servicios más utilizados con fines maliciosos como el spam, las estafas o los hoax
*  Objetivo detrás de estos mensajes es la **distribución masiva de malware** para realizar delitos informáticos de todo tipo. 
*  Cerca de un 95% del correo electrónico es spam según un estudio en EEUU

Su éxito consiste en el envío masivo de mensajes. EJ, si se envían 20 millones de eMails y el 1% de los destinatarios pican, se habrá conseguido que 200.000 personas se infecten con malware o caigan en phisghing

### SPAM

El spam son los mensajes de correoe no solicitados, generalmente de tipo publicitario y enviados en cantidades masivas

* Medio más utilizado es el correo
* Hay medios: grupos de noticias, mensajería instantánea, SMS, redes sociales, wikis, foros, pop-up's, voip, etc

Los **spammers** usan servidores de correo mal configurados (open-relay), ordenadores zombie o cuentas de correo comprometidas para realizar el envío masivo de correos.

Algunas técnicas para evitar el spam son [_SPF_](https://es.wikipedia.org/wiki/Sender_Policy_Framework) o [_DomainKeys_](https://es.wikipedia.org/wiki/DomainKeys_Identified_Mail)


### Hoax y cadenas

Un **hoax o bulo** es un mensaje de correo electrónico con contenido falso o engañoso y normalmente distribuido en cadena. 

Algunos informan sobre virus desastrosos, niños enfermos o desaparecidos, otros contienen fórmulas para hacerse millonario o crean cadenas de la suerte como las que existen por correo postal.

> Las cadenas y los hoax tienen muchas similitudes y los objetivos que se persiguen con ambos son los mismos: captar direcciones de correo para usarse como spam o saturar la red y los servidores de correo.


### Scam

El **scam o estafa** es una variante del spam en el que se produce una estafa y por tanto, pérdida monetaria por parte de la víctima que sufre el scam.

* Hay muchísimas variantes, se identifican porque suelen estar mal traducidos y acaban pidiendo dinero por adelantado.

* El scam también se aplica a sitios web que tienen como intención ofrecer un producto o servicio que en realidad es falso, por tanto una estafa, así como redes sociales o páginas de encuentros.

Un tipo común de mensajes de este tipo, son los que se conocen como cartas o [_estafas nigerianas_](https://es.wikipedia.org/wiki/Estafa_nigeriana). Consiste en engañar al incauto con una supuesta fortuna y persuadirlo para que pague una suma de dinero por adelantado. Son una versión actual del [_timo de la estampita_](https://es.wikipedia.org/wiki/Timo_de_la_estampita) o del [_cuento del tío_](https://es.wikipedia.org/wiki/El_cuento_del_t%C3%ADo).


### Phishing, vishing y smishing


Todos usan **ingeniería social** para aprovecharse de la ingenuidad de la víctima para convencerla de que realice una acción.

* **Phishing**: es una comunicación falsa por _eMail_, suplantando un banco y solicitando el usuario y contraseña del cliente del banco por alguna razón, como un problema en los sistemas informáticos de la entidad.

* **Vishing**, se solicita mediante un eMail que el usuario  que resuelva un problema relacionado con su tarjeta de crédito mediante una llamada _telefónica_. Cuando la víctima llama, una grabación le solicita información personal, como su usuario, contraseña, pin de su tarjeta. También se usan llamadas automatizadas mediante ToIP (lo que es fácil de hacer con software como *Asterisk*). 

- - - 

* El **smishing** es una variante del phishing pero usando los _SMS_ de la telefonía móvil.

Las víctimas reciben SMS con mensajes como:

- "Estamos confirmando que se ha dado de alta para un servicio de citas. Se le cobrará 2 dólares al día a menos que cancele su petición: www.?????.com."
- "El cheque es preparado para usted. Favor gracias de llamarnos para completar las informaciones al número ?????" (nótese que parece que el texto está traducido por algún traductor online).
- "Hola. Anoche lo pasé muy bien contigo. Favor llamame al ????? para quedar"

Cuando visitan la dirección web, las víctimas son incitadas o forzadas a descargar algún programa que suele ser malware.

### Drive by download

Técnica que los ciberdelincuentes utilizan para propagar malware 

* Aprovechan las vulnerabilidades existentes en sitios web  
* Inyectan código malicioso entre el código original del sitio
* El objetivo es infectar de manera masiva los ordenadores de los usuarios desprevenidos con solo entrar a un sitio web comprometido.

Para redireccionar a las víctimas a estos sitios, se utilizan los sistemas de mensajería comentados anteriormente.


### Drive by download: Proceso de Infección

![Drive By Download](img/06/drive_by_download.png){width=90%}

### Drive by download: Proceso de Infección

1. La víctima realiza una consulta al sitio comprometido tras haber recibido algún mensaje por alguna vía.
2. El sitio web consultado devuelve la petición que contiene embebido en su código el script dañino previamente inyectado por el atacante.
3. Una vez que el script se descarga en el sistema de la víctima, se conecta a otro servidor, denominado *Hop Point*, y descarga otros scripts maliciosos que contienen exploits.
4. Cada exploit tiene el objetivo de explotar vulnerabilidades que el equipo víctima.
5. Si se encuentra alguna vulnerabilidad, se infecta el equipo de la víctima con malware.




### eMail: Recomendaciones

* Para minimizar los problemas se recomienda:

	* No descargar adjuntos de correos sospechosos
	* Analizar todos los adjuntos
	* Activar el bloqueo de phising que llevan de serie navegadores como Firefox (Preferencias, Seguridad, Bloquear sitios ...) o Chrome (Opciones, Privacidad, Habilitar protección contra phising)
	* No hacer caso de correos de bancos o servicios pidiendo contraseñas
	* No dar dinero por adelantado en ningún correo que así nos lo soliciten (excepto compras por Internet, lógicamente)
	* Rechazar ofertas y reclamos sospechosos (ganador de un premio, herencia, etc.)
	* No fiarse de los acortadores de URL ni los códigos QR
	* No reenviar cadenas de email


## Seguridad perimetral


### Seguridad perimetral

> **Seguridad perimetral**: conjunto de métodos y técnicas para proteger una red informática que se basan en el establecimiento de recursos de seguridad en el perímetro externo de la red

El perímetro de una red corporativa son los límites entre la red de la organización y las redes a las que ella se interconecta, por ejemplo Internet. 

* El perímetro de la red es la primera zona a proteger frente a ataques externos a la organización
* No obstante, **muchos ataques provienen de usuarios internos** y hay que proteger la red también de estos ataques

### Seguridad perimetral: Zonas o Áreas

En el perímetro de la red se suelen definir niveles de confianza: 

* Permitiendo el acceso de determinados usuarios internos o externos a determinados servicios
* Denegando cualquier tipo de acceso a otros 

La seguridad perimetral también define zonas o áreas donde la seguridad se trata en bloque:

* **Red interna**: red donde se ubican los usuarios internos
* **Red externa**: generalmente es la red Internet
* **Extranet**: zona de acceso de proveedores o empleados
* **DMZ** o zona desmiliarizada: zona donde se ubican los servidores con acceso externo

### Seguridad perimetral: Zonas o Áreas

![Firewall con tres zonas: interna, DMZ e internet](img/06/dmz-firewall.jpg){width=90%}

### Cortafuegos

> **Cortafuegos o firewall**: es un dispositivo software o hardware que forma parte de un sistema o red y que está diseñado para proteger dicho sistema o red bloqueando los accesos no autorizados y permitiendo sólo los autorizados, cumpliendo con las directrices definidas en la política de seguridad de la organización.

Los cortafuegos pueden ser implementados en hardware, software o una combinación de ambos.

* Todo el tráfico que entra o salga de la red pasa a través del cortafuegos que lo examina 
	* Bloquea el tráfico que no cumple los criterios de seguridad especificados en la política.


### Cortafuegos

> **Un cortafuegos en el perímetro no es suficiente para proteger la red** 
  
Es conveniente combinarlo con otros sistemas, como:****

* Los detectores de intrusos (**IDS**/IPS) 
	* Ej: Snort, Suricata... 
* Los dispositivos de gestión unificada de amenazas (**UTM**)
	* EJ de empresas: Fortinet, Sophos, Check Point

### Políticas de acceso en cortafuegos

Hay dos políticas básicas en la configuración de un cortafuegos:

* **Política restrictiva**: Se deniega todo el tráfico excepto el que está explícitamente permitido. Por tanto hay que habilitar expresamente el tráfico de los servicios que se necesiten.

* **Política permisiva**: Se permite todo el tráfico excepto el que esté explícitamente denegado. Cada servicio potencialmente peligroso necesitará ser aislado básicamente caso por caso, mientras que el resto del tráfico no será filtrado

La restrictiva es la más segura, requiere configuración explícita de tráfico entrante, tiene más carga administrativa.

### Tipos de cortafuegos

* Cortafuegos personales: 
	* Software que se instalan en la máquina que se desea proteger 
	* Son típicos en entornos domésticos y empresariales
	* En los servidores ubicados en la DMZ, se usan para “endurecer” todavía más su seguridad. 
	* Ej: Firewall incluido en los sistemas Windows, iptables en los sistemas GNU/Linux. Firewalls de terceros como Zone Alarm o Comodo Pro.

* Cortafuegos perimetrales: 
	* Habituales en entornos empresariales. 
	* Se ubican en el perímetro de la red, en la frontera entre la red interna y la red externa o Internet. 
	*  Por razones de seguridad, es conveniente que sean **equipos dedicados para esa función** y con un S.O. sin vulnerabilidades.
	*  Es muy común instalar cortafuegos hardware o dedicados como los ASA o PIX de Cisco Systems, aunque también se usan mucho los cortafuegos por software.

### Tipos de cortafuegos: en función de capa OSI {.allowframebreaks}

* **De filtrado de paquetes**: 
	* También llamados cortafuegos s**in estado**: filtran el tráfico basándose en IPs, puertos TCP/UDP, tanto de origen como destino.
	*  No realizan seguimiento de conexiones o si forman parte de una secuencia anterior (estado). 
	* Funcionan a nivel de red y transporte (capas 3 y 4 del modelo OSI) como filtro de paquetes IP.  
	* A menudo en este tipo de cortafuegos se permiten filtrados según campos de nivel de enlace de datos como la dirección MAC.

- - -

* **De aplicación**: 
	* También llamados **pasarelas de nivel de aplicación** (ALG, Application Level Gateway) o **proxys** de aplicación. 
	* Actúan sobre la capa de aplicación del modelo OSI. **Entienden las aplicaciones y protocolos para los que están diseñados** (por ejemplo: FTP, DNS o HTTP) y permiten detectar si un protocolo no deseado se coló a través de un puerto no estándar o si se está abusando de un protocolo de forma perjudicial. 
	* Ejemplo: si una organización quiere bloquear el tráfico web relacionado con una palabra en concreto, puede habilitarse el filtrado de contenido para bloquear esa palabra en particular. Es mucho más seguro y fiable cuando se compara con un cortafuegos de filtrado de paquetes, ya que repercute en las siete capas del modelo de referencia OSI, aunque también es más lento. Un ejemplo de cortafuegos de aplicación es ISA Server o Forefront TMG de Microsoft.

- - -

* **De estado**: 
	* Este tipo de cortafuegos permite llevar un seguimiento de cada paquete individual y asignarlo a una **sesión o conexión **previa. 
	* Esta tecnología se conoce generalmente como la inspección de estado de paquetes, ya que mantiene registros de todas las conexiones que pasan por el cortafuegos, siendo capaz de determinar si un paquete indica el inicio de una nueva conexión, es parte de una conexión existente, o es un paquete erróneo. 
	* También se conoce como seguimiento de conexiones (**connection tracking**). 
	* Este tipo de cortafuegos puede ayudar a prevenir ataques contra conexiones en curso o ciertos ataques de denegación de servicio. **Iptables/Netfilter** es un ejemplo de cortafuegos de estado. 

- - - 

* **De próxima generación (NGFW)**: 	
	* Los NGFW (Next Generation FireWall) son hoy en día una realidad y muchos fabricantes de seguridad ofrecen este tipo de cortafuegos que ofrecen, además de las tradicionales, medidas más avanzadas como detección y prevención de intrusos, inspección HTTPS, control de aplicaciones, prevención de pérdida de datos (DLP), VPN, detección de APT's (Amenazas persistentes avanzadas), antispam, antivirus o filtrado de contenidos.


<!--
## 5.4.1  Tipos de ataques al sistema

### Spoofing: 
O suplantación de la personalidad. Este ataque consiste en falsear algún dato de un PC atacado. Este tipo de ataque se usa en redes ethernet conmutadas, es decir, redes que hacen uso de un switch como elemento de interconexión entre diferentes PC's.

* **Arp spoofing**: Consiste en engañar a la tabla arp que los equipos guardan en memoria. Esta tabla relaciona IP con direcciones MAC. Con esta técnica podemos hacer creer al equipo atacado que la IP del atacante es la de otro equipo también atacado en la red.
* **DNS spoofing**: consiste en falsear la respuesta del servidor DNS, por ejemplo para proporcionar la IP de un equipo con una página similar a la de un banco para pedir las contraseñas.

### Sniffing o análisis de tráfico: 

En redes comunicadas mediante hub es un juego de niños ya que el hub difunde todos los mensajes por todos los puertos. Mediante un switch es más complicado porque los mensajes sólo se envían por el puerto adecuado. Para conseguir este ataque se suele utilizar el MAC flooding que consiste en saturar la memoria del switch para que pierda su tabla de direcciones de forma que termina trabajando como un hub y se puede ver todo el tráfico de red sin problema.

#### Inundación de peticiones SYN o SYN Flood:

Consiste en hacer una petición de conexión a un servidor y no contestar. Este ataque produce una saturación en las conexiones abiertas del servidor y puede llegar a producir el colapso del servidor. Con el comando netstat se pueden revisar las conexiones abiertas y comprobar si estamos siendo atacados con este método.

#### Denegación de servicio:

También conocido por sus siglas DoS (Denial of Service). Se realiza contra servidores para evitar que sigan dando el servicio que ofrecen. La mayoría de estos ataques son realizados desde muchos ordenadores que han sido convertidos en zombies. Este ataque se llama DoS distribuido. 

* **Zombie**: Ordenador en el  que un cracker ha instalado software malicioso para hacerse con el control del mismo.
  


-->

## Bibliografía

https://es.wikipedia.org/wiki/Protocolo_de_resoluci%C3%B3n_de_direcciones

http://www.securitybydefault.com/2010/10/como-anadir-una-entrada-estatica-en-la.html

https://es.wikipedia.org/wiki/Sender_Policy_Framework

https://es.wikipedia.org/wiki/DomainKeys_Identified_Mail

