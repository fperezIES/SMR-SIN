
## Introducción

Al elegir un proyecto, es importante considerar:
- **Complejidad:** Seleccionar un proyecto que sea desafiante pero realista para el nivel de conocimientos de los estudiantes.
- **Interés:** Elegir un tema que sea de interés para los estudiantes y que les motive a investigar y aprender.
- **Recursos:** Asegurarse de que se dispone de los recursos necesarios para llevar a cabo el proyecto, como hardware, software, y acceso a Internet.
- **Originalidad:** Intentar que el proyecto tenga un componente de originalidad o innovación. Algunos ejemplos de cómo añadir originalidad incluyen:
    - **Enfoque en un sector específico:** Adaptar el proyecto a las necesidades de seguridad de un sector en particular, como la sanidad o la banca.
    - **Comparación de herramientas:** Evaluar y comparar diferentes herramientas de seguridad, como cortafuegos o sistemas de detección de intrusiones.
    - **Desarrollo de una solución novedosa:** Crear una herramienta o un sistema de seguridad que aborde un problema específico.
- **Evaluación:** Planificar métodos de evaluación diversos para valorar el éxito del proyecto y demostrar los resultados de aprendizaje (ver ).

## Proyectos

### 1. Implementación de un Sistema de Alta Disponibilidad para un Servicio Web Crítico:

- **Descripción:** Construir un clúster de servidores web (por ejemplo, con Apache o Nginx) que garantice la alta disponibilidad de un servicio web crítico, como una tienda online o una plataforma de aprendizaje. El objetivo es asegurar que el servicio web permanezca accesible de forma continua, incluso en caso de fallo de un servidor.
- **Tecnologías:** Balanceador de carga (HAProxy), servidores web (Apache/Nginx), sistema de archivos distribuido (GlusterFS), herramientas de monitorización (Nagios).
- **Enfoque:** Configurar un clúster de servidores, implementar un balanceador de carga para distribuir el tráfico entre los servidores, configurar la replicación de datos para mantener la consistencia de la información, y establecer la conmutación por error automática en caso de fallo de un servidor. Además, se debe monitorizar el sistema para asegurar su correcto funcionamiento y detectar posibles problemas.
- **Herramientas de código abierto:** Keepalived (para la conmutación por error), Pacemaker (gestor de clústeres).

#### 2. Diseño e Implementación de una Red Segura para una PYME:

- **Descripción:** Crear una red segura para una pequeña o mediana empresa, incluyendo la segmentación de la red para separar el tráfico sensible del tráfico general, la implementación de un cortafuegos para proteger la red de amenazas externas, la configuración de una VPN para el acceso remoto seguro de los empleados, y la definición de políticas de seguridad para el uso de la red y los dispositivos.
- **Tecnologías:** Router, switch, cortafuegos (pfSense), servidor VPN (OpenVPN), sistema de detección de intrusiones (Snort).
- **Enfoque:** Analizar las necesidades de seguridad de la PYME, diseñar la topología de la red, configurar los dispositivos de red para implementar las medidas de seguridad, y documentar las políticas de seguridad.
- **Herramientas de código abierto:** OpenWrt (para el router), VyOS (para el cortafuegos)

## 1. Clúster de Alta Disponibilidad con Balanceo de Carga

**Descripción**  
Diseñar e implementar un clúster de servidores que presten un servicio web o de base de datos con tolerancia a fallos. El objetivo es asegurar que, aunque falle un servidor, el servicio continúe operativo mediante otro nodo activo.

**Puntos clave**

- Configuración de un balanceador de carga (HAProxy, Nginx, etc.).
- Replicación de la base de datos (MySQL/MariaDB con modo réplica, por ejemplo).
- Monitorización y gestión de la conmutación por error (failover) con herramientas como Keepalived o Pacemaker.
- Uso de firewalls y restricciones de acceso para endurecer la seguridad del clúster.

**Tecnologías sugeridas**

- **Sistemas operativos**: Linux (Ubuntu Server, CentOS, Debian).
- **Balanceo**: HAProxy o Nginx.
- **Alta disponibilidad**: Keepalived, Pacemaker, Corosync.
- **Monitorización**: Nagios, Zabbix o Prometheus.

---

## 2. Infraestructura Contenedorizada y Segura con Docker + Kubernetes

**Descripción**  
Crear una infraestructura de contenedores para desplegar aplicaciones de forma escalable. El proyecto se enfoca en la seguridad de los contenedores, la gestión de secretos y la alta disponibilidad en un clúster Kubernetes.

**Puntos clave**

- Instalación y configuración de un clúster Kubernetes (K3s o MicroK8s podrían ser versiones más ligeras para prácticas).
- Creación de pods replicados para alta disponibilidad.
- Implementación de un sistema de control de acceso (Roles, RBAC) y medidas de seguridad (certificados TLS, etc.).
- Políticas de red dentro de Kubernetes (Network Policies) para aislar los servicios entre sí y reducir la superficie de ataque.

**Tecnologías sugeridas**

- **Contenedores**: Docker / Podman.
- **Orquestación**: Kubernetes (K3s, MicroK8s, Minikube).
- **Seguridad**: Uso de certificados y control de acceso RBAC.
- **Storage**: Volúmenes persistentes para datos críticos.