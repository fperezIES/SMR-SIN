---
icon: material/file-edit
---



# Práctica: Instalación y Configuración de Suricata en pfSense

## 1 Introducción

Suricata es un sistema de detección y prevención de intrusiones (IDS/IPS) de código abierto que se integra con pfSense para reforzar la seguridad de la red. A grandes rasgos, se encarga de inspeccionar el tráfico que atraviesa tu firewall, comparándolo con unas reglas o firmas que describen comportamientos maliciosos conocidos. Si encuentra coincidencias, puede bloquear o registrar esos eventos, ayudando a proteger la red de ataques.


![](../img/suricata/suricata_logo.jpg){:style="width: 80%;" class="center"}
### 1.1 ¿Por qué Suricata en pfSense?

1. **Protección avanzada:** Suricata es capaz de detectar y bloquear múltiples tipos de amenazas, desde malware hasta intentos de fuerza bruta.
2. **Multihilo (multi-threading):** Suricata aprovecha los procesadores multinúcleo, lo que le permite gestionar un volumen de tráfico elevado y procesar varias reglas simultáneamente.
3. **Código abierto:** Al igual que pfSense, Suricata es open source. Esto facilita la auditoría del código, la colaboración de la comunidad y la transparencia.
4. **Análisis de tráfico enriquecido:** Además de las típicas reglas basadas en firmas (signature-based), Suricata ofrece funcionalidades de inspección profunda de paquetes (DPI), registro de flujos (flows) y estadísticas avanzadas.


### 1.2 Ventajas respecto a Snort

Aunque ambos son muy populares y eficaces como IDS/IPS, Suricata aprovecha mejor los procesadores multinúcleo gracias a su arquitectura multihilo. Además, ofrece funciones como el análisis de flujo y herramientas para inspeccionar tráfico TLS, lo que puede suponer un plus en redes con mucho volumen de datos.


## 2 Objetivos

- **Instalar Suricata** en pfSense.  
- **Configurar Suricata** como IDS/IPS.  
- **Ajustar las reglas** y alertas para una monitorización eficiente.  
- **Verificar el funcionamiento** correcto de Suricata en el entorno de red.

> **Nota:** Para una guía visual detallada, se recomienda visualizar el video  
> _"Suricata Network IDS/IPS Installation, Setup, and How To Tune The Rules & Alerts on pfSense 2020"_  
> enlazado en la bibliofrafía.


## 3 Requisitos Previos

- Conocimientos básicos de administración de redes y seguridad informática.  
- Acceso a una instancia de pfSense configurada y operativa.  
- Conexión a Internet para descargar actualizaciones y reglas.

Debes deshabilitar todo el el offloading a hardware de las interfaces de red para que Suricata funcione correctamente.
> **Disable all hardware offloading in the ui** (System / Advanced / Networking)

Requisitos de RAM:

- En **entornos de prueba** o con tráfico muy bajo, Suricata puede funcionar razonablemente con **2 GB de RAM**.
- Para un **entorno de producción** con tráfico medio y reglas estándar, se suelen recomendar al menos **4 GB** (y a menudo más, dependiendo de las reglas y el uso de funciones extra).
- En **escenarios de alto rendimiento** o con mucho tráfico (por ejemplo, redes corporativas grandes), puede que necesites **8 GB, 16 GB o más**, sobre todo si habilitas análisis detallados o un gran número de reglas.

## 4 Instrucciones Generales

### 4.1 Acceso a pfSense

1. Inicia sesión en la interfaz web de pfSense utilizando tus credenciales administrativas.  

### 4.2 Instalación de Suricata

1. Navega a **System** > **Package Manager** > **Available Packages**.  
2. Busca **"Suricata"** en la lista de paquetes disponibles.  
3. Haz clic en **Install** junto a Suricata y confirma la instalación.  

### 4.3 Configuración inicial

Una vez instalado, ve a **Services** > **Suricata**.  

En la pestaña **Global Settings**, Elige las reglas que vas utilizar, vamos a utilizar las reglas ofrecidas de forma gratuita, marca las siguientes opciones:

En primer lugar elegiremos las reglas que vamos a usar. En la sección `Please Choose The Type Of Rules You Wish To Download` maca las siguientes casillas:
- `ETOpen is a free open source set of Suricata rules whose coverage is more limited than ETPro.`
* Usa una URL personalizada para `Use a custom URL for ETOpen downloads`, añade la url que puedes encontrar en el siguiente enlace  [Emerging Threads ](https://rules.emergingthreats.net/OPEN_download_instructions.html)
- `The Snort Community Ruleset is a GPLv2 Talos-certified ruleset that is distributed free of charge without any Snort Subscriber License restrictions.`

Ahora, vamos a indicar el periodo de actualización y también vamos a activar un tiempo de aplicación máximo de los bloqueos para evitar posibles problemas de falsos positivos.
- En `Rules Update Settings > Update Interval` especifica `1 DAY`
* En `General Settigs > Remove Blocked Hosts Interval`  especifica `1 HOUR`


Finalmente vamos a actualizar las reglas:

- Dirígete  `Suricata > updates`  y pulsa el botón update

Toma una captura en la que se muestre la fecha en que se actualizaron las reglas.

#### 4.3.1 **Información sobre versiones de las reglas de EmergingThreats**

1. **ET Open**
    
    - Es la versión de acceso **gratuito** y de **código abierto**.
    - Recibe actualizaciones periódicas y cubre la mayoría de amenazas emergentes.
    - Ideal para estudiantes y pequeñas organizaciones que necesiten un punto de partida para proteger sus redes.
2. **ET Pro**
    
    - Versión **comercial** que ofrece reglas adicionales, cobertura más amplia y actualizaciones más frecuentes.
    - Pensada para entornos críticos, grandes organizaciones o proveedores de servicios de seguridad.
    - Incluye soporte especializado y acceso a reglas **exclusivas**.


### 4.4 Configuración de las interfaces

En la pestaña **Interfaces**
1.  Añade una nueva interfaz donde Suricata monitorizará el tráfico.  
   - Selecciona la interfaz de red adecuada (por ejemplo, LAN o WAN).  En un escenario real la información interesante la obtendremos de monitorizar la LAN.
2. Configura el modo de operación:  
		Lo podemos hacer desde "Alert and Block Settings". Si marcamos la casilla funcionará como un IPS, en caso contrario como un IDS. Se recomienda empezar las pruebas sin bloquear.
   - **IDS (Sistema de Detección de Intrusos):** Monitorea el tráfico y genera alertas sin bloquearlo.  
   - **IPS (Sistema de Prevención de Intrusos):** Monitorea y bloquea el tráfico malicioso en tiempo real.  

Una vez añadida la Interfaz, ve `LAN categories`y añádelas todas.



## 5 Configuración Detallada de Reglas y Pruebas de Funcionamiento

Ahora en la pestaña `LAN Rules` de la interfaz añadida a Suricata,  activa todas las reglas que consideres que se deberían aplicar a esta interfaz.


### 5.1 Configuración de Reglas: Paso a Paso

1. Accede a **Services** > **Suricata**>Interfaces y selecciona la pestaña **Rules**.  
2. Observa que las reglas se agrupan en diferentes categorías (por ejemplo, _emerging-trojan_, _emerging-malware_, etc.).  
3. **Activa o desactiva** las reglas según tus necesidades:  
   - **Habilita** las reglas relevantes para tu entorno (p.e., servicios que corren en tu red o tipos de ataques que deseas mitigar).  
   - **Deshabilita** reglas que consideres irrelevantes para evitar falsos positivos innecesarios.  
4. Configura las acciones para cada regla:  
   - **Alert:** Solo genera una alerta cuando se detecta una amenaza.  
   - **Drop:** Bloquea el tráfico identificado como malicioso (recomendado en modo IPS).  
5. **Guarda** los cambios realizados.  

### 5.2 Ajuste de Alertas y Minimización de Falsos Positivos

1. En la pestaña **Rules**, revisa detenidamente los registros de alertas y decide qué reglas son importantes.  
2. Si observas falsos positivos en ciertas reglas, puedes:  
   - Ajustar la **sensibilidad** de la regla.  
   - **Deshabilitar** temporalmente la regla y monitorear el impacto.  
   - **Modificar** su acción de _Drop_ a _Alert_ mientras investigas.  

### 5.3 Pruebas de Funcionamiento

Una vez configuradas las reglas, es fundamental **validar** que Suricata actúa correctamente ante eventos sospechosos.  
A continuación, se describen algunos métodos de prueba que puedes utilizar:

1. **Escaneo de Puertos**:  
   - Realiza un escaneo de puertos (por ejemplo, con Nmap) desde una máquina externa a tu red.  
   - Verifica en la pestaña **Alerts** si Suricata detecta y genera alertas sobre el escaneo.  
   - Si Suricata está en modo IPS, comprueba si el escaneo es bloqueado.  

2. **Simulación de Tráfico Malicioso**:  
   - Existen herramientas y scripts que generan tráfico malicioso simulado (por ejemplo, [**MSFVenom**](https://www.offensive-security.com/metasploit-unleashed/msfvenom/)).  
   - Lanza un ataque de prueba (en un entorno controlado) y revisa que Suricata lo detecte y bloquee.  

3. **Prueba de Descarga de EICAR**:  
   - El archivo de prueba [**EICAR**](https://www.eicar.org/) es una forma segura de comprobar la reacción de las soluciones de seguridad.  
   - Intenta descargar el archivo EICAR desde un equipo dentro de la red protegida por Suricata.  
   - Suricata debería generar una alerta e incluso bloquear la descarga si la regla adecuada está activada.  

4. **Verificación de Logs y Alertas**:  
   - Navega a **Services** > **Suricata** > **Alerts** para visualizar los eventos registrados.  
   - Asegúrate de que las alertas o bloqueos coincidan con tus pruebas.  



## 6 Verificación y Monitorización

- Accede de forma periódica a la pestaña **Alerts** para **visualizar las alertas** generadas por Suricata.  
- Verifica que Suricata esté **detectando** y, si está en modo IPS, **bloqueando** correctamente el tráfico malicioso.  
- Ajusta las reglas (añadiendo o excluyendo) según los hallazgos de tus pruebas de seguridad y la actividad habitual de la red.  

## 7 Buenas prácticas

- **Selecciona cuidadosamente las reglas:** Activar todas las reglas disponibles puede generar falsos positivos y sobrecargar el sistema. Comienza con un conjunto básico (por ejemplo, las reglas de _Emerging Threats Open_) y ve ajustando.
- **Monitorea los registros:** Revisa periódicamente los logs de Suricata. Si hay demasiados falsos positivos, puede que necesites afinar las reglas o pasar ciertas IP/puertos a una lista blanca (whitelist).
- **Mantén el sistema actualizado:** Tanto pfSense como Suricata reciben actualizaciones de seguridad y mejoras. Asegúrate de aplicar parches y actualizaciones de reglas de forma regular.
- **Pruebas de rendimiento:** Si notas ralentizaciones en la red, evalúa cuántas reglas tienes activas y la carga en el procesador. Suricata consume más recursos cuantas más reglas y análisis profundos active.


## 8 Bibliografía

- [Documentación Suricata](https://docs.suricata.io/en/latest/)
	- [Suricata Rule format](https://docs.suricata.io/en/latest/rules/intro.html)
- [Emerging Threads Rules](https://rules.emergingthreats.net/)
- [Netgate Snort vs Suricata](https://www.netgate.com/blog/suricata-vs-snort)


<iframe width="560" height="315" src="https://www.youtube.com/embed/S0-vsjhPDN0?si=KpF0-gafv9FED0Cl" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<!--
https://www.youtube.com/watch?v=u1gZrJEQ_30
-->

