
## **UD2 - Seguridad pasiva**

### 1. **Seguridad física en el CPD**
   - **Protección física del centro de datos**: Controles de acceso, cámaras de vigilancia, barreras físicas
   - **Diseño del CPD**: Ubicación, redundancia, refrigeración y sistemas de climatización
   - **Control ambiental**: Sistemas de detección y extinción de incendios, monitorización de temperatura y humedad
   - **Seguridad ante desastres naturales**: Planificación de contingencias ante incendios, inundaciones, terremotos
   - **Redundancia y alta disponibilidad de infraestructuras**: UPS, generadores de respaldo, conexiones de red redundantes

### 2. **Almacenamiento de la información**
   - **Tipos de almacenamiento**:
     - **Almacenamiento local**: Discos duros, SSDs, almacenamiento conectado directamente (DAS)
     - **Almacenamiento en red**: NAS (Network Attached Storage), SAN (Storage Area Network)
     - **Almacenamiento en la nube**: Servicios de almacenamiento distribuido
   - **Seguridad en el almacenamiento de la información**:
     - **Cifrado en reposo**: AES, BitLocker, LUKS
     - **Control de acceso a los datos**: Políticas de permisos, autenticación y acceso basado en roles (RBAC)
   - **Clasificación de la información**: Datos sensibles, confidenciales y públicos, gestión del ciclo de vida de los datos
   - **Cumplimiento normativo**: GDPR y otros marcos legales sobre el almacenamiento de información

### 3. **Copias de seguridad**
   - **Tipos de copias de seguridad**:
     - **Backup completo**: Ventajas y desventajas
     - **Backup incremental**: Optimización de almacenamiento y tiempos de copia
     - **Backup diferencial**: Comparación con el backup incremental
   - **Métodos y herramientas de backup**:
     - **Backup local y en red**: Servidores dedicados, software de backup
     - **Backup en la nube**: Servicios de copia en la nube (Azure, AWS, Google Cloud)
   - **Políticas de copias de seguridad**:
     - **Estrategia de copias 3-2-1**: Tres copias, dos tipos de almacenamiento, una fuera del sitio
     - **Frecuencia de backups**: Diarios, semanales, mensuales
     - **Restauración de copias de seguridad**: Verificación periódica y pruebas de recuperación
   - **Cifrado de copias de seguridad**: Importancia del cifrado en los medios de backup para proteger contra el acceso no autorizado

### 4. **Borrado seguro y recuperación de datos**
   - **Técnicas de borrado seguro**:
     - **Sobrescritura de datos**: Herramientas para el borrado seguro (DBAN, SDelete)
     - **Borrado criptográfico**: Destrucción de claves de cifrado para datos cifrados
     - **Destrucción física**: Trituración de discos duros, desmagnetización (degaussing)
   - **Normativas y estándares de borrado seguro**:
     - **Normas internacionales**: DoD 5220.22-M, NIST 800-88
     - **Cumplimiento normativo**: GDPR y políticas de borrado de datos
   - **Recuperación de datos**:
     - **Software de recuperación de datos**: Herramientas para recuperar datos perdidos o corruptos
     - **Recuperación física de discos**: Laboratorios especializados en recuperar datos de medios dañados
   - **Problemas de privacidad y seguridad en la recuperación de datos**: Riesgos asociados a la recuperación de datos no autorizada

---

### **Justificación de las adiciones:**

1. **Seguridad física en el CPD**: Se profundiza en las medidas de control físico y ambiental para proteger los centros de datos, además de considerar la planificación ante desastres naturales, lo cual es fundamental para evitar interrupciones graves de servicio.

2. **Almacenamiento de la información**: Se añaden detalles sobre los diferentes tipos de almacenamiento (local, en red y en la nube) y las técnicas de protección, como el cifrado y control de acceso, que son esenciales para garantizar la seguridad de los datos.

3. **Copias de seguridad**: Se detallan los diferentes tipos de backups (completo, incremental y diferencial) y se introduce el concepto de la estrategia **3-2-1**, un estándar de la industria para garantizar la disponibilidad de las copias. Además, se incluye la importancia del cifrado en las copias de seguridad.

4. **Borrado seguro y recuperación de datos**: Se profundiza en las técnicas de **borrado seguro** para garantizar que los datos eliminados no puedan ser recuperados, así como en las herramientas y normativas relacionadas. También se aborda la **recuperación de datos**, algo relevante para gestionar incidentes de pérdida o corrupción de información.
