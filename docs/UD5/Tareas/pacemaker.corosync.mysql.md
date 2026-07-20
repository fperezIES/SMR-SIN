---
icon: material/file-edit
---

TODO

https://www.ibm.com/docs/es/db2/11.5?topic=software-pacemaker-linux


https://linuxyotrascosas.wordpress.com/2022/02/22/como-crear-un-cluster-ha-activo-pasivo-con-pacemaker-corosync-y-mysql/


Aquí tienes una guía detallada para configurar un servidor web de alta disponibilidad con **Apache**, **MySQL** y herramientas de gestión de alta disponibilidad como **Pacemaker** y **Corosync**. La configuración incluye una IP virtual flotante para garantizar que el servicio esté siempre disponible.

---

### **Configuración de un Entorno de Alta Disponibilidad para WordPress**

Este documento detalla cómo configurar un clúster de alta disponibilidad para WordPress utilizando **Apache**, **MariaDB (MySQL)** con replicación y un sistema de alta disponibilidad con **Pacemaker**, **Corosync**, y **Keepalived**.

---

## **Requisitos Previos**

### **Infraestructura**

1. **Máquinas necesarias**:
    
    - **Dos nodos Linux** (AlmaLinux 9 o equivalente).
    - **Sistema de almacenamiento compartido opcional** (como GlusterFS o NFS).
2. **Red**:
    
    - Configuración de red con conectividad entre los nodos.
    - Una IP flotante para gestionar el tráfico (gestionada por Keepalived).
3. **Software necesario**:
    
    - Apache (`httpd`).
    - PHP (`php`, `php-mysqlnd`).
    - MariaDB (o MySQL).
    - WordPress.
    - Pacemaker, Corosync y Keepalived.

---

## **Pasos de Configuración**

### **1. Configuración Base en los Nodos**

En ambos nodos:

1. **Actualizar el sistema**:
    
    ```bash
    sudo dnf update -y
    ```
    
2. **Instalar paquetes necesarios**:
    
    ```bash
    sudo dnf install -y httpd mariadb-server mariadb php php-mysqlnd pacemaker corosync pcs keepalived wget unzip
    ```
    
3. **Habilitar servicios necesarios**:
    
    ```bash
    sudo systemctl enable --now pcsd httpd mariadb
    ```
    

---

### **2. Configuración de Pacemaker y Corosync**

1. **Configurar usuario `hacluster`**:
    
    ```bash
    sudo passwd hacluster
    ```
    
2. **Autenticación de nodos**: En uno de los nodos:
    
    ```bash
    sudo pcs cluster auth nodo1 nodo2
    ```
    
3. **Crear el clúster**:
    
    ```bash
    sudo pcs cluster setup --name wordpress_cluster nodo1 nodo2
    ```
    
4. **Iniciar y habilitar el clúster**:
    
    ```bash
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```
    

---

### **3. Configuración de la IP Virtual**

1. **Crear un recurso de IP flotante**:
    
    ```bash
    sudo pcs resource create virtual_ip ocf:heartbeat:IPaddr2 ip=192.168.1.100 cidr_netmask=24 op monitor interval=30s
    ```
    

---

### **4. Configuración de Apache y PHP**

1. **Configurar el VirtualHost de Apache**: En ambos nodos, edita el archivo:
    
    ```bash
    sudo nano /etc/httpd/conf.d/wordpress.conf
    ```
    
    Añade la configuración:
    
    ```apache
    <VirtualHost *:80>
        ServerName 192.168.1.100
        DocumentRoot /var/www/html/wordpress
        <Directory /var/www/html/wordpress>
            AllowOverride All
            Require all granted
        </Directory>
    </VirtualHost>
    ```
    
2. **Sincronizar los archivos de WordPress**: Si no usas almacenamiento compartido (como GlusterFS), sincroniza los archivos entre los nodos:
    
    ```bash
    rsync -avz /var/www/html/wordpress/ nodo2:/var/www/html/wordpress/
    ```
    
3. **Reiniciar Apache**:
    
    ```bash
    sudo systemctl restart httpd
    ```
    

---

---

Aquí tienes una guía detallada para configurar un servidor web de alta disponibilidad con **Apache**, **MySQL** y herramientas de gestión de alta disponibilidad como **Pacemaker** y **Corosync**. La configuración incluye una IP virtual flotante para garantizar que el servicio esté siempre disponible.

---

# **Configuración de un Servidor Web con Alta Disponibilidad**

## **Requisitos Previos**

1. **Hardware o Máquinas Virtuales**:
    
    - Dos nodos Linux con **AlmaLinux 9** (o similar).
    - Red configurada y conectividad entre los nodos.
    - Dos interfaces de red, una para comunicación privada entre los nodos.
2. **Software Necesario**:
    
    - Apache (`httpd`).
    - MySQL o MariaDB.
    - Pacemaker y Corosync.
    - Keepalived para la gestión de IP flotante.
3. **Acceso Administrativo**:
    
    - Usuario con privilegios de `sudo` o acceso root.

---

## **Pasos de Configuración**

### **1. Configuración de los Nodos**

En ambos nodos:

1. **Actualizar el sistema**:
    
    ```bash
    sudo dnf update -y
    ```
    
2. **Instalar Apache y MariaDB**:
    
    ```bash
    sudo dnf install -y httpd mariadb-server mariadb
    ```
    
3. **Instalar Pacemaker y Corosync**:
    
    ```bash
    sudo dnf install -y pacemaker corosync pcs
    ```
    
4. **Habilitar servicios necesarios**:
    
    ```bash
    sudo systemctl enable --now pcsd httpd mariadb
    ```
    

---

### **2. Configuración de Pacemaker y Corosync**

1. **Configurar usuario `hacluster`**: Establece una contraseña para el usuario `hacluster` (requerido por pcs):
    
    ```bash
    sudo passwd hacluster
    ```
    
2. **Autenticación de nodos en el clúster**: En uno de los nodos:
    
    ```bash
    sudo pcs cluster auth nodo1 nodo2
    ```
    
    Sustituye `nodo1` y `nodo2` por los nombres de host o IP de tus nodos.
    
3. **Crear el clúster**:
    
    ```bash
    sudo pcs cluster setup --name cluster_web nodo1 nodo2
    ```
    
4. **Iniciar el clúster**:
    
    ```bash
    sudo pcs cluster start --all
    ```
    
5. **Habilitar el clúster al inicio**:
    
    ```bash
    sudo pcs cluster enable --all
    ```
    

---

### **3. Configuración de Recursos del Clúster**

1. **Crear un recurso de IP flotante**:
    
    ```bash
    sudo pcs resource create virtual_ip ocf:heartbeat:IPaddr2 ip=192.168.1.100 cidr_netmask=24 op monitor interval=30s
    ```
    
    Sustituye `192.168.1.100` por la IP virtual que desees usar.
    
2. **Crear un recurso para Apache**:
    
    ```bash
    sudo pcs resource create webserver systemd:httpd op monitor interval=30s
    ```
    
3. **Configurar dependencias (Apache depende de la IP virtual)**:
    
    ```bash
    sudo pcs constraint colocation add webserver with virtual_ip INFINITY
    sudo pcs constraint order virtual_ip then webserver
    ```
    
4. **Verificar configuración del clúster**:
    
    ```bash
    sudo pcs status
    ```
    

---

### **4. Configuración de Apache**

1. **Configurar una página web básica**: Edita el archivo de configuración:
    
    ```bash
    sudo nano /var/www/html/index.html
    ```
    
    Añade el siguiente contenido:
    
    ```html
    <h1>Servidor Web de Alta Disponibilidad</h1>
    ```
    
2. **Probar Apache localmente**: Accede desde el navegador:
    
    ```
    http://<IP_del_nodo>
    ```
    

---

### **5. Configuración de MySQL con Replicación**

1. **Configurar MySQL en Modo Maestro-Esclavo**: En el **nodo maestro**:
    
    - Edita el archivo de configuración:
        
        ```bash
        sudo nano /etc/my.cnf.d/mariadb-server.cnf
        ```
        
        Añade:
        
        ```ini
        [mysqld]
        server-id=1
        log_bin=mysql-bin
        ```
        
    - Reinicia MySQL:
        
        ```bash
        sudo systemctl restart mariadb
        ```
        
    
    En el **nodo esclavo**:
    
    - Configura el archivo similar al maestro, pero cambia el `server-id` a 2.
    - Conecta el esclavo al maestro:
        
        ```sql
        CHANGE MASTER TO
        MASTER_HOST='IP_del_maestro',
        MASTER_USER='usuario_replica',
        MASTER_PASSWORD='contraseña_replica',
        MASTER_LOG_FILE='mysql-bin.000001',
        MASTER_LOG_POS=1;
        START SLAVE;
        ```
        
2. **Probar la Replicación**:
    
    - Crea una base de datos en el maestro y verifica su existencia en el esclavo.

---

### **6. Configuración de Keepalived**

1. **Instalar Keepalived**:
    
    ```bash
    sudo dnf install -y keepalived
    ```
    
2. **Configurar Keepalived**: Edita el archivo `/etc/keepalived/keepalived.conf` en ambos nodos:
    
    ```conf
    vrrp_instance VI_1 {
        state MASTER
        interface eth0
        virtual_router_id 51
        priority 100
        advert_int 1
        authentication {
            auth_type PASS
            auth_pass 1234
        }
        virtual_ipaddress {
            192.168.1.100
        }
    }
    ```
    
    Cambia `state` a `BACKUP` y `priority` a `50` en el nodo secundario.
    
3. **Inicia Keepalived**:
    
    ```bash
    sudo systemctl enable --now keepalived
    ```
    

---

### **7. Pruebas de Alta Disponibilidad**

1. **Prueba la IP flotante**:
    
    - Accede a la IP virtual desde un navegador: `http://192.168.1.100`.
    - Detén Apache en un nodo y verifica que el otro toma el control:
        
        ```bash
        sudo systemctl stop httpd
        ```
        
2. **Prueba la replicación de MySQL**:
    
    - Inserta datos en el nodo maestro y verifica que aparecen en el esclavo.

---

### **8. Monitorización y Gestión**

- Usa comandos de Pacemaker para verificar el estado:
    
    ```bash
    sudo pcs status
    ```
    
- Verifica los registros de MySQL para la replicación:
    
    ```bash
    sudo tail -f /var/log/mariadb/mariadb.log
    ```
    

---

Esta configuración permite a tus alumnos entender cómo funcionan los conceptos de **IP virtual**, **failover**, y **replicación de datos**, proporcionando un entorno práctico para explorar alta disponibilidad. 
````

Bibliografía

https://puerto53.com/linux/configuracion-de-un-cluster-de-mysql-en-linux-con-pacemaker-y-replica-de-datos-con-drbd/

https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/6/html/cluster_administration/s1-creating-cluster-ccs-ca