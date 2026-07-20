---
icon: material/file-edit
---

# Introducción


<!--

Existe sucesor nftables https://wiki.nftables.org/wiki-nftables/index.php/What_is_nftables%3F

-->


Un firewall es un sistema de seguridad de red que monitoriza y controla el tráfico entrante y saliente en una red o host basándose en una serie de reglas. 

Es muy importante indicar que un cortafuegos protege del tráfico que atraviesan diferentes redes, pero no protege la red local de ataques que se produzcan dentro de una misma subred.

El firewall por defecto en Linux y el más utilizado es el framework **netfilter** junto a las aplicaciones **iptables** y más recientemente **nftables** (que introduce una nueva sintaxis).

![Netfilter Logo](img/iptables/netfilter-logo.png){width=70%}


El proyecto [**netfilter**](https://www.netfilter.org/index.html) permite el filtrado de paquetes, la traducción de direcciones y puertos de red (`NA[P]T`), el registro de paquetes, la puesta en cola de paquetes en el espacio de usuario y otras manipulaciones de paquetes.

[**iptables**](https://www.netfilter.org/projects/iptables/index.html) es un software de cortafuegos genérico que le permite definir conjuntos de reglas. Cada regla dentro de una tabla de IP consiste en una cantidad de clasificadores (coincidencias de iptables) y una acción conectada (objetivo de iptables).

[nftables](https://www.netfilter.org/projects/nftables/index.html) es el sucesor de [iptables](https://www.netfilter.org/projects/iptables/index.html) , permite una clasificación de paquetes  más flexible, escalable y de mayor rendimiento. 

Actualmente el sistema instalado por defecto es **nftables**, anque la sintaxis de iptables sigue siendo aceptada.

Alternativamente es posible usar **ufw** (The Uncomplicated Firewall) que es un frontend para iptables simplificado, pero perdemos gran cantidadad de opciones de configuración.




# Iptables


## Características de iptables

iptables es un software que se encarga de actuar como un firewall o cortafuegos en nuestra red o host. 


iptables es el cortafuegos incluido en sistemas Linux desde la versión 2.4, aunque actualmente de forma interna se utiliza **nftables** que introdujo una nueva sintaxis.  Hoy en día todavía se utiliza iptables ampliamente en el mundo de la administración de sistemas Linux y también en el mundo de la administración de redes.


iptables dispone de varias **tablas** creadas de forma predeterminada, con el objetivo de facilitar enormemente la posibilidad de añadir diferentes **cadenas** y **reglas**. Un detalle muy importante, es que **de forma predeterminada tenemos todo en «aceptar»**, es decir, todo el tráfico se permite, no hay ninguna regla para denegar el tráfico, pero podemos cambiar la política predeterminada de forma fácil y rápida.

<!--
### Mejoras de rendimiento

Como puedes ver, Iptables son una solución muy buena para realizar configuraciones y administración del firewall del sistema. Pero más allá de las configuraciones que podamos llevar a cabo, Iptables es capaz de mejorar el rendimiento de nuestro sistema de una forma que puede ser bastante significativa. Una de las formas en las cuales lo hace, es a través de la configuración de la tasa de paquetes. Si estos cuentan con actividad limitada de ancho de banda, se pueden establecer la cantidad de los mismos que se pueden enviar o recibir en un tiempo determinado. Esto evita las sobrecargas del sistema, y asegura que el tráfico tenga una distribución uniforme.

Otra de las formas en las cuales nos puede ayudar, es con las reglas de filtrado de paquetes. Si conocemos el tráfico que es innecesario, se pueden establecer reglas para poder bloquearlo antes de que llegue a entrar al sistema. Esto nos va a ayudar a reducir de forma considerable en algunos casos, reducir la cantidad de tráfico que circula por la red, mejorando el rendimiento de una forma generalizada. A esto le podemos sumar las reglas de enrutamiento, las cuales son tremendamente útiles en gran cantidad de casos diferentes.

Cuando disponemos de redes muy complejas, con múltiples segmentos de red o rutas diferentes, estas reglas de enrutamiento nos garantizan que los paquetes que circulan se envían por la ruta más eficiente y directa posible. Esto reduce los tiempos de latencia y de nuevo, mejora el rendimiento general de nuestra red. Por no hablar de todas las funciones avanzadas para los controles de calidad QoS. Esta cuando tienen la configuración adecuada, puede establecer prioridades para garantizar que se cumplen todos los requisitos de ancho de banda. Por lo cual todas las aplicaciones de verán directamente beneficiadas en su rendimiento.
-->

### ¿Qué se puede hacer con iptables?

* **Filtrado**: inspección de paquetes a nivel de protocolo para determinar qué hacer con los paquetes
* **NAT**: Network Address Translation, permite a una máquina actuar como traductor entre redes.
* **Alteración de paquetes**: esto se suele hacer con el uso de iptables, para que esos paquetes pasen a otros programas, y estos en función de las etiquetas, hacen una cosa u otra. Esto se realiza mediante mangle, que con el marcado, prepara los paquetes para un procesamiento futuro.

## Funcionamiento y arquitectura

Aunque en un primer momento iptables pueda parecer sencillo de usar, las carácterísticas avanzadas son algo más complicadas. A continuación, podéis ver un esquema resumido del funcionamiento de iptables.

![Arquitectura IP tables](img/iptables/iptables_esquema_sencillo.jpeg){width=70%}



La estructura de iptables se basa en **tablas**, muchas de ellas ya están creadas de forma predeterminada. Dentro de las tablas tenemos las **cadenas**, que también tenemos algunas creadas de forma predeterminada. Finalmente, dentro de las cadenas tenemos las diferentes **reglas** que podemos configurar. 

### Tablas 

De forma predeterminada tenemos un total de cinco tablas:

-   **Tabla filter**: es la tabla por defecto, si no definimos una tabla para añadir una regla, siempre se irá a la tabla filter. En esta tabla tenemos un total de tres cadenas por defecto, dependiendo de lo que nos interese, tendremos que usar una de las siguientes cadenas: 
	- **INPUT**: son los paquetes en sentido entrante, los que llegan al sistema
	- **OUTPUT**: son los paquetes en sentido saliente, desde el equipo hacia fuera 
	- **FORWARD**: sirve para filtrar los paquetes que van de una interfaz de red a otra, paquetes enrutados.

-   **Tabla nat**: esta tabla se encarga de hacer el NAT, transformar la IP privada en pública y al revés. Dentro de NAT tenemos tres cadenas: 
	- **PREROUTING**: altera los paquetes antes de enrutarlos, aquí se hace el **DNAT** o reenvío de puertos
	- **POSTROUTING**: altera los paquetes después de enrutarlos, aquí se hace el **SNAT** o **MASQUERADE**
	- **OUTPUT**: paquetes generados por el firewall que atravesará el NAT configurado

-   **Tabla mangle**: esta tabla se encarga de hacer la alteración de los paquetes, es donde se configura el QoS para la calidad del servicio, alterar cabeceras TCP etc. En esta tabla tenemos las cinco cadenas: **PREROUTING, INPUT, FORWARD, OUTPUT, POSTROUTING**.

-   **Tabla raw**: esta tabla no se suele usar. Sirver para configurar excepciones para evitar seguir ciertas conexiones, se usa junto al target NOTRACK. Tenemos la cadena PREROUTING y OUTPUT.

- Tabla security: Esta tabla se utiliza para reglas de red Mandatory Access Control (MAC), como las que permiten los objetivos: **SECMARK** and **CONNSECMARK**. Tienel las cadenas: INPUT, OUTPUT y FORWARD.

### Reglas 

El funcionamiento a la hora de añadir una regla es el siguiente:

-   Las reglas que incorporamos a las cadenas siempre tienen un objetivo (target) (**\-j** en la regla).
-   Cuando el firewall recibe un paquete, comprueba si el paquete concuerda con una regla que hayamos dado de alta. Si concuerda, se ejecuta la orden objetivo, sino, pasa a la siguiente regla hasta el final de la cadena.
-   **Las reglas se verifican en un orden secuencia**l, desde la primera regla hasta la última. Es muy importante el orden, si bloqueamos todo primero y luego permitimos algo más específico, bloquearemos todo el tráfico y la regla más específica no se comprobará.
-   En el caso de que ninguna regla satisfaga el paquete, entonces se utilizará la política de la cadena que tengamos (regla global).

### Objetivos (Targets)

Los objetivos que tienen las diferentes reglas son las siguientes:

-   **ACCEPT**: acepta el paquete y lo pasa al siguiente nivel
-   **DROP**: bloquea el paquete y no lo pasa al siguiente nivel.
-   **QUEUE**: es un objetivo especial, que pasa al paquete en una cola destinada al procesamiento en espacio de usuario. Esto se puede usar para utilizar otros programas externos.
-   **RETURN**: tiene el mismo efecto que si hubiésemos llegado al final de la cadena. Si la regla estaba en una cadena de las que hay por defecto, entonces se ejecuta la política de la cadena. Para una regla que esté en una cadena definida por el usuario, se sale de ella, y se continúa atravesando la cadena anterior al salto, justo después de la regla con la que se saltó.


## Comandos básicos de iptables

Lo primero que debemos tener en cuenta a la hora de configurar este firewall, es que **necesitamos permisos de superusuario para realizar las diferentes órdenes.** Es totalmente necesario para que se ejecuten, de lo contrario no funcionará. Las siguientes órdenes no llevan «sudo» porque suponemos que ya estás con el superusuario (sudo su).

### Argumentos principales 

-   **-t**, --table table Selecciona la tabla que queramos
-   **-A**, --append chain rule-specification Añadimos una nueva regla en una determinada cadena
-   **-C**, --check chain rule-specification Comprobamos que existe una determinada regla en una determinada cadena
-   **-D**, --delete chain rule-specification Borramos la regla que pongamos en una determinada cadena
-   **-D**, --delete chain rulenum Borramos la regla número X en una determinada cadena
-   **-I**, --insert chain [rulenum] rule-specification Insertamos una nueva cadena con un número en una determinada tabla
-   **-R**, --replace chain rulenum rule-specification Reemplaza una determinada cadena en una tabla, sirve para moverla de número.
-   **-L**, --list [chain] Muestra el listado de reglas de una cadena
-   **-F**, --flush [chain] Elimina todas las reglas de una determinada cadena.
-   **-Z**, --zero [chain [rulenum]] Pone los contadores de una determinada regla a 0.
-   **-N**, --new-chain chain Creamos una nueva cadena en una determinada tabla
-   **-X**, --delete-chain [chain] Borramos una determinada cadena (vacía) en una determinada tabla
-   **-P**, --policy chain target Aplicamos la política por defecto, se cumple cuando ninguna regla de las cadenas se cumplen.
-   **-E**, --rename-chain old-chain new-chain Renombra una cadena añadida anteriormente
-   **-h**, muestra la ayuda
-   **-v**, --verbose Salida que se usa en conjunto con –L, sirve para mostrar más información que lo que proporciona el comando –L.
-   **-n**, --numeric Las direcciones IP y los números de puertos aparecerán con números. Por ejemplo si filtramos el puerto 80, con el –L típico aparecerá www, y no 80.
-   **-x**, --exact Muestra el valor exacto del contador de paquetes y bytes, en lugar de usar K, M o G para los valores.
-   **--line-numbers Al mostrar el listado de reglas, mostrará el número exacto de la regla. Ideal para usar –D y el número (eliminar) o –I para introducir delante o detrás de dicha regla.**

### Condiciones principales

-   **-p**, --protocol protocol. Filtra el paquete por protocolo, el protocolo especificado puede ser: **tcp, udp, Idplite, icmp, esp, ah, sctp**.

-   **-s**, --source address\[/mask\]\[,…\]. Dirección IP de origen del paquete. Podemos tener una IP o una subred (indicando la máscara en formato CIDR). También podemos poner nombres de host (dominios, webs etc), pero es una mala idea porque no es eficiente. Se pueden especificar varias direcciones de origen, (192.168.1.1,192.168.1.2) pero creará diferentes reglas para satisfacerlas.

* **-d**, --destination address[/mask][,…]. Dirección IP de destino del paquete. Se comporta exactamente igual que -s.

* **-m**, --match match. Especifica si queremos llamar a los módulos extendidos de iptables, para realizar determinadas acciones como:

    -   Poner múltiples puertos de origen y destino (módulo multiport).
    
    -   Controlar conexiones (módulo conntrack).
    
    -   Evitar fuerza bruta (módulo recent, ideal para SSH).
    
    -   Limitar el número de conexiones (módulo limit y connlimit).
    
    -   Rango de direcciones IP (iprange).



* **-j**, --jump target. Especifica el **objetivo(target)** de la regla, si queremos aceptar, denegar e incluso reenviar el paquete a otra cadena para su posterior tratamiento. Siempre en cualquier regla vamos a tener un –j para decirle qué queremos hacer. Si no incorporamos un –j se añadirá la regla y contará los paquetes, pero no hará nada. Si utilizamos –j para reenviar a otra cadena, una vez que en la otra cadena haya terminado, volverá a la original.

-   **-g**, --goto chain. Sirve para reenviar el tráfico a otra cadena, pero a diferencia de jump, no volverá a la cadena original por donde entró.

-  **-i**, --in-interface name. Nombre de la interfaz por donde un paquete se recibe. Solo vale para las cadenas de entrada como INPUT, FORWARD y PREROUTING. Si ponemos ! Significa todas menos esa interfaz. Si ponemos un + al final del nombre, cualquier interfaz con el inicio del nombre la cogerá para comprobarla. Imaginemos eth0, eth1 y eth2. Si queremos poner las tres simplemente con poner eth+ es suficiente.

-  **-o**, --out-interface name. Nombre de la interfaz por donde un paquete sale. Solo vale para las cadenas de salida como OUTPUT, FORWARD y POSTROUTING.


### Condiciones puerto TCP o UDP

Si utilizas el protocolo TCP o UDP, es posible que quieras filtrar por número de puerto origen y/o destino, a continuación, tenéis los dos argumentos que puedes usar:

-  --sport --source-port. Selecciona puertos de origen para permitir o denegar. Si usamos ! excluye.
-   --dport --destination-port. Selecciona puertos de destino para permitir o denegar. Si usamos ! excluye.

Existen muchas mas condiciones para una configuración avanzada del firewall, pero las elementales ya las tenemos listadas.

### Reestablecer configuración de iptables 

En las últimas versiones de Linux no es posible parar el servicio de iptables, **para poder permitir todo el tráfico de red y dejar el firewall con los parámetros por defecto**, tenemos que ejecutar las siguientes órdenes:

```
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```

**Estos comandos eliminan las reglas y cadenas de las tablas filter, nat y mangle. Además establecen la política por defecto de aceptar todo el tráfico**.

Una vez que hayamos hecho esto, ya tendremos el firewall «reseteado» y con el permitir todo. Ahora que ya sabemos cómo resetearlo, vamos a ver las diferentes órdenes.

### Visualizar las tablas actuales

Si quieres ver el contenido de las diferentes cadenas y reglas que tenemos en una determinada tabla, a continuación, puedes ver cómo se verían:

```
iptables -t filter --list
iptables -t mangle --list
iptables -t nat --list
iptables -t raw --list
```



### Ver el estado del firewall

El parámetro -L muestra las reglas que tenemos configuradas. V permite recibir más información sobre las conexiones y N nos devuelve las direcciones IP y sus correspondientes puertos sin pasar por un servidor DNS.

```
iptables -L -n -v
```

Este es uno de los comandos más importantes para ver el estado del cortafuegos.

### Configurar la política por defecto

Las políticas sirven para que cuando no se encuentra una regla dentro de la cadena, se ejecute la política por defecto. La política por defecto de todas las cadenas es **ACCEPT**, pero hay dos opciones: **ACCEPT** o **DROP**

**-P, –policy chain target**

Ejemplo de poítica restrictiva:

```
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP
```

Con esto bloquearíamos todo el tráfico, por lo que a continuación debemos empezar a crear reglas que permitan el tráfico que nos gustaría permitir.

A continuación, podéis ver todas las políticas que tenemos en las diferentes tablas:

-   iptables -t filter -P (INPUT | OUTPUT | FORWARD) (ACCEPT | DROP)
-   iptables -P (INPUT | OUTPUT | FORWARD) (ACCEPT | DROP)
-   iptables -t mangle -P (INPUT | OUTPUT | FORWARD | PREROUTING | POSTROUTING) (ACCEPT | DROP)




## Módulos (-m match) de iptables

#### Multiport

Es una extensión de iptables que nos da la posibilidad de agrupar reglas similares con diferentes puertos TCP y UDP en una sola. Multiport permite poner varios puertos salteados, y también varios puertos seguidos, el máximo es de 15 argumentos de puertos. Ejemplo:

```
iptables –A INPUT –p tcp –m multiport --dports 80,81,1000:1200 –j ACCEPT
```

Gracias a este módulo, tenemos la posibilidad de usar varios puertos en la misma regla.

#### iprange

iprange nos permite poner varias direcciones IP de origen o destino de una vez, sin necesidad de poner decenas de reglas. También permite poner tanto IP de origen como de destino, la sintaxis es la siguiente:

```
[!]--src-range ip-ip
[!]--dst-range ip-ip
```

El funcionamiento de esta regla es bastante sencilla, simplemente ponemos IP inicio e IP final.

#### Connlimit

El módulo connlimit se encarga de **restringir el número de conexiones simultáneas** realizadas por una dirección IP, es ideal para limitar el número de conexiones para evitar DoS.

-  --connlimit-upto n . Marcamos si el número de conexiones es igual o menor que N (luego podemos permitir o denegar).
-  --connlimit-above n . Marcamos si el número de conexiones es mayor que N (luego podemos permitir o denegar).
-  --connlimit-mask prefix_length . Marcamos por rango de subred (es igual que un host haga 2 conexiones, que dos host de la misma subred hagan 1 conexión cada uno).
-  --connlimit-saddr . Aplicar la limitación al grupo de origen, es la de por defecto si no se especifica nada.
-   --connlimit-daddr . Aplica la limitación al grupo de destino.

Por ejemplo, imaginemos que queremos permitir dos conexiones SSH por cliente únicamente:

```
iptables -A INPUT -p tcp --dport 22 -m connlimit --connlimit-above 2 -j DROP
```


#### Conntrack

Este módulo sirve para realizar un tracking de las conexiones, sirve para gestionar la entrada y salida de paquetes antes y después del establecimiento de la conexión. Dentro de este módulo hay varias opciones, pero la más importante es **--ctstate** que nos permite aceptar o denegar diferentes tipos de paquetes. Dentro de ctstate tenemos varios estados, destacan los siguientes:

-   INVALID: El paquete recibido es inválido y no pertenece a ninguna conexión.
-   NEW: Conexiones nuevas que se realizan, o que está asociado a una conexión que aún no es bidireccional.
-   ESTABLISHED: Conexiones establecidas, pasan primero por NEW ya que han tenido respuesta
-   RELATED: Paquete que está relacionado a una conexión existente, pero que no es parte de ella, como FTP pasivo.

Imaginemos que queremos acceder a cualquier sitio, sin embargo, no queremos que absolutamente nadie acceda a nosotros.

```
iptables –P INPUT DROP
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```

#### Limit

El módulo limit nos permite limitar, tanto tráfico, como número de logs a escribir en el syslog como intentos de conexión. Tiene dos argumentos principalmente:

-   --limit N . Este argumento especifica el número máximo de coincidencias de media por segundo (N/s), minuto (N/m), hora (N/h) o día (N/d) a permitir. N especifica el número
-   --limit-burst N . Indica la ráfaga más larga que se puede producir antes de comprobar el límite --limit.

El valor por defecto si no se especifica nada es 3 coincidencias por hora, a ráfagas de 5. Imaginemos la siguiente regla: «iptables -A INPUT -m limit -j LOG», el funcionamiento es el siguiente:

-   La primera vez que se alcanza esta regla, se registran los primeros cinco paquetes.
-   Después, pasarán veinte minutos antes de que vuelva a registrarse un paquete con esta regla (3 coincidencias entre 60 minutos igual a 20 minutos, ya que es MEDIA).
-   Además, cada veinte minutos que pasen sin que un paquete alcance la regla, la ráfaga recuperará un paquete.
-   Si no sucede nada durante 100 minutos, la ráfaga quedará completamente recargada; de vuelta entonces a la situación inicial.

#### Recent

El módulo recent sirve para limitar el número de conexiones por segundo a nivel de IP, esto es ideal para protegernos de ataques al puerto SSH porque un atacante probará múltiples contraseñas. Por ejemplo, si queremos proteger nuestro SSH, podríamos ejecutar la siguiente regla:

```
iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --set --name ssh –rsource
iptables -A INPUT -p tcp --dport 22 -m state –state NEW -m recent --rcheck --seconds 60 --hitcount 4 --name ssh --rsource -j DROP
````


Esta regla permite solamente hacer cuatro intentos de conexión al cabo de 60 segundos, es una ventana «deslizante».


## Ejemplos básicos de utilización

Comencemos con ejemplos sencillos.

Bloquear la dirección IP 192.168.1.10 para que no realice ninguna comunicación a nuestro servidor:

```
iptables -A INPUT -s 192.168.1.10 -j DROP
```

Bloquear las direcciones IP 192.168.1.20, 192.168.1.30, 192.168.1.40, 192.168.1.50 para que no realicen ninguna comunicación a nuestro servidor:


```
iptables -A INPUT –s 192.168.1.20,192.168.1.30,192.168.1.40,192.168.1.50 –j DROP
```


Bloquear toda la subred 192.168.2.0/24 para que no realicen ninguna comunicación a nuestro servidor, excepto la dirección IP 192.168.2.20 que sí se permitiría:

```
iptables –A INPUT –s 192.168.2.20 –j ACCEPT
iptables –A INPUT –s 192.168.2.0/24 –j DROP
```

Bloquear la dirección IP de 192.168.4.50 para que nosotros no podamos hacer ninguna comunicación:

```
iptables –A OUTPUT –d 192.168.4.50 –j DROP
```

Bloquear las direcciones IP 192.168.4.51 y 192.168.4.52 para que nosotros no podamos hacer ninguna comunicación:

```
iptables –A OUTPUT –d 192.168.4.51,192.168.4.52 –j DROP
```


Bloquear el acceso a www.google.es desde iptables:

```
iptables –A OUTPUT –d www.google.es –j DROP
```


Queremos bloquear el acceso a nuestro servidor de la MAC 00:01:02:03:04:05, y el resto permitir todo:


```
iptables -A INPUT -m mac --mac-source 00:01:02:03:04:05 -j DROP
```


Queremos permitir el acceso a nuestro servidor a la dirección MAC MAC 00:01:02:03:04:06, y el resto denegar todo:


```
iptables -A INPUT -m mac --mac-source 00:01:02:03:04:05 -j ACCEPT  
iptables -A INPUT -j DROP
```


Bloquear la dirección IP 192.168.1.10 para realizar ping desde nuestro nuestro servidor:

```
iptables -A INPUT -s 192.168.1.10 -p icmp -j DROP
```


Bloquear las direcciones IP 192.168.1.20, 192.168.1.30, 192.168.1.40, 192.168.1.50 para que no realicen PING a nuestro servidor (en una sola regla):

```
iptables -A INPUT -s 192.168.1.20,192.168.1.30,192.168.1.40,192.168.1.50 -p icmp -j DROP
```

Bloquear toda la subred 192.168.2.0/24 para que no realicen PING a nuestro servidor, excepto la dirección IP 192.168.2.20 que sí se permitiría:

```
iptables -A INPUT -s 192.168.2.20 -p icmp -j ACCEPT  
iptables -A INPUT -s 192.168.2.0/24 -p icmp -j DROP
```


Bloquear la dirección IP de 192.168.4.50 para que nosotros no podamos hacer ping.

```
iptables -A OUTPUT -d 192.168.4.50 -p icmp -j DROP
```

Bloquear las direcciones IP 192.168.4.51 y 192.168.4.52 para que nosotros no podamos hacer ping.

```
iptables -A OUTPUT -d 192.168.4.51,192.168.4.52 -p icmp -j DROP
```

Bloquear el ping a www.google.es desde iptables.


```
iptables -A OUTPUT -d www.google.es -p icmp -j DROP
```


Tal y como podéis ver, el funcionamiento es bastante sencillo con las reglas basadas en IP con origen y destino. También podríamos usar el módulo **iprange** para configurar un rango de IPs:

Bloquear un rango de direcciones IP que van desde 192.168.5.1 hasta la dirección 192.168.5.50 para que no puedan hacer ping desde nuestro servidor.

```
iptables -A OUTPUT -m iprange --dst-range 192.168.5.1-192.168.5.50 -p icmp -j DROP
```

También podemos filtrar de forma más avanzada el protocolo ICMP. Imaginemos que tenemos un usuario que quiere poder hacer ping a cualquier host, pero sin embargo, no quiere que NADIE le pueda hacer ping a él. ¿Cómo lo podemos hacer con iptables si el propio PING tiene comunicación bidireccional?

```
iptables -A INPUT -s IP -p icmp --icmp-type echo-request -j DROP
```

Bloquear cualquier acceso entrante por la interfaz eth0 (únicamente), por lo tanto permitir el acceso eth1.

```
iptables -A INPUT -i eth0 -j DROP
iptables -A INPUT -i eth1 -j ACCEPT
```


Bloquear el tráfico saliente por la interfaz eth0 (únicamente), por lo tanto permitir el acceso eth1.

```
iptables -A OUTPUT -o eth0 -j DROP
iptables -A OUTPUT -o eth1 -j ACCEPT
```

Permitir cualquier tipo de tráfico entrante y saliente por eth0, y denegar cualquier tráfico entrante o saliente por eth1.

```
iptables -A INPUT -i eth0 -j ACCEPT
iptables -A OUTPUT -o eth0 -j ACCEPT
iptables -A INPUT -i eth1 -j DROP
iptables -A OUTPUT -o eth1 -j DROP
```

## Ejemplos  con puertos TCP y UDP

Si quieres empezar a ver cómo funciona el protocolo TCP y UDP, a continuación tenéis algunos ejemplos:

Para permitir conexiones a SSH

```sh
iptables -A INPUT -p tcp -s 203.0.113.0/24 --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -m conntrack --ctstate ESTABLISHED -j ACCEPT
```


Bloquear el acceso web a www.google.es y permitir todo lo demás (ping) por ejemplo.

```
iptables -A OUTPUT -d www.google.es -p tcp --dport 80 -j DROP
```

Bloquear el acceso FTP a cualquier IP o dominio, y permitir todo lo demás.

```
iptables -A OUTPUT -p tcp --dport 21 -j DROP
```


Bloquear el acceso SSH a la IP 192.168.1.50, y permitir todo lo demás.

```
iptables -A OUTPUT -d 192.168.1.50 -p tcp --dport 22 -j DROP
```


Bloquear el acceso Telnet a la subred 192.168.2.0, y permitir todo lo demás.

```
iptables -A OUTPUT -d 192.168.2.0/24 -p tcp --dport 23 -j DROP
```


Bloquear el acceso de 192.168.1.50 a nuestro servidor web.

```
iptables -A INPUT -s 192.168.1.50 -p tcp --dport 80 -j DROP
```

Bloquear el acceso de 192.168.1.150 y 192.168.1.151 a nuestro servidor SSH.

```
  iptables -A INPUT -s 192.168.1.150,192.168.1.151 -p tcp --dport 22 -j DROP
```

Bloquear el acceso de toda la subred 192.168.2.0/24 a nuestro servicio telnet

```
iptables -A INPUT -s 192.168.2.0/24 -p tcp --dport 23 -j DROP
```

Bloquear el acceso de todo el mundo al servidor OpenVPN, excepto la dirección IP 77.77.77.77 que sí se permite.

```
iptables -A INPUT -s 77.77.77.77 -p tcp --dport 1194 -j ACCEPT
 iptables -A INPUT -p tcp –dport 1194 -j DROP
```

Bloquear el acceso DNS a 8.8.8.8 y permitir todo lo demás (ping) por ejemplo.

```
iptables -A OUTPUT -d 8.8.8.8 -p tcp --dport 53 -j DROP
iptables -A OUTPUT -d 8.8.8.8 -p udp --dport 53 -j DROP
```

Bloquear el acceso al puerto 1194 a cualquier IP o dominio, y permitir todo lo demás.

```
iptables -A INPUT -p udp --dport 1194 -j DROP
```

Tenemos un servidor DNS en nuestro servidor. Queremos que solo los equipos de la subred 192.168.1.0/24 se puedan comunicar con él, y bloquear todos los demás accesos.

```
iptables -A INPUT -s 192.168.1.0/24 -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -s 192.168.1.0/24 -p udp --dport 53 -j ACCEPT
iptables -A INPUT -p tcp --dport 53 -j DROP
iptables -A INPUT -p udp --dport 53 -j DROP
```

Bloquear el acceso a nuestro servidor web, del rango de IP 192.168.100.0/24, que proviene de la interfaz eth0.

```
iptables -A INPUT -s 192.168.100.0/24 -i eth0 -p tcp --dport 80 -j DROP
```

Bloquear el acceso a nuestro servidor ssh, del rango de IP 192.168.100.0/24, que proviene de la interfaz eth1.

```
iptables -A INPUT -s 192.168.100.0/24 -i eth1 -p tcp --dport 22 -j DROP
```

## Ejemplos avanzados de uso

Si quieres aprender más sobre iptables, a continuación, tenéis algunos ejemplos donde hacemos uso del módulo connlimit.

Permitir únicamente 10 conexiones Telnet por cliente.

```
iptables -A INPUT -p tcp --dport 23 -m connlimit --connlimit-above 10 --connlimit-mask 32 -j DROP
```

Permitir únicamente 10 conexiones Telnet por cliente (hacerlo de otra manera al anterior).

```
iptables -A INPUT -p tcp --dport 23 -m connlimit --connlimit-upto 10 --connlimit-mask 32 -j ACCEPT
 iptables -A INPUT -p tcp --dport 23 -j DROP
```

Permitir únicamente 10 conexiones web en el rango de IP 10.0.0.0/8, y denegar si supera este número.

```
iptables -A INPUT -s 10.0.0.0/8 -p tcp --dport 80 -m connlimit --connlimit-above 10 --connlimit-mask 8 -j DROP
```

Permitir únicamente 20 conexiones HTTP por cada cliente, en cuanto se supere mandamos un TCP Reset.

```
iptables -A INPUT -p tcp --dport 80 -m connlimit --connlimit-above 20 --connlimit-mask 32 -j REJECT --reject-with tcp-reset
```

O este forma:

```
iptables -A INPUT -p tcp –syn --dport 80 -m connlimit --connlimit-above 20 --connlimit-mask 32 -j REJECT --reject-with tcp-reset
```

Tal y como podéis ver, este firewall es realmente completo y podremos hacer una gran cantidad de configuración muy avanzadas, para controlar en detalle todas las conexiones entrantes y salientes.

También cabe recordar que los cambios que podamos realizar en la configuración de iptables, son efímeros, por lo cual debemos guardarlos para que estos persistan tras posibles reinicios que se puedan producir en el servidor.


### NAT

Este firewall también se encarga de hacer NAT de nuestra conexión. Para hacer el NAT de nuestra IP pública (o interfaz que tenga esta IP pública), debemos poner:

```

SNAT Estático: 

iptables -t nat -A POSTROUTING -s 192.168.1.0/24 –o eth1 -j SNAT –to IP_eth1


SNAT Dinámico: 

iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth1 -j MASQUERADE
```


Lo normal es usar MASQUERADE para hacer NAT independientemente de la dirección IP que tenga la interfaz física o lógica.

Para abrir puertos tenemos que añadir una regla en la cadena PREROUTING de la tabla NAT.

```
iptables -t nat -A PREROUTING -i eth1 -p tcp –dport 22 -j DNAT --to-destination 192.168.1.1
```

La tabla PREROUTING también nos permite modificar los puertos al vuelo, de tal forma que si recibimos paquetes en el puerto 2121, lo podemos transformar al 21.

```
iptables -t nat -A PREROUTING -i eth1 -p tcp –dport 2121 -j DNAT --to-destination 192.168.1.1:21
```


Una vez que ya conocemos en detalle este cortafuegos, vamos a ver los ejemplos básicos de utilización y también los más avanzados.



## Haciendo las reglas persistentes

Las reglas de iptables se guardan en memoria, se perderían al reiniciar el equipo. Si queremos hacerlas persistentes, una forma sencilla es usar el paquete `iptables-persistent`


```
sudo apt install iptables-persistent
```

Durante la instalación se preguntará si quieres guardar tus rehlas actuales.


Si cambias las reglas y deseas que se guarden debes utilizar el siguiente comand:

```
sudo netfilter-persistent save
```


# Enunciado

Utiliza un servidor Debian o derivado con SSH instalado.


Utiliza iptables como firewall para proteger el acceso al servidor:


* La política del firewall debería ser restrictiva.
* Las conexiones SSH entrantes deberían estar limitada al rango de la red interna.
* Desde el servidor se debería poder usar la web (puertos 80, 443) y DNS (53) 
* La configuración debería permanecer tras un reinicio del servidor


# Bibliografía

netfilter: https://www.netfilter.org/
iptables: https://manpages.debian.org/jessie/iptables/iptables.8.en.html
nfttables: https://www.netfilter.org/projects/nftables/manpage.html

ufw: https://wiki.ubuntu.com/UncomplicatedFirewall?action=show&redirect=UbuntuFirewall




Tutoriales:
https://wiki.debian.org/iptables
https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html


https://www.digitalocean.com/community/tutorials/iptables-essentials-common-firewall-rules-and-commands
https://www.digitalocean.com/community/tutorials/how-to-list-and-delete-iptables-firewall-rules


https://www.redeszone.net/tutoriales/seguridad/iptables-firewall-linux-configuracion/

https://www.hostinger.es/tutoriales/iptables-asegurar-ubuntu-vps-linux-firewall/
https://fp.josedomingo.org/seguridadgs/u03/iptables.html
http://www.upv.es/visor/media/a4627863-0d1e-9b43-a207-f713fe47711c/v

https://www.sysadmit.com/2016/04/linux-tutorial-iptables-un-firewall-fiable-Capitulo-1.html
https://www.sysadmit.com/2016/05/linux-tutorial-iptables-un-firewall-fiable-capitulo-2.html

https://www.ibm.com/docs/es/qsip/7.4?topic=tasks-advanced-iptables-rules-examples
https://access.redhat.com/documentation/es-es/red_hat_enterprise_linux/6/html/security_guide/sect-security_guide-iptables