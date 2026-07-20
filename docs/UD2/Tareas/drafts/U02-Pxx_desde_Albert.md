---
icon: material/file-edit
---



# Práctica Tema2 XX: Tareas sacadas de apuntes de Albert

@.	En este ejercicio tienes que calcular el espacio disponible cuando configuramos distintas combinaciones de discos en RAID

a)

* 1TB, 1TB, 1TB, 1TB en RAID 0
* 1TB, 1TB, 1TB, 1TB en RAID 1
* 1TB, 1TB, 1TB, 1TB en RAID 5
* 1TB, 1TB, 1TB, 1TB en RAID 6

a)

* 1TB, 2TB, 3TB, 4TB en RAID 0
* 1TB, 2TB, 3TB, 4TB en RAID 1
* 1TB, 2TB, 3TB, 4TB en RAID 5
* 1TB, 2TB, 3TB, 4TB en RAID 6

a)

* 4TB, 1.5TB, 4TB, 4TB en RAID 0
* 4TB, 1.5TB, 4TB, 4TB en RAID 1
* 4TB, 1.5TB, 4TB, 4TB en RAID 5
* 4TB, 1.5TB, 4TB, 4TB en RAID 6

@.	Explica la diferencia entre RAID 0 y RAID 5, indica para qué usos crees que pueden resultar convenientes.

> **Ejercicios propuestos**
>
> **2.1.1.** Estudia las instalaciones de tu centro. Dadas las características del mismo, ¿dónde crees que podría instalarse un pequeño CPD?
> 
> 
> 
> > **Ejercicios propuestos**
>
> **2.1.2.** Busca información sobre distintos sistemas de protección electrónica, sus aplicaciones y costes de implantación.
>
> **2.1.3.** Investiga acerca de los precios y características de periféricos como teclado, ratón con lector de huella, o lector de huella USB, así como el software compatible con ellos. Realiza una tabla resumen.

> **Ejercicios propuestos**
>
> **2.1.4.** Investiga sobre los distintos sistemas de climatización y contra incendios que podrían ser adecuados para el CPD de una pequeña empresa.
>

> **Ejercicios propuestos**
>
> **2.2.1.**  Un SAI tiene un precio elevado lo cual no suele ser asequible para un usuario doméstico. En el mercado existen alternativas más económicas para proteger los equipos contra subidas y picos de tensión. Busca información sobre estos equipos y selecciona el que te parezca más adecuado para tu ordenador personal. Puedes consultar las páginas de fabricantes como Emerson, APC, Eaton o Socomec.
> 
> **2.2.2.** Realiza una tabla comparativa que analice diferentes tipos y modelos de SAIs existentes en el mercado teniendo en cuenta el tiempo extra que proporcionan, si protegen contra picos de tensión, el número de equipos que se pueden conectar y el precio. Teniendo en cuenta estos parámetros, decide el SAI a comprar para:
> 
> * Un ordenador personal para un casa.
> * Un servidor de un centro pequeño como un instituto.
> * Un CPD con 10 servidores.
> 
> Si para el ordenador personal consideras que los SAIS en línea serían demasiado caros, indica a su vez otros dispositivos que puedan proteger un equipo de subidas o bajadas  de tensión.


## RAID

>
>
>> **Ejercicios propuestos**
>
> **2.4.1.** Dado el conjunto de 4 discos que muestra la figura, calcula los bloques de paridad y completa la figura.
> 
> ![RAID 5](imagenes/tema02_raid5ejercicio.jpg)\
>
> 
> * Imagina que se perdiera el disco 1. Llega una petición de lectura de la línea F, ¿podría realizarse? ¿cómo?
> * Una vez que se ha conseguido un disco adecuado se decide sustituir el disco dañado y recuperar el funcionamiento normal usando los 4 discos. Recupera la información de dicho disco utilizando solo la información de los discos 0, 2 y 3.
>




> **Ejercicios propuestos**
>
> **2.5.1.** Realiza un listado de sistemas operativos multiusuario y multiprocesador. Consulta en internet si pueden utilizarse para montar un cluster.
> 
> **2.5.2.** Busca información sobre las conexiones de alta velocidad mencionadas antes. ¿Qué velocidades proporcionan? ¿Qué requisitos hardware necesitamos para utilizarlas?
> 



## Ejercicios de comprobación

> **2.6.1.** ¿Qué características debe reunir un edificio donde queremos instalar un Centro de Proceso de Datos?
> 
> **2.6.2.** Una empresa de construcción ha decidido trasladar sus oficinas a un nuevo edificio. Estudia las distintas opciones y presenta un informe razonado sobre las ventajas e inconvenientes de los distintos edificios y cual sería el idóneo. 
> Realiza una tabla comparativa con todos los factores.
> 1) Se trata de 3 edificios situados en diferentes zonas, todas ellas seguras, en la misma área geográfica y sin riesgo de inundaciones y todos tienen un buen sistema antiincencios.
> 2) El primero de ellos está en el centro de la ciudad junto a una serie de edificios de oficinas y algunas viviendas.
> 3) El segundo se encuentra en un gran parque empresarial con múltiples oficinas pero donde no hay fábricas.
> 4) El tercero está en un polígono industrial donde hay fábricas de metalurgia, factorías de vehículos y otras serie de fábricas de maquinaria pesada.
> 5) Todos los edificios tienen control de acceso mediante guardias y cámaras.
> 6) En el edificio céntrico disponemos de 7 plantas en un edificio compartido con otras empresa.
> 7) En el parque empresarial disponemos de un edificio de 6 plantas.
> 8) En el polígono industrial de un edificio de 2 plantas.
> 9) Las tres zonas están bien  comunicadas y las instalaciones no presentan problemas eléctricos.
> 10) En el edificio del centro se tendrán que realizar reformas para amortiguar los sonidos.
> 11) La empresa ha decidido realizar la inversión necesaria para instalar sistemas de control de acceso biométrico al CPD y sistemas de alimentación necesarios.
> 
> **2.6.3.** ¿Qué diferencias hay entre sensores y detectores?
> 
> **2.6.4.** ¿Es posible que el hardware funcione sin corriente eléctrica? Razona tu respuesta.
> 
> **2.6.5.** Explica paso a paso como funciona un SAI antes, durante y después de una caída de la red eléctrica
>
> **2.6.6.** Busca información sobre el funcionamiento de los sistemas de detección precoz de incendios e ilústralo con casos reales de uso.
>
> **2.6.7.** Calcula el bloque de paridad de las siguientes líneas de datos en un sistema RAID 5:
>
>![](imagenes/tema02_Ejerc1Raid.jpg)\
> 
> **2.6.8.** Imaginemos la siguiente linea de datos en un sistema RAID 5 donde se acaba de estropear el disco 1. Recupera el bloque del disco que ha fallado para solucionar el problema.
> 
> ![](imagenes/tema02_ejerc2Raid.jpg)\

>

