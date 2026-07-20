---
icon: material/file-edit
---



A continuación, se proporciona una guía para configurar una VPN de acceso remoto utilizando WireGuard en pfSense. Este proceso permite a los clientes conectarse de forma segura a la red interna a través de una conexión cifrada.

**Información Necesaria**

Antes de comenzar, es fundamental definir los siguientes parámetros:

- **Diseño**: Acceso remoto con un túnel y múltiples pares.
- **WAN del Firewall**: 198.51.100.6
- **Puerto de Escucha**: 51820
- **Subred del Túnel**: 10.6.210.0/24
- **Dirección del Túnel**: 10.6.210.1/24
- **Direcciones de los Pares**: 10.6.210.2 - 10.6.210.254
- **Endpoints de los Pares**: Dinámicos

**Generación de Claves**

WireGuard requiere pares de claves pública/privada para cada par, incluido el firewall.

- **Claves del Túnel**:
    
    - Al configurar un túnel en pfSense, haga clic en "Generar" para crear automáticamente las claves privada y pública. La clave pública será necesaria para los clientes.
- **Claves de los Pares**:
    
    - Cada cliente necesita su propio par de claves. Estas pueden generarse en el cliente o en un sistema con las utilidades de WireGuard instaladas.
        
    - Para generar las claves desde la línea de comandos:
        
        bash
        
        CopiarEditar
        
        `wg genkey | tee privatekey | wg pubkey > publickey`
        
    - La clave privada se utilizará en el cliente, y la clave pública se configurará en el firewall.
        

**Configuración del Túnel**

1. Navegue a **VPN > WireGuard > Tunnels** en pfSense.
2. Haga clic en "Add Tunnel".
3. Complete los siguientes campos:
    - **Enable**: Marcado
    - **Description**: RemoteAccess
    - **Listen Port**: 51820
    - **Interface Keys**: Haga clic en "Generate" para crear un nuevo par de claves.
    - **Interface Addresses**: 10.6.210.1/24
4. Haga clic en "Save".

**Configuración de los Pares**

1. Mientras edita el túnel, desplácese hacia abajo hasta la sección de pares.
2. Haga clic en "Add Peer".
3. Complete los siguientes campos:
    - **Enable**: Marcado
    - **Description**: Nombre del cliente (por ejemplo, el nombre de la persona o dispositivo)
    - **Dynamic Endpoint**: Marcado
    - **Public Key**: La clave pública del cliente generada previamente
    - **Allowed IPs**: La dirección IP del túnel asignada al cliente con máscara /32 (por ejemplo, 10.6.210.2/32)
4. Haga clic en "Save".

Repita estos pasos para cada cliente que desee agregar.

**Reglas de Firewall**

1. **Permitir tráfico de WireGuard en la WAN**:
    
    - Vaya a **Firewall > Rules**, pestaña WAN.
    - Haga clic en "Add" para crear una nueva regla.
    - Configure los siguientes parámetros:
        - **Action**: Pass
        - **Interface**: WAN
        - **Protocol**: UDP
        - **Source**: any
        - **Destination**: WAN Address
        - **Destination Port Range**: 51820
        - **Description**: Permitir tráfico a WireGuard
    - Haga clic en "Save" y luego en "Apply Changes".
2. **Permitir tráfico dentro del túnel de WireGuard**:
    
    - Vaya a **Firewall > Rules**, pestaña WireGuard.
    - Haga clic en "Add" para crear una nueva regla.
    - Configure los siguientes parámetros:
        - **Action**: Pass
        - **Interface**: WireGuard
        - **Protocol**: Any
        - **Source**: any
        - **Destination**: any
        - **Description**: Permitir tráfico desde pares de WireGuard
    - Haga clic en "Save" y luego en "Apply Changes".

**Configuración del Cliente**

La configuración del cliente variará según la plataforma. A continuación, se muestra un ejemplo básico para una configuración de túnel dividido:

ini

CopiarEditar

`[Interface] PrivateKey = CLAVE_PRIVADA_DEL_CLIENTE ListenPort = 51820 Address = 10.6.210.2/24  [Peer] PublicKey = CLAVE_PUBLICA_DEL_FIREWALL AllowedIPs = 10.6.210.1/32, 10.6.0.0/24 Endpoint = 198.51.100.6:51820`

Asegúrese de reemplazar `CLAVE_PRIVADA_DEL_CLIENTE` y `CLAVE_PUBLICA_DEL_FIREWALL` con las claves correspondientes.

**Finalización**

Después de configurar el cliente y activar la VPN, el cliente debería poder transmitir tráfico a las redes listadas en `AllowedIPs` de su configuración.

Para más detalles, puede consultar la documentación oficial de pfSense sobre la configuración de WireGuard para acceso remoto.


## Bibliografía

<iframe width="560" height="315" src="https://www.youtube.com/embed/8jQ5UE_7xds?si=W1tT5zr3nEJ4dLFj" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


- [Netgate Documentation Wireguard remote access example](https://docs.netgate.com/pfsense/en/latest/recipes/wireguard-ra.html)
- [WireGuard Home Page](https://www.wireguard.com/)
	- [Wireward CLI Doc](https://www.wireguard.com/quickstart/#command-line-interface)

- [WireGuard Man pages](https://manpages.debian.org/unstable/wireguard-tools/index.html)
- [CLI Mac](https://blog.scottlowe.org/2021/06/28/using-wireguard-on-mac-via-cli/)

https://upcloud.com/resources/tutorials/get-started-wireguard-vpn

