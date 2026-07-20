---
icon: material/file-edit
---


### Práctica: Configuración de RAID 5 en Windows 11 Virtualizado con VirtualBox

#### Objetivos:
El objetivo de esta práctica es aprender a configurar un sistema de RAID 5 en un entorno Windows 11 virtualizado mediante el uso de VirtualBox. Los estudiantes adquirirán una comprensión práctica de cómo implementar RAID 5 para mejorar la tolerancia a fallos y el rendimiento en sistemas de almacenamiento. Al finalizar, deberás ser capaz de:

1. Crear y configurar una máquina virtual de Windows 11 en VirtualBox.
2. Añadir varios discos virtuales a la máquina para simular un entorno de RAID 5.
3. Configurar RAID 5 mediante la herramienta de administración de discos de Windows 11.
4. Comprender las ventajas y desventajas del RAID 5.

#### Requisitos previos:
- Conocimientos básicos sobre máquinas virtuales y VirtualBox.
- Familiaridad con la administración de discos en Windows.
- **VirtualBox** instalado en tu equipo.
- Imagen ISO de **Windows 11** disponible, puedes conseguirla del programa Azure Dev Tools for Teaching.

---

#### Parte 1: Configuración del Entorno de Virtualización

1. **Crear una nueva máquina virtual con Windows 11**:
   - Abre **VirtualBox** y selecciona la opción **Nueva** para crear una nueva máquina virtual.
   - Asigna un nombre a la máquina (por ejemplo, **Windows 11 RAID**), elige la versión de Windows 11 (64 bits), y selecciona la cantidad de RAM recomendada (mínimo 4 GB).
   - Selecciona la opción de **crear un disco duro virtual** para almacenar el sistema operativo. Elige **VHD (Virtual Hard Disk)** y un tamaño adecuado (40 GB o más) en **almacenamiento dinámicamente asignado**.

2. **Instalar Windows 11 en la máquina virtual**:
   - Asocia la **ISO de Windows 11** al lector de CD/DVD virtual de VirtualBox.
   - Inicia la máquina virtual y sigue el proceso de instalación de Windows 11.

---

#### Parte 2: Añadir Discos Virtuales para RAID 5

3. **Añadir discos virtuales adicionales**:
   - Apaga la máquina virtual una vez finalizada la instalación.
   - En el menú de configuración de VirtualBox, selecciona la máquina virtual de Windows 11 y haz clic en **Almacenamiento**.
   - Añade **tres discos duros virtuales** adicionales (cada uno de 10 GB o más, según disponibilidad) para simular los discos que usarás en el RAID 5.
   - Guarda la configuración y reinicia la máquina virtual.

---

#### Parte 3: Configuración del RAID 5

4. **Iniciar la máquina y acceder a la herramienta de Administración de Discos**:
   - Una vez dentro de Windows 11, abre el menú de **Inicio**, busca **Administración de discos** y ábrelo.
   - Verás los tres discos adicionales que has añadido, aparecerán como discos no asignados.

%% TODO: Espacios de almacenamiento Windows 10 %%

5. **Convertir los discos a dinámicos**:
   - Haz clic derecho en cada uno de los discos adicionales y selecciona **Convertir a disco dinámico**.

6. **Crear el RAID 5**:
   - Haz clic derecho sobre uno de los discos dinámicos y selecciona **Nuevo volumen distribuido**.
   - Selecciona los otros discos dinámicos para incluirlos en el RAID 5.
   - Asigna una letra de unidad, selecciona el sistema de archivos **NTFS**, y configura el tamaño del volumen.
   - Completa el asistente para finalizar la creación del RAID 5.

---

#### Parte 4: Validación y Prueba del RAID 5

7. **Verificar la creación del RAID 5**:
   - En la ventana de administración de discos, deberías ver un único volumen RAID 5 con el espacio total asignado correctamente (sumatoria de los discos menos uno para paridad).
   - Asegúrate de que el nuevo volumen es accesible en el **Explorador de Archivos** y que puedes escribir y leer datos desde él.

8. **Simulación de fallo en un disco**:
   - Apaga la máquina virtual y elimina uno de los discos virtuales en la configuración de VirtualBox.
   - Reinicia la máquina y observa cómo Windows marca el RAID como degradado. Verifica que los datos aún son accesibles.

9. **Reconstrucción del RAID**:
   - Apaga la máquina nuevamente y vuelve a agregar un disco virtual del mismo tamaño que el anterior.
   - Reinicia Windows y utiliza la administración de discos para reconstruir el RAID 5.

---

#### Parte 5: Informe final

10. **Informe de resultados**:
   - Al finalizar la práctica, redacta un informe en el que expliques los pasos seguidos para configurar el RAID 5, así como las ventajas y desventajas que observaste en su funcionamiento. Describe también el comportamiento del RAID durante la simulación de fallo y la reconstrucción.

# Bibliografía

[RAID en Windows 11](https://support.microsoft.com/es-es/windows/espacios-de-almacenamiento-en-windows-b6c8b540-b8d8-fb8a-e7ab-4a75ba11f9f2)