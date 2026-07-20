# **Alta disponibilidad en Linux**

La alta disponibilidad (HA, por sus siglas en inglés) es un enfoque que busca minimizar el tiempo de inactividad de servicios críticos en un sistema. En entornos Linux, se implementa mediante herramientas y configuraciones que aseguran que los servicios sigan funcionando incluso ante fallos en hardware, software o red.

A continuación, exploramos los conceptos, componentes y tecnologías clave para implementar alta disponibilidad en Linux.


## **Conceptos Clave en Alta Disponibilidad**

1. **Disponibilidad**:    
    - La medida del tiempo en que un sistema está operativo y accesible.
    - **99.999% ("cinco nueves")** de disponibilidad es un estándar común, lo que significa menos de 5 minutos de inactividad por año.
    
1. **Failover**:    
    - Proceso automático que transfiere un servicio de un nodo fallido a otro nodo activo del clúster.
    
1. **Clúster de Alta Disponibilidad**:    
    - Un conjunto de nodos (servidores) configurados para trabajar juntos, compartiendo cargas y respaldándose mutuamente.
    
1. **Split-Brain**:    
    - Situación donde los nodos pierden comunicación entre sí y actúan de manera independiente, lo que puede provocar conflictos.
    
1. **Quórum**:    
    - El número mínimo de nodos que deben estar operativos para que el cluster tome decisiones críticas.

---

## **Componentes de un Sistema de Alta Disponibilidad en Linux**

1. **Hardware redundante**:
    
    - Múltiples servidores físicos o virtuales.
    - Almacenamiento compartido (como SAN o NFS).
    - Fuentes de alimentación y redes redundantes.
2. **Monitorización y detección de fallos**:
    
    - Identificación automática de problemas en nodos o servicios.
3. **Failover automático**:
    
    - Reasignación de recursos y servicios a nodos activos.
4. **Reconfiguración dinámica**:
    
    - Ajuste automático del sistema ante cambios o fallos.
5. **Almacenamiento compartido o replicado**:
    
    - Acceso común a los datos para garantizar la consistencia entre nodos.

---

## **Tecnologías para Alta Disponibilidad en Linux**

### **1. Gestores de Clústeres**

Estos sistemas permiten gestionar los nodos y recursos del clúster:

- **Pacemaker**:
    - Administra recursos como servicios y aplicaciones.
    - Realiza failover automático.
- **Corosync**:
    - Proporciona comunicación entre nodos y gestión de quórum.

### **2. Gestión de IP Flotante**

Herramientas como **Keepalived** y **HAProxy** permiten utilizar una dirección IP flotante que siempre apunta al nodo activo, asegurando la continuidad del servicio.

### **3. Replicación de Datos**

Para mantener los datos consistentes entre nodos:

- **GlusterFS**: Sistema de archivos distribuido.
- **DRBD**: Replica datos en tiempo real entre nodos.

### **4. Balanceadores de Carga**

Distribuyen el tráfico entre varios nodos:

- **HAProxy**: Equilibrador de carga eficiente.
- **Nginx**: Puede configurarse como proxy inverso con balanceo.

### **5. Virtualización y Contenedores**

Plataformas como **Proxmox**, **VMware** y **Kubernetes** integran funciones de alta disponibilidad para servicios virtualizados y basados en contenedores.



---

## **Pasos para Implementar Alta Disponibilidad en Linux**

### **1. Planificación**

- Identificar servicios críticos.
- Establecer niveles de redundancia requeridos.
- Seleccionar las herramientas adecuadas.

### **2. Configuración de un Clúster Básico**

#### **A. Requisitos**:

- Dos o más nodos con Linux.
- Herramientas: Pacemaker, Corosync, Keepalived.

#### **B. Configuración**:

1. Instalar los paquetes necesarios:
    
    ```bash
    sudo dnf install -y pacemaker corosync pcs keepalived
    ```
    
2. Configurar el clúster con Pacemaker y Corosync.
3. Definir recursos y dependencias (por ejemplo, servicios como Apache o bases de datos).

### **3. Añadir Almacenamiento Compartido**

Utilizar **NFS** o **GlusterFS** para que los nodos compartan datos.

### **4. Configurar un Balanceador de Carga**

Configurar **HAProxy** para distribuir el tráfico entrante entre los nodos del clúster.

---

## **Casos de Uso**

### **1. Servidor Web con Alta Disponibilidad**

- Clúster que utiliza Apache o Nginx.
- IP flotante gestionada por Keepalived.
- Datos sincronizados con GlusterFS.

### **2. Base de Datos Redundante**

- Clúster de MariaDB con replicación maestro-esclavo.
- DRBD para replicar datos a nivel de bloque.

### **3. Plataforma de Contenedores**

- Kubernetes con múltiples nodos maestros y agentes.
- Provisión de servicios críticos con pods replicados.

---

## **Ventajas de Alta Disponibilidad en Linux**

- **Reducción de tiempos de inactividad**: Asegura la continuidad de servicios críticos.
- **Automatización del failover**: Respuesta rápida a fallos.
- **Escalabilidad**: Posibilidad de añadir nodos según crecen las necesidades.

---

## **Desafíos**

- **Complejidad de configuración**: Requiere conocimientos avanzados para implementar y mantener.
- **Costos iniciales**: Hardware redundante y licencias pueden incrementar el presupuesto.
- **Split-brain**: Puede provocar inconsistencias si no se gestiona correctamente.

---

## **Ejemplo Práctico**

### **Configurar un Clúster con Pacemaker y Corosync**

1. **Configurar el clúster**:
    
    ```bash
    sudo pcs cluster auth nodo1 nodo2
    sudo pcs cluster setup --name mi_cluster nodo1 nodo2
    sudo pcs cluster start --all
    ```
    
2. **Definir un recurso**:
    
    ```bash
    sudo pcs resource create webserver systemd:httpd op monitor interval=30s
    sudo pcs resource create ip_flotante ocf:heartbeat:IPaddr2 ip=192.168.1.100 cidr_netmask=24
    ```
    
3. **Configurar dependencias**:
    
    ```bash
    sudo pcs constraint colocation add webserver with ip_flotante
    sudo pcs constraint order ip_flotante then webserver
    ```
    

---

La alta disponibilidad en Linux es una habilidad esencial en ciberseguridad y administración de sistemas, permitiendo a los alumnos explorar soluciones prácticas para entornos críticos. ¿Quieres que elabore ejercicios prácticos adicionales?

# Pacemaker y Corosync

**Pacemaker** y **Corosync** son dos herramientas complementarias que forman la base de un clúster de alta disponibilidad en sistemas Linux. Aunque trabajan juntas, tienen roles bien diferenciados dentro del clúster:

---

## **1. Corosync: La Capa de Comunicación**

**Corosync** es la herramienta que proporciona la comunicación y coordinación entre los nodos del clúster.

#### **Funcionalidades principales de Corosync**:

1. **Mensajería entre nodos**:
    
    - Gestiona la comunicación en tiempo real entre los nodos del clúster.
    - Asegura que todos los nodos tengan una visión consistente del estado del clúster.
2. **Detección de fallos**:
    
    - Supervisa la disponibilidad de los nodos mediante mensajes de "heartbeat" (latidos).
    - Si un nodo no responde durante un tiempo determinado, se considera caído.
3. **Gestión de quórum**:
    
    - Corosync asegura que solo los nodos con "quórum" (mayoría funcional) puedan tomar decisiones críticas, como asumir un servicio o recursos.
    - Previene la situación de **split-brain**, donde dos partes del clúster podrían intentar gestionar los mismos recursos al mismo tiempo.
4. **Protocolo de consenso**:
    
    - Usa un protocolo de consenso para garantizar que las decisiones sobre la gestión del clúster (como iniciar o detener servicios) sean aceptadas por todos los nodos activos.

---

## **2. Pacemaker: El Gestor de Recursos**

**Pacemaker** se encarga de administrar y supervisar los recursos del clúster, como servicios, IPs flotantes, y sistemas de archivos.

#### **Funcionalidades principales de Pacemaker**:

1. **Gestión de recursos**:
    
    - Define qué recursos (servicios, bases de datos, etc.) deben estar activos en cada momento.
    - Gestiona su ubicación y asegura que no se ejecuten en múltiples nodos simultáneamente, a menos que esté configurado para ello (por ejemplo, servicios activos-activos).
2. **Failover y recuperación**:
    
    - Detecta fallos en servicios o nodos y realiza automáticamente un **failover**, moviendo los recursos a otro nodo activo.
    - Garantiza la continuidad del servicio con tiempos mínimos de inactividad.
3. **Dependencias y restricciones**:
    
    - Permite definir relaciones entre recursos (por ejemplo, "Apache debe estar activo solo después de configurar la IP flotante").
    - Establece restricciones para evitar conflictos o problemas en la gestión de recursos.
4. **Escalabilidad**:
    
    - Puede gestionar clústeres con docenas de nodos, asegurando que los servicios estén distribuidos de forma eficiente y confiable.
5. **Interfaz para el administrador**:
    
    - Ofrece herramientas como `pcs` (Pacemaker Configuration System) para configurar y supervisar los recursos y nodos del clúster.

---

## **Resumen de la Colaboración**

| **Función**                | **Corosync**                                                    | **Pacemaker**                                                           |
| -------------------------- | --------------------------------------------------------------- | ----------------------------------------------------------------------- |
| **Comunicación**           | Gestiona la comunicación entre nodos del cluster.               | No gestiona la comunicación directa, se apoya en Corosync.              |
| **Detección de nodos**     | Detecta nodos activos y fallos en ellos.                        | Usa esta información para tomar decisiones sobre los recursos.          |
| **Gestión de quórum**      | Asegura que las decisiones se tomen solo con quórum suficiente. | Actúa según las políticas definidas basándose en el estado de Corosync. |
| **Gestión de recursos**    | No gestiona recursos directamente.                              | Controla los recursos y los reasigna en caso de fallos.                 |
| **Recuperación de fallos** | Detecta fallos de nodos mediante heartbeats.                    | Realiza el failover de los recursos a nodos activos.                    |

---

### **Ejemplo Práctico**

Imagina que tienes un servicio crítico (como Apache) configurado en un clúster de dos nodos:

1. **Corosync**:
    - Supervisa que ambos nodos estén operativos y se comuniquen.
    - Si un nodo deja de responder, lo marca como "caído" y avisa a Pacemaker.
2. **Pacemaker**:
    - Decide mover el servicio Apache al nodo activo y reiniciarlo allí.
    - Se asegura de que Apache no se ejecute en más de un nodo simultáneamente (evitando conflictos).
