---
icon: material/file-edit
---

TODO:

A continuación se presenta una práctica paso a paso para que los alumnos creen un pequeño clúster en AlmaLinux 9 con el objetivo de desplegar WordPress. La idea principal es montar un entorno escalable y robusto que incluya:

- **Dos nodos web** que servirán la aplicación WordPress.
- **Un nodo para la base de datos** (MariaDB).
- **Un balanceador de carga** (HAProxy o Nginx) que reparta el tráfico entre los nodos web.

Esta práctica puede realizarse en un entorno virtualizado (por ejemplo, utilizando máquinas virtuales en VirtualBox o KVM) o mediante contenedores. Sin embargo, en esta primera aproximación, supondremos el uso de máquinas virtuales.

---

### Objetivos de la práctica

1. **Familiarizarse con la instalación y configuración de AlmaLinux 9** como sistema base.
2. **Crear un clúster web** donde varios nodos sirvan una instancia de WordPress.
3. **Configurar un balanceador de carga** que reparta las peticiones entre los nodos web.
4. **Desplegar y probar WordPress** para verificar que el entorno funciona correctamente.
5. **Entender conceptos de alta disponibilidad y escalabilidad** de aplicaciones web.

---

### Requisitos previos

- Conocimientos básicos de Linux (instalación, usuarios, paquetes).
- Conexión a internet en las máquinas virtuales para descargar paquetes.
- Tres o cuatro máquinas virtuales con AlmaLinux 9 instalado. Se recomienda:
    - **1 VM para balanceador:** `balancer01` (1 vCPU, 1-2GB RAM).
    - **2 VM para nodos web:** `web01`, `web02` (cada uno con 1 vCPU, 2GB RAM).
    - **1 VM para la base de datos:** `db01` (1 vCPU, 2GB RAM).
- Redes configuradas de forma que las máquinas puedan comunicarse entre sí (por ejemplo, todas en una red interna o mediante direcciones IP estáticas internas).

---

### Pasos de la práctica

#### 1. Preparación del sistema base (en cada VM)

- Actualizar el sistema:
    
    ```bash
    sudo dnf update -y
    ```
    
- Instalar utilidades básicas:
    
    ```bash
    sudo dnf install -y vim net-tools wget curl firewalld
    sudo systemctl enable --now firewalld
    ```
    
- Asignar nombres y hostnames coherentes a cada VM (editar `/etc/hostname` y `/etc/hosts`):
    
    ```bash
    # Ejemplo en balancer01
    sudo hostnamectl set-hostname balancer01
    
    # Añadir en /etc/hosts (en cada máquina):
    192.168.56.10 balancer01
    192.168.56.11 web01
    192.168.56.12 web02
    192.168.56.13 db01
    ```
    

#### 2. Configuración del servidor de base de datos (db01)

- Instalar MariaDB:
    
    ```bash
    sudo dnf install -y mariadb-server
    sudo systemctl enable --now mariadb
    ```
    
- Asegurar la instalación:
    
    ```bash
    sudo mysql_secure_installation
    # Establecer contraseña root, eliminar usuarios anónimos, deshabilitar acceso root remoto, etc.
    ```
    
- Crear la base de datos y el usuario para WordPress:
    
    ```bash
    sudo mysql -u root -p
    CREATE DATABASE wordpress;
    CREATE USER 'wpuser'@'%' IDENTIFIED BY 'wp_pass';
    GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'%';
    FLUSH PRIVILEGES;
    EXIT;
    ```
    
- Ajustar el firewall en db01 para permitir el acceso a la base de datos desde los nodos web:
    
    ```bash
    sudo firewall-cmd --permanent --add-service=mysql
    sudo firewall-cmd --reload
    ```
    
- En el fichero `/etc/my.cnf.d/mariadb-server.cnf` asegurar que `bind-address` sea la IP interna de la VM `db01` o que permita conexiones desde la misma red interna.
    
- Reiniciar el servicio:
    
    ```bash
    sudo systemctl restart mariadb
    ```
    

#### 3. Configuración de los nodos web (web01, web02)

- Instalar el servidor web (Apache) y PHP:
    
    ```bash
    sudo dnf install -y httpd php php-mysqlnd php-fpm php-gd php-xml php-mbstring
    sudo systemctl enable --now httpd
    ```
    
- Ajustar el firewall:
    
    ```bash
    sudo firewall-cmd --permanent --add-service=http
    sudo firewall-cmd --reload
    ```
    
- Descargar y configurar WordPress:
    
    ```bash
    cd /var/www/html
    sudo rm -f index.html
    sudo wget https://wordpress.org/latest.tar.gz
    sudo tar xzf latest.tar.gz
    sudo mv wordpress/* .
    sudo rm -rf wordpress latest.tar.gz
    sudo chown -R apache:apache /var/www/html
    ```
    
- Configurar el fichero `wp-config.php`:
    
    ```bash
    cd /var/www/html
    sudo cp wp-config-sample.php wp-config.php
    sudo vim wp-config.php
    # Añadir:
    # define('DB_NAME', 'wordpress');
    # define('DB_USER', 'wpuser');
    # define('DB_PASSWORD', 'wp_pass');
    # define('DB_HOST', 'db01'); // o la IP de db01
    # define('DB_CHARSET', 'utf8');
    # define('DB_COLLATE', '');
    ```
    
- Confirmar que el sitio funciona accediendo desde el navegador a `http://web01/` y `http://web02/`. Debería aparecer la pantalla de instalación inicial de WordPress (aunque todavía no están balanceados).
    

#### 4. Configuración del balanceador de carga (balancer01)

- Instalar HAProxy (u otro balanceador, como Nginx):
    
    ```bash
    sudo dnf install -y haproxy
    ```
    
- Editar `/etc/haproxy/haproxy.cfg` para configurar el balanceo:
    
    ```bash
    sudo vim /etc/haproxy/haproxy.cfg
    ```
    
    Ejemplo de configuración mínima:
    
    ```
    frontend http_front
      bind *:80
      default_backend web_back
    
    backend web_back
      balance roundrobin
      server web01 192.168.56.11:80 check
      server web02 192.168.56.12:80 check
    ```
    
- Habilitar y arrancar HAProxy:
    
    ```bash
    sudo systemctl enable --now haproxy
    ```
    
- Ajustar firewall:
    
    ```bash
    sudo firewall-cmd --permanent --add-service=http
    sudo firewall-cmd --reload
    ```
    

#### 5. Pruebas finales

- Acceder desde el navegador a `http://balancer01/`. Debería mostrarse la página inicial de instalación de WordPress.
- Completar el proceso de instalación de WordPress (nombre del sitio, usuario administrador, contraseña).
- Una vez completada la instalación, recargar la página varias veces o detener temporalmente `httpd` en uno de los nodos para comprobar que el balanceador reparte las peticiones y que el sistema sigue siendo accesible a pesar de que un nodo web pueda fallar.

#### 6. Opciones de mejora

- Añadir HTTPS con certificados autofirmados.
- Añadir sincronización de ficheros entre los nodos web (NFS o GlusterFS) para compartir el contenido subido a WordPress.
- Monitorizar el estado del clúster con herramientas como `haproxy-stats` o Prometheus.

---

### Resultados esperados

Al finalizar la práctica, los alumnos deberían tener:

- Un mayor entendimiento de cómo crear entornos escalables y tolerantes a fallos.
- Un WordPress funcionando sobre un clúster de dos nodos web y un backend de base de datos.
- Un balanceador de carga redirigiendo el tráfico entre los nodos web de forma transparente.
- Mayor confianza a la hora de configurar servicios en un entorno Linux tipo Enterprise (AlmaLinux 9).

Esta práctica sentará las bases para explorar posteriormente temas de contenedores, orquestadores (como Kubernetes) o despliegues en la nube, aplicando los mismos conceptos fundamentales de alta disponibilidad y escalabilidad.




https://www.digitalocean.com/community/tutorial-series/load-balancing-wordpress-with-haproxy

https://www.virtuozzo.com/application-platform-docs/wordpress-cluster-devops/

https://www.youtube.com/watch?v=GbHu4trSt8k

https://www.haproxy.com/blog/enable-sticky-sessions-in-haproxy#how-to-implement-sticky-sessions-in-haproxy

https://www.stackscale.com/blog/high-availability-wordpress-website/