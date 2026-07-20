---
icon: material/file-edit
---

TODO://

### Práctica: Configuración de una VPN Site-to-Site con pfSense (IPsec)

#### Objetivo:

Aprender a configurar una VPN Site-to-Site entre dos redes remotas utilizando **pfSense**. Este ejercicio permitirá a los alumnos comprender los principios básicos de las VPN, así como su implementación práctica.

---

### Requisitos previos:

1. Dos máquinas virtuales o físicas con **pfSense** instaladas (una para cada red).
2. Dos redes LAN simuladas con diferentes rangos de IP (por ejemplo, **Red A: 192.168.1.0/24** y **Red B: 192.168.2.0/24**).
3. Acceso a Internet simulado o configurado entre las máquinas pfSense (a través de un router virtual, por ejemplo).
4. Cliente en cada red para pruebas de conectividad (pueden ser máquinas virtuales con Windows o Linux).
5. Conocimientos básicos de pfSense y redes.

---

### Escenario:

1. Configurar una VPN **IPsec** entre dos redes simuladas utilizando pfSense para permitir la comunicación entre ambas.
2. Verificar la conectividad entre clientes de ambas redes.

---

### Pasos:

#### 1. Configuración inicial de las redes:

1. **Red A (pfSense A):**
    - WAN: Configura una IP pública simulada (por ejemplo, **10.0.0.1**).
    - LAN: Configura la red local **192.168.1.0/24**.
2. **Red B (pfSense B):**
    - WAN: Configura una IP pública simulada (por ejemplo, **10.0.0.2**).
    - LAN: Configura la red local **192.168.2.0/24**.

#### 2. Configuración de IPsec en pfSense A:

1. Accede a la interfaz web de pfSense A.
2. Ve a **VPN > IPsec** y habilita el servicio.
3. Configura una nueva fase 1:
    - **Key Exchange Version**: IKEv2.
    - **Interface**: WAN.
    - **Remote Gateway**: 10.0.0.2.
    - **Authentication Method**: Pre-Shared Key.
    - **Pre-Shared Key**: Introduce una clave segura (por ejemplo, `ClaveSegura123`).
    - **Phase 1 Proposal**:
        - Encryption Algorithm: AES (256 bits).
        - Hash Algorithm: SHA256.
        - DH Group: 14 (2048 bits).
        - Lifetime: 28800 segundos.
4. Configura una nueva fase 2:
    - **Mode**: Tunnel IPv4.
    - **Local Network**: 192.168.1.0/24.
    - **Remote Network**: 192.168.2.0/24.
    - **Phase 2 Proposal**:
        - Protocol: ESP.
        - Encryption Algorithms: AES (256 bits).
        - Hash Algorithms: SHA256.
        - PFS key group: Same as phase 1.

#### 3. Configuración de IPsec en pfSense B:

Repite los pasos de configuración en pfSense B, pero:

- Configura el **Remote Gateway** como **10.0.0.1**.
- Configura las redes locales y remotas en la fase 2 de forma inversa:
    - **Local Network**: 192.168.2.0/24.
    - **Remote Network**: 192.168.1.0/24.
- Usa el mismo **Pre-Shared Key** definido en pfSense A.

#### 4. Configuración de reglas de firewall:

1. En ambas máquinas pfSense:
    - Ve a **Firewall > Rules > IPsec**.
    - Crea una regla que permita todo el tráfico desde la red remota hacia la red local.

#### 5. Verificación de la conexión VPN:

1. En pfSense A y B, ve a **Status > IPsec** y verifica que el túnel esté establecido.
2. Desde un cliente en la **Red A (192.168.1.x)**, haz un ping a un cliente en la **Red B (192.168.2.x)**.
3. Comprueba la conectividad utilizando herramientas como **traceroute** o intentos de conexión a servicios básicos (por ejemplo, HTTP, SSH).

#### 6. Solución de problemas (si es necesario):

- Revisa los registros en **Status > System Logs > IPsec**.
- Comprueba que las reglas de firewall permiten el tráfico entre las redes.
- Verifica que las configuraciones de redes y fases coincidan en ambas máquinas pfSense.

---

### Evaluación:

1. El túnel VPN debe estar activo y estable.
2. Los clientes de ambas redes deben poder comunicarse entre sí.
3. Explica en un informe:
    - La utilidad de una VPN site-to-site.
    - Las configuraciones clave realizadas.
    - Posibles aplicaciones prácticas en un entorno empresarial.

---

### Extensión (opcional):

1. Configurar restricciones de tráfico en las reglas del firewall (por ejemplo, solo permitir SSH).
2. Probar con otro protocolo de VPN soportado por pfSense, como **OpenVPN**.

---

Este ejercicio ayuda a consolidar los conceptos de VPN y su implementación práctica en entornos simulados, preparándote para situaciones reales en ciberseguridad y redes. ¡Buena suerte! 🚀