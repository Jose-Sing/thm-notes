# Hashing Basics (Conceptos Básicos de Hashing)

1. ¿Qué es el Hashing?

----------------------

El _hashing_ es un proceso criptográfico unidireccional que toma una entrada de datos (o "mensaje") de cualquier longitud y produce una cadena de bytes de tamaño fijo, conocida como **hash** o **digest**. Este proceso enmascara el valor original de los datos.

**Características fundamentales de un buen algoritmo de hash:**

* **Unidireccional (One-way):** Es computacionalmente inviable revertir el proceso para obtener los datos originales a partir del hash.

* **Determinístico:** La misma entrada siempre producirá exactamente el mismo hash.

* **Efecto Avalancha (Avalanche Effect):** Un cambio minúsculo en la entrada (incluso un solo bit o letra) producirá un hash de salida completamente diferente.

* **Resistencia a Colisiones:** Debe ser extremadamente difícil encontrar dos entradas diferentes que produzcan el mismo hash exacto.

**Casos de uso principales:**

* Verificación de la integridad de los datos (ej. comprobar que un archivo descargado o transferido no ha sido alterado).

* Almacenamiento seguro de contraseñas.

* * *

2. Hashing vs. Codificación vs. Encriptación

--------------------------------------------

Es un error común confundir estos tres términos. Aquí tienes la diferencia clave para tenerla clara:

| **Concepto**                  | **Propósito Principal**                              | **¿Es Reversible?**                            | **Ejemplos Comunes**          |
| ----------------------------- | ---------------------------------------------------- | ---------------------------------------------- | ----------------------------- |
| **Hashing**                   | Integridad y validación.                             | **NO.** Es unidireccional.                     | MD5, SHA-1, SHA-256, Argon2.  |
| **Codificación (Encoding)**   | Usabilidad y compatibilidad de datos entre sistemas. | **SÍ.** No requiere clave, solo el algoritmo.  | Base64, Base32, ASCII, UTF-8. |
| **Encriptación (Encryption)** | Confidencialidad de los datos.                       | **SÍ.** Requiere una clave de desencriptación. | AES, RSA, DES.                |

_Nota sobre algoritmos comunes en la terminal:_ Herramientas como `md5sum`, `sha1sum`, `sha256sum` y `sha512sum` producen sus salidas en formato hexadecimal. MD5 y SHA-1 se consideran obsoletos para fines de seguridad debido a vulnerabilidades de colisión, pero aún se usan para verificaciones rápidas de integridad de archivos no críticos.

* * *

3. Prácticas Seguras para el Almacenamiento de Contraseñas

----------------------------------------------------------

Nunca se deben almacenar contraseñas en texto plano. El proceso seguro estándar involucra el uso de _Salting_ y algoritmos robustos:

1. **Selección del Algoritmo:** Se elige una función de derivación de claves robusta y lenta por diseño, como Argon2, Scrypt, Bcrypt o PBKDF2 (estas dificultan los ataques de fuerza bruta).

2. **Generación del Salt:** Se genera un _salt_ único y aleatorio (ej. `Y4UV*^(=go_!`). El _salt_ previene los ataques de "Rainbow Tables" (tablas de hashes precalculados) y asegura que dos usuarios con la misma contraseña tengan hashes diferentes.

3. **Concatenación:** Se une la contraseña con el _salt_. Si la contraseña es `AL4RMc10k`, la cadena resultante podría ser `AL4RMc10kY4UV*^(=go_!`.

4. **Cálculo del Hash:** Se calcula el valor hash de la cadena combinada usando el algoritmo elegido.

5. **Almacenamiento:** Se almacena de forma segura tanto el valor hash resultante como el _salt_ único utilizado en la base de datos.

* * *

4. Hashing en Entornos Linux (El archivo `/etc/shadow`)

-------------------------------------------------------

En sistemas Linux (como Fedora o distribuciones orientadas a la seguridad), el archivo `/etc/shadow` contiene el campo de contraseña encriptada. Este campo guarda la frase de contraseña hasheada estructurada en componentes separados por el símbolo `$`.

**Formato estándar:** `$id_algoritmo$opciones$salt$hash`

**Ejemplo práctico:**

`strategos:$y$j9T$76UzfgEM5PnymhQ7TlJey1$/OOSg64dhfF.TigVPdzqiFang6uZA4QA1pzzegKdVm4:19965:0:99999:7:::`

Desglose de los cuatro componentes principales separados por `$`:

* **`y`**: Indica el identificador del algoritmo utilizado (en este caso, **yescrypt**).

* **`j9T`**: Son parámetros/opciones pasadas al algoritmo (como el costo de procesamiento).

* **`76UzfgEM5PnymhQ7TlJey1`**: Es el _salt_ utilizado.

* **`/OOSg64dhfF.TigVPdzqiFang6uZA4QA1pzzegKdVm4`**: Es el valor del hash final.

**Identificadores de Algoritmos Comunes en Linux (Prefijos):**

* `$1$` = MD5

* `$2a$`, `$2b$`, `$2y$` = Blowfish (Bcrypt)

* `$5$` = SHA-256

* `$6$` = SHA-512

* `$y$` = Yescrypt (El estándar más moderno en muchas distribuciones actuales).

_(Referencia útil en la terminal: `man 5 crypt`)_

* * *

5. Auditoría y Recuperación de Contraseñas

------------------------------------------

Aunque el hashing es un proceso unidireccional y no puede ser descifrado matemáticamente, se pueden adivinar las contraseñas originales calculando hashes de posibles entradas y comparándolos con el hash objetivo.

**Bases de datos en línea (Lookup Tables):**

Útiles para hashes sin _salt_ o muy comunes.

* [https://hashes.com/](https://hashes.com/)

* [https://crackstation.net/](https://crackstation.net/)

**Hashcat:**

Es una de las herramientas de recuperación de contraseñas más potentes, utilizando la GPU para acelerar los ataques de fuerza bruta o de diccionario.

**Sintaxis básica de ataque:**

`hashcat -m <hash_type> -a <attack_mode> hashfile wordlist`

| **Parámetro**      | **Descripción**                                                                                                                                                                                                                    |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-m <hash_type>`   | Especifica el tipo de hash en formato numérico (ej. `-m 1000` para NTLM, `-m 1800` para SHA-512 de Linux). _Nota: Consulta `man hashcat` o la wiki oficial (`hashcat.net/wiki/doku.php?id=example_hashes`) para el código exacto._ |
| `-a <attack_mode>` | Especifica el modo de ataque. Ejemplos: `-a 0` (Ataque de diccionario/Straight), `-a 3` (Fuerza bruta con máscaras).                                                                                                               |
| `hashfile`         | El archivo de texto local que contiene el hash o hashes que deseas crackear.                                                                                                                                                       |
| `wordlist`         | La lista de palabras (diccionario) que utilizarás en el ataque (ej. `rockyou.txt`).                                                                                                                                                |

**Visualización de resultados guardados:**

`hashcat -m <hash_type> --show hashfile wordlist`

Este comando le indica a Hashcat que no intente crackear de nuevo, sino que consulte su archivo interno llamado `hashcat.potfile`. Este _potfile_ es una base de datos local donde Hashcat almacena temporalmente todos los hashes que ha logrado descifrar exitosamente en sesiones anteriores, mostrándolos junto con sus respectivas contraseñas en texto plano.

* * *

¿Te gustaría que hagamos un pequeño ejercicio práctico simulado donde te doy un hash en formato de Linux y analizamos qué comando exacto de Hashcat usarías para auditarlo?
