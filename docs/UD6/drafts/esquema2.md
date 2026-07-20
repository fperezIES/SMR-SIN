A continuación se presenta un **esquema unificado** que fusiona los temas de «UD6 - Seguridad en redes» y «UD7 - Seguridad perimetral», centrándonos en los aspectos **específicos de la seguridad en redes**. Asimismo, se proponen **cuatro prácticas** recomendadas, incluyendo las solicitadas sobre **firewalls** y **VPNs**.

---

## **Seguridad en Redes y Seguridad Perimetral (Esquema Unificado)**

### 1. Repaso inicial y panorama actual

1. **Contexto y evolución de la seguridad en redes**
    - Por qué es relevante la seguridad de red hoy día.
    - Principales amenazas actuales orientadas a la infraestructura de red.
2. **Repaso de conceptos clave ya vistos**
    - Criptografía básica (RSA, AES, hash).
    - Principios de seguridad (Confidencialidad, Integridad, Disponibilidad).
    - Conceptos de segmentación, VLAN, encapsulación, etc.

> **Objetivo**: Asegurar que todos los alumnos parten de una base común y comprenden el panorama de amenazas.

---

### 2. Seguridad en redes inalámbricas (WLAN) y cableadas (LAN)

1. **WLAN: protocolos y configuraciones seguras**
    - WPA2, WPA3 y autenticación 802.1X con RADIUS.
    - Amenazas frecuentes (Rogue AP, desautenticación, sniffing de tráfico Wi-Fi).
2. **LAN: segmentación y control de acceso**
    - Segmentación con VLANs: mejora de la seguridad y contención de incidentes.
    - Listas de control de acceso (ACLs) en routers y switches.
    - NAC (Network Access Control): verificación de dispositivos y políticas de cumplimiento.

> **Enfoque**: Configuración correcta de LAN/WLAN, ya no tanto los fundamentos, sino las **mejores prácticas** y riesgos específicos.

---

### 3. Firewalls y segmentación avanzada (DMZ)

1. **Tipos de firewalls**
    - Packet filtering, Stateful, Application-Layer, Next-Generation.
    - Diferencias entre hardware y software firewall.
2. **Configuración y políticas de filtrado**
    - Creación de reglas seguras, minimización de servicios expuestos.
    - Monitoreo de registros y alertas.
3. **DMZ y segmentación avanzada**
    - Concepto de Zona Desmilitarizada.
    - Diseño de topologías seguras (DMZ para servicios públicos, subredes aisladas).

> **Objetivo**: Comprender cómo proteger el perímetro de la red mediante la segmentación y la definición de reglas de filtrado.

---

### 4. Redes Privadas Virtuales (VPN) y conexiones remotas

1. **Concepto y tipos de VPN**
    - SSL VPN, IPSec VPN, L2TP, OpenVPN.
2. **Configuraciones típicas**
    - Acceso remoto seguro para teletrabajo.
    - Site-to-site para interconectar sedes.
3. **Retos y soluciones prácticas**
    - Fugas de DNS, uso de MFA, split tunneling vs. full tunneling.
    - Consideraciones de rendimiento y cifrado.

> **Objetivo**: Conocer la implementación de VPNs a nivel práctico y las medidas de seguridad asociadas.

---

### 5. Sistemas de detección y prevención de intrusiones (IDS/IPS)

1. **Funcionamiento y clasificación**
    - Basados en firma vs. análisis de anomalías.
    - IDS de red (NIDS) vs. IDS de host (HIDS).
2. **IPS: respuesta activa**
    - Automatización de la mitigación: bloqueo de paquetes o direcciones IP.
    - Integración con firewalls y SIEM.
3. **Limitaciones y falsos positivos**
    - Configuración y tunning de reglas para reducir falsos positivos.
    - Importancia del monitoreo continuo.

> **Objetivo**: Saber desplegar e integrar IDS/IPS en una infraestructura de red para detección temprana de amenazas.

---

### 6. Proxies, WAF y seguridad en aplicaciones

1. **Proxies directos e inversos**
    - Filtrado de contenido y caché para usuarios internos (proxy directo).
    - Protección de servidores web y aplicaciones (proxy inverso).
2. **WAF (Web Application Firewall)**
    - Detección y bloqueo de ataques web comunes (SQLi, XSS).
    - Limitaciones y bypasses habituales.
3. **Buenas prácticas en aplicaciones**
    - Certificados SSL/TLS y configuración segura de HTTPS.
    - Integración con SIEM para correlacionar eventos de seguridad.

> **Objetivo**: Proteger aplicaciones web y el tráfico entrante/saliente en la red.

---

### 7. Mitigación de DDoS y Zero Trust

1. **Ataques DDoS**
    - Vectores comunes (volumétricos, amplificación, capa de aplicación).
    - Estrategias de mitigación (Rate limiting, scrubbing centers, CDNs).
2. **Zero Trust Architecture (ZTA)**
    - Principios de confianza cero y microsegmentación.
    - Autenticación continua, MFA y verificación de identidad de dispositivos.
3. **Retos en la implementación**
    - Adaptación de infraestructuras heredadas.
    - Políticas y cultura de seguridad.

> **Objetivo**: Presentar enfoques modernos de seguridad perimetral y de acceso, frente a amenazas avanzadas.

---

### 8. Integración, monitoreo y respuesta a incidentes

1. **SIEM (Security Information and Event Management)**
    - Centralización de logs y correlación de eventos.
    - Alertas y automatización de respuesta (SOAR).
2. **Flujo de trabajo en incidentes**
    - Detección, contención, erradicación y recuperación.
    - Informe postincidente y mejora continua.
3. **Casos de uso reales**
    - Análisis de logs de firewall + IDS/IPS + WAF.
    - Ejemplo práctico de correlación en un SIEM.

> **Objetivo**: Aprender a gestionar eventos y responder rápidamente ante incidentes de seguridad.

---

## **Propuesta de 4 Prácticas (Laboratorios)**

1. **Práctica 1: Configuración y endurecimiento de un Firewall**
    
    - Uso de un firewall (p. ej., pfSense, iptables o similar) para definir reglas de filtrado entrante y saliente.
    - Creación de logs y monitorización de eventos.
    - Configuración de listas blancas y negras, y análisis básico de tráfico.
2. **Práctica 2: Implementación de una VPN de acceso remoto**
    
    - Despliegue de una VPN (IPsec/SSL/OpenVPN) para que usuarios externos se conecten a la red interna.
    - Configuración segura: cifrado, certificados y autenticación multifactor si es posible.
    - Verificación de la conexión mediante pruebas de ping, acceso a recursos internos, etc.
3. **Práctica 3: IDS/IPS básico en entorno real o virtual**
    
    - Instalación y configuración de un IDS/IPS (ej. Snort, Suricata).
    - Generación de tráfico de prueba para detectar o bloquear ataques simulados (escaneos de puertos, intentos de fuerza bruta).
    - Análisis de logs e identificación de falsos positivos.
4. **Práctica 4: Segmentación de redes y DMZ**
    
    - Configuración de VLANs en un switch y creación de una DMZ para servicios públicos.
    - Creación de reglas de acceso entre VLANs y la DMZ en el firewall.
    - Pruebas de aislamiento: verificar que los servicios internos no son accesibles desde VLANs no autorizadas.

> **Nota**: Cada práctica puede adaptarse al tiempo disponible. Es conveniente agrupar la práctica 3 (IDS/IPS) con la 1 (Firewall), si se desea un entorno de laboratorio más completo.

---

## **Distribución temporal sugerida (4 semanas, 5 h/semana)**

- **Semana 1 (5 h)**: Repaso + Seguridad en WLAN/LAN + Firewalls y DMZ
- **Semana 2 (5 h)**: VPN + IDS/IPS + **Práctica 1 (Firewall)**
- **Semana 3 (5 h)**: Proxies, WAF, DDoS, Zero Trust + **Práctica 2 (VPN)**
- **Semana 4 (5 h)**: SIEM, respuesta a incidentes + **Prácticas 3 (IDS/IPS) y 4 (Segmentación/DMZ)**

_Esta planificación es flexible; se puede ajustar según la profundidad de cada contenido y el ritmo de la clase._

---

### Conclusión

El esquema anterior unifica los contenidos de seguridad en redes y seguridad perimetral, **enfocado en aspectos prácticos y específicos de la protección de la infraestructura de red**. Las **cuatro prácticas** propuestas proporcionan una experiencia completa: desde la **configuración y monitorización de firewalls**, pasando por la **implementación de VPN**, hasta la **detección de intrusos** y la **segmentación en entornos reales**. Con esta estructura, los alumnos podrán **asentar los conocimientos** y **adquirir competencias técnicas** esenciales para el mundo laboral.