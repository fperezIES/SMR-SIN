---
icon: material/file-edit
title: Bastionado SSH
---

<!-- Tomada de curso seguridad del CEFIRE 2020 -->


# Propuesta de Herramientas:

- Máquina con sistema operativo Linux: Centos, Debian, Ubuntu,...
- Servicio de SSH corriendo en la máquina Linux: OpenSSH
- Máquina que permita la conexión al servicio para realizar las pruebas de seguridad.
Se podrá utilizar una máquina asociado al pentesting (Ej: Kali Linux) o una
herramienta gráfica de un entorno Windows (Ej: Putty, MobaXTerm,...)
- Opcional. Herramientas de auditoría de seguridad del servicio: nmap, metasploit,
hydra, scripts de githup




# Escenario:
La mayoría de los administradores de nuestra empresa utilizan el protocolo SSH para las tareas relativas a la administración de dispositivos de red (switchs, routers y firewalls) y de los sistemas operativos Linux. Se ha detectado que entre ellos tienen malas prácticas relativas a la seguridad:

* Tiene contraseñas débiles para el usuario de máximo privilegio (root) y comparten entre ellos la contraseña.
* Acceden desde cualquier dispositivo de la red para administrar las máquinas.
* Se dejan las sesiones abiertas en los terminales de equipos de usuarios después de
realizar una tarea de administración.
*  Algunos usuarios que acceden al servicio no tienen configurada la contraseña para su
usuario.
*  Ejecutan el entorno gráfico de las máquinas que administran para realizar simples
acciones.
*  No se está auditando y monitorizando el servicio ssh.
*  El acceso al servicio muestra la información de las versiones del software utilizado y
detalles que no son necesarios.
*  El servicio ssh no se actualiza con regularidad y tiene vulnerabilidades que pueden ser explotadas.


Responsable de la Seguridad de la empresa ha decido que este servicio crítico y tan utilizado por los administradores tiene que ser bastionado, de cara aumentar el nivel de seguridad de las tareas asociada a la utilización del SSH. Por lo que ha decidido que se implementen las siguientes medidas de seguridad:

1. No será posible el acceso remoto con el usuario root.
2. Deberá existir un tiempo máximo de sesión de 30 minutos.
3. Si la sesión permanece sin actividad durante 5 minutos se bloqueará.
4. Los usuarios deberán acceder al servicio con un certificado. Las claves privadas de los
usuarios deberá tener configurados los permisos correctamente para que un usuario no pueda manipular claves privadas de otros usuarios.
5. Sólo está permitido el acceso al servicio para cierto usuario y desde determinadas IPs. Basar la confianza de los equipos en parámetros de filtrado host de SSH en lugar del fichero .rhosts
6. Se debe implementar la versión segura del protocolo.
7. No es posible que a través del servicio se realice “Port-forwarding” ni ”X11
Forwarding”.
8. Los usuario recibirán en el inicio de sesión recibirán un mensaje indicando al tipo de
sistema que están accediendo y las obligación de cumplir con la normativa interna de
la empresa.
9. Se establecerán como protocolo de cifrado SHA2-512, SHA2-256.
10. Los logs del servicio están activados para poder enviárselos al SOC.
Todas estas medidas deben ser implementadas en el software que alberga el servicio ssh. El alumno podrá implementar medidas adicionales si así lo desea.


Para la realización de la práctica es necesaria una máquina Linux que albergue el servicio (Ej: OpenSSH). Y otra máquina que se conecte con el servicio que permita realizar las pruebas de seguridad (Ej: Kali Linux).


El ejercicio quedará validado con evidencias a través de capturas de pantalla de cada uno de las 10 medidas de seguridad indicadas anteriormente y su prueba de seguridad asociada. Cada parámetro de configuración del servicio tendrá la validez de 1 punto.


**Entrega de pruebas**: El formato aceptado para la evaluación de la actividad será un único documento en formato PDF

# Referencia:
* [https://www.ssh.com/academy/ssh/protocol](https://www.ssh.com/academy/ssh/protocol)
* [https://www.ccn-cert.cni.es/pdf/guias/series-ccn-stic/guias-de-acceso-publico-ccn-stic/3674-ccn-stic-619-implementacion-de-seguridad-sobre-centos7/file.html](https://www.ccn-cert.cni.es/pdf/guias/series-ccn-stic/guias-de-acceso-publico-ccn-stic/3674-ccn-stic-619-implementacion-de-seguridad-sobre-centos7/file.html)
* [https://nvlpubs.nist.gov/nistpubs/ir/2015/nist.ir.7966.pdf](https://nvlpubs.nist.gov/nistpubs/ir/2015/nist.ir.7966.pdf)
* [https://www.ssi.gouv.fr/en/guide/openssh-secure-use-recommendations/](https://www.ssi.gouv.fr/en/guide/openssh-secure-use-recommendations/)
* [https://github.com/jtesta/ssh-audit](https://github.com/jtesta/ssh-audit)
* [https://goteleport.com/blog/how-to-ssh-properly/](https://goteleport.com/blog/how-to-ssh-properly/)
* [https://smallstep.com/blog/use-ssh-certificates/](https://smallstep.com/blog/use-ssh-certificates/)