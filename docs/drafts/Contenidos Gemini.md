
Para estructurar el temario del módulo de **Seguridad Informática (0226)** del ciclo de SMR adaptado a un escenario de **20 semanas lectivas reales (a 4 horas semanales, 80 horas en total)**, la estrategia pedagógica más efectiva es seguir una progresión de "lo físico y conceptual" a "lo lógico y en red".

Esta distribución equilibra la carga teórica con la eminentemente práctica que requiere el perfil técnico de SMR, asignando más tiempo a los bloques de almacenamiento, copias de seguridad y seguridad en redes, que son los de mayor complejidad técnica y volumen de contenidos.

## Planificación General y Temporalización

| **Unidad de Trabajo (UT)** | **Título**                                        | **Semanas** | **Horas** | **RA asociados** |
| -------------------------- | ------------------------------------------------- | ----------- | --------- | ---------------- |
| **UT 1**                   | Introducción a la Seguridad y Marco Legal         | 2           | 8 h       | RA 5             |
| **UT 2**                   | Seguridad Física y Pasiva en Sistemas             | 3           | 12 h      | RA 1             |
| **UT 3**                   | Almacenamiento Seguro, Respaldo y Criptografía    | 5           | 20 h      | RA 2             |
| **UT 4**                   | Seguridad Activa, Malware y Recuperación de Datos | 4           | 16 h      | RA 3             |
| **UT 5**                   | Seguridad en Redes, Privacidad y Comunicaciones   | 6           | 24 h      | RA 4             |
| **TOTAL**                  |                                                   | **20**      | **80 h**  |                  |

## Desarrollo del Índice de Contenidos por Unidad de Trabajo

### UT 1: Introducción a la Seguridad Informática y Marco Legal

**Duración:** 2 semanas (8 horas)

**Resultados de Aprendizaje:** RA 5

**Criterios de Evaluación:** 5a, 5b, 5c, 5d, 5e, 5f

- **1.1. Conceptos generales de seguridad de la información**
    
    - La triada de la seguridad: Confidencialidad, Integridad y Disponibilidad (CID / CIA).
        
    - Vulnerabilidad, amenaza, riesgo e impacto.
        
    - Diferencias esenciales entre seguridad física y seguridad lógica.
        
- **1.2. Legislación y normativa sobre protección de datos**
    
    - El Reglamento General de Protección de Datos (RGPD / GDPR) y la LOPDGDD.
        
    - Principios del tratamiento de datos de carácter personal y necesidad de control de acceso.
        
    - Figuras legales: Responsable del tratamiento, Encargado del tratamiento y Delegado de Protección de Datos (DPO).
        
    - Derechos de los ciudadanos: Acceso, rectificación, supresión (olvido), oposición, limitación y portabilidad.
        
- **1.3. Legislación sobre servicios de la sociedad de la información**
    
    - Ley de Servicios de la Sociedad de la Información y de Comercio Electrónico (LSSI-CE).
        
    - Normativa sobre comunicaciones comerciales y correo electrónico (spam).
        
- **1.4. Normas sobre gestión de seguridad de la información**
    
    - Introducción a los Sistemas de Gestión de la Seguridad de la Información (SGSI).
        
    - Estándares de la serie ISO/IEC 27000 (especialmente ISO 27001 e ISO 27002).
        
    - Repercusiones legales, económicas y reputacionales del incumplimiento normativo.
        
- _Enfoque Práctico:_ Análisis de casos reales de sanciones por brechas de privacidad; auditoría básica de una web de comercio electrónico para verificar el cumplimiento de la LSSI-CE y RGPD; redacción de un documento básico de consentimiento y política de privacidad.
    

### UT 2: Seguridad Física y Pasiva en Sistemas

**Duración:** 3 semanas (12 horas)

**Resultados de Aprendizaje:** RA 1

**Criterios de Evaluación:** 1a, 1b, 1c, 1d, 1e, 1f, 1g, 1h, 1i

- **2.1. Protección física de equipos y servidores**
    
    - Ubicación física segura: control de accesos a CPDs y salas de servidores (llaves, tarjetas, biometría).
        
    - Condiciones ambientales: climatización, control de temperatura y humedad, prevención de incendios y cableado estructurado seguro.
        
    - Sistemas de seguridad antirrobo y chasis con cerradura (Kensington, precintos).
        
- **2.2. Continuidad de suministro eléctrico**
    
    - Anomalías eléctricas: cortes, sobretensiones, caídas de tensión y picos.
        
    - Sistemas de Alimentación Ininterrumpida (SAI / UPS): tipos (Offline, Line-Interactive, Online) y características básicas (VA, autonomía).
        
    - Cálculo de dimensionamiento y selección de puntos de aplicación en la infraestructura.
        
    - Instalación, verificación del funcionamiento y monitorización mediante software de gestión de SAI.
        
- **2.3. Políticas de acceso y seguridad en el equipo**
    
    - Seguridad en arranque: contraseñas de BIOS/UEFI, desactivación de arranque externo y Secure Boot.
        
    - Políticas de contraseñas: longitud, complejidad, caducidad y bloqueo por intentos fallidos en sistemas operativos.
        
    - Sistemas biométricos: huella dactilar, reconocimiento facial/retina. Ventajas, limitaciones y casos de uso.
        
    - Introducción a las Listas de Control de Acceso (ACLs) y principio de mínimo privilegio.
        
- _Enfoque Práctico:_ Dimensionamiento de un SAI para un aula o rack de servidores; configuración de software de apagado automático con SAI; securización del firmware (UEFI) y aplicación de directivas locales de políticas de contraseñas y bloqueo de cuentas en Windows/Linux.
    

### UT 3: Almacenamiento Seguro, Respaldo y Criptografía

**Duración:** 5 semanas (20 horas)

**Resultados de Aprendizaje:** RA 2 (parcialmente vinculado al 1g del RA 1 en ACLs de ficheros)

**Criterios de Evaluación:** 2a, 2b, 2c, 2d, 2e, 2f, 2g, 2h, 2i, 2j

- **3.1. Fundamentos y políticas de almacenamiento**
    
    - Factores inherentes al almacenamiento: rendimiento (IOPS/tasa), disponibilidad y accesibilidad.
        
    - Interpretación de documentación técnica y políticas de retención de datos.
        
- **3.2. Sistemas de almacenamiento redundante, distribuido y en red**
    
    - Almacenamiento local (DAS) vs. Almacenamiento en red (NAS y SAN).
        
    - Tecnologías de redundancia: Niveles RAID por software y hardware (RAID 0, 1, 5, 6, 10).
        
    - Medios de almacenamiento remotos (Nube privada/pública, FTP, SFTP) y extraíbles (discos externos, cintas LTO, memorias USB).
        
- **3.3. Criptografía aplicada al almacenamiento**
    
    - Conceptos básicos: cifrado simétrico y asimétrico, funciones hash.
        
    - Cifrado de discos completos y volúmenes (BitLocker, LUKS, VeraCrypt).
        
    - Cifrado de dispositivos extraíbles y carpetas confidenciales.
        
- **3.4. Copias de seguridad e imágenes de respaldo**
    
    - Estrategias de copia de seguridad: completa, incremental y diferencial.
        
    - Diseño de planes de respaldo: la regla 3-2-1 del backup.
        
    - Esquemas y frecuencias de rotación (p. ej., Abuelo-Padre-Hijo / GFS).
        
    - Herramientas de automatización de copias de seguridad en Windows y Linux (rsync, Cobian, Veeam, herramientas nativas).
        
    - Creación, clonación y restauración de imágenes de respaldo (bare-metal recovery) con herramientas especializadas (Clonezilla, Acronis, Macrium).
        
- _Enfoque Práctico:_ Montaje y simulación de fallos en sistemas RAID (con máquinas virtuales o discos físicos); cifrado de pendrives y particiones del sistema con VeraCrypt/BitLocker; diseño de una tarea programada de backup incremental rotativo; creación de una imagen del sistema operativo y restauración exitosa sobre una máquina limpia.
    

### UT 4: Seguridad Activa, Malware y Recuperación de Datos

**Duración:** 4 semanas (16 horas)

**Resultados de Aprendizaje:** RA 3

**Criterios de Evaluación:** 3a, 3b, 3c, 3d, 3e, 3f

- **4.1. Software malicioso (Malware)**
    
    - Clasificación del malware: Virus, Gusanos, Troyanos, Ransomware, Spyware, Adware, Rootkits y Botnets.
        
    - Vectores de infección habituales y técnicas de ocultación.
        
    - Herramientas de protección y desinfección: Antivirus tradicionales, soluciones EDR, antimalware especializado y herramientas de escaneo online/bootables (Rescue Disks).
        
    - Instalación, prueba y actualización de firmas en software antimalware.
        
- **4.2. Mantenimiento, actualizaciones y verificación del software**
    
    - Gestión de parches y actualizaciones periódicas (Windows Update / WSUS, repositorios Linux).
        
    - Verificación de origen y autenticidad en la instalación de aplicaciones: comprobación de sumas hash (MD5, SHA-256) y firmas digitales de ejecutables.
        
    - Control de cuentas de usuario (UAC) y ejecución en entornos aislados (Sandbox / Máquinas virtuales).
        
- **4.3. Planes de contingencia y respuesta ante incidentes**
    
    - Concepto de plan de contingencia y continuidad de negocio.
        
    - Protocolos de actuación ante una infección (aislamiento de red, contención, análisis y desinfección).
        
- **4.4. Recuperación de datos**
    
    - Causas de pérdida de datos: fallos lógicos (borrado accidental, formateo, corrupción) vs. fallos físicos.
        
    - Estructura de sistemas de archivos y cómo funciona el borrado de ficheros.
        
    - Técnicas y herramientas para la recuperación de datos borrados o particiones dañadas (TestDisk, PhotoRec, Recuva).
        
- _Enfoque Práctico:_ Análisis en entorno aislado (máquina virtual congelada) del comportamiento de malware simple o archivos EICAR; escaneo y desinfección usando discos de rescate de arranque; comprobación de integridad de descargas mediante hashes; recuperación práctica de archivos eliminados permanentemente (Shift+Supr) en un pendrive formateado usando PhotoRec o TestDisk.
    

### UT 5: Seguridad en Redes, Privacidad y Comunicaciones

**Duración:** 6 semanas (24 horas)

**Resultados de Aprendizaje:** RA 4

**Criterios de Evaluación:** 4a, 4b, 4c, 4d, 4e, 4f, 4g, 4h

- **5.1. Control y monitorización de servicios de red**
    
    - Inventariado de sistemas y control de servicios de red activos (puertos, protocolos).
        
    - Herramientas de escaneo de red y puertos (Nmap, Netstat).
        
    - Riesgos de la monitorización no autorizada en redes cableadas (sniffing) y medidas de protección (uso de switches gestionados, tablas CAM, protocolos seguros).
        
- **5.2. Ingeniería social y fraudes informáticos**
    
    - Técnicas de ingeniería social: Phishing, Spear Phishing, Smishing, Vishing, Baiting y Shoulder Surfing.
        
    - Robo de información y credenciales en línea.
        
    - Minimización del tráfico de correo no deseado (Spam) y publicidad maliciosa (Adblockers, filtros antispam, listas negras/blancas).
        
- **5.3. Seguridad en comunicaciones y redes inalámbricas**
    
    - Vulnerabilidades inherentes a las redes Wi-Fi.
        
    - Propiedades de seguridad y evolución de protocolos inalámbricos: WEP (obsoleto), WPA/WPA2 (PSK y Enterprise) y WPA3.
        
    - Buenas prácticas en securización Wi-Fi: ocultación de SSID, filtrado MAC (limitaciones), aislamiento de clientes, redes de invitados y control de potencia.
        
- **5.4. Identificación digital, certificados y criptografía asimétrica**
    
    - Infraestructura de Clave Pública (PKI): Autoridades de Certificación (CA) y registro.
        
    - Certificados digitales: estructura (X.509), obtención, instalación y exportación (ej. FNMT o DNIe en España).
        
    - Firma electrónica: funcionamiento, validez legal y aplicaciones prácticas (firma de documentos PDF, correos seguros con S/MIME o PGP).
        
    - Métodos de transmisión segura de la información: HTTPS, SSH, VPN (conceptos básicos y protocolos fundamentales como OpenVPN o WireGuard).
        
- **5.5. Cortafuegos (Firewalls) en sistemas y servidores**
    
    - Concepto, funcionamiento y tipos de cortafuegos (filtrado de paquetes, estado, capa de aplicación).
        
    - Zonas de red: LAN, WAN y DMZ (Zona Desmilitarizada).
        
    - Instalación, configuración y políticas de filtrado en equipos cliente (Windows Defender Firewall con seguridad avanzada, UFW / iptables en Linux).
        
    - Configuración básica de reglas en un firewall de red o servidor perimetral (apertura y redirección de puertos, bloqueo de ICMP/servicios).
        
- _Enfoque Práctico:_ Auditoría de puertos locales y de red con Nmap; captura y análisis pedagógico de tráfico en red local con Wireshark (comparando HTTP vs HTTPS o Telnet vs SSH); configuración de la seguridad de un punto de acceso Wi-Fi simulado o real; obtención de un certificado digital de pruebas, firma y validación de documentos y correos electrónicos; creación de un juego de reglas estrictas de entrada y salida en el cortafuegos del sistema operativo para permitir únicamente servicios esenciales.
    

## Estrategia Metodológica y Uso de las Sesiones Lectivas

Dada la asignación de **4 horas semanales**, un modelo eficaz para estructurar cada semana de clase es la división en bloques bloques teórico-prácticos continuos:

1. **Primera sesión semanal (2 horas):** Introducción conceptual, explicación de los fundamentos técnicos, demostración guiada por parte del docente (mediante proyector y entornos virtualizados) y resolución de dudas iniciales.
    
2. **Segunda sesión semanal (2 horas):** Taller de laboratorio / Práctica individual o por parejas. Los alumnos replican los entornos de seguridad en sus equipos (preferiblemente utilizando hipervisores de virtualización como VirtualBox o VMware para no comprometer el aula) y elaboran un **cuaderno de prácticas / informe técnico**, el cual servirá como herramienta principal de evaluación de los criterios de saber hacer.