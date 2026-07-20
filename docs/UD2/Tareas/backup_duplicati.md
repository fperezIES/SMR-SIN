---
icon: material/file-edit
---


## Práctica: Configuración y Uso de Duplicati con TrueNAS como Destino de Copias de Seguridad

### Objetivos:
1. Configurar **Duplicati** para realizar copias de seguridad automáticas de archivos locales.
2. Utilizar **TrueNAS** como destino remoto para almacenar las copias de seguridad mediante **SMB** o **WebDAV**.
3. Aplicar técnicas de cifrado y deduplicación para asegurar y optimizar las copias de seguridad.
4. Configurar políticas de retención para limitar el espacio de almacenamiento utilizado en **TrueNAS**.


### ¿Qué es WebDAV?
**WebDAV** (Web Distributed Authoring and Versioning) es una extensión del protocolo HTTP que permite a los usuarios gestionar archivos en un servidor remoto a través de la web. A diferencia de protocolos más tradicionales como **FTP**, **WebDAV** proporciona una experiencia de uso más moderna y flexible, ya que permite manipular archivos de manera colaborativa (crear, mover, eliminar) mediante interfaces web compatibles con la mayoría de navegadores y sistemas operativos. Es recomendable en escenarios donde se necesitan funcionalidades avanzadas de colaboración en línea y gestión de archivos en entornos distribuidos, como oficinas con múltiples sedes o usuarios que necesitan acceso remoto a recursos compartidos.

### Escenarios recomendados para WebDAV:
- Empresas que requieren acceso a archivos desde ubicaciones remotas mediante un navegador o aplicaciones que soporten WebDAV.
- Entornos donde se necesita una gestión sencilla y accesible de archivos a través de la web, sin instalar software adicional en los clientes.
- Situaciones en las que se busque aprovechar el cifrado y las características de seguridad del protocolo HTTPS para transferir archivos de forma segura.

### Pasos:

### Parte 1: Preparación del entorno

1. **Acceso a TrueNAS**:
   - Asegúrate de que el servidor **TrueNAS** esté encendido y correctamente configurado.
   - Verifica que tienes una carpeta compartida (dataset) en **TrueNAS** accesible mediante **SMB** o **WebDAV**. Para configurar **WebDAV**:
     - Habilita el servicio **WebDAV** en el panel de control de **TrueNAS**.
     - Crea un recurso compartido y asigna permisos a los usuarios que vayan a acceder a este recurso.
     - Verifica que puedes acceder al recurso compartido utilizando la dirección de **WebDAV** (por ejemplo, `http://192.168.x.x:8080/dav` o `https://192.168.x.x/dav` si usas HTTPS).

2. **Instalación de Duplicati** (si no está instalado):
   - Descarga **Duplicati** desde [https://www.duplicati.com/download](https://www.duplicati.com/download) e instálalo en tu equipo.
   - Inicia **Duplicati** y accede a la interfaz web a través del navegador (por defecto, se abrirá en `localhost:8200`).

### Parte 2: Configuración de Duplicati

1. **Crear una nueva tarea de copia de seguridad**:
   - En la interfaz de **Duplicati**, haz clic en **Agregar nueva copia de seguridad**.
   - Selecciona **Configuración avanzada** para acceder a opciones más detalladas.

2. **Paso 1: Configurar el cifrado**:
   - Elige una contraseña segura para cifrar las copias de seguridad. Esto garantiza que los datos estén protegidos contra accesos no autorizados.
   - Guarda la contraseña en un lugar seguro, ya que es necesaria para restaurar las copias.

3. **Paso 2: Selección de origen de datos**:
   - Elige una carpeta o varios archivos en tu equipo local para respaldar. Puedes usar una carpeta de prueba (por ejemplo, `C:\Documentos\Prueba` o `~/Documentos/Prueba` en Linux).
   - Incluye archivos de diferentes tipos (documentos, imágenes, etc.) para demostrar cómo **Duplicati** maneja diferentes formatos de datos.

4. **Paso 3: Seleccionar el destino**:
   - Elige **SMB** o **WebDAV** como protocolo de almacenamiento, según cómo hayas configurado tu **TrueNAS**.
   - Introduce la dirección de red de **TrueNAS** (por ejemplo, `https://192.168.x.x/dav` para **WebDAV**), junto con las credenciales de acceso al recurso compartido.
   - Verifica la conexión para asegurarte de que **Duplicati** puede acceder al destino configurado.

5. **Paso 4: Configuración avanzada**:
   - **Deduplicación**: Activa la opción de deduplicación para evitar almacenar múltiples copias de los mismos archivos, optimizando el uso del espacio de almacenamiento.
   - **Frecuencia**: Programa una copia de seguridad diaria automática (elige una hora adecuada).
   - **Políticas de retención**: Configura una política de retención que permita eliminar las copias de seguridad más antiguas y conservar solo las recientes. Esto es importante para limitar el espacio ocupado en **TrueNAS**. Ejemplo de política de retención:
     - "Conservar todas las copias de seguridad durante 7 días".
     - "Conservar solo una copia semanal durante el último mes".
     - "Conservar una copia mensual durante los últimos 6 meses".

6. **Paso 5: Guardar y ejecutar la tarea**:
   - Guarda la configuración de la copia de seguridad.
   - Ejecuta la primera copia de seguridad manualmente para asegurarte de que todo funcione correctamente.

### Parte 3: Restauración de archivos

1. **Simulación de pérdida de datos**:
   - Elimina o mueve algunos archivos de la carpeta de prueba para simular una pérdida de datos.
   
2. **Restauración desde Duplicati**:
   - En la interfaz de **Duplicati**, ve a la sección **Restaurar**.
   - Elige la copia de seguridad más reciente almacenada en **TrueNAS**.
   - Selecciona los archivos que eliminaste previamente y restaúralos en su ubicación original.

### Parte 4: Análisis de resultados

1. **Verificación de la copia de seguridad**:
   - Comprueba los archivos restaurados y asegúrate de que coincidan con los originales.
   - Revisa el log de **Duplicati** para confirmar que la copia de seguridad y restauración se realizaron sin errores.

2. **Evaluación del espacio utilizado en TrueNAS**:
   - Accede a **TrueNAS** y verifica cuánto espacio ocupan las copias de seguridad en el servidor.
   - Observa el efecto de la deduplicación en la optimización del espacio.

### Parte 5: Preguntas de reflexión

1. ¿Por qué es importante utilizar cifrado en las copias de seguridad? ¿Qué impacto tendría la pérdida de la contraseña de cifrado?
2. ¿Cómo puede la deduplicación mejorar el rendimiento y la gestión del almacenamiento en una empresa?
3. ¿Qué ventajas tiene usar un servidor NAS como destino de las copias de seguridad frente a otros tipos de almacenamiento (local o en la nube)?
4. ¿Qué medidas tomarías para garantizar que las copias de seguridad sean consistentes y siempre estén disponibles en caso de un desastre?

### Entregable:

Debes entregar una memoria en formato pdf que contenga la siguiete información:

- Capturas de pantalla de la configuración de la copia de seguridad en **Duplicati**.
- Informe detallado de los pasos seguidos y los resultados obtenidos (incluyendo restauración y análisis de deduplicación).
- Respuesta a las preguntas de reflexión.

