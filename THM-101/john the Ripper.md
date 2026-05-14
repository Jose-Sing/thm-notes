💀 John the Ripper: Guía de Referencia
======================================

John the Ripper (JtR) es una herramienta de cracking de contraseñas de código abierto que soporta cientos de tipos de hashes y cifrados.
📥 Enlaces de Instalación y Recursos

------------------------------------

* **Linux:** [Guía de instalación (GitHub)](https://github.com/openwall/john/blob/bleeding-jumbo/doc/INSTALL)

* **Windows (64-bit):** [Descargar v1.9.0-jumbo-1](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win64.zip)

* **Windows (32-bit):** [Descargar v1.9.0-jumbo-1](https://www.openwall.com/john/k/john-1.9.0-jumbo-1-win32.zip)

* **Diccionarios:** [SecLists (Daniel Miessler)](https://github.com/danielmiessler/SecLists)

* **Openwall Wiki:** https://www.openwall.com/john/

* * *

🚀 Sintaxis Básica
------------------

El comando fundamental sigue esta estructura:

Bash
    john [opciones] [ruta_del_archivo]

| **Componente** | **Descripción**                                        |
| -------------- | ------------------------------------------------------ |
| `john`         | Invoca el programa.                                    |
| `[opciones]`   | Banderas específicas para el modo de ataque o formato. |
| `[file path]`  | El archivo que contiene el hash a crackear.            |

* * *

🤖 Cracking Automático
----------------------

Permite que John detecte el tipo de hash e intente crackearlo usando reglas integradas. _Nota: Puede ser poco fiable con ciertos formatos._

**Sintaxis:**

Bash
    john --wordlist=[ruta_diccionario] [ruta_archivo_hash]

**Ejemplo:**

Bash
    john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt

* * *

🔍 Identificación de Hashes
---------------------------

Antes de crackear, es vital saber qué tipo de hash tienes.

### Uso de `hash-identifier`

1. **Descargar:**
   Bash
      wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py

2. **Ejecutar:**
   Bash
      python3 hash-id.py

3. **Pegar el hash:** Ingresa el hash (ej. `2e728dd31fb5949bc39cac5a9f066498`) para ver las posibles coincidencias.

* * *

🎯 Cracking por Formato Específico
----------------------------------

Si ya conoces el tipo de hash, especificarlo acelera significativamente el proceso.

**Sintaxis:**

Bash
    john --format=[formato] --wordlist=[ruta_diccionario] [ruta_archivo]

**Ejemplo:**

Bash
    `john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt`

> [!TIP]
> 
> **¿Cómo encontrar el formato correcto?**
> 
> Usa el comando `--list=formats` y fíltralo con `grep`:
> 
> `john --list=formats | grep -iF "md5"`

* * *

👥 Herramienta: `unshadow`
--------------------------

Se utiliza para combinar los archivos `/etc/passwd` y `/etc/shadow` de un sistema Linux para que John pueda procesar los datos de las contraseñas.

**Sintaxis:**

Bash
    unshadow [ruta_passwd] [ruta_shadow] > unshadowed.txt

**Ejemplo de uso:**

Bash
    unshadow local_passwd local_shadow > unshadowed.txt

* * *

⚡ Modo Single Crack
-------------------

Este modo utiliza información del usuario (como el nombre de usuario o nombre real) para generar variaciones y adivinar la contraseña. Es muy rápido para contraseñas débiles basadas en el nombre del usuario.

**Sintaxis:**

Bash
    john --single --format=[formato] [ruta_archivo]

### Formato de archivo requerido

Para que el modo `--single` funcione, el archivo debe tener el formato `usuario:hash`.

* **Incorrecto:** `1efee03cdcb96d90ad48ccc7b8666033`

* **Correcto:** `mike:1efee03cdcb96d90ad48ccc7b8666033`

**Ejemplo práctico (preparando el archivo):**

Bash
    echo "joker:$(cat hash07.txt)" > testhash.txt
    john --single --format=raw-md5 testhash.txt





---

## Custom Rules

Estos estan definidos en `/etc/john/john.conf`. Esto se crea o se utiliza cuando tenemos una idea de como es el PW que buscamos crackear. "exploit password complexity predictability""

Wiki of the rules: https://www.openwall.com/john/doc/RULES.shtml



Sintax para definir el rule: [List.Rules:THMRules]. abajo de esto se agrega los modifiers >>

**Common modifiers:**

> * `Az`: Takes the word and appends [al final] it with the characters you define
> 
> * `A0`: Takes the word and prepends [al inicio] it with the characters you define
> 
> * `c`: Capitalises the character positionally
> 
> Los siguentes deben de ir entre " "
> 
> * `[0-9]`: Will include numbers 0-9  
> 
> * `[0]`: Will include only the number 0
> 
> * `[A-z]`: Will include both upper and lowercase  
> 
> * `[A-Z]`: Will include only uppercase letters
> 
> * `[a-z]`: Will include only lowercase letters
> 
> * `[a]`: Will include only `a`
> 
> * `[!£$%@]`: Will include the symbols `!`, `£`, `$`, `%`, and `@`



So, asumiendo que el PW es similar a `polopassword` el rule puede ser: 

`[List.Rules:PoloPassword]`

`cAz"[0-9] [!£$%@]"`



Para llamar el rule se usa el flag: `--rule=NombreRulePassword`

Ejemplo con full command: `john --wordlist=[path to wordlist] --rule=PoloPassword [path to file]`



---

## 🗃️Zip2John



John usara el comando `zip2john` para convertir archivos Zip en un file hash para que John pueda entenderlo. 



`zip2john [options] [zip file] > [output file]`

* `[options]`: Allows you to pass specific checksum options to `zip2john`; this shouldn’t often be necessary
* `[zip file]`: The path to the Zip file you wish to get the hash of
  
  

Ejemplo: `zip2john zipfile.zip > zip_hash.txt`



una ves que tenemos el file convertido podemos usar el el comando john:

`john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt`



### Practica de THM:

se nos plantea un archivo comprimido que ademas su contenido esta protegido con un password, el archivo es `John-the-Ripper-The-Basics/Task09/secure.zip`

> Siguiendo el tema se procede con lo siguente para extraer el hash del password y guardarlo en un file: 
> 
> `zip2john  John-the-Ripper-The-Basics/Task09/secure.zip > zip_hash.txt`
> 
> 
> 
> Ya con ese file afuera junto con el hash se procede a crackearlo:
> 
> `john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt`
> 
> 
> 
> Dandonos como resultado:
> `pass123          (secure.zip/zippy/flag.txt)`
> 
> 
> 
> Sabiendo el PW procedemos a descomprimir el archivo:
> 
> `unzip John-the-Ripper-The-Basics/Task09/secure.z
> ip`
> 
> que nos extrae el archivo protegido con el pass ya encontrado, por lo que lo ejecutamos con cat he ingresamos el PW que ya sabemos:
> 
> `cat zippy/flag.txt.`
> 
> Contenido:
> 
> `THM{w3ll_d0n3_h4sh_r0y4l}`



---

## Cracking RAR Archive | rar2john

es posible hacer algo similar a lo anterior pero para RAR files utilizando el comando `rar2john`. Esto convertira el archivo RAR en un hash que john puede entender.



`rar2john [rar file] > [output file]`

* `rar2john`: Invokes the `rar2john` tool
* `[rar file]`: The path to the RAR file you wish to get the hash of



> **Example:**
> 
> `/opt/john/rar2john rarfile.rar > rar_hash.txt`

 Ya con el rar file en hash podemos tirarlo direccto a john: 

`john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt`



### Practica:

Archivo RAR: `~/John-the-Ripper-The-Basics/Task10/`.



> Optener el hash:
> 
> `rar2john John-the-Ripper-The-Basics/Task10/secure.rar > rarhash.txt`
> 
> Crackear:
> 
> `john --wordlist=/usr/share/wordlists/rockyou.txt rarhash.txt `
> 
> > password         (secure.rar)  
> 
> Extraemos con el comando `unrar x`
> 
> ` unrar x John-the-Ripper-The-Basics/Task10/secure
> .rar `
> 
> Ejecutamos con cat: 
> 
> `cat flag.txt` = THM{r4r_4rch1ve5_th15_t1m3}



---

## Cracking SSH key with John | ssh2john



> Para autenticarse de forma segura en los servidores remotos sin usar un PW se genera un "Private Key" o `id_rsa` mediante el comando `ssh-keygen` que junto con el par publico `id_rsa.pub` permite cifrar la conexion  y verificar tu identidad automaticamente.



`ssh2john` es otro John conversion tool para convertir el `id_rsa` en un hash que john pueda entender.

* Si ssh2john no esta instalado en la maquina, se puede usar python y el file: `python3 /opt/john/ssh2john.py`

* Si esta en Kali: `python /usr/share/john/ssh2john.py`



**Sintax:**

`ssh2john [id_rsa private key file] > [output file]`

* `ssh2john`: Invokes the `ssh2john` tool
* `[id_rsa private key file]`: The path to the id_rsa file you wish to get the hash of

**Example:**

/opt/john/ssh2john.py id_rsa > id_rsa_hash.txt



### Practica:

> Obtener el hash:
> 
> `/opt/john/ssh2john.py John-the-Ripper-The-Basics/Task11/id_rsa > rsa-hash.txt`
> 
> Crack it:
> 
> `john --wordlist=/usr/share/wordlists/rockyou.txt rsa-hash.txt`
> 
> > mango            (John-the-Ripper-The-Basics/Task11/id_rsa)   
> 
> 
