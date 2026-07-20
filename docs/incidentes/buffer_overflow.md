

## Buffer Overflow

Un buffer overflow ocurre cuando un programa escribe más datos en un buffer (área de memoria) de los que puede contener. Esto provoca que los datos excedentes sobreescriban áreas de memoria adyacentes, corrompiendo o sobrescribiendo datos existentes[](https://book.hacktricks.xyz/binary-exploitation/stack-overflow).Características principales:

- Suele ocurrir en lenguajes como **C y C++** que no realizan comprobación de límites automáticamente.
- Permite sobrescribir variables y estructuras de control importantes.
- Puede llevar a la ejecución de código arbitrario si se sobrescribe el puntero de instrucción.
- Comúnmente explotado en funciones vulnerables como strcpy(), gets(), sprintf(), etc[](https://book.hacktricks.xyz/binary-exploitation/stack-overflow).

## Stack Overflow

El stack overflow es un tipo específico de buffer overflow que ocurre cuando se sobrescribe memoria en la pila (stack) del programa[](https://en.wikipedia.org/wiki/Stack_buffer_overflow).Aspectos clave:

- Afecta a variables locales y parámetros de funciones almacenados en la pila.
- Permite sobrescribir la dirección de retorno de una función, alterando el flujo de ejecución.
- Muy peligroso ya que la pila contiene direcciones de retorno de todas las funciones activas[](https://en.wikipedia.org/wiki/Stack_buffer_overflow).
- Puede ser explotado para ejecutar código malicioso inyectado en la pila.

## Impacto y explotación

Ambas vulnerabilidades pueden tener graves consecuencias:

- Ejecución de código arbitrario con los privilegios del programa vulnerable.
- Corrupción de datos y crash del programa.
- Escalada de privilegios si el programa corre con permisos elevados.

Los atacantes suelen explotar estas vulnerabilidades mediante:

- Inyección de shellcode malicioso en el buffer.
- Sobrescritura de punteros de función o direcciones de retorno.
- Técnicas como ROP (Return Oriented Programming) para evadir protecciones[](https://www.rapid7.com/blog/post/2019/02/19/stack-based-buffer-overflow-attacks-what-you-need-to-know/).

## Mitigaciones

Algunas medidas para prevenir estos ataques incluyen:

- Uso de funciones seguras que validen límites de buffers.
- Implementación de canarios de pila (stack canaries).
- Aleatorización del espacio de direcciones (ASLR).
- Marcado de áreas de memoria como no ejecutables.
- Compilación con protecciones como stack cookies y SafeSEH[](https://www.rapid7.com/blog/post/2019/02/19/stack-based-buffer-overflow-attacks-what-you-need-to-know/).

En resumen, los buffer y stack overflows son vulnerabilidades críticas que requieren atención cuidadosa por parte de desarrolladores y administradores de sistemas para prevenirlas y mitigarlas adecuadamente.


## Lenguajes más vulnerables

Los buffer overflows y stack overflows afectan principalmente a los siguientes lenguajes de programación:

- **C**: Es el lenguaje más susceptible a buffer overflows debido a que no realiza comprobaciones automáticas de límites de arrays y permite manipulación directa de punteros[](https://revista.seguridad.unam.mx/numero23/uno-de-los-cl-sicos-buffer-overflow).
- **C++**: Aunque tiene algunas protecciones adicionales, sigue siendo vulnerable si se usan características de bajo nivel similares a C[](https://revista.seguridad.unam.mx/numero23/uno-de-los-cl-sicos-buffer-overflow).

## Lenguajes parcialmente afectados

- **Ensamblador**: Al ser de muy bajo nivel, permite acceso directo a memoria y es propenso a estos errores si no se maneja cuidadosamente.
- **Fortran**: Versiones antiguas pueden ser vulnerables en ciertas circunstancias.

## Lenguajes generalmente seguros

- **Java**
- **C#**
- **Python**
- **Ruby**
- **JavaScript**

Estos lenguajes de alto nivel implementan comprobaciones automáticas de límites y gestión de memoria que previenen la mayoría de buffer overflows[](https://jaymonsecurity.es/reversing-buffer-overflow-format-string/)