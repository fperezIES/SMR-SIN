# Comandos AlmaLinux


## Configuración del sistema

### Habilitar repositorios EPEL

```
dnf -y install epel-release
```
### Cambiar nombre a la máquina

 Para cambiar el nombre de una máquina en Linux, sigue estos pasos:

1. Cambia el nombre de la máquina con el siguiente comando:

   ```sh
   sudo hostnamectl set-hostname nuevo_nombre
   ```

2. Edita el archivo `/etc/hosts` para reemplazar el viejo nombre por el nuevo:

   ```sh
   sudo nano /etc/hosts
   ```

3. Comprueba el cambio de nombre con el siguiente comando:

   ```sh
   hostname
   ```

## Configuración de red

A continuación se presenta un procedimiento sencillo para configurar una dirección IP estática en AlmaLinux 9. Ten en cuenta que esta distribución, al igual que otras basadas en Red Hat, utiliza por defecto **NetworkManager**, por lo que las herramientas recomendadas son **nmcli** o el editor de conexiones en texto **nmtui**.

### Opción 1: Usar `nmtui` (Interfaz de texto)

1. Ejecuta:
    
    ```bash
    nmtui
    ```

2. Selecciona **"Editar una conexión"**.
3. Escoge la conexión asociada a tu interfaz.
4. Cambia el "Método" de `Automático (DHCP)` a `Manual`.
5. Introduce la dirección IP, la máscara de red, la puerta de enlace y el DNS.
6. Guarda los cambios y sal.
7. Ejecuta:
    
    ```bash
    nmcli connection up [NOMBRE_DE_LA_CONEXIÓN]
	```


### Opción 2: Usar `nmcli` (línea de comandos)

1. **Identificar la interfaz de red**  
    Ejecuta:
    
    ```bash
    ip link
    ```
    
    Localiza el nombre de tu interfaz, por ejemplo `ens33` o `eth0`.
    
2. **Listar las conexiones existentes**
    
    ```bash
    nmcli connection show
    ```
    
    Esto mostrará las conexiones administradas por NetworkManager. Anota el nombre de la conexión asociada a tu interfaz (por ejemplo, `ens33`).
    
3. **Configurar la IP estática**  
    - Supongamos que quieres asignar la IP `192.168.1.100` con la máscara `/24` (255.255.255.0) y la puerta de enlace `192.168.1.1`. También supondremos que el DNS será `8.8.8.8`.
    
    Ejecuta:
    
    ```bash
    nmcli connection modify ens33 ipv4.addresses 192.168.1.100/24
    nmcli connection modify ens33 ipv4.gateway 192.168.1.1
    nmcli connection modify ens33 ipv4.dns 8.8.8.8
    nmcli connection modify ens33 ipv4.method manual
    ```
    
    Ajusta el nombre de la conexión y la interfaz según corresponda.
    
4. **Aplicar los cambios**
    
    ```bash
    nmcli connection up ens33
    ```
    
    Una vez hecho esto, tu equipo debería tener la IP estática asignada.
    


### Verificación

- Comprueba la configuración:
    
    ```bash
    ip addr show ens33
    ```
    
- Verifica conectividad:
    
    ```bash
    ping 8.8.8.8
    ```
    

Si todo está correctamente configurado, verás que tu sistema conserva la IP estática después de reiniciar la interfaz o la máquina.



## Firewalld

```
firewall-cmd --permanent --zone=public --list-all
```

### Abrir un puerto en Firewall

```
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent

sudo firewall-cmd --reload
```
sudo


## Configuración de servicios


### Instalar Apache

Actualiza el sistema:

```
sudo dnf update
```

Instala apache:

```
sudo dnf install httpd
```

Inicia y habilita el servicio: 

```
sudo systemctl start httpd  
sudo systemctl enable httpd
```

Añade los puertos al firewall
```
sudo firewall-cmd --permanent --add-service={http,https}
sudo firewall-cmd --reload
```




# Referencias

- [Server-World.info](https://www.server-world.info/en/note?os=AlmaLinux_9)