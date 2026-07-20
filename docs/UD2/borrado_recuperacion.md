
## Borrado y Recuperación de Datos

### Borrado de Datos

Cuando se borra un archivo en un sistema operativo, normalmente solo se elimina el puntero al archivo, marcando los sectores como disponibles. Los datos permanecen en el disco hasta que se sobrescriben, lo que permite su recuperación con herramientas especializadas.

Para eliminar información confidencial o sensible, es necesario utilizar métodos de borrado seguro que sobrescriban los datos originales, impidiendo su recuperación.

#### Métodos de Borrado Seguro

* **Sobrescritura Múltiple:** Escribir datos aleatorios sobre el espacio del archivo varias veces.
    * **Método de Gutmann:** Sobrescribe el área con 35 patrones diferentes. Es considerado uno de los métodos más seguros pero también el más lento.
    * **Estándar DoD 5220.22-M:** Estándar del Departamento de Defensa de EE.UU. que especifica sobrescrituras múltiples con diferentes patrones.
* **Herramientas de Software:**
    * **Eraser:** Software libre que implementa varios métodos de borrado seguro.
    * **SDelete:** Herramienta de Sysinternals que cumple con estándares de borrado seguro.
    * **Comando `dd` en Linux:** Puede usarse para sobrescribir un disco completo con ceros o datos aleatorios.
    * **`secure-delete` en Linux:** Paquete que incluye utilidades para borrar archivos, espacio libre, memoria RAM y swap de forma segura.
* **Destrucción Física:** Métodos como triturar, desmagnetizar o incinerar los discos, utilizados cuando el borrado lógico no es suficiente.

### Importancia del Borrado Seguro

El borrado seguro es una medida de seguridad activa que previene el acceso no autorizado a información confidencial. Es especialmente relevante al desechar dispositivos de almacenamiento o al reutilizar medios en entornos con información sensible.

### Recuperación de Datos

A pesar de los esfuerzos de borrado, existen técnicas avanzadas de recuperación de datos que pueden extraer información residual. Por ello, en casos donde la seguridad es crítica, se recomiendan métodos de destrucción física complementarios.
