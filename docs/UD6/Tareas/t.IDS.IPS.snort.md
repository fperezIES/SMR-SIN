---
icon: material/file-edit
---


### Práctica: **Instalación, Configuración y Prueba de Snort en pfSense**

#### Objetivos:

1. Aprender a instalar Snort en un firewall basado en pfSense.
2. Configurar Snort para monitorear y proteger una red.
3. Verificar su funcionalidad mediante pruebas prácticas.

#### Recursos necesarios:

- Un servidor o máquina virtual con pfSense instalado y configurado.
- Acceso a Internet desde pfSense para descargar paquetes.
- Una red de prueba con al menos una máquina cliente conectada al firewall.
- Vídeo tutorial de referencia: [Instalación y configuración de Snort en pfSense](https://www.youtube.com/watch?v=2q_g9GgkvWA).

---

### Parte 1: Instalación de Snort

1. Accede a la interfaz web de pfSense desde tu navegador.
2. Navega a **System > Package Manager**.
3. En la pestaña **Available Packages**, busca "Snort".
4. Haz clic en **Install** junto al paquete Snort y espera a que la instalación se complete.
5. Una vez instalado, ve a **Services > Snort**.

---

### Parte 2: Configuración inicial de Snort

1. **Configurar las interfaces**:
    
    - En la pestaña **Interfaces**, selecciona la interfaz de red en la que deseas habilitar Snort (por ejemplo, WAN o LAN).
    - Activa la casilla **Enable** para habilitar Snort en esa interfaz.
    - Selecciona un tipo de modo: _Inline_ (bloqueo de tráfico) o _Legacy_ (solo detección).
2. **Descarga de reglas**:
    
    - Ve a la pestaña **Global Settings**.
    - Configura las fuentes de reglas:
        - Habilita **Snort VRT Rules** (requiere registrarse en el sitio oficial de Snort para obtener una clave o.oinkcode).
        - Activa **ET Open Rules** como fuente gratuita.
    - Descarga las reglas haciendo clic en **Update Rules**.
3. **Configurar las políticas de reglas**:
    
    - En la pestaña **Categories**, selecciona las reglas que deseas activar. Por ejemplo:
        - Ataques DDoS.
        - Malware y spyware.
        - Fuerza bruta.
    - Guarda los cambios.
4. **Ajustes de alertas**:
    
    - Configura la opción para enviar alertas en tiempo real o registrar eventos en los logs.

---

### Parte 3: Pruebas prácticas

1. **Simulación de tráfico malicioso**:
    
    - Desde una máquina cliente de la red, utiliza herramientas como **nmap** para realizar un escaneo básico contra otra máquina de la red.
    - Observa si Snort detecta el escaneo como actividad sospechosa.
2. **Prueba con tráfico benigno**:
    
    - Navega por Internet desde un cliente conectado al firewall para comprobar que Snort no genera falsos positivos con tráfico legítimo.
3. **Bloqueo de tráfico malicioso**:
    
    - Activa el modo _Inline_ para que Snort no solo detecte, sino que también bloquee ataques.
    - Repite el escaneo de prueba y verifica que el tráfico malicioso sea bloqueado.

---

### Parte 4: Evaluación

1. **Comprobación de alertas**:
    - Revisa los logs de Snort en la pestaña **Alerts**.
    - Identifica las entradas relacionadas con las pruebas realizadas.
2. **Análisis de efectividad**:
    - Evalúa si las reglas activadas cubren correctamente los tipos de ataques probados.
    - Ajusta las categorías de reglas según sea necesario.

---

### Parte 5: Informe de la práctica

Elaborad un informe detallado que incluya:

- Capturas de pantalla del proceso de instalación y configuración.
- Ejemplos de alertas generadas por Snort.
- Análisis sobre los resultados de las pruebas realizadas.
- Sugerencias para mejorar la configuración de Snort según lo observado.

---

Esta práctica permite a los estudiantes comprender cómo implementar un sistema de detección y prevención de intrusos (IDS/IPS) en un entorno de red real utilizando pfSense.

## Bibliografía 

- [Netgate Snort vs Suricata](https://www.netgate.com/blog/suricata-vs-snort


<iframe width="560" height="315" src="https://www.youtube.com/embed/2q_g9GgkvWA?si=nSwJ08De2lQzdTsG" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>




<iframe width="560" height="315" src="https://www.youtube.com/embed/JtMzCw32Gq8?si=c5SKalraFThKMHAE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


