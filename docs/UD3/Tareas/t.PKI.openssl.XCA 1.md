---
icon: material/file-edit
---

openSSL
xca

https://documentation.suse.com/es-es/sles/15-SP5/html/SLES-all/cha-security-xca.html

# Creación de un certificado SSL/TLS multisite


## Introducción

En esta práctica vamos a realizar las siguientes actividades:

* Crear una Autoridad de Certificación (AC) para firma de certificados.
* Crear una solicitud de firma de certificado (CSR) para un servidor web que funcionará en varios dominios.
* Instalar el certificado generado en un servidor web.

![XCA Logo](img/certmultisite/xca_logo.png){:style="width: 50%;" class="center"}

Usaremos la herramienta de creación, revocación y almacenamiento de certificados digitales **XCA**, disponible en los repositorios de muchas distribuciones GNU/Linux y también para Windows. La versión para Windows está disponible en [la página de descargas de XCA https://hohnstaedt.de/xca/index.php/download](https://hohnstaedt.de/xca/index.php/download).

En nuestro caso realizaremos la práctica en una máquina virtual con sistema operativo **Debian**.

## Enunciado

### Creación de Autoridad de Certificación

@. Instala la aplicación XCA en na máquina virtual con sistema operativo Debian, después ejecútala y toma una captura de la interfaz gráfica:

Instala:
```sh
sudo apt-get update 
sudo apt-get install xca
```

Ejecuta:

```sh
xca
```


@. Antes de trabajar con el programa XCA, es necesario crear una base de datos donde el programa guardará y gestionará todos los certificados que solicitemos o que creemos. Esto se hace con `File -> New Database`. Elegimos un nombre y una ruta. Nos pedirá una contraseña para proteger nuestros certificados y las claves privadas de ellos. Vuelve a tomar una captura de la interfaz XCA después de haber creado la base de datos.

@. Una vez creada la base de datos ya podemos generar y tramitar certificados. El primer paso es constituirnos como una AC que podrá certificar y validad certificados digitales de otras personas o servidores web. Para ello en la pestaña `Certificates` pulsa `New Certificate` en el menú de la derecha. 
En la ventana que aparece, en la pestaña `Source` u Origen, debes seleccionar el *template* `[default] CA` y pulsar `Apply All`. La aplicación XCA dispone de una serie de plantillas (*templates*) para configurar parámetros por defecto en función del uso del certificado. En este caso, ya que vamos a crear nuestra propia Autoridad de Certificación, debemos seleccionar ese *template*. Como vamos a crear una AC raíz, deja marcado `Create a self signed certificate` y elegimos `SHA 256` como algoritmo de firma. Recordemos que ni MD5 ni SHA 1 se recomiendan por seguridad. Recuerda pulsar `Apply All`:

![Creación de certificado de CA](img/certmultisite/xca_ca1.png){:class="center"}

@. Observa que una vez seleccionado el template default CA, en la pestaña `Extensions`, se ha marcado `Certification Authority` como tipo de Certificado y con una validez de 10 años. Esto es así porque en la plantilla por defecto se ha definido esa validez. El programa te permite generar otra plantilla personalizada con distintos parámetros, como por ejemplo los años de validez. Por ejemplo, hay algunas AC como la GVA o la FNMT que tienen 20 de años de validez en sus certificados. Observa también que en la pestaña `Key Usage` (utilización de la clave), se ha marcado `Certificate Sign` y `CRL Sign`. Esto es así porque éste es el uso habitual de un certificado de una CA: firmar y revocar otros certificados. Toma una captura de la pestaña `Extensions` y otra de la pestaña `Key Usage`.

@. En la pestaña `Sujeto` o `Subject` tienes que escribir los campos que te identificarán como AC. En la siguiente pantalla hay un ejemplo de los valores que puedes poner. **Cámbialos personalizándolo a tu nombre y evita usar acentos**. Puedes cambiar los campos de Organización, unidad organizativa, Common name, etc. a tu gusto, pero que sean valores que tengan sentido como si fueran para una Autoridad Certificadora real, toma una captura de los datos introducidos:

![Creación de certificado de CA, detalles de Sujeto](img/certmultisite/xca_ca2.png){:class="center"}


@. Tienes que crear una nueva clave privada para usar en el certificado de la AC. Recuerda que los certificados digitales utilizan la criptografía asimétrica o de clave pública y por tanto todo certificado va asociado a un par de claves. Pulsa en `Generate a new key` y selecciona `RSA` de `4096 bits`, que es una buena longitud de clave. Después de esto, ya puedes pulsar `Create`y después de unos instantes se habrá generado tu certificado de AC.



@. Ahora pulsa `Aceptar` y ya tendremos la AC constituida. 

![Certificado CA creado](img/certmultisite/xca_ca3.png){:class="center"}



@. Esta AC la exportaremos a un fichero `.crt` en formato `PEM` (codificado en base64) para poder importarlo en la lista de AC's autorizadas y confiables de nuestro navegador. Para ello selecciona el certificado creado y pulsa Export. 

![Exportando certificado CA](img/certmultisite/xca_ca4.png){:class="center"}

@. Una vez exportado, lo puedes importar en tu navegador. Por ejemplo, desde `Firefox`, esta tarea se realizada desde `Preferences -> Privacy & Security -> Certificates -> View Certificates -> Authorities -> Import...`. 

![Importando CA en Firefox](img/certmultisite/firefox_CA_import1.png){:class="center"}

Después marca como mínimo `Trust this CA to identify websites` y pulsa `OK`:

![Importando CA en Firefox](img/certmultisite/firefox_CA_import2.png){:class="center"}

@. En la pestaña Autoridades puedes buscar el certificado importado buscando por el nombre de organización (IES Villa de Aspe en mi ejemplo):

![CA añadido a Firefox](img/certmultisite/firefox_CA_import3.png){:class="center"}

### Creación de certificado SSL para servidor web

@. Llegados a este punto, vamos a simular que somos una organización con presencia en Internet y que necesitamos un certificado digital de servidor web para el sitio web de la organización y que responde a varios dominios simultáneamente. Para asegurar que tú eres quien hace la práctica tu apellido debe aparecer en el dominio (sin acentos y debes usar solamente caracteres ingleses), por ejemplo en mi caso, que me llamo Pérez, los dominios serán (se deben cambiar para que coincidan con nuestro apellido): 

* www.servidorperez.com
* www.cursoperez.com 
* www.servidorperez.es 
* www.cursoperez.es

A continuación debes ir a la pestaña `Certificate Signing Request` de XCA y pulsar `New Request`. Selecciona el *template* `[default] HTTPS_server` (o `TLS_server`, depende de la versión de XCA) y `SHA 256` como firma y pulsa `Apply all`. De esta forma se ajustan los parámetros para un certificado de servidor web seguro. Los atributos `unstructuredName` y `challengePassword` no se suelen usar aunque algunas AC's lo pueden solicitar, para esta práctica no hacen falta.

@. En la pestaña `Sujeto` o `Subject` tienes que escribir los campos que te identificarán como servidor web, ya que el uso que le vas a dar al certificado que estás solicitando. En la siguiente pantalla hay un ejemplo de los valores que puedes poner. **Cámbialos personalizándolo al dominio de tu servidor y evita usar acentos**. Es fundamental que el `Common Name` lo pongas correctamente y debe ser el FQDN (nombre de dominio completo) que usará tu servidor en Internet:

![Creando petición de certificado](img/certmultisite/cert_ssl1.png){:class="center"}


@. En la pestaña `Sujeto` o `Subject` pulsaremos el botón `Generate a new key`. Dejaremos el `Keytype` y el tamaño de clave con los valores que se indican por defecto (que son `RSA`y `2048 bit`) y finalmente pulsamos `Create`.

@. A continuación vamos a añadir los nombres de dominio adicionales en la pestaña `Extensions`. Pulsa el botón `Edit` situado al lado del campo `X509v3 Subject Alternative Name` y en la ventana que aparece introduce todos los nombres adicionales del tipo DNS, como en el siguiente ejemplo. Observa que **puedes introducir * como prefijo en los nombres** (nota: no vale algo como *.dominio.*):

![Añadiendo dominios adicionales](img/certmultisite/cert_ssl2.png){width=70%}

@. Pulsa `Aplicar` y `Aceptar` y aparecerá la nueva **CSR** en la pestaña de `Certificate signing requests`, pero pendiente de firma. En un escenario real, deberías exportar este CSR y enviarlo a una AC para, que una vez validado el dominio y la organización, proceda a firmar digitalmente tu CSR y convertirla en un certificado digital de servidor listo para usar. 
En nuestra práctica, como nosotros mismo somos la AC, procedemos a firmar la CSR seleccionándola y pulsando `Firma` en el menú contextual que aparece al hacer clic en el botón derecho del ratón. 
En el procedimiento de firma, aplicamos el *template* de `HTTPS_server` (o `TLS_Server`, en función de la versión de XCA), seleccionaremos `SHA 256` como algoritmo de firma y seleccionamos nuestra AC para firmar en la lista desplegable `Use this Certificate for signing`:

![F¡rma de CSR](img/certmultisite/cert_ssl3.png){:class="center"}

@. Asegúrate que está marcado `Copy extensions from the request` para que se copien las extensiones adicionales como `Subject Alternative Name` del CSR original al certificado que se va a crear. **A pesar de ello, es posible que las extensiones de nombres DNS alternativos no se copien desde la CSR, con lo cual habría que darlas de nuevo manualmente en la pestaña `Extensions`**. 

![Añadiendo dominios adicionales](img/certmultisite/cert_ssl4.png){:class="center"}

@. Finalmente pulsa `Aceptar` y el certificado aparecerá generado colgando del certificado de tu AC en la jerarquía de certificados:

![Nuevo certificado creado desde CSR cuelga de CA](img/certmultisite/cert_ssl5.png){:class="center"}

### Configuración de servidor Web con certificado SSL

@. En este momento, ya puedes exportar tanto el certificado del servidor web recién generado, como la clave privada asociada, ambos en formato PEM. Normalmente desde XCA el certificado se exporta con extensión `.crt` y la clave privada con extensión `.pem`, aunque a la hora de instalarlos en un servidor web como Apache, es indiferente la extensión que tenga. 

![Exportando certificado](img/certmultisite/cert_ssl6.png){:class="center"}

![Exportando clave privada](img/certmultisite/cert_ssl7.png){:class="center"}

@. Copia tanto el certificado como clave privada en el directorio habitual de tu distribución GNU/Linux, como por ejemplo `/etc/ssl/certs` para certificados y `/etc/ssl/private` para claves privadas en el caso de Debian y derivadas o `/etc/pki/tls/certs` para Red Hat, CentOS o Fedora, y asegurándote de que la clave privada tiene permisos 600 y es propietario root únicamente, para que nadie excepto root tenga acceso a la clave privada. Los comandos a utilizar son:

```sh
sudo cp Servidor_web_perez.crt /etc/ssl/certs/
sudo cp Servidor_web_perez.pem /etc/ssl/private/
```

@. Instala Apache con mósulos SSL ejecutando los siguientes comandos:

```sh
sudo apt-get install apache2 
sudo a2enmod ssl
sudo a2ensite default-ssl 
sudo service apache2 reload
```

@. Configura tu servidor Apache para que funcione con SSL/TLS. Básicamente se trata de cargar el módulo `ssl` (que hemos instalado en el paso anterior), de que escuche en el puerto 443 y de indicar la ruta al certificado y clave privada en la configuración de Apache. Se deben usar las directivas:

* `SSLEngine`
* `SSLCertificate`
* `SSLCertificateKeyFile`

Para hacerlo, vamos a editar el sitio ssl por defecto mediante el comando:

```sh
sudo nano /etc/apache2/sites-enabled/default-ssl.conf
```

Una vez configurado, el fichero debe ser similar al ejemplo siguiente:

![Configuración de certificados en servidor Apache](img/certmultisite/apache1.png){width=70%}

Para que se apliquen los cambios **reinicia apache**:

```sh
sudo service apache2 reload
```

@. Una vez configurado y reiniciado Apache, vamos a simular que los dominios que has añadido como nombres alternativos, se resuelven en Internet. Para ello lo más fácil es editar el fichero `hosts` de tu sistema e indicarle que todos esos dominios se sirven en localhost (127.0.0.1):

```sh
sudo nano /etc/hosts
```
Debes cambiar la línea de localhost para que quede como la siguiente.
```sh
127.0.0.1       localhost www.cursoperez.com www.servidorperez.es www.cursoperez.es
```
A continuación se muestra el aspecto del fichero una vez terminado:

![Modificación de fichero hosts](img/certmultisite/apache2.png){width=70%}


@. Comprueba ahora que conectándote por https a cualquiera de estos dominios, se muestra la página por defecto de tu servidor web usando TLS y sin mostrar ninguna alerta de seguridad, ya que el certificado incluye todos esos nombres DNS. 

![Página web con candado verde en Firefox](img/certmultisite/apache3.png){width=70%}

@. Haz la prueba añadiendo algún dominio no incluido en el certificado y mostrando una alerta de seguridad del navegador al no corresponder el dominio con el certificado.

![Página web con candado verde en Firefox](img/certmultisite/apache4.png){:class="center"}
