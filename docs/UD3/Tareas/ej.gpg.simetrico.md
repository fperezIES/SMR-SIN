---
icon: material/file-edit
---


# Ejercicio: Cifrado simétrico con GnuPG

En esta práctica vamos a utilizar la herramienta GPG (GNU Privacy Guard) para realizar cifrado simétrico de mensajes.

Pretty Good Privacy (PGP privacidad bastante buena) es un programa originalmente desarrollado por Phil Zimmermann en 1991 y cuya finalidad es proteger la información distribuida a través de Internet mediante el uso de criptografía de clave pública, así como facilitar la autenticación de documentos gracias a firmas digitales.

La Internet Engineering Task Force se ha basado en el diseño de PGP para crear el estándar de Internet **OpenPGP** (RFC4880). GPG o GnuPG es una implementación completa y gratuita del estandar definido en la RFC4880.

![Logo gpg](../img/gnupg_logo.png){:style="width: 50%;" class="center"}


PGP es un criptosistema híbrido que **combina técnicas de criptografía simétrica y criptografía asimétrica.** Esta combinación permite aprovechar lo mejor de cada uno: **El cifrado simétrico es más rápido que el asimétrico** o de **clave pública**, mientras que este, a su vez, **proporciona una solución al problema de la distribución de claves** en forma segura y garantiza el no repudio de los datos y la no suplantación.

La herramienta GPG puede usarse para cifrado simétrico y asimétrico, pero **en esta práctica nos vamos a centrar únicamente en el simétrico.**

Para realizar esta práctica usaremos un sistema operativo Linux.

En **GPG**, el modo de cifrado se selecciona automáticamente según el algoritmo de cifrado utilizado, y no es algo que se configure explícitamente con GPG. Se usará el **modo CBC** (Cipher Block Chaining) de forma implicita.

## Objetivo:
Aprender a utilizar el cifrado simétrico con **GnuPG** para proteger información confidencial. En esta práctica, los alumnos cifrarán, descifrarán y verificarán la integridad de los archivos cifrados.


## Requisitos previos:
- Conocimientos básicos sobre el concepto de cifrado simétrico.
- Sistema operativo con **GnuPG** instalado. 

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


## Paso 1: Crear un archivo para cifrar
Primero, vamos a crear un archivo de texto que será cifrado. Este archivo simulará ser un documento confidencial.

1. Abre la terminal y navega a tu carpeta de trabajo (puedes crear una nueva si lo prefieres):
    ```bash
    mkdir ~/practica_gpg
    cd ~/practica_gpg
    ```

2. Crea un archivo de texto simple:
    ```bash
    echo "Este es un documento confidencial. No debe ser leído por personas no autorizadas." > archivo_confidencial.txt
    ```

3. Verifica que el archivo se ha creado correctamente:
    ```bash
    cat archivo_confidencial.txt
    ```

## Paso 2: Cifrar el archivo con una clave simétrica

El cifrado simétrico utiliza una sola contraseña (clave) para cifrar y descifrar la información. Usaremos el comando `gpg` para cifrar el archivo.

1. Para cifrar el archivo con una contraseña, ejecuta:
    ```bash
    gpg --symmetric archivo_confidencial.txt
    ```

2. El sistema te pedirá que ingreses una contraseña. Asegúrate de elegir una contraseña segura y de recordarla, ya que será necesaria para descifrar el archivo.

    ```plaintext
    Introduzca la frase de contraseña:
    ```

3. Una vez hecho esto, se creará un nuevo archivo con el sufijo `.gpg`:
    ```bash
    ls
    ```
    Verás algo como esto:
    ```plaintext
    archivo_confidencial.txt  archivo_confidencial.txt.gpg
    ```

## Paso 3: Verificar el archivo cifrado

El archivo original sigue existiendo, pero el archivo con extensión `.gpg` está cifrado. Puedes verificar el tipo de archivo utilizando el siguiente comando:

```bash
file archivo_confidencial.txt.gpg
```

Esto debería devolver algo similar a:
```plaintext
archivo_confidencial.txt.gpg: GPG symmetrically encrypted data (AES cipher)
```

## Paso 4: Eliminar el archivo original

Para asegurar la confidencialidad del archivo, es recomendable eliminar el archivo original no cifrado una vez que ya esté cifrado.

1. Elimina el archivo original:
    ```bash
    rm archivo_confidencial.txt
    ```

2. Verifica que solo queda el archivo cifrado:
    ```bash
    ls
    ```
    Deberías ver solamente el archivo cifrado:
    ```plaintext
    archivo_confidencial.txt.gpg
    ```

## Paso 5: Descifrar el archivo

Ahora, vamos a realizar el proceso inverso: descifrar el archivo para volver a obtener el contenido original.

1. Para descifrar el archivo cifrado, utiliza el siguiente comando:
    ```bash
    gpg --decrypt archivo_confidencial.txt.gpg
    ```

2. Se te pedirá que ingreses la contraseña que utilizaste para cifrar el archivo.

3. El contenido del archivo será mostrado en la terminal, y si deseas guardar el archivo descifrado en un archivo de texto, puedes redirigir la salida a un nuevo archivo:
    ```bash
    gpg --output archivo_descifrado.txt --decrypt archivo_confidencial.txt.gpg
    ```

4. Verifica el contenido del archivo descifrado:
    ```bash
    cat archivo_descifrado.txt
    ```

## Paso 6: Limpieza

Para finalizar la práctica, puedes eliminar todos los archivos creados durante la sesión, si así lo deseas:

```bash
rm archivo_confidencial.txt.gpg archivo_descifrado.txt
```



# Preguntas de reflexión:

> 1. ¿Por qué es importante usar una contraseña segura para el cifrado simétrico?
> 2. ¿Qué ventajas y desventajas tiene el cifrado simétrico frente al cifrado asimétrico?
> 3. ¿Qué sucede si olvidamos la contraseña que usamos para cifrar un archivo?

---

# Tareas adicionales:

1. **Cambiar el algoritmo de cifrado:** Por defecto, GnuPG utiliza el cifrado AES. Investiga cómo cambiar el algoritmo de cifrado utilizado por GPG (por ejemplo, usando CAST5) y realiza una prueba.
   
   Pista: utiliza la opción `--cipher-algo` en el comando de cifrado.

2. **Comparar tiempos de cifrado/descifrado:** Realiza pruebas cifrando archivos más grandes (como un archivo de vídeo o imagen) y compara el tiempo que tarda GnuPG en cifrar y descifrar. Puedes usar el comando `time` para medirlo:
   
   ```bash
   time gpg --symmetric archivo_grande.mp4
   ```



# Bibliografía

- [The GNU Privacy Guard ](https://www.gnupg.org/)
* [gpg man page](https://www.gnupg.org/documentation/manuals/gnupg24/gpg.1.html)
* [Gpg4win](https://www.gpg4win.org/download.html)
* [RFC4880](https://tools.ietf.org/html/rfc4880)
* [PGP](https://es.wikipedia.org/wiki/Pretty_Good_Privacy)
