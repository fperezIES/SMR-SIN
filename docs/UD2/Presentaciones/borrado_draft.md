
## Borrado de datos

### Borrado de ficheros de disco

* El SO marca como vacíos los sectores que alojaban dicho fichero 
	* Quita el puntero del fichero correspondiente
* **No se borran físicamente los sectores** ocupados por el fichero 

* Se borrará cuando se escriba encima nuevos datos.


* **Es posible recuperar la información borrada **
	* Existen diversas **utilidades** que facilitan esta tarea
* Si borramos algo por error:
	* Debemos apagar el ordenador (para evitar escrituras)
	* Arrancar con un sistema que permita buscar archivos en disco.  
	
### Borrado seguro

* Para eliminar información confidencial o sensible
	* No es suficiente borrar o formatear
* Existen varios métodos, el método **Gutmann** es el más complejo y completo:
["Secure Deletion of Data from Magnetic and Solid-State Memory"](https://www.cs.auckland.ac.nz/~pgut001/pubs/secure_del.html) de Gutmann
	* Consiste en escribir sobre los datos 35 patrones diferentes
	* Hace extremadamente difícil recuperar el contenido original.
* En discos actuales con dos o tres escrituras de datos aleatorios es suficiente.

>El borrado seguro de datos es una **medida de seguridad activa**
>
>* Evita acceso a datos en discos desechados 


### Programas para borrado seguro (Windows)

* **Eraser** 
	* https://eraser.heidi.ie/](https://eraser.heidi.ie/))
	* software libre con licencia GPL e incluye el método Gutmann. 

  
* **SDelete** 
	* [https://docs.microsoft.com/en-us/sysinternals/downloads/sdelete](https://docs.microsoft.com/en-us/sysinternals/downloads/sdelete)
	*  Implementa estándar DOD 5220.22-M de limpieza y sanitización del Pentágono.

### Borrado seguro en GNU/Linux

Paquete **secure-delete** 

* se basa en el paper  de Gutmann
	
* Contiene:
	* **srm y sfill**
		* para borrar de forma segura ficheros o espacio libre en nuestro sistema
	* **sdmem**
		*  para borrar de forma segura la memoria RAM del sistema
	* **sswap**
		* para borrar de forma segura la memoria swap del sistema

