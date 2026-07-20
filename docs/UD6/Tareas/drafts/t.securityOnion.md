---
icon: material/file-edit
---


// TODO:
# Thread hunting

**Introducción a Security Onion:**

Security Onion es una plataforma de código abierto diseñada para la monitorización de la seguridad en redes (Network Security Monitoring), análisis de registros (log management) y detección de intrusiones (IDS). Está basada en la integración de múltiples herramientas como Zeek (antes conocido como Bro), Suricata, Kibana, Elasticsearch, Logstash y otras utilidades destinadas a la recolección y análisis de eventos de seguridad.

### ¿Qué hace Security Onion?

1. **Recolección de datos**
    
    - Captura y analiza el tráfico de red en tiempo real.
    - Permite recopilar registros de sistemas y servicios, incluyendo eventos de seguridad.
2. **Detección de intrusiones**
    
    - Utiliza motores IDS como Suricata o Zeek.
    - Analiza paquetes y flujos de red para detectar posibles amenazas o comportamientos anómalos.
3. **Análisis y correlación**
    
    - Almacena los registros en bases de datos como Elasticsearch.
    - Ofrece herramientas de visualización y búsqueda (Kibana, Dashboards, etc.) para identificar patrones de ataque.
4. **Investigación de incidentes**
    
    - Proporciona un entorno centralizado donde los analistas pueden investigar alertas y eventos.
    - Integra funcionalidades para reconstruir sesiones de red y profundizar en la evidencia.

### ¿Es Security Onion un SIEM?

- **No exactamente:** Aunque incorpora muchas funciones de análisis de eventos y correlación que podríamos encontrar en soluciones SIEM (Security Information and Event Management), Security Onion se centra más en la monitorización de la red, la detección y la investigación de incidentes.
- Un **SIEM** clásico se enfoca en recopilar eventos de múltiples fuentes (sistemas, aplicaciones, dispositivos de red, etc.), correlacionarlos a gran escala y generar alertas consolidadas según las reglas definidas.
- **Security Onion** puede actuar como parte de una estrategia SIEM al brindar datos de IDS, registros y alertas centralizados, pero no es un SIEM completo con todas las funcionalidades “clásicas” (como la orquestación de respuestas automatizadas o la gestión centralizada de la seguridad en toda la empresa).

En resumen, **Security Onion se usa principalmente para la monitorización, detección y análisis forense de eventos de seguridad en la red**, mientras que un SIEM típico abarca un rango aún mayor de fuentes de datos y permite una correlación más amplia de eventos de seguridad.

### Conclusión

- **Security Onion** es una solución excelente para montar un entorno de monitorización de seguridad en red y análisis de logs, combinando herramientas robustas de IDS, recolección, correlación y visualización.
- **No sustituye completamente a un SIEM**, pero puede complementar y potenciar las funciones de un SIEM existente o incluso actuar como una solución central para análisis de seguridad en entornos pequeños o medianos.

La decisión de utilizar Security Onion, un SIEM o ambos depende de los objetivos de seguridad, el tamaño de la empresa y la complejidad de la infraestructura.

## Bibliografía

https://securityonionsolutions.com/
https://www.youtube.com/@security-onion

https://www.youtube.com/watch?v=k22Pt19OTdo