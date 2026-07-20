---
icon: material/file-edit
title: Auditoría de seguridad de servidores Linux
---

<!-- Tomada de curso seguridad del CEFIRE 2020 -->

# Auditoria de seguridad de servidores Linux (Lynis)

# Introducción

Linux es considerado un sistema operativo más seguro que Windows. Esto se debe principalmente a que existe menos malware diseñado para atacar las múltiples distribuciones disponibles. Sin embargo, esto no significa que no haya problemas que puedan afectar a la privacidad, la seguridad o provocar errores con las aplicaciones instaladas.

Lynis es una herramienta de auditoría muy completa que podemos utilizar en Linux. Está disponible para distribuciones tan populares como Ubuntu, Fedora, Debian y OpenSUSE. Básicamente, lo que hace es analizar todo el software instalado en nuestro equipo y buscar posibles problemas que debamos corregir. De esta forma, sabremos si tenemos que realizar cambios, instalar actualizaciones, etc.

![Logo CISOFY](../img/lynis/lynis.png){:style="width: 40%;" class="center"}

**Lynis** es una herramienta muy interesante para ayudarnos a auditar y fortificar nuestro servidor. Es muy sencilla de usar y, una vez descargada, tan solo debemos ejecutarla para que analice el software instalado en busca de problemas de configuración o de seguridad, proporcionándonos recomendaciones. Se distribuye bajo licencia GPL v3, por lo que cualquiera puede utilizarla para analizar su distribución sin ningún coste.

Cuando se está ejecutando, nos muestra por pantalla información acerca de nuestro sistema en aspectos como:

- Resumen del sistema y actualizaciones pendientes
- Gestor de arranque
- Servicios que se inician con el sistema
- Memoria y procesos
- Usuarios, grupos y sistemas de autenticación disponibles
- Shells del sistema
- Sistemas de archivos y almacenamiento
- Servicio de nombres
- Gestores de paquetes
- Impresoras
- Servidores de correo electrónico
- Estado del firewall
- Servicios instalados (web, SSH, SNMP, etc.)
- Servicios de logging como syslog
- Tareas programadas (cron y at)
- Presencia de SELinux o AppArmor
- Sistemas de integridad de archivos instalados
- Escáneres de malware y rootkits instalados

Finalmente, se nos presenta un completo informe de todos los pasos realizados con el fin de que tomemos las medidas necesarias para fortalecer la seguridad de nuestro servidor. También es capaz de encontrar vulnerabilidades y configuraciones por defecto, y para ello emplea colores llamativos para captar nuestra atención.

# Enunciado

> Analiza la seguridad de un servidor Linux en el que estén habilitados, como mínimo, un servidor SSH y un servidor Apache, utilizando la herramienta Lynis.

**Revisa la bibliografía antes de empezar.**


Debes crear una memoria para la práctica que contenga:
1. ¿Cómo has instalado Lynis? (Recuerda que Almalinux es similar a otras distribuciones como: CentOS, Fedora RHEL, Rocky...)
2. ¿Cómo has ejecutado Lynis?
3. El informe generado por Lynis.
4. Un análisis del informe obtenido en el que expliques, bajo tu punto de vista, los problemas más importantes que se has encontrado.
	- Detalla y explica las mejoras sugeridas relacionadas con las contraseñas y autenticación de usuairos.
	- Detalla y explica las mejoras sugeridas relacionadas con SSH.
5. ¿Cómo podrías mitigar los problemas de seguridad detectados por Lynis en tu servidor?
6. ¿Crees que es suficiente con realizar una auditoría de seguridad puntual, o debería ser un proceso continuo? Justifica tu respuesta.


## Investigación opcional

Para ampliar la práctica, investiga brevemente sobre otras herramientas de auditoría como **OpenSCAP** o **Chkrootkit**. Compara sus funcionalidades básicas con Lynis y escribe una breve reflexión sobre cuál de ellas crees que podría complementar mejor las funciones de auditoría de seguridad en un servidor Linux.



### Escenario práctico opcional

Si tienes acceso a un entorno de pruebas, puedes realizar una auditoría de seguridad adicional en un servidor configurado intencionalmente con más servicios. Por ejemplo, agrega un servidor FTP o una base de datos y ejecuta la auditoría de Lynis sobre el servidor con estos servicios habilitados. Anota cualquier configuración adicional que Lynis detecte como potencialmente insegura y añade las medidas de corrección necesarias.


# Bibliografía

* [https://cisofy.com/documentation/lynis/get-started/](https://cisofy.com/documentation/lynis/get-started/)
* [Habilitar repositorios EPEL (Extra Packages for Enterprise Linux (EPEL) en Almalinux](https://reintech.io/blog/installing-using-epel-repository-almalinux-9#google_vignette)
- [Tutorial Lynis  Almalinux](https://www.techrepublic.com/article/how-to-run-a-security-audit-on-almalinux-with-lynis/)
- [https://www.linuxtotal.com.mx/index.php?cont=info_seyre_011](https://www.linuxtotal.com.mx/index.php?cont=info_seyre_011)

* [https://packages.cisofy.com/community/#debian-ubuntu](https://packages.cisofy.com/community/#debian-ubuntu)
* [https://www.redeszone.net/tutoriales/seguridad/lynis-auditoria-seguridad-linux/](https://www.redeszone.net/tutoriales/seguridad/lynis-auditoria-seguridad-linux/)
* [https://computernewage.com/2016/09/03/lynis-auditoria-seguridad-linux/](https://computernewage.com/2016/09/03/lynis-auditoria-seguridad-linux/)



<!--


Para instalar Lynis en Almalinux:

```sh
sudo dnf install epel-release -y
sudo dnf install lynis -y
```

Para ejecutar una auditoría:

```sh
sudo lynis audit system
```


-->