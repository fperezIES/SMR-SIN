---
icon: material/file-edit
---


# Ejercicio: Comprobación de ficheros y URLs con VirtusTotal.com

En esta práctica nos familiarizaremos con **VirusTotal**, un servicio en línea ampliamente reconocido para el análisis de archivos y URLs en busca de malware y otras amenazas de seguridad. Lanzado inicialmente por Bernardo Quintero, uno de los fundadores de "Una-al-día" de Hispasec, VirusTotal ha sido propiedad de Google desde 2012. Este potente recurso permite a los usuarios subir archivos o enlaces sospechosos, los cuales son examinados por múltiples motores antivirus y herramientas de detección. Al proporcionar un informe detallado de posibles infecciones o actividades maliciosas, VirusTotal se ha convertido en una herramienta esencial para profesionales de la seguridad y usuarios preocupados por proteger sus sistemas. A lo largo de esta práctica, utilizaremos VirusTotal para analizar diversos archivos y enlaces, lo que nos ayudará a comprender mejor su funcionamiento y la importancia de utilizar múltiples fuentes para la detección eficaz de amenazas.

Para realizar la práctica, necesitarás dos archivos comprimidos que contienen malware. Puedes descargarlos en un archivo ZIP con contraseña [aquí](virustotal.zip). La contraseña para descomprimir los archivos es "**virustotal**".

1. **Conéctate a la página de VirusTotal:** [https://virustotal.com](https://virustotal.com/).
    
2. **Sube el archivo** **Pwdump7.zip** desde el formulario que aparece.
    
3. **Solicita un nuevo análisis** y captura el resultado obtenido.
    
4. **Comprime el archivo Pwdump7 varias veces en cascada** para ofuscarlo (comprime tres o cuatro veces más, de modo que quede algo como Pwdump7.zip.zip.zip). Vuelve a analizarlo y observa si hay diferencias. Esto te demostrará la calidad de algunos motores antivirus.
    
5. **Realiza los mismos pasos con el malware de muestra** **eicar.com** que está en Moodle y compara los resultados. Es posible que tengas que desactivar temporalmente tu antivirus para poder descargarlo.
    
6. **Sube cualquier otro archivo ejecutable o instalador** que tengas en tu equipo (o descargado de Internet), realiza el análisis y compáralo con el anterior.
    
7. **Recientemente recibí un SMS con el siguiente enlace** (no lo abras porque está identificado como phishing):
    
    > "Su paquete ha sido puesto en espera debido a que falta un número de calle en el paquete. Por favor, actualice la información de entrega: https://rebrand.ly/3Correos"
    
    Copia y pega esta dirección en el **analizador de URL** de VirusTotal y revisa el resultado.
    
8. **Analiza alguna URL que te parezca sospechosa** y observa los resultados.
    
9. **Reflexiona:** Si fueras un desarrollador de malware, ¿para qué usarías VirusTotal?