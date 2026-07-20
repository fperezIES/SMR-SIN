---
icon: material/file-edit
---



# Supuesto Práctico: Plan de Gestión de Riesgos

## Escenario 1: Empresa de Desarrollo de Software (_TechSolutions S.L._)

_TechSolutions S.L._ es una empresa con sede en Valencia que se dedica al desarrollo de software para clientes internacionales. La empresa tiene 30 empleados distribuidos en tres equipos de desarrollo, un equipo de administración y un pequeño equipo de marketing. Debido a su modelo de negocio basado en la nube, la empresa depende en gran medida de la infraestructura tecnológica.

### Modernización:

- TechSolutions decide migrar toda su infraestructura a la nube utilizando servicios de un proveedor de terceros para reducir costes en hardware y aumentar la escalabilidad.
- El CEO decide implementar una nueva herramienta de gestión de proyectos para optimizar el trabajo de los equipos de desarrollo y una plataforma interna para el intercambio de documentación técnica.
- También se implementa una nueva web corporativa basada en un CMS de código abierto.

### Problemas:

- A las pocas semanas, la empresa experimenta interrupciones en el servicio de su herramienta de gestión, afectando los plazos de entrega de varios proyectos.
- Además, hay sospechas de accesos no autorizados a la plataforma de documentación técnica.

<!--
### Tareas para los alumnos:

- Realizar un **inventario de activos**: servidores en la nube, plataformas de gestión de proyectos, herramientas de desarrollo, etc.
- Identificar **amenazas**: fallos de seguridad en la nube, vulnerabilidades en el CMS, acceso no autorizado a la documentación interna.
- Proponer un **plan de mejora**: cifrado de documentos, segmentación de redes, revisión de permisos de acceso, uso de contraseñas robustas y autenticación multifactor.
-->



## Escenario 2: Clínica Veterinaria (_PetCare SL_)

_PetCare SL_ es una pequeña clínica veterinaria en Alicante con cinco empleados. La mayor parte de su trabajo diario depende de la gestión de historiales médicos de mascotas, programación de citas y facturación a los clientes. Actualmente utilizan una herramienta de gestión interna alojada en un servidor físico en la clínica.

### Modernización:

- La clínica decide digitalizar todos los historiales de mascotas y migrarlos a una nueva herramienta de gestión alojada en un servidor externo, buscando mejorar la accesibilidad y la seguridad de los datos.
- El servidor externo es contratado a bajo coste y gestionado por una empresa local que garantiza el almacenamiento seguro.
- También crean una página web donde los clientes pueden solicitar citas y acceder al historial médico de sus mascotas.

### Problemas:

- Poco después de la migración, los empleados detectan ralentizaciones en el sistema de gestión, y algunos historiales médicos aparecen incompletos o corruptos.
- Además, hay indicios de que terceros no autorizados han accedido a los datos de los clientes a través de la página web.

<!--
### Tareas para los alumnos:

- Realizar un **inventario de activos**: servidores externos, bases de datos de historiales médicos, página web, equipos en la clínica.
- Identificar **amenazas**: ataques a la web, corrupción de datos durante la migración, vulnerabilidades en el servidor contratado.
- Proponer un **plan de mejora**: pruebas de seguridad en el servidor externo, copia de seguridad regular de los datos, protección de la página web con certificados SSL y autenticación.
-->



## Escenario 3: Restaurante con Servicio de Comida a Domicilio (_GourmetExpress_)

_GourmetExpress_ es un restaurante en Madrid especializado en comida para llevar y entrega a domicilio. Recientemente, han decidido implementar un sistema de pedidos en línea para mejorar la eficiencia de los pedidos y pagos. La empresa tiene 15 empleados, incluyendo chefs, repartidores y personal de atención al cliente.

### Modernización:

- Se desarrolla una aplicación móvil para la gestión de pedidos y pagos en línea.
- El restaurante decide almacenar todos los datos de los clientes y los pagos directamente en un servidor que se aloja en un cuarto de almacenamiento dentro del restaurante.
- También se lanza una página web con un CMS de código abierto para gestionar las reservas y promociones.

### Problemas:

- A las pocas semanas del lanzamiento, algunos clientes informan que sus datos de pago han sido comprometidos.
- Además, el sistema de pedidos se desconecta frecuentemente, afectando la operativa del restaurante.

<!--
### Tareas para los alumnos:

- Realizar un **inventario de activos**: aplicación móvil, servidor de almacenamiento de datos, sistema de pago, página web.
- Identificar **amenazas**: violaciones de seguridad en el sistema de pago, malware en el servidor, vulnerabilidades del CMS.
- Proponer un **plan de mejora**: implementar cifrado de datos de pago, revisar la seguridad del servidor, usar una pasarela de pago segura, realizar auditorías regulares de la web.
-->



## Escenario 4: Asociación Cultural sin Ánimo de Lucro (_CulturaUrbana_)

_CulturaUrbana_ es una asociación cultural en Barcelona que organiza eventos y actividades en la ciudad. Actualmente gestionan la inscripción de los participantes y la información de los eventos a través de una plataforma en línea. La asociación tiene siete voluntarios encargados de la administración de la plataforma y la organización de los eventos.

### Modernización:

- La asociación decide mejorar su plataforma de gestión de eventos añadiendo nuevas funcionalidades, como la venta de entradas y la integración con redes sociales.
- Además, compran un nuevo servidor para almacenar los datos de los participantes y realizar copias de seguridad internas, y desarrollan una web informativa con un CMS de código abierto.

### Problemas:

- Tras implementar los cambios, la web sufre varios intentos de ataque y las cuentas de redes sociales de la asociación son comprometidas.
- El nuevo servidor, ubicado en una oficina sin medidas de seguridad, sufre un corte eléctrico, lo que provoca la pérdida de parte de los datos de los participantes.

<!--
### Tareas para los alumnos:

- Realizar un **inventario de activos**: servidor interno, plataforma de eventos, página web, redes sociales.
- Identificar **amenazas**: ataques a la web y redes sociales, pérdida de datos por cortes eléctricos, falta de seguridad en el servidor.
- Proponer un **plan de mejora**: mejorar la seguridad física y lógica del servidor, implementar una política de copias de seguridad externas, proteger las cuentas de redes sociales con autenticación multifactor.
-->

## Escenario 5: Clínica Estética (_Venus SA_)

_Venus SA_ es una clínica estética con sede en Ibiza que cuenta con 10 empleados, incluyendo doctores, personal administrativo y un recepcionista. La empresa gestiona su información en formato físico y tiene una baja dependencia tecnológica. El CEO de la clínica ha decidido modernizar la gestión de la clínica mediante la digitalización de los historiales médicos y la implementación de nuevas herramientas tecnológicas.

### Modernización:

- _Venus SA_ decide desarrollar una herramienta informática para gestionar:
    - Historiales médicos de los pacientes.
    - Nóminas de los empleados.
    - Relaciones con proveedores.
- Se informatizarán todos los historiales médicos actualmente en formato físico.
- Se adquirirá un servidor para alojar la herramienta y la nueva página web corporativa.
- La página web será desarrollada usando un CMS de código abierto.
- El servidor será instalado en un cuarto de almacenamiento de productos de limpieza.
- El recepcionista, tras recibir una formación básica, será el encargado del mantenimiento del sistema y la digitalización de los historiales.

### Problemas:

- A los pocos días de implementar el nuevo sistema, se producen caídas constantes en el servicio, impidiendo que el personal utilice la herramienta informática de manera eficiente.
- El personal debe volver a utilizar los métodos manuales de trabajo debido a los fallos continuos del sistema.

<!--
### Tareas para los alumnos:

- Realizar un **inventario de activos**: servidor local, equipos médicos informatizados, herramienta de gestión de historiales, nóminas y proveedores, página web corporativa.
- Identificar **amenazas**: fallos en el servidor (ubicado en un lugar inadecuado), vulnerabilidades del CMS de la página web, formación insuficiente del recepcionista encargado del mantenimiento, errores en la digitalización de historiales.
- Proponer un **plan de mejora**: reubicar el servidor en un lugar con medidas de seguridad física adecuadas, separar los servicios del servidor (página web y herramienta de gestión), realizar auditorías de seguridad del CMS, contratar personal especializado para el mantenimiento del sistema o mejorar la formación del recepcionista.
-->



# Actividad: Plan de Gestión de Riesgos

**Objetivo:** Los alumnos deberán realizar un plan de gestión de riesgos para la empresa seleccionada, evaluando el contexto actual y proponiendo mejoras en seguridad.

### Fases del Plan

1. **Inventario de activos**:    
    - Identificar los activos tecnológicos, humanos y físicos involucrados en el proceso. Ejemplo: servicios, servidores, comunicaciones, información, personas, etc.
    
1. **Análisis de amenazas**:    
    - Identificar las posibles amenazas que podrían afectar a los activos. 
	    - Ejemplo: fallos hardware, ciberataques, errores humanos, seguridad física,  etc.
	    
1. **Valoración de riesgos**:    
    - Evaluar la probabilidad y el impacto de las amenazas identificadas. 
	    - Ejemplo:  **Alta probabilidad** de un ataque a la página web por vulnerabilidades del CMS, con un impacto de **fuga de datos** sensibles.
    - Cumplimiento normativo.
    
1. **Plan de mejora de la seguridad**:    
    - Proponer soluciones para mitigar los riesgos. 
	    - Ejemplo:  Formar adecuadamente al personal encargado del mantenimiento del sistema o contratar personal especializado en TI.

### Preguntas clave para la actividad:

1. ¿Qué activos clave has identificado en la empresa? ¿Cuál es su valor?
2. ¿Qué amenazas consideras más probables? ¿Cuáles serían las consecuencias de que se materialicen?
3. ¿Qué riesgos se consideran inaceptables? ¿Cómo deberían gestionarse?
4. ¿Qué medidas de seguridad añadirías para mitigar los riesgos? Considera tanto medidas técnicas como organizativas.

### Instrucciones para la entrega:

- Realiza un **informe** que incluya el inventario de activos, el análisis de amenazas, la valoración de riesgos y un plan de mejora.
- Propón soluciones realistas que respeten las restricciones presupuestarias planteadas en el supuesto.

# Materiales a considerar

[INCIBE: # [Plan Director de Seguridad](https://www.incibe.es/empresas/que-te-interesa/plan-director-seguridad)](https://www.incibe.es/empresas/que-te-interesa/plan-director-seguridad)
	- Contiene en la sección de **descargas** diferentes plantillas para inventariar activos y analizar riesgos. También hay un checklist de controles.


[Metodología Margerit v3 (ENS)](https://administracionelectronica.gob.es/pae_Home/pae_Documentacion/pae_Metodolog/pae_Magerit.html)
	- Define metodología para gestión del riesgo en sistemas informáticos en el ENS
	- En su libro 3 muestra Técnicas para valoración de impactos y riesgos.


[UNE-ISO/IEC 27001:2017 y UNE-ISO/IEC 27002:2017 ](https://www.industriaconectada40.gob.es/difusion/Paginas/enlaces-interes.aspx)
	- ISO/IEC 27001: Define qué es un SGSI.
	- ISO/IEC 27002: Enumera controles que se deberían establecer para cumplir 27001.
