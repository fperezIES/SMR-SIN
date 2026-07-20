---
icon: material/file-edit
---


# Establecimiento de Políticas de Contraseñas y Gestión de Permisos en AlmaLinux 9

## Introducción

En este documento, exploraremos cómo establecer políticas de contraseñas adecuadas en AlmaLinux 9 y comprenderemos el sistema de permisos en Linux. La configuración adecuada de políticas de contraseñas y el entendimiento del sistema de permisos en Linux son fundamentales para mantener la seguridad y el control de acceso en AlmaLinux 9. A través de PAM y la gestión cuidadosa de propietarios, grupos y permisos, los administradores pueden proteger eficazmente los recursos del sistema y garantizar que solo los usuarios autorizados tengan acceso a información y funcionalidades sensibles.

## Establecimiento de Políticas de Contraseñas en AlmaLinux 9

### 2.1 Introducción a la Seguridad de Contraseñas

Las contraseñas son la primera línea de defensa contra accesos no autorizados. Establecer políticas robustas de contraseñas es crucial para proteger los sistemas y datos sensibles. En AlmaLinux 9, la gestión de políticas de contraseñas se realiza principalmente a través de PAM (Pluggable Authentication Modules) y el archivo de configuración `pwquality.conf`.

### 2.2 Configuración de Políticas con PAM

PAM es un marco flexible que maneja la autenticación de usuarios en sistemas Linux. El módulo `pam_pwquality.so` permite imponer políticas de contraseñas como longitud mínima, complejidad y reutilización.

#### Ubicación de Archivos Clave

- **Archivo de configuración de políticas de contraseñas**: `/etc/security/pwquality.conf`
- **Configuración de PAM para contraseñas**: `/etc/pam.d/system-auth`

### 2.3 Parámetros Clave de Configuración

Al configurar políticas de contraseñas, los siguientes parámetros son esenciales:

- `minlen`: Longitud mínima de la contraseña.
- `dcredit`: Requisito de dígitos (números).
- `ucredit`: Requisito de letras mayúsculas.
- `lcredit`: Requisito de letras minúsculas.
- `ocredit`: Requisito de caracteres especiales.
- `difok`: Número mínimo de caracteres diferentes de la contraseña anterior.
- `remember`: Cantidad de contraseñas antiguas a recordar para evitar reutilización.
- `maxrepeat`: Máximo de caracteres repetidos consecutivos.

### 2.4 Parámetros de Bloqueo de Cuenta por Intentos Fallidos

Para evitar intentos de acceso no autorizados por fuerza bruta, es importante definir un límite de intentos fallidos antes de bloquear temporalmente una cuenta. Esto se configura mediante el módulo `pam_faillock.so`.

#### Configuración de `pam_faillock` para Intentos Fallidos

Edita el archivo `/etc/pam.d/system-auth` y añade o modifica las siguientes líneas en la sección de autenticación:

```bash
auth required pam_faillock.so preauth silent deny=3 unlock_time=600
auth [default=die] pam_faillock.so authfail deny=3 unlock_time=600
```

- `deny=3`: Número máximo de intentos fallidos antes de bloquear la cuenta (en este caso, 3 intentos).
- `unlock_time=600`: Tiempo (en segundos) que la cuenta permanecerá bloqueada antes de desbloquearse automáticamente (600 segundos equivalen a 10 minutos).

> **Nota**: Estos valores pueden ajustarse según las políticas de seguridad de la organización.

Para restablecer el contador de intentos fallidos de un usuario, usa:

```bash
faillock --user usuario --reset
```

### 2.5 Ejemplos de Configuración de Políticas de Contraseña

#### Establecer Longitud Mínima de Contraseña

Para requerir una longitud mínima de 12 caracteres, edite `/etc/security/pwquality.conf`:

```bash
minlen = 12
```

#### Exigir Complejidad en Contraseñas

Para requerir al menos una letra mayúscula, una minúscula, un dígito y un carácter especial:

```bash
ucredit = -1
lcredit = -1
dcredit = -1
ocredit = -1
```

#### Evitar Reutilización de Contraseñas

Para recordar las últimas 5 contraseñas:

Edite `/etc/pam.d/system-auth` y agregue en la sección `password`:

```bash
password requisite pam_pwhistory.so use_authtok remember=5
```

#### Configurar Caducidad de Contraseñas

Para establecer que las contraseñas caduquen cada 90 días, use:

```bash
chage -M 90 usuario
```

Para establecerlo globalmente, edite `/etc/login.defs`:

```bash
PASS_MAX_DAYS 90
```

### 2.6 Aplicación y Verificación de Políticas

Después de configurar las políticas, pruebe cambiando la contraseña de un usuario:

```bash
passwd usuario
```

Intente establecer una contraseña que no cumpla con las políticas para verificar que el sistema la rechaza.

## Sistema de Permisos en Linux

### 3.1 Fundamentos de Propietario, Grupo y Otros

Cada archivo y directorio en Linux tiene asociado un propietario, un grupo y permisos asignados a tres categorías:

- **Propietario (User)**: El usuario que posee el archivo.
- **Grupo (Group)**: Un grupo de usuarios con permisos comunes.
- **Otros (Others)**: Cualquier otro usuario no incluido en las categorías anteriores.

### 3.2 Permisos Básicos

- **Lectura (r)**: Permite leer el contenido.
- **Escritura (w)**: Permite modificar el contenido.
- **Ejecución (x)**: Permite ejecutar archivos o acceder a directorios.

### 3.3 Visualización de Permisos

Usa `ls -l` para listar archivos con detalles:

```bash
ls -l
```

Salida de ejemplo:

```bash
-rw-r--r-- 1 juan desarrollo 1024 Oct 10 12:00 documento.txt
```

#### Interpretación:

- `-rw-r--r--`: Permisos del archivo.
  - `-`: Tipo de archivo (archivo regular).
  - `rw-`: Propietario puede leer y escribir.
  - `r--`: Grupo puede leer.
  - `r--`: Otros pueden leer.

### 3.4 Cambio de Propietario de Fichero

#### Uso del Comando `chown`

Sintaxis:

```bash
chown [opciones] propietario[:grupo] archivo
```

#### Ejemplos:

- Cambiar propietario:

  ```bash
  chown maria documento.txt
  ```

- Cambiar propietario y grupo:

  ```bash
  chown maria:ventas documento.txt
  ```

- Cambiar solo grupo:

  ```bash
  chown :ventas documento.txt
  ```

### 3.5 Cambio de Permisos

#### Uso del Comando `chmod`

Sintaxis:

```bash
chmod [opciones] modo archivo
```

#### Modos de Permisos

- **Simbólico**: Usa letras y signos.

  - Añadir permiso de ejecución al propietario:

    ```bash
    chmod u+x script.sh
    ```

  - Quitar permiso de lectura al grupo:

    ```bash
    chmod g-r documento.txt
    ```

- **Numérico (Octal)**: Usa números para representar permisos.

  - Valores:
    - Lectura (r): 4
    - Escritura (w): 2
    - Ejecución (x): 1

#### Ejemplos:

- Permisos completos al propietario, lectura y ejecución al grupo, sin permisos a otros:

  ```bash
  chmod 750 programa.bin
  ```

- Permisos de lectura y escritura al propietario y grupo, solo lectura a otros:

  ```bash
  chmod 664 informe.pdf
  ```

### 3.6 Utilidad y Gestión de Grupos

#### Creación de Grupos

Crear un nuevo grupo:

```bash
groupadd desarrolladores
```

#### Modificación de Grupos

Cambiar el nombre de un grupo:

```bash
groupmod -n nuevos_desarrolladores desarrolladores
```

#### Eliminación de Grupos

Eliminar un grupo existente:

```bash
groupdel antiguos_desarrolladores
```

#### Añadir Usuarios a Grupos

Agregar un usuario a un grupo secundario:

```bash
usermod -aG desarrolladores juan
```

> **Nota**: El parámetro `-a` es crucial para no eliminar al usuario de otros grupos secundarios.

#### Ver Grupos de un Usuario

Mostrar los grupos a los que pertenece un usuario:

```bash
groups juan
```



## Referencias Adicionales

- Manual de PAM: `man pam`
- Manual de chown: `man chown`
- Manual de chmod: `man chmod`
- Manual de groupadd, groupmod, groupdel: `man groupadd`, `man groupmod`, `man groupdel`
- Documentación oficial de AlmaLinux: [https://almalinux.org](https://almalinux.org)