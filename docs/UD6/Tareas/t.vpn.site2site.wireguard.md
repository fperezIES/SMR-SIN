---
icon: material/file-edit
---


# Configuración de una VPN site to site con WireGuard en pfSense



WireGuard es un protocolo de red y una solución de VPN (Red Privada Virtual) moderna y simplificada. Se diseñó para ser más rápido, seguro y fácil de configurar que otras tecnologías VPN tradicionales como OpenVPN o IPSec. Está implementado dentro del kernel de sistemas operativos como Linux, lo que permite un rendimiento óptimo.


![](Logo_of_WireGuard.svg){:style="width: 80%;" class="center"}


### **¿Cómo funciona?**

WireGuard establece conexiones punto a punto mediante claves públicas y privadas, similar al modelo de criptografía asimétrica. El proceso incluye:

1. **Generación de claves:** Cada dispositivo genera una clave pública y una privada.
2. **Intercambio de claves:** Las claves públicas se comparten con los dispositivos remotos.
3. **Conexión segura:** Una vez configurados, los dispositivos se comunican cifrando todo el tráfico con algoritmos como ChaCha20, garantizando privacidad y seguridad.

WireGuard funciona a nivel de capa 3 (red IP) y emplea una lista blanca de pares, donde solo los dispositivos autorizados pueden intercambiar tráfico.

### **Ventajas de WireGuard**

1. **Simplicidad:** La configuración y el código fuente son mucho más compactos que el de otras soluciones VPN, facilitando su mantenimiento y auditoría.
2. **Rendimiento:** Al estar integrado en el kernel y usar algoritmos optimizados, ofrece velocidades superiores con menor uso de CPU.
3. **Seguridad:** Usa criptografía moderna y elimina configuraciones complejas que suelen ser fuentes de errores.
4. **Compatibilidad multiplataforma:** Disponible para Linux, Windows, macOS, Android, iOS, y otros.
5. **Escalabilidad:** Ideal para conexiones desde pequeños entornos hasta infraestructuras grandes, gracias a su bajo consumo de recursos.

WireGuard es una excelente opción para redes modernas que necesitan soluciones VPN rápidas, seguras y eficientes.


**Casos de uso de WireGuard**:

- Establecer una conexión directa entre dos o más dispositivos con configuraciones personalizadas.
- Reemplazar VPNs tradicionales con un enfoque más rápido y seguro.
- Útil para administradores que desean un control total y no necesitan gestión centralizada.

## Objetivo


Esta práctica te ayudará a implementar una VPN sencilla y segura entre dos redes utilizando WireGuard en pfSense. Puedes personalizarla según las necesidades de tu infraestructura, añadiendo más peers, configuraciones avanzadas de firewall o ajustes de subred.

Se propone configurar una VPN sitio a sitio entre dos sistemas pfSense utilizando WireGuard, con el objetivo de permitir la comunicación segura entre dos redes LAN en ubicaciones diferentes. Esta configuración corresponde con el vídeo de la bibliografía que ilustra cómo realizar la práctica.

![](Captura%20de%20pantalla%202025-01-17%20a%20las%2011.57.30.png){:style="width: 80%;" class="center"}
#### Requisitos previos

1. **Dos sistemas pfSense operativos** en redes separadas (Puedes cambiar los rangos de IPs como creas conveniente):
    - Red A: LAN (192.168.23.0/24), WAN (169.69.69.43)
    - Red B: LAN (192.168.17.0/24), WAN (169.69.69.44)
2. **Subred para el túnel**: 10.69.69.0/24.
3. Acceso administrativo a ambos sistemas pfSense.
4. Conocimiento básico sobre redes y subredes.

## Pasos a realizar

#### Paso 1: Configuración inicial en pfSense

1. **Acceder a la configuración**:
    
    - Ve a `VPN > WireGuard` en el menú de pfSense.
2. **Crear un túnel**:
    
    - Haz clic en "Add Tunnel".
    - Configura el puerto de escucha (por defecto: 51820).
    - Haz clic en "Generate" para generar las claves públicas y privadas.
    - Guarda la configuración.
3. **Asignar IP del túnel**:
    
    - En el túnel creado, asigna una IP dentro de la subred 10.69.69.0/24:
        - **Sitio A**: 10.69.69.1/24
        - **Sitio B**: 10.69.69.2/24


#### Paso 2: Configuración de los peers

1. **Añadir peers**:
    
    - **Sitio A**:
        - Agrega un peer con la clave pública del Sitio B.
        - Configura las redes permitidas (Allowed IPs): 10.69.69.0/24, 192.168.17.0/24.
    - **Sitio B**:
        - Agrega un peer con la clave pública del Sitio A.
        - Configura las redes permitidas: 10.69.69.0/24, 192.168.23.0/24.
2. **Configurar el endpoint**:
    
    - Sitio A apunta al IP WAN de Sitio B (169.69.69.44).
    - Sitio B apunta al IP WAN de Sitio A (169.69.69.43).


#### Paso 3: Asignación de interfaces

1. **Crear interfaces**:
    
    - Ve a `Interfaces > Assignments`.
    - Asigna el túnel WireGuard a una nueva interfaz (ejemplo: `WG_Tunnel`).
    - Configura el MTU a 1420 y MSS a 1420.
2. **Configurar rutas estáticas**:
    
    - Ve a `System > Routing > Gateways` y añade una pasarela para la IP del túnel.
    - Configura rutas estáticas para redirigir el tráfico de una LAN a la otra.


#### Paso 4: Reglas de firewall

1. **Reglas en la WAN**:
    
    - Permite el tráfico UDP entrante en el puerto configurado para WireGuard (51820).
2. **Reglas en el túnel**:
    
    - Crea una regla para permitir todo el tráfico entre las redes (opcionalmente, filtra según tus necesidades).

#### Paso 5: Verificar la conexión

1. **Probar la conectividad**:
    
    - Desde un dispositivo en la LAN del Sitio A, haz ping a un dispositivo en la LAN del Sitio B (y viceversa).
2. **Revisar el estado**:
    
    - Ve a `VPN > WireGuard > Status` para confirmar que ambos peers están conectados.


#### Extensión: Configuración avanzada

1. **Optimizar el tamaño de la subred**:
    - Ajusta la máscara de subred del túnel (por ejemplo, /30 para solo dos IPs).
2. **Agregar múltiples peers o subredes**:
    - Añade rutas estáticas adicionales y configura reglas de firewall según sea necesario.



## Bibliografía

<iframe width="560" height="315" src="https://www.youtube.com/embed/WXkWP-JZOd8?si=G1yxpyo-eJ64Tuf9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


 - [WireGuard Site-to-Site VPN Configuration Example](https://docs.netgate.com/pfsense/en/latest/recipes/wireguard-s2s.html)
 - [Gateways en pfSense](https://docs.netgate.com/pfsense/en/latest/routing/gateways.html)