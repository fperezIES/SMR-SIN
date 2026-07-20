---
icon: material/file-edit
---


# Ejercicio: Cifrado Asimétrico y firma con GnuPG

Anteriormente, utilizamos GPG (GNU Privacy Guard) para realizar **cifrado simétrico** de mensajes. En esta práctica, vamos a utilizar la misma herramienta para **cifrado asimétrico** y firma de mensajes.

**Pretty Good Privacy (PGP)** es un programa desarrollado por Phil Zimmermann en 1991, diseñado para proteger la información transmitida por Internet mediante **criptografía de clave pública** y facilitar la autenticación de documentos a través de **firmas digitales**. Basado en el diseño de PGP, el estándar **OpenPGP** (RFC4880) fue desarrollado por la Internet Engineering Task Force. GPG, o GnuPG, es una implementación completa y gratuita de este estándar.

![Logo gpg](../img/gnupg_logo.png){:style="width: 50%;" class="center"}

PGP es un criptosistema híbrido que **combina criptografía simétrica y asimétrica**. Esto permite aprovechar las ventajas de ambos: **la criptografía simétrica es más rápida**, mientras que la asimétrica resuelve el problema de la distribución segura de claves y garantiza la **autenticidad** y el **no repudio** de los datos.

**Nota:** Aunque GPG permite el cifrado simétrico y asimétrico, **en esta práctica nos centraremos en el cifrado asimétrico**.

Usaremos un sistema operativo Linux preferiblemente.

## Paso 1: Instalar GPG

### En Linux

La mayoría de las distribuciones de Linux incluyen GPG por defecto. Si no es así, puedes instalarlo usando el gestor de paquetes de tu distribución:

```bash
# Para distribuciones basadas en Debian
sudo apt-get install gnupg

# Para distribuciones basadas en Red Hat
sudo dnf install gnupg
```

### En macOS

Puedes instalar GPG utilizando Homebrew:

```bash
brew install gnupg
```

### En Windows

Descarga e instala Gpg4win desde la página oficial: [https://www.gpg4win.org/](https://www.gpg4win.org/)

## Paso 2: Generar un Par de Claves

Ejecuta el siguiente comando en la terminal:

```bash
gpg --full-generate-key
```

Sigue las instrucciones:

1. **Tipo de clave:** Elige `RSA and RSA` (opción por defecto).
2. **Tamaño de la clave:** Se recomienda 3072 o 4096 bits para mayor seguridad.
3. **Tiempo de expiración:** Puedes establecer una fecha de caducidad o dejarla sin caducar. Por seguridad establece 1 año de expiración.
4. **Información de usuario:**
   - **Nombre completo**
   - **Dirección de correo electrónico** (se usará para identificar el certificado)
   - **Comentario** (opcional)
5. **Contraseña:** Crea una contraseña segura para proteger tu clave privada.


Si recibes algún error relacionado con `pinentry`, asegurate de que está instalado:

```sh
sudo dnf install pinentry
```

Si el sistema operativo que estás utilizando no tiene interfaz gráfica, además tendrás que cambiar la configuración:

```
sudo nano /etc/gnupg/gpg-agent.conf
```

Añade o cambia la línea relacionada con el programa pinentry para que quede como la siguiente:

```
pinentry-program /usr/bin/pinentry-curses
```

Ahora si vuelves a intentar generar las claves debería funcionar.

## Paso 3: Verificar las Claves Generadas

Para listar tus claves públicas:

```bash
gpg --list-keys
```

Para listar tus claves privadas:

```bash
gpg --list-secret-keys
```

## Paso 4: Exportar tu Clave Pública

Para compartir tu clave pública con otros, expórtala de la siguiente manera:

```bash
gpg --output clave_publica.asc --export --armor tu_correo@example.com
```

Esto crea un archivo `clave_publica.asc` que puedes enviar a otras personas.

Si quieres, puedes hacer una copia de tu clave privada exportándola mediante:

```bash
gpg --output clave_privada.pgp --armor --export-secret-key username@email
```

Esto crea un archivo `clave_privada.asc` al que nadie más que tú debería poder acceder.

## Paso 5: Importar la Clave Pública de un Destinatario

Cuando recibas la clave pública de alguien, impórtala con:

```bash
gpg --import clave_publica_destinatario.asc
```

## Paso 6: Cifrar un Mensaje o Archivo

Para cifrar un archivo para un destinatario específico:

```bash
gpg --encrypt --recipient  correo_destinatario@example.com archivo.txt 
```

Esto generará un archivo cifrado `archivo.txt.gpg`.

## Paso 7: Descifrar un Mensaje o Archivo

Para descifrar un archivo que te han enviado:

```bash
gpg --output archivo_descifrado.txt --decrypt archivo.txt.gpg
```

Se te pedirá la contraseña de tu clave privada.

## Paso 8: Firmar un Archivo Digitalmente

Para agregar una firma digital a un archivo:

```bash
gpg --sign archivo.txt
```

Esto crea un archivo `archivo.txt.gpg` firmado.

También es posible generar un documento de firma separado del archivo original:

```bash
gpg --detach-sign archivo.txt
```

Este comando creará un archivo `archivo.txt.sig` que contendrá la firma del fichero archivo.txt pero separada en un fichero independiente.

## Paso 9: Verificar una Firma Digital

Para verificar la firma de un archivo:

```bash
gpg --verify archivo.txt.gpg
```

Si la firma está separada:

```
gpg --verify archivo.txt.sig archivo.txt
```

Estos comandos indicarán `Good` o `BAD`para indicar si la firma es correcta o incorrecta.

## Paso 10: Cifrar y Firmar Simultáneamente

Puedes cifrar y firmar un archivo en un solo paso:

```bash
gpg --encrypt --sign --recipient correo_destinatario@example.com archivo.txt
```

## Ejemplo Práctico Completo

Supongamos que deseas enviar un archivo cifrado y firmado a `juan@example.com`.

1. **Importa la clave pública de Juan:**

   ```bash
   gpg --import clave_publica_juan.asc
   ```

2. **Cifra y firma el archivo:**

   ```bash
   gpg --encrypt --sign --recipient juan@example.com mensaje.txt
   ```

3. **Envía `mensaje.txt.gpg` a Juan.**

Cuando Juan reciba el archivo, podrá verificar tu firma a partir de tu clave pública y podrá descifrar el mensaje usando su clave privada.

## Consejos de Seguridad

- **Protege tu clave privada:** Nunca compartas tu clave privada y utiliza una contraseña segura.
- Establece siempre una fecha de caducidad para limitar el impacto de un acceso no autorizado a tu clave privada.
- **Revoca tu clave si es necesario:** Si pierdes el control de tu clave privada, genera un certificado de revocación.


---

# Tareas adicionales:

**Comparar tiempos de cifrado/descifrado:**

Realiza pruebas cifrando archivos más grandes (como un archivo de vídeo o imagen) y compara el tiempo que tarda GnuPG en cifrar y descifrar usando cifrado simétrico y asimétrico. Puedes usar el comando `time` para medirlo:
   
   ```bash
time gpg --symmetric archivo_grande.mp4
   ```

```shell
time gpg –v –a –o /tmp/archivo.cifrado --encrypt --recipient cuenta@correo.es archivo_grande.mp4
   ```
   


> **Pregunta final:** Después de usar ambos tipos de cifrado (simétrico y asimétrico), ¿qué ventajas observas en el cifrado simétrico frente al asimétrico?


### Consejos y Buenas Prácticas

- **Mantén tu clave privada segura**. Nunca la compartas ni la subas a servidores de claves.
- **Revoca y actualiza tu clave** si sospechas que ha sido comprometida.
- Usa fechas de expiración para limitar el posible uso fraudulento en caso de verse comprometida.
- Usa siempre una **contraseña segura** para proteger tu clave privada.

## Bibliografía

- [The GNU Privacy Guard ](https://www.gnupg.org/)
* [Manual de GnuPG](https://www.gnupg.org/gph/es/manual.html)
* [Gpg4win](https://www.gpg4win.org/download.html)
* [RFC4880](https://tools.ietf.org/html/rfc4880)
* [PGP](https://es.wikipedia.org/wiki/Pretty_Good_Privacy)