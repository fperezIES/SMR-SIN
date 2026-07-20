---
icon: material/file-edit
---




# Ejercicio: Comprobación del estado SMART de las unidades de disco

---

**Objetivo:**

Aprender a verificar el estado SMART (Self-Monitoring, Analysis, and Reporting Technology) de las unidades de disco para anticipar posibles fallos y garantizar la integridad de los datos.

**Introducción:**

SMART es una tecnología incorporada en los discos duros y unidades SSD que permite monitorizar diversos indicadores de fiabilidad y rendimiento. Al analizar estos datos, es posible predecir fallos y tomar medidas preventivas antes de que ocurran pérdidas de datos.

---
## **Para usuarios de Windows:**

#### **1. Utilizando el Símbolo del sistema:**

a) Abre el **Símbolo del sistema** como administrador:
- Haz clic derecho en el menú Inicio y selecciona "Símbolo del sistema (Administrador)".

b) Ejecuta el siguiente comando:
```
wmic diskdrive get status
```

c) Observa el estado de cada unidad. Un resultado "OK" indica que no se han detectado problemas.

#### **2. Utilizando PowerShell:**

a) Abre **PowerShell** como administrador:
- Haz clic derecho en el menú Inicio y selecciona "Windows PowerShell (Administrador)".

b) Ejecuta el siguiente comando:

```bash
Get-WmiObject -namespace root\wmi -class MSStorageDriver_FailurePredictStatus
```

c) Revisa el valor de **PredictFailure** para cada unidad:
- **False:** No se predicen fallos.
- **True:** Se predicen posibles fallos; se recomienda hacer copias de seguridad y considerar el reemplazo de la unidad.

#### **3. Utilizando CrystalDiskInfo:**

a) Descarga e instala **CrystalDiskInfo** desde su [sitio oficial](https://crystalmark.info/en/software/crystaldiskinfo/).

b) Ejecuta el programa y observa el estado de salud de cada disco:
- **Buen estado:** Indica que la unidad funciona correctamente.
- **Precaución o Malo:** Señala problemas que requieren atención inmediata.

c) Analiza los detalles de los atributos SMART para identificar parámetros críticos.

---

## **Para usuarios de Linux:**

#### **1. Instalación de smartmontools:**

a) Abre una terminal.

b) Instala el paquete *smartmontools*:
- **Debian/Ubuntu:**

```
  sudo apt-get install smartmontools
```
- **Fedora/CentOS:**

```
  sudo dnf install smartmontools
```

#### **2. Identificación de las unidades de disco:**

a) Ejecuta:

```
sudo fdisk -l
```

b) Anota los identificadores de las unidades (ejemplo: `/dev/sda`).

#### **3. Verificación del estado SMART:**

a) Activa el soporte SMART en la unidad (si no lo está):

```
sudo smartctl -s on /dev/sdX
```

*(Reemplaza `/dev/sdX` por el identificador de tu unidad).*

b) Ejecuta un test rápido:

```
sudo smartctl -H /dev/sdX
```

c) Observa el resultado:
- **PASSED:** La unidad está en buen estado.
- **FAILED:** Se han detectado problemas.

#### **4. Obtención de un informe detallado:**

a) Ejecuta:

```
sudo smartctl -a /dev/sdX
```

b) Analiza los atributos SMART para detectar valores anómalos.

---

**Análisis de Resultados:**

- **Interpretación de Atributos SMART Clave:**
  - **Reallocated Sector Count:** Indica sectores reasignados; valores altos pueden ser preocupantes.
  - **Current Pending Sector Count:** Sectores pendientes de reasignación.
  - **Offline Uncorrectable Sector Count:** Sectores que no pueden ser corregidos; pueden provocar errores de lectura/escritura.

- **Acciones Recomendadas:**
  - Si se detectan valores anormales, realizar copias de seguridad inmediatas.
  - Considerar el reemplazo de la unidad afectada.

---

**Conclusiones:**

- La monitorización regular del estado SMART permite anticipar fallos y proteger los datos.
- Herramientas como `smartctl` y *CrystalDiskInfo* facilitan la obtención y análisis de esta información.
- Es fundamental interpretar correctamente los indicadores para tomar decisiones informadas.


En macOS, puedes comprobar las estadísticas **SMART** (Self-Monitoring, Analysis, and Reporting Technology) de tus discos duros o unidades SSD directamente desde la aplicación de terminal. Estas estadísticas te ofrecen información sobre la salud y el estado de las unidades, lo que puede ayudar a prevenir fallos antes de que ocurran.

# Para usuarios de macOS:

#### 1. **Usar la Utilidad de Discos**
La **Utilidad de Discos** es la opción gráfica más fácil para comprobar el estado SMART en macOS.

1. Abre **Utilidad de Discos** (puedes encontrarla en *Aplicaciones > Utilidades* o buscándola en Spotlight).
2. Selecciona el disco duro o SSD que quieras comprobar.
3. En la parte inferior de la ventana, busca el campo **SMART Status** (Estado SMART), que indicará si el disco está "Verificado" o si está fallando.

Aunque esta herramienta es útil, no ofrece detalles avanzados. Para obtener un análisis más profundo, puedes usar la terminal.



#### 2. **Usar el comando `diskutil` en la terminal**
El comando `diskutil` en macOS te permite verificar el estado SMART de manera rápida desde la terminal.

1. Abre la **Terminal** (puedes encontrarla en *Aplicaciones > Utilidades* o buscándola en Spotlight).
2. Ejecuta el siguiente comando para listar todos los discos conectados:

   ```bash
   diskutil list
   ```

   Esto te mostrará una lista de discos conectados y sus identificadores (por ejemplo, `/dev/disk0`, `/dev/disk1`).

3. Para comprobar el estado SMART de una unidad específica, utiliza el siguiente comando:

   ```bash
   diskutil info /dev/diskX
   ```

   (Reemplaza `/dev/diskX` por el identificador del disco que quieras comprobar).

4. Busca el campo que dice **SMART Status**. Las respuestas típicas pueden ser:
   - **SMART Status: Verified**: Indica que el disco está en buen estado.
   - **SMART Status: Failing**: Indica que el disco puede fallar pronto y debería ser reemplazado.



#### 3. **Usar herramientas de terceros**

Si necesitas una vista más detallada de las estadísticas SMART, puedes usar aplicaciones de terceros. Algunas de las más populares son:

- [**DriveDx**](https://binaryfruit.com/drivedx): Una aplicación de pago que ofrece un análisis exhaustivo de las estadísticas SMART de tu disco. Proporciona gráficos y advertencias avanzadas.
- [**SMART Utility**](https://www.volitans-software.com/apps/smart-utility/): Otra herramienta que ofrece información detallada sobre el estado de tu disco, con informes más completos que la utilidad de discos por defecto de macOS.

Ambas aplicaciones son fáciles de usar y proporcionan un análisis mucho más detallado que las opciones nativas de macOS.

---

### Conclusión:
Para un chequeo rápido, puedes utilizar la **Utilidad de Discos** o el comando `diskutil` desde la terminal. Si quieres un análisis más profundo y detallado sobre las estadísticas SMART de tu disco, las herramientas de terceros como **DriveDx** o **SMART Utility** son muy recomendables.


---

**Cuestionario:**

1. **¿Qué es SMART y cuál es su función principal en las unidades de disco?**

2. **¿Cuál es la diferencia entre un test rápido y un informe detallado en la comprobación SMART?**

3. **Menciona al menos tres atributos SMART críticos y explica su importancia.**

4. **¿Qué medidas debes tomar si tu unidad de disco muestra fallos en los tests SMART?**

5. **¿Por qué es importante activar el soporte SMART en algunas unidades antes de realizar las pruebas?**

---

**Bibliografía y Recursos Adicionales:**

- **Documentación de smartmontools:** [www.smartmontools.org](https://www.smartmontools.org/)
- **CrystalDiskInfo:** [crystalmark.info](https://crystalmark.info/en/software/crystaldiskinfo/)
- **Información sobre SMART:** [Wikipedia - S.M.A.R.T.](https://es.wikipedia.org/wiki/S.M.A.R.T.)

---

**Nota:** Siempre maneja con cuidado los comandos y herramientas que interactúan a bajo nivel con el hardware para evitar daños o pérdidas de datos.

---


