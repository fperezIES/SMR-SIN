---
icon: material/file-edit
---


//TODO:

https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/9/html/deploying_web_servers_and_reverse_proxies/index

### 2.3. Ejemplo de configuración con Nginx

Para los alumnos que estéis profundizando en la administración de sistemas o servidores web, un caso muy común es utilizar **Nginx** como proxy inverso. Imaginemos que tenemos dos aplicaciones en diferentes puertos (por ejemplo, la aplicación A en el puerto 8081 y la aplicación B en el 8082). Podríamos configurar un archivo de configuración en Nginx así:

```nginx
server {
    listen 80;
    server_name midominio.com;

    location /appA/ {
        proxy_pass http://127.0.0.1:8081/;
    }

    location /appB/ {
        proxy_pass http://127.0.0.1:8082/;
    }
}
```

1. La directiva `listen 80;` indica que Nginx va a escuchar en el puerto 80 (HTTP).
2. `server_name midominio.com;` especifica el dominio al que se le aplicará esta configuración.
3. La ruta `/appA/` redirige la petición al puerto `8081`, donde está la aplicación A.
4. La ruta `/appB/` redirige la petición al puerto `8082`, donde está la aplicación B.

De este modo, el usuario solo accede a `http://midominio.com/appA/` o `http://midominio.com/appB/`, sin conocer los puertos internos ni la estructura real de aplicaciones.

### 2.4. Balanceo de carga con Nginx

Si quisiéramos balancear la carga entre varios servidores que dan el mismo servicio (imaginemos que tenemos dos servidores que ofrecen la misma aplicación en el puerto 8080), podríamos configurar algo como:

```nginx
upstream backend_app {
    server 192.168.1.100:8080;
    server 192.168.1.101:8080;
}

server {
    listen 80;
    server_name midominio.com;

    location / {
        proxy_pass http://backend_app;
    }
}
```

- La directiva `upstream` define un “grupo” de servidores (en este caso, dos IP con el mismo servicio).
- `proxy_pass` dirige todas las peticiones al grupo `backend_app`, repartiéndolas entre los servidores configurados.
