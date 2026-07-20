---
icon: material/file-edit
---


## Instalación y configuración de proxy directo Squid

Squid es una herramienta muy versátil no solo para filtrar contenido, sino también para mejorar el rendimiento de la red cacheando archivos y gestionando peticiones de manera eficiente.

En esta práctica veremos cómo:

1. Instalar el paquete **Squid** en AlmaLinux 9.
2. Configurar el servicio para que funcione como proxy HTTP/HTTPS.
3. Aprender a configurar ACLs (Access Control Lists) para filtrar/bloquear sitios web según diferentes criterios:
    - Bloquear dominios concretos.
    - Bloquear por tipo de contenido (p. ej., extensiones).
    - Bloquear por rangos de dirección IP.
4. Verificar el correcto funcionamiento del proxy y de las reglas de bloqueo.


## Instalación de Squid

1. **Actualizar repositorios**
    
    ```bash
    sudo dnf update -y
    ```
    
2. **Instalar Squid**
    
    ```bash
    sudo dnf install -y squid
    ```
    
3. **Verificar la instalación**
    
    - Comprobar si el servicio está habilitado y arrancado:
        
        ```bash
        systemctl status squid
        ```
        
    - Si no estuviera activo, iniciarlo y habilitarlo en el arranque:
        
        ```bash
        sudo systemctl start squid
        sudo systemctl enable squid
        ```
        

> **Nota**: AlmaLinux 9 usa `systemd`, por lo que los comandos de arranque y parada son a través de `systemctl`.


## Configuración básica de Squid

El archivo de configuración principal se encuentra en:

```
/etc/squid/squid.conf
```

1. **Realizar una copia de seguridad** del archivo de configuración original:
    
    ```bash
    sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.bak
    ```
    
2. **Editar el archivo** con vuestro editor favorito (p. ej., `vim`, `nano`):
    
    ```bash
    sudo nano /etc/squid/squid.conf
    ```
    
3. **Parámetros clave** que se suelen revisar:
    - `http_port 3128`: Puerto por defecto donde escucha Squid (puede cambiarse según conveniencia).
    - `acl localnet src 192.168.0.0/16` (u otros rangos de tu red) para definir las subredes que podrán usar el proxy.
    - `http_access allow localnet` para permitir a esas subredes acceder a Internet a través del proxy.

Por ejemplo, si tenemos una subred `192.168.1.0/24`, podríamos añadir:

```plain
acl localnet src 192.168.1.0/24
http_access allow localnet
```

y asegurarnos de que tenemos una línea `http_access deny all` después de haber definido quién tiene permitido el acceso.

> **Nota**: La directiva `http_access` se evalúa en el orden en que aparece en el fichero: en cuanto se cumple una ACL `allow/deny`, se deja de evaluar el resto.

Tras realizar los cambios iniciales, guardamos el archivo y **reiniciamos** el servicio:

```bash
sudo systemctl restart squid
```


## Configuración de ACL para bloqueo de sitios web

Squid permite filtrar el tráfico a través de ACLs. Podemos bloquear dominios, IP, extensiones de archivos y más. Vamos a ver varios ejemplos:

### Bloqueo de dominios concretos

Supongamos que queremos bloquear el acceso a redes sociales como _facebook.com_ o _twitter.com_. Podemos crear una lista con los dominios prohibidos:

1. **Crear un fichero con dominios bloqueados**:
    
    ```bash
    sudo nano /etc/squid/bloqueados_dominios.txt
    ```
    
    Contenido de ejemplo:
    
    ```
    .facebook.com
    .twitter.com
    ```
    
    (El punto inicial `.` indica que se bloquee _cualquier subdominio_ de esos dominios.)
    
2. **Definir la ACL y la regla de acceso** en `/etc/squid/squid.conf`:
    
    ```plain
    # ACL para sitios bloqueados
    acl sitios_bloqueados dstdomain "/etc/squid/bloqueados_dominios.txt"
    http_access deny sitios_bloqueados
    ```
    
    > **Importante**: Estas líneas deben ir **antes** de la regla final que permita el tráfico o que lo niegue a todo. El orden es fundamental.
    
3. **Reiniciar el servicio** Squid:
    
    ```bash
    sudo systemctl restart squid
    ```
    

### Bloqueo por tipo de archivo (extensiones)

Podemos bloquear, por ejemplo, la descarga de archivos `.exe` o `.mp3`:

1. **Crear un fichero** con la lista de extensiones:
    
    ```bash
    sudo nano /etc/squid/bloqueados_extensiones.txt
    ```
    
    Contenido de ejemplo:
    
    ```
    .exe
    .mp3
    .mp4
    ```
    
2. **Definir la ACL** en `squid.conf`:
    
    ```plain
    acl extensiones_bloqueadas url_regex -i "/etc/squid/bloqueados_extensiones.txt"
    http_access deny extensiones_bloqueadas
    ```
    
3. **Reiniciar Squid**:
    
    ```bash
    sudo systemctl restart squid
    ```
    

### Bloqueo por direcciones IP o rangos

Para bloquear el acceso a un rango de IP externo (por ejemplo, 10.10.0.0/16), podemos definir:

1. **Definir la ACL** en `squid.conf`:
    
    ```plain
    acl ip_prohibidas dst 10.10.0.0/16
    http_access deny ip_prohibidas
    ```
    
2. Reiniciar:
    
    ```bash
    sudo systemctl restart squid
    ```
    


## Pruebas de funcionamiento

1. **Configurar un navegador** (en la misma red) para que use el proxy:    
    - IP del servidor: (por ejemplo) `192.168.1.100`
    - Puerto: `3128` (o el que tengas configurado en `squid.conf`)
2. **Probar el acceso a sitios bloqueados**:    
    - Accede a `https://www.facebook.com` o `https://twitter.com`.
    - Si todo está bien configurado, el navegador deberá mostrar un error de Squid indicando que el acceso está denegado.
3. **Probar la descarga de archivos con extensiones prohibidas**:    
    - Intenta descargar un archivo `.exe` o `.mp3` desde algún sitio de prueba.
    - Squid debería devolver un mensaje de bloqueo.
4. **Revisar los logs** en el servidor:    
    - Logs principales en `/var/log/squid/access.log`.
    - Podréis ver las entradas con códigos HTTP, direcciones IP, destinos, etc.



## Tareas y ejercicios adicionales 

1. **Bloquear páginas durante un horario concreto**  
    Investiga cómo usar ACLs basadas en horario (`acl HORARIO time`), de modo que, por ejemplo, se bloqueen ciertas webs en horario lectivo.
    
2. **Configurar autenticación**  
    Configura Squid para requerir usuario/contraseña antes de permitir la navegación. Squid soporta autenticación básica con archivos locales o incluso integración con LDAP/AD.
    
3. **Optimizar la caché**  
    Explora parámetros de caché (directivas `cache_dir`, `maximum_object_size`, `minimum_object_size`, etc.) para mejorar el rendimiento en tu red local.
    
4. **Lista blanca**  
    Crea una lista blanca de dominios y un enfoque donde **solo** se permitan sitios listados en ella.
    
5. **Gestión avanzada de logs**  
    Configura rotación de logs y aprende a interpretar los distintos tipos de códigos de estado que aparecen en los registros (`TCP_HIT`, `TCP_MISS`, etc.).


<!--
 Con esta práctica, se sienta la base para futuras configuraciones más avanzadas, como la autenticación de usuarios o la aplicación de horarios específicos de navegación.
	-->


## Bibliografía



- [Documentación Squid](https://www.squid-cache.org/Doc/)
	- [FAQ](https://wiki.squid-cache.org/SquidFaq/)
	- [ACLs](https://wiki.squid-cache.org/SquidFaq/SquidAcl)
	- [SSL-Bump](https://wiki.squid-cache.org/ConfigExamples/Intercept/SslBumpWithIntermediateCA)
- [Tutorial servidor proxy en Almalinux 9:](https://howtoforge.es/como-instalar-y-configurar-el-servidor-proxy-squid-en-rocky-linux-alma-linux-9/)
- [Sever-World: instalación de squid](https://www.server-world.info/en/note?os=AlmaLinux_9&p=squid&f=1)
- [Server-World: configuración de cliente linxu](https://www.server-world.info/en/note?os=AlmaLinux_9&p=squid&f=2)
- [Tutorial ACLs](https://nexolinux.com/proxy-squid-control-de-accesos-acl-ii-2/#google_vignette)


