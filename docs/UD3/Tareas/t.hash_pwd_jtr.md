---
icon: material/file-edit
---

# Práctica: Fortaleza de contraseñas en Linux

## Introducción

En esta práctica utilizaremos la herramienta **John the Ripper** (JtR) para comprobar la fortaleza de las contraseñas de las cuentas de usuario en un sistema Linux.

Para entender el proceso, estudiaremos cómo se almacenan las contraseñas y los usuarios en un sistema operativo basado en Linux, y luego utilizaremos JtR para intentar descubrir las contraseñas de usuarios que hemos creado previamente.

![JtR logo](../img/John-the-Ripper-Logo.jpg){:style="width: 50%;" class="center"}

**John the Ripper** ([https://www.openwall.com/john/](https://www.openwall.com/john/)) es una herramienta de código abierto para la auditoría de seguridad y recuperación de contraseñas. Su versión Jumbo admite una gran variedad de tipos de cifrado y hash en múltiples plataformas, incluidos sistemas Linux, macOS, Windows, aplicaciones web y muchos más. A diferencia de otras herramientas, JtR no se limita a utilizar ataques de fuerza bruta, sino que emplea varios modos más avanzados, como el **single crack**, que permite un análisis más rápido e inteligente de las contraseñas más comunes e inseguras.

## Preparativos: Configuración de AlmaLinux

Usaremos **AlmaLinux** en esta práctica, ya que es una distribución basada en RHEL (Red Hat Enterprise Linux), que es muy común en entornos empresariales.

## Gestión de usuarios en AlmaLinux

A continuación, repasamos algunos comandos útiles que utilizaremos durante la práctica:

* Para añadir un usuario en AlmaLinux:

  ```sh
  sudo adduser nombreUsuario
  ```

* Para cambiar la contraseña del usuario actual:

  ```sh
  passwd
  ```

* Para cambiar la contraseña de otro usuario:

  ```sh
  sudo passwd nombreUsuario
  ```

* Para forzar a un usuario a cambiar la contraseña en su próximo inicio de sesión:

  ```sh
  sudo chage -d 0 nombreUsuario
  ```

## Almacenamiento de usuarios y contraseñas en AlmaLinux

Al igual que en otras distribuciones Linux, AlmaLinux almacena la información de usuarios y contraseñas en los archivos `/etc/passwd` y `/etc/shadow`. Veamos en detalle el contenido y propósito de estos archivos.

1. Crea los siguientes usuarios con las contraseñas indicadas:
   - Usuario **pablo** con contraseña **123456**.
   - Usuario **pedro** con contraseña **pedro123**.

   Estos usuarios tienen contraseñas comunes y vulnerables, lo que nos permitirá probar JtR más fácilmente.

2. Verifica los usuarios existentes mostrando el contenido del archivo `/etc/passwd`:

```sh
sudo cat /etc/passwd
sudo cat /etc/shadow
```



El fichero  `/etc/shadow` tiene 9 campos separados por `:`.

`User : Encrypted Password : Date of last change password : Min. password age : Max. password age : Password warning period : Password interactive period : Account expiration date : Reserved field`

En el campo ***encrypted Password*** (segundo campo) podemos distinguir separado por  `$`:

`$ id $ Salt $ Encrypted  Password`


|  **Id** 	|  **Hash Algorithm Name**	|   **Length**	|
|---	|---	|---	|
|  1 	|   MD5	|  	22 characters 	|
|  2a 	|  Blow Fish	 	|   	|
|  5 	|   SHA 256 (since glibc 2.7)	|  43 characters 	|
|  6 	|   SHA 512 (since glibc 2.7)	|   86 characters	|
 
El `Salt` y el  `Encrypted Password` están formados por **[a-z A-Z 0-9 . /]**



   > **Tareas**:
   > 
   > - Muestra una captura en la que se vea que los usuarios creados aparecen en el archivo `/etc/passwd`.
   > - Investiga el significado de cada campo en una línea del archivo `/etc/passwd`.
   > - Verifica los permisos de los archivos `/etc/passwd` y `/etc/shadow` con el comando `ls -l`. **Explica las diferencias en los permisos**.
   > - Muestra una captura del archivo `/etc/shadow` que almacena las contraseñas. ¿Qué tipo de hash se utiliza? 


> **Contesta a las siguientes preguntas:**
> 
> 3. **Hash de contraseñas**: Investiga qué es una función **hash** y su utilidad en la seguridad de contraseñas.
> 
> 4. **Rainbow Tables**: ¿Qué son las **rainbow tables** y cómo pueden utilizarse para descifrar contraseñas?
> 
> 5. Crea dos usuarios (`user1` y `user2`) con la misma contraseña (`flower`). Verifica si el hash generado para ambos usuarios es el mismo o diferente, y explica por qué ocurre esto.
> 
> 6. **Salted hash**: Investiga qué es un **salted hash** y por qué es importante en la seguridad de contraseñas.

 
## Instalación de John the Ripper en AlmaLinux

No disponemos de un paquete pre-compilado en los repositorios de AlmaLinux, así que tenemos que descargar el código y compilarlo nosotros mismos:

Descargamos desde repositorio oficial:
```bash
git clone --depth=1 https://github.com/openwall/john
```

 Instalamos requisitos:
```bash
sudo dnf install -y openssl-devel
sudo dnf install make
sudo dnf groupinstall "Development Tools"
```

Compilamos el código:
```sh
cd john/src
./configure
make
```

 Comprobamos si funciona: hacemos pruebas de rendimiento:
```bash
cd ../run/
./john --test
```



   > **Tareas**:
   > 
   > - Muestra una captura del rendimiento de diferentes tipos de cifrado en tu máquina.

## Crackeando contraseñas

Para crackear las contraseñas de los usuarios, primero combinaremos los contenidos de `/etc/passwd` y `/etc/shadow` en un único archivo con el comando `unshadow`, que encontrarás en la misma carpeta que el comando `john`:

```sh
sudo ./unshadow /etc/passwd /etc/shadow > unshadowed.txt
```

1. Ejecuta el comando y verifica el contenido del archivo `unshadowed.txt` para los usuarios `pablo` y `pedro`, **edita el fichero y deja únicamente las líneas de los usuarios que te interesan**.

2. Inicia el proceso de cracking con **John the Ripper**:

   ```sh
   sudo ./john unshadowed.txt
   ```

   Este comando ejecuta varios modos de cracking de contraseñas de forma secuencial. JtR puede funcionar en varios modos, para saber más consultar la documentación y los tutoriales de la bibliografía.

3. Si la contraseña de un usuario es débil, **John the Ripper** la descifrará rápidamente. Si es fuerte, tardará más tiempo.


# Ejecución de varias instancias de JtR (opcional)

Aquí tienes el párrafo modificado con la explicación del símbolo `&`:

En este ejemplo, en lugar de utilizar una sola instancia de JtR, vamos a emplear dos. Esto aumentará la eficiencia, ya que JtR, en su compilación por defecto, se ejecuta en un único hilo. Para poder ejecutar dos instancias de JtR simultáneamente, es necesario utilizar el parámetro de sesión.

> Primero, crea dos copias del archivo generado con `unshadow` y edítalas de manera que en cada copia solo quede la línea correspondiente a uno de los usuarios cuya contraseña deseamos descifrar.
> 
> A continuación, ejecutaremos un proceso de JtR para cada archivo, asignando una sesión distinta. Para ello, puedes emplear los siguientes comandos:
> 
> ```sh
> sudo john --session=s1 nombreFichero1 &
> ```
> 
> ```sh
> sudo john --session=s2 nombreFichero2 &
> ```

 El símbolo `&` al final de cada comando indica que el proceso se ejecutará en segundo plano (background), lo que permite continuar utilizando la terminal mientras JtR sigue funcionando. De esta manera, ambos procesos se ejecutarán simultáneamente sin bloquear el uso de la terminal.



### Consultar las contraseñas crackeadas

Para ver las contraseñas que ha descifrado John the Ripper, utilizamos el siguiente comando:

```sh
sudo ./john --show unshadowed.txt
```

> **Tareas**:
> 
> - Muestra una captura en la que se vean las contraseñas obtenidas.

## Conclusiones

Al finalizar esta práctica, deberías ser capaz de responder a las siguientes preguntas:

1. ¿Qué es John the Ripper y para qué se utiliza?
2. ¿Por qué se utiliza el archivo `/etc/shadow` para almacenar contraseñas?
3. ¿Cómo se almacena una contraseña en dicho archivo? Explica el formato de almacenamiento.

### Consideraciones legales y éticas:

Es importante recordar que el uso de **John the Ripper** para crackear contraseñas sin autorización es ilegal y antiético. Solo debes utilizar estas técnicas en entornos controlados, como pruebas de penetración autorizadas o en entornos educativos con los permisos adecuados.

## Bibliografía

- [Página oficial de John the Ripper password Cracker](https://www.openwall.com/john/)
- [Página the GitHub de John the Ripper](https://github.com/openwall/john)
	- [Documentación GitHub John the Ripper](https://github.com/openwall/john/tree/bleeding-jumbo/doc)
		- [Instrucciones de instalación](https://github.com/openwall/john/blob/bleeding-jumbo/doc/INSTALL)
- [Tutorial JtR ccatyberciti](https://www.cyberciti.biz/faq/unix-linux-password-cracking-john-the-ripper/)
- [Tutorial JtR RedesZone.net](https://www.redeszone.net/seguridad-informatica/john-the-ripper/)
- [Tutorial JtR FreeCodeCamp](https://www.freecodecamp.org/news/crack-passwords-using-john-the-ripper-pentesting-tutorial/)
- [Formato de shadow slashroot](https://www.slashroot.in/how-are-passwords-stored-linux-understanding-hashing-shadow-utils)
- [Formato de shadow linuxize](https://linuxize.com/post/etc-shadow-file/)
- [Chuleta de uso de Git](https://education.github.com/git-cheat-sheet-education.pdf)

<!--
https://gist.github.com/goffinet/83565ebec963fed0c74d
-->

