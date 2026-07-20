## UD4 - Fortificación hosts



4.1 **Control de acceso y autenticación**

- **Contraseñas seguras**
- **Autenticación multifactor (MFA)**
- **Biometría y otros métodos de autenticación avanzada**

4.2 **Seguridad en el arranque**

- **BIOS/UEFI**: Configuración segura
- **Gestores de arranque**: Protección y cifrado
- **Medidas de seguridad física**: Protección contra accesos no autorizados al hardware

4.3 **Cifrado y protección de datos**

- **Cifrado de discos**: BitLocker, LUKS, etc.
- **Cifrado de particiones y carpetas**: Opciones avanzadas de seguridad
- **Protección de datos en reposo y en tránsito**

4.4 **Actualización, parches y gestión de vulnerabilidades**

- **Actualización de software y sistema operativo**: Automatización de parches
- **Verificación de integridad del software**: Control de versiones y firmas digitales
- **Gestión de vulnerabilidades**: Evaluación y mitigación de riesgos

4.5 **Monitorización y auditoría**

- **Registro de eventos y logs**: Análisis de eventos de seguridad
- **Herramientas de monitorización**: IDS/IPS, SIEM
- **Auditoría de sistemas**: Verificación y cumplimiento de políticas de seguridad

4.6 **Protección contra malware**

- **Tipos de malware**: Virus, gusanos, ransomware
- **Software antimalware**: Soluciones de prevención y eliminación
- **Medidas de protección avanzada**: EDR, sandboxing

# Prácticas

Aquí tienes algunas prácticas que puedes proponer para el tema de **fortalecimiento de hosts** en tus clases de ciberseguridad. Estas actividades ayudarán a los estudiantes a comprender y aplicar diferentes técnicas de protección en sistemas operativos y equipos de red:

### 1. Configuración de Políticas de Contraseñas

**Objetivo:** Establecer políticas de contraseñas robustas en sistemas operativos.

**Práctica:** 
- Configura políticas de contraseñas en un sistema Windows o Linux. Incluye requisitos de longitud, complejidad, y caducidad de contraseñas.
- Prueba con los estudiantes la creación de usuarios y verificación de que las políticas se cumplen.
  
**Resultados Esperados:** Los estudiantes comprenderán cómo las políticas de contraseñas fortalecen la seguridad del sistema y cómo implementarlas en diferentes entornos.

---

### 2. Configuración de Firewall Local

**Objetivo:** Aprender a configurar y gestionar un firewall local.

**Práctica:**
- Configura el firewall en un sistema Windows o Linux para bloquear todos los puertos, excepto los necesarios para el funcionamiento de ciertos servicios.
- Realiza pruebas de conexión antes y después de activar las reglas del firewall.

**Resultados Esperados:** Los estudiantes comprenderán cómo controlar el tráfico de red en un host para reducir el riesgo de acceso no autorizado.

---

### 3. Desactivación de Servicios Innecesarios

**Objetivo:** Identificar y desactivar servicios innecesarios en el sistema.

**Práctica:**
- Revisa la lista de servicios activos en Windows (a través de `services.msc`) o en Linux (mediante `systemctl` o `service`).
- Desactiva servicios que no sean necesarios para el funcionamiento del sistema, como el servicio de impresión en una máquina sin impresora.

**Resultados Esperados:** Aprenderán a optimizar y reducir la superficie de ataque de un host deshabilitando servicios no esenciales.

---

### 4. Actualización del Sistema y Gestión de Parches

**Objetivo:** Enseñar la importancia de las actualizaciones de seguridad.

**Práctica:**
- Realiza una actualización manual en un sistema Windows o Linux, asegurándote de aplicar todos los parches de seguridad.
- Los estudiantes pueden investigar las vulnerabilidades que se corrigen con estas actualizaciones.

**Resultados Esperados:** Los alumnos entenderán la importancia de mantener el sistema actualizado para prevenir ataques basados en vulnerabilidades conocidas.

---

### 5. Configuración de Permisos de Archivos y Directorios

**Objetivo:** Gestionar permisos de archivos y directorios para fortalecer la seguridad.

**Práctica:**
- Configura permisos en Linux usando comandos como `chmod`, `chown` y `chgrp` para restringir el acceso a archivos y carpetas.
- Configura permisos en Windows mediante las propiedades de seguridad.

**Resultados Esperados:** Los estudiantes aprenderán a proteger archivos sensibles y a gestionar los permisos para limitar el acceso a usuarios no autorizados.

---

### 6. Configuración de BitLocker o LUKS para Encriptación de Disco

**Objetivo:** Proteger datos sensibles mediante la encriptación de disco.

**Práctica:**
- Configura BitLocker en Windows o LUKS en Linux para encriptar el disco o una partición específica.
- Simula el proceso de acceso a la información en un disco encriptado, mostrando la importancia de proteger físicamente el equipo.

**Resultados Esperados:** Los alumnos comprenderán cómo la encriptación protege datos confidenciales en caso de robo o acceso no autorizado al equipo.

---

### 7. Configuración de Seguridad en Navegadores Web

**Objetivo:** Aplicar configuraciones de seguridad en navegadores para reducir los riesgos al navegar.

**Práctica:**
- Configura un navegador para que bloquee pop-ups, cookies de terceros y active "No rastrear".
- Instala una extensión de bloqueo de anuncios y simula navegación segura en entornos sospechosos.

**Resultados Esperados:** Los estudiantes aprenderán cómo mejorar la seguridad al navegar en internet, evitando rastreos y contenidos maliciosos.

---

### 8. Auditoría de Seguridad Básica

**Objetivo:** Realizar una auditoría de seguridad en un sistema operativo.

**Práctica:**
- Usa herramientas como Lynis (para Linux) o la herramienta de auditoría de seguridad de Windows para identificar posibles debilidades.
- Analiza el informe generado y plantea posibles soluciones para mejorar el sistema.

**Resultados Esperados:** Los estudiantes comprenderán cómo identificar vulnerabilidades en un host y sugerir medidas de fortalecimiento basadas en los resultados de una auditoría.

---

Estas prácticas, además de ser aplicables, pueden motivar a los alumnos a continuar explorando el fortalecimiento de hosts como una parte esencial de la ciberseguridad. Además, permiten una combinación de teoría y aplicación práctica que solidifica el aprendizaje.