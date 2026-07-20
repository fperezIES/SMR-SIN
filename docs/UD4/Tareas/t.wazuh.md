---
icon: material/file-edit
---

#  Wazuh

Wazuh es una plataforma de seguridad gratuita y de código abierto que permite la implementación de sistemas HIDS (Host Intrusion Detection Systems), XDR (Extended Detection and Response) y SIEM (Security Information and Event Management). Protege tanto redes locales como sistemas virtualizados en contenedores en la nube.

![Logo de Wazuh](wazuh-logo.png){:style="width: 40%;" class="center"}

Actualmente, Wazuh es una herramienta ampliamente utilizada por miles de organizaciones en todo el mundo. Es importante destacar que, aunque la sede de la empresa se encuentra en Silicon Valley, sus creadores son españoles. Para conformar el proyecto, Wazuh utiliza una serie de componentes, tanto propios como basados en otras herramientas de código abierto ya existentes.

## Arquitectura de Wazuh

La arquitectura de Wazuh se basa en **agentes** que se ejecutan en los puntos finales monitorizados y que envían datos de seguridad a un **servidor central**. Los dispositivos sin agente, como cortafuegos, conmutadores, routers y puntos de acceso, también son compatibles y pueden enviar activamente datos de registro vía Syslog, SSH o utilizando su API. El servidor central decodifica y analiza la información entrante y pasa los resultados al **indexador de Wazuh** para su indexación y almacenamiento.

El **clúster del indexador de Wazuh** es una colección de uno o más nodos que se comunican entre sí para realizar operaciones de lectura y escritura en índices. Las implementaciones pequeñas de Wazuh, que no requieren procesar grandes cantidades de datos, pueden ser manejadas fácilmente por un clúster de un solo nodo. Se recomiendan clústeres de múltiples nodos cuando hay muchos puntos finales monitorizados, se anticipa un gran volumen de datos o se requiere alta disponibilidad.

Para entornos de producción, se recomienda desplegar el servidor de Wazuh y el indexador de Wazuh en hosts diferentes. En este escenario, Filebeat se utiliza para enviar de forma segura las alertas de Wazuh y los eventos archivados al clúster del indexador de Wazuh (de uno o varios nodos) utilizando cifrado TLS.

El siguiente diagrama representa la arquitectura de una implementación de Wazuh. Muestra los componentes de la solución y cómo el **servidor de Wazuh** y los nodos del **indexador de Wazuh** pueden configurarse como clústeres, proporcionando balanceo de carga y alta disponibilidad.

![Arquitectura de Wazuh](wazuh-architecture.png){:style="width: 80%;" class="center"}

### Componentes principales

- **Agentes Wazuh**: Se instalan en puntos finales, como servidores o routers, y permiten recoger registros de eventos de seguridad que posteriormente serán enviados al servidor Wazuh. Se ejecutan en múltiples sistemas operativos como Linux, Windows, macOS, Solaris, AIX, etc. En estos equipos, Wazuh realiza funciones de HIDS y XDR.

- **Servidor Wazuh**: Analiza los datos recibidos de los agentes. Los procesa a través de decodificadores y reglas, utilizando inteligencia de amenazas para buscar indicadores de compromiso (IOC) conocidos. Un solo servidor puede analizar datos de cientos o miles de agentes y escalar horizontalmente cuando se configura como un clúster. Este componente central también se utiliza para administrar los agentes, configurándolos y actualizándolos de forma remota cuando sea necesario. El servidor está compuesto, entre otros módulos, por:

  - **Panel web (Dashboard)**: Es la interfaz de usuario web para la visualización y el análisis de datos. Incluye paneles listos para usar para eventos de seguridad, aplicaciones vulnerables detectadas, datos de monitoreo de integridad de archivos, resultados de evaluación de configuración, eventos de monitoreo de infraestructura en la nube y otros. También se utiliza para administrar la configuración de Wazuh y monitorear su estado. Está implementado utilizando el proyecto de código abierto Kibana.

  - **Indexador de datos**: Este componente central de Wazuh indexa y almacena alertas generadas por el servidor de Wazuh y proporciona capacidades de análisis y búsqueda de datos casi en tiempo real. Puede configurarse como un clúster de un solo nodo o de varios nodos, proporcionando escalabilidad y alta disponibilidad. El indexador de Wazuh almacena datos como documentos JSON y está implementado utilizando el proyecto de código abierto Elasticsearch. La idea de funcionamiento es simple: los agentes generan eventos de seguridad que son enviados al servidor, y este los almacena en bases de datos.

## Instalación de los componentes centrales (servidor)

Aunque existe la posibilidad de usar una máquina virtual preconfigurada y contenedores docker. Para nuestro propósito de primer contacto, seguiremos la guía de [inicio rápido](https://documentation.wazuh.com/current/quickstart.html) para instalar Wazuh en un servidor Linux.

### Requisitos recomendados

| **Agents** | **CPU** | **RAM** | **Storage (90 days)** |
| ---------- | ------- | ------- | --------------------- |
| **1–25**   | 4 vCPU  | 8 GiB   | 50 GB                 |
| **25–50**  | 8 vCPU  | 8 GiB   | 100 GB                |
| **50–100** | 8 vCPU  | 8 GiB   | 200 GB                |

Es posible instalar el servicio en una máquina virtual con un mínimo de 4 GiB de RAM. Se recomienda Almalinux o Ubuntu.

1. Descargamos y ejecutamos el asistente de instalaicón de Wazuh (Tarda un buen rato):

```sh
curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```
Si obtienes un error por no cumplir los requisitos mínimos, puedes forzar la instalación añadiendo el parámetro `-i` al comando anterior.


Es importante tomar nota de la **contraseña** que se muestras al final.
Una vez terminada la instalaicón podemos acceder a la interfaz web:

```
https://IP_servidor_wazuh
```

Wazuh Manager usa los puertos 1514/tcp and 1515/tcp para comunicarse con los agentes. Therefore, you are required to allow both ports in your Linux firewall.

```sh
sudo firewall-cmd --permanent --add-port={1514,1515}/tcp

sudo firewall-cmd --reload

```

**PROBLEMAS CONOCIDOS**

 A veces, al iniciar vía web en una máquina virtual, nos da un error que dice  “Wazuh Dasbioard is not ready yet” o un error de timeout al verificar la versión del API.

 Para solucionarlo basta reiniciar los servicios  de wazuh:
 
```sh
systemctl restart wazuh-indexer
systemctl restart wazuh-manager
systemctl restart wazuh-desktop
```

## Instalación de los agentes

Puedes añadir agentes siguiendo las instrucciones de la sección de la documentación [Enroll Agents][enrollagents]. Ahí se muestras como crear grupos para organizar los agentes y cómo obtener los comandos necesarios para instalar y configurar los agentes en diferentes sistemas operativos.

Para crear **grupos**, desde la interfaz web:

1. Haz click en el menú superior derecho **☰** -> **Server management** -> **Endpoint Groups**.
2. Haz click en "Add new group", indica el nombre y ya está.

Para añadir **agentes**, desde la interfaz web:

1. Haz click en el menú superior derecho **☰** -> **Server management** -> **Endpoints Summary**.
2. Haz click en el botón "Deploy new agent" y elige las opciones y rellena los datos según tu necesidad.
3. Copia el comando indicado al final de la pantalla y ejecútalo en la máquina a monitorizar.

Recuerda que en windows debes ejecutar los comandos en una ventana de powershell ejecutada con permisos de administrador.

Adicionalmente puedes optar por instalar y configurar el agente manualmente siguiendo las guías para ello:

- Instrucciones de instalación del [agente en linux][linuxagent]
- Instrucciones de la instalación del [agente en windows][windowsagent]


## Prueba las capacidades de Wazuh

Para tomar contacto con la herramienta tienes varios casos de uso práctico en la sección [Proof of Concept guide][proofofconcept]. Puedes ver algunos ejemplos demostrados en el **vídeo** incluido en los recursos adicionales al final de este documento.


## ¿Qué tienes que entregar?

Un pdf en el que muestres pruebas de haber realizado lo siguiente:

- Instalación del  servidor Wazuh.
- Instalación de un mínimo de dos agentes, preferiblemente uno en windows y otro en linux.
- Probar un mínimo de dos casos de la guía [Proof of Concept guide][proofofconcept]. Indicar qué dos casos se han elegido y mostrar evidencias de su comprobación.

[quickstart]:https://documentation.wazuh.com/current/quickstart.html
[linuxagent]:https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-linux.html
[windowsagent]:https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html
[proofofconcept]:https://documentation.wazuh.com/current/proof-of-concept-guide/index.html
[enrollagents]:https://documentation.wazuh.com/current/cloud-service/getting-started/enroll-agents.html

## Recursos adicionales

- **Sitio oficial de Wazuh**: [https://wazuh.com/](https://wazuh.com/)
- **Documentación de Wazuh**: [https://documentation.wazuh.com/current/index.html](https://documentation.wazuh.com/current/index.html)
	- [Guía rápida de inicio][quickstart]
	- [Guía de agentes][enrollagents]
	- [Agentes](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)
	- [Arquitectura](https://documentation.wazuh.com/current/getting-started/architecture.html)
	- [Proof of Concept guide][proofofconcept]

- [Guía de instalación en Rocky Linux](https://centlinux.com/install-wazuh-server-on-linux/)

## Tutorial de Wazuh

<iframe width="560" height="315" src="https://www.youtube.com/embed/3CaG2GI1kn0?si=DfUA_Cw8eKwAUkaV" title="Reproductor de vídeo de YouTube" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>