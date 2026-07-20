
## **UD3 - Criptografía**

### 1. **Introducción a la criptografía**
   - **Historia de la criptografía**: Desde el cifrado de César hasta los sistemas modernos
   - **Objetivos de la criptografía**: Confidencialidad, integridad, autenticación, no repudio
   - **Terminología básica**: Algoritmo, clave, texto en claro, texto cifrado
   - **Criptografía clásica vs moderna**: Códigos manuales frente a algoritmos computacionales
   - **Criptografía de clave secreta vs clave pública**: Diferencias y aplicaciones

### 2. **Cifrado simétrico**
   - **Concepto de cifrado simétrico**: Una sola clave para cifrado y descifrado
   - **Algoritmos de cifrado simétrico**:
     - **DES**: Historia y vulnerabilidades
     - **3DES**: Evolución de DES
     - **AES (Advanced Encryption Standard)**: El estándar actual
   - **Modos de operación**: ECB, CBC, CFB, OFB, GCM (diferencias y cuándo utilizarlos)
   - **Ventajas y desventajas del cifrado simétrico**: Velocidad vs gestión de claves
   - **Casos de uso**: Cifrado de archivos, comunicaciones internas, VPNs

### 3. **Cifrado asimétrico**
   - **Concepto de cifrado asimétrico**: Uso de claves pública y privada
   - **Algoritmos de cifrado asimétrico**:
     - **RSA**: Funcionamiento y uso en firma y cifrado
     - **Diffie-Hellman**: Intercambio de claves de manera segura
     - **ElGamal**: Alternativa a RSA
     - **ECC (Elliptic Curve Cryptography)**: Cifrado de curva elíptica para mayor seguridad con claves más cortas
   - **Ventajas y desventajas del cifrado asimétrico**: Seguridad vs rendimiento
   - **Casos de uso**: Envío seguro de mensajes, intercambio de claves, certificados digitales

### 4. **Infraestructura de clave pública (PKI)**
   - **Concepto de PKI**: Organización para la gestión de claves públicas y privadas
   - **Certificados digitales**:
     - **Autoridades de certificación (CA)**: Emisión y revocación de certificados
     - **Certificados X.509**: Estructura y campos importantes
     - **Cadenas de confianza**: Verificación de la autenticidad de las CA
   - **Proceso de generación de certificados**: Claves públicas, solicitud de firma de certificado (CSR)
   - **Firma digital**: Garantía de integridad y autenticidad
   - **Sistemas de revocación de certificados (CRL y OCSP)**: Gestión de certificados comprometidos
   - **Aplicaciones de la PKI**: HTTPS, VPNs, correo electrónico seguro, autenticación de usuarios

### 5. **Funciones hash y autenticación**
   - **Concepto de funciones hash**: Resumen criptográfico de un mensaje
   - **Algoritmos hash comunes**:
     - **MD5**: Vulnerabilidades y por qué se ha dejado de usar
     - **SHA-1**: Problemas de colisión
     - **SHA-2 y SHA-3**: Estándares actuales
   - **Casos de uso de las funciones hash**: Verificación de integridad de archivos, firmas digitales
   - **HMAC (Hash-based Message Authentication Code)**: Verificación de autenticidad junto con integridad

### 6. **Criptografía aplicada**
   - **TLS/SSL**: Uso de criptografía para asegurar las comunicaciones en Internet
   - **PGP/GPG**: Criptografía para correos electrónicos y archivos
   - **Blockchain**: Criptografía en la base de los sistemas de registro distribuido
   - **Criptografía cuántica**: El futuro de la criptografía ante los ataques de ordenadores cuánticos

---

### **Justificación de las adiciones:**

1. **Introducción a la criptografía** se expande con un enfoque histórico y técnico, proporcionando una base sólida para los estudiantes que no están familiarizados con el tema.
   
2. **Cifrado simétrico** detalla los principales algoritmos y modos de operación, como AES y CBC, para asegurar que los alumnos comprendan cómo y cuándo usar cada uno de ellos en un contexto real.

3. **Cifrado asimétrico** se complementa con algoritmos importantes como RSA, Diffie-Hellman y ECC, y explica el intercambio de claves, que es esencial en sistemas como HTTPS y correo seguro.

4. **Infraestructura de clave pública (PKI)** proporciona una visión global de cómo se gestionan las claves públicas y los certificados digitales, algo fundamental en el cifrado asimétrico y en aplicaciones como HTTPS.

5. **Funciones hash y autenticación** es crucial para explicar cómo se asegura la integridad de los datos y mensajes en sistemas de autenticación y firmas digitales.

6. **Criptografía aplicada** cubre ejemplos concretos de cómo la criptografía se utiliza en herramientas y tecnologías reales como **TLS/SSL**, **PGP**, y **blockchain**, dando a los estudiantes un contexto práctico.

