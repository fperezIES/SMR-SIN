---
icon: material/file-edit
---

# Configuración de una VPN Site-to-Site con OpenVPN y pfSense


En esta práctica se propone el uso de OpenVPN junto con pfSense para configurar una VLAN site to site entre dos oficinas remotas.

![](../img/vpn/OpenVPN_logo.svg.png){:style="width: 80%;" class="center"}

### pfSense

pfSense es una plataforma de firewall y router basada en FreeBSD, conocida por su flexibilidad y capacidad para gestionar redes de manera eficiente. Uno de los usos más destacados de pfSense es su capacidad para configurar y administrar redes privadas virtuales (VPN), proporcionando una solución robusta para proteger y optimizar las comunicaciones de una red.

**Características principales del uso de VPN en pfSense**:

1. **Soporte para múltiples protocolos VPN**: pfSense permite implementar protocolos como OpenVPN, IPsec, WireGuard y L2TP/IPsec, adaptándose a las necesidades específicas de seguridad y rendimiento.
    
2. **Seguridad avanzada**: Integra herramientas para la autenticación de usuarios, el cifrado robusto y la gestión granular de políticas, garantizando conexiones seguras y controladas.
    
3. **Gestión centralizada**: A través de su interfaz gráfica, pfSense facilita la configuración y monitorización de las VPN, permitiendo gestionar usuarios remotos o conexiones site-to-site con relativa facilidad.




### OpenVPN 

[**OpenVPN**](https://openvpn.net/) es una solución de software libre ampliamente utilizada para implementar redes privadas virtuales (VPN). Su principal ventaja es su flexibilidad, ya que soporta una gran variedad de configuraciones y se basa en protocolos seguros como SSL/TLS para garantizar la confidencialidad e integridad de los datos. Además, es compatible con múltiples sistemas operativos, lo que lo convierte en una opción versátil tanto para entornos personales como empresariales.

**Usos principales de OpenVPN**:

1. **VPN Site-to-Site**: En este modo, OpenVPN conecta dos redes físicas separadas, como la sede principal de una empresa y una sucursal remota. Esto permite que los dispositivos en ambas redes se comuniquen como si estuvieran en la misma red local, facilitando el acceso a recursos compartidos de forma segura a través de Internet.
    
2. **VPN de Acceso Remoto**: Este enfoque permite a usuarios individuales conectarse de manera segura a una red privada desde cualquier lugar. Es ideal para trabajadores remotos o estudiantes que necesitan acceder a los recursos internos de una organización, como servidores, bases de datos o aplicaciones empresariales, sin comprometer la seguridad.


Gracias a su robustez, escalabilidad y soporte para configuraciones avanzadas, OpenVPN es una solución confiable tanto para pequeñas implementaciones como para grandes redes corporativas.

## Escenario

Tienes dos oficinas remotas que deben compartir recursos de red de manera segura. Una de las oficinas se configurará como el servidor OpenVPN y la otra como el cliente.


![](../img/vpn/pfsense_vpn_site2site.drawio.png)


## Objetivo

Configurar una VPN Site-to-Site utilizando OpenVPN en dos instancias de pfSense para conectar dos redes separadas y permitir el tráfico entre ellas de manera segura.


- Dos máquinas virtuales con pfSense instaladas.
    
- Dos redes LAN simuladas o reales con rangos diferentes, por ejemplo:
    
    - Red 1: 192.168.100.0/24
        
    - Red 2: 192.168.20.0/24


- En cada red necesitarás uno o varios equipos que actúen como cliente/servidor para comprobar el correcto funcionamiento de la VPN.

- Conectividad entre ambos firewalls pfSense a través de sus interfaces WAN usando una conexión VPN OpenVPN.

## Entregable


Debes entregar una memoria en formato PDF que incluya los siguientes elementos:

1. Informe breve que explique:
    
    - Los pasos realizados.        
    - Problemas encontrados y cómo los solucionaron.        
    - Ventajas de una VPN Site-to-Site en un entorno empresarial.
2. Capturas de pantalla de:
    
    - Configuración del servidor OpenVPN en la sede principal..
        
    - Configuración del cliente OpenVPN en la sede secundaria.
        
    - Pruebas de conectividad entre ambas redes.
        



# Bibliografía




- [OpenVPN](https://openvpn.net/)
- [Documentación VPN pfSense](https://docs.netgate.com/pfsense/en/latest/vpn/index.html)
	* [Configuración de OpenVPN para acceso remoto](https://docs.netgate.com/pfsense/en/latest/recipes/openvpn-ra.html)
	* [Añadir usuarios remotos a OpenVPN](https://docs.netgate.com/pfsense/en/latest/recipes/openvpn-ra-users.html)
	* [Instalación de clientes remotos OpenVPN](https://docs.netgate.com/pfsense/en/latest/recipes/openvpn-ra-client.html)