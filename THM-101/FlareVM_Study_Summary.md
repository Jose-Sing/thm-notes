# 🛡️ TryHackMe — Cybersecurity 101
## Module: FlareVM — Malware Analysis Environment | Study Summary
> **Keywords:** FlareVM · Static Analysis · Dynamic Analysis · PEStudio · FLOSS · Process Explorer · Process Monitor · CFF Explorer · HxD · Malware Investigation · PE Files · IAT

---

## 📚 Table of Contents

1. [¿Qué es FlareVM?](#1-qué-es-flarevm)
2. [Arsenal de Tools — Categorías](#2-arsenal-de-tools--categorías)
3. [Tools de Investigación — Overview](#3-tools-de-investigación--overview)
4. [Análisis Práctico de Malware — Task 4 ⭐](#4-análisis-práctico-de-malware--task-4-)
5. [Quick Reference Cheatsheet](#5-quick-reference-cheatsheet)
6. [Key Terminology](#6-key-terminology)
7. [My Notes & Questions](#7-my-notes--questions)

---

## 1. ¿Qué es FlareVM?

**FlareVM** (Forensics, Logic Analysis, and Reverse Engineering VM) es un entorno Windows completamente equipado con herramientas especializadas para malware analysis, incident response, y reverse engineering. Fue desarrollado por el **FLARE Team de FireEye/Mandiant**.

> En otras palabras: es una VM de Windows que viene pre-instalada con todas las herramientas que un analista de malware necesita — en lugar de instalarlas una por una, FlareVM las agrupa y organiza listas para usar.

**Audiencia objetivo:** Reverse engineers, malware analysts, incident responders, forensic investigators, y penetration testers.

---

## 2. Arsenal de Tools — Categorías

| Categoría | Tools incluidas |
|---|---|
| **Reverse Engineering & Debugging** | Ghidra, x64dbg, OllyDbg, Radare2, Binary Ninja, PEiD |
| **Disassemblers & Decompilers** | CFF Explorer, Hopper Disassembler, RetDec |
| **Static & Dynamic Analysis** | Process Hacker, PEview, Dependency Walker, DIE (Detect It Easy) |
| **Forensics & Incident Response** | Volatility, Rekall, FTK Imager |
| **Network Analysis** | Wireshark, Nmap, Netcat |
| **File Analysis** | FileInsight, Hex Fiend, HxD |
| **Scripting & Automation** | Python, PowerShell Empire |
| **Sysinternals Suite** | Autoruns, Process Explorer, Process Monitor |

---

## 3. Tools de Investigación — Overview

Estas son las tools estándar usadas en investigaciones iniciales. Cada una tiene un rol específico:

| Tool | Tipo de Análisis | Valor Principal |
|---|---|---|
| **PEStudio** | Static | Analiza propiedades de ejecutables sin ejecutarlos — hashes, imports, entropy, strings |
| **FLOSS** | Static | Extrae y de-ofusca strings de malware automáticamente |
| **Process Explorer** | Dynamic | Muestra jerarquía de procesos (padre-hijo), DLLs cargadas, rutas, y conexiones de red |
| **Process Monitor (Procmon)** | Dynamic | Monitorea actividad del sistema en tiempo real — archivos, registro, procesos, red |
| **CFF Explorer** | Static | Análisis profundo de PE files — hashes, secciones, DOS header, imports |
| **HxD** | Static | Hex editor — inspección de bytes raw de cualquier archivo |
| **Wireshark** | Dynamic | Captura y análisis de tráfico de red |

---

### Process Monitor (Procmon)

Herramienta Windows que **registra en tiempo real** toda la actividad de: sistema de archivos, registro de Windows, y procesos/threads.

**Uso en investigación:**
- Monitorear qué archivos lee o escribe un proceso sospechoso
- Ver qué claves de registro accede un binario
- Confirmar conectividad de red de un proceso
- Detectar comportamiento anómalo de procesos legítimos (ej: LSASS siendo accedido por tools como Mimikatz)

![El log de Procmon muestra que lsass.exe leyó un archivo exitosamente — se filtra por proceso para reducir el ruido](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727302306550.png)

> ⚠️ **LSASS en contexto de malware:** LSASS maneja autenticación en Windows. Tools como Mimikatz intentan acceder a su memoria para robar credenciales (credential dumping). Si ves patrones de acceso inusuales a lsass.exe durante un análisis, es una señal de alerta.

---

### Process Explorer (Procexp)

Muestra todos los procesos activos con su **relación padre-hijo**, DLLs cargadas, usuario propietario, y conectividad de red.

**Uso en investigación:**
- Identificar procesos hijo anómalos (ej: Word.exe spawneando cmd.exe)
- Ver a qué DLLs accede un proceso
- Verificar conectividad TCP/IP de un proceso específico
- Detectar abuse de procesos legítimos (LNK files, ISO files, documentos Office)

![Process Explorer mostrando la jerarquía de procesos — CFF Explorer aparece como proceso hijo de explorer.exe](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727171765939.png)

> Los threat actors abusan frecuentemente de documentos Word, LNK files, e ISO files para spawner procesos maliciosos. Monitorear la jerarquía padre-hijo es crítico.

---

### HxD (Hex Editor)

Permite ver y editar el **contenido binario raw** de cualquier archivo en formato hexadecimal.

**Uso en investigación:**
- Identificar el tipo de archivo por sus **magic bytes** (los primeros bytes del archivo)
- Detectar archivos mal clasificados o con extensión falsa
- Análisis forense de archivos sospechosos

![HxD mostrando possible_medusa.txt — los primeros bytes 4D 5A revelan que es un ejecutable PE disfrazado de .txt](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727170785692.png)

**Magic Bytes clave para reconocer:**

| Magic Bytes (Hex) | Tipo de Archivo | Lo que significa |
|---|---|---|
| `4D 5A` | PE Executable (.exe, .dll) | **MZ header** — es un ejecutable Windows |
| `50 4B 03 04` | ZIP / Office OOXML | Archivo comprimido o documento moderno de Office |
| `25 50 44 46` | PDF | `%PDF` — archivo PDF |
| `FF D8 FF` | JPEG | Imagen JPEG |
| `89 50 4E 47` | PNG | Imagen PNG |

> 🔑 **Regla de oro:** Si un archivo `.txt` empieza con `4D 5A`, NO es un archivo de texto — es un ejecutable disfrazado. HxD te lo revela inmediatamente.

---

### CFF Explorer

Análisis profundo de archivos **PE (Portable Executable)**. Permite ver hashes, secciones, headers, imports, y metadatos del archivo.

**Uso en investigación:**
- Generar hashes (MD5, SHA-1, SHA-256) para verificar integridad
- Comparar hashes contra bases de datos como VirusTotal
- Examinar el DOS Header (e_magic value)
- Ver imports y exports del binario

![CFF Explorer mostrando detalles de cryptominer.bin — fecha de creación, arquitectura, y hashes del archivo](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727170785706.png)

---

### PEStudio

Herramienta de **static analysis** para ejecutables — obtiene toda la información posible del binario sin necesidad de ejecutarlo.

**Qué analiza:**
- Hashes del archivo (MD5, SHA-1, SHA-256, Imphash)
- Entropy (nivel de compresión/cifrado)
- Strings embebidas
- API calls importadas (IAT — Import Address Table)
- Metadata y versión del archivo
- Rich Header (presencia o ausencia)
- Indicadores de blacklist

![PEStudio mostrando información estática de windows.exe — hashes, descripción, y metadatos del archivo](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727174706257.png)

> **Entropy:** Un valor cercano a 8.0 indica que el archivo probablemente está empaquetado o cifrado — comportamiento común en malware sofisticado.

---

### FLOSS (FLARE Obfuscated String Solver)

Extrae automáticamente strings de binarios usando análisis estático avanzado — incluyendo strings ofuscados que `strings.exe` normal no detectaría.

**Tipos de strings que extrae:**

| Tipo | Descripción |
|---|---|
| **Static strings** | Strings literales hardcodeadas en el binario |
| **Stack strings** | Strings construidas en el stack en tiempo de ejecución y luego descartadas |
| **Tight strings** | Strings generadas en loops cerrados |
| **Decoded strings** | Strings que fueron ofuscadas y FLOSS logró decodificar |

```powershell
# Comando básico — output a pantalla
FLOSS.exe .\archivo.exe

# Redirigir output a un archivo de texto para análisis posterior
FLOSS.exe .\archivo.exe > resultado.txt
```

**Lo que puedes encontrar en los strings extraídos:**
- URLs de servidores C2 (Command & Control)
- Direcciones IP hardcodeadas
- Rutas de archivos y claves de registro
- API calls (funciones Windows que usa el malware)
- Mensajes de error y configuración
- Claves de cifrado

---

### Wireshark

Captura y analiza tráfico de red en tiempo real.

![Wireshark mostrando paquetes capturados con protocolo TLSv1.2 — fuente, destino, y datos en hex/ASCII](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727170785704.png)

> TLSv1.2 en tráfico sospechoso indica comunicación cifrada — puede estar ocultando tráfico C2 o exfiltración de datos.

🔗 [Módulo Wireshark en TryHackMe](https://tryhackme.com/module/wireshark)

---

## 4. Análisis Práctico de Malware — Task 4 ⭐

**Escenario:** Un archivo `windows.exe` fue descargado por un usuario el 09/24/2024 a las 3:43 AM y fue marcado como amenaza potencial. El archivo está en `C:\Users\Administrator\Desktop\Sample`.

![Archivos disponibles en la carpeta Sample para el análisis de este task](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727170544603.png)

**Enfoque inicial:** Static analysis para obtener información antes de ejecutar nada.

---

### Step 1 — Análisis con PEStudio

**Abrir:** `windows.exe` con PEStudio.

![PEStudio mostrando hashes, descripción y metadatos de windows.exe — se observa texto en ruso en el campo version](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727174706257.png)

**Hallazgos del análisis:**

| Campo | Valor | Interpretación |
|---|---|---|
| MD5 | `9FDD4767DE5AEC8E577C1916ECC3E1D6` | Comparar en VirusTotal — si no hay detecciones, podría ser malware nuevo |
| SHA-1 | `A1BC55A7931BFCD24651357829C460FD3DC4828F` | Mismo uso — verificación de integridad |
| Description | Windows Registry Editor | 🚩 **Red flag** — el malware se hace pasar por REGEDIT |
| Ubicación | Carpeta de descargas del usuario | 🚩 REGEDIT legítimo vive en `C:\Windows\System32`, no en descargas |

![PEStudio mostrando texto en ruso en los metadatos de version de windows.exe](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727175229441.png)

**Más hallazgos:**

| Indicador | Observación | Significance |
|---|---|---|
| **Texto en ruso** en metadata | "Редактор реестра" (Registry Editor), "Операционная система Microsoft® Windows®" | 🚩 Muy sospechoso si la organización no opera en entorno ruso |
| **Rich Header ausente** | El archivo no tiene Rich Header | 🚩 Indica posible packing u ofuscación para evadir análisis estático |
| **Entropy elevada** | Cerca de 8.0 | 🚩 Probable archivo empaquetado o cifrado |

---

#### API Calls sospechosas (IAT — Import Address Table)

En PEStudio ir a **Functions → Blacklist tab** para ver las APIs blacklisteadas al inicio de la lista.

![PEStudio mostrando las API calls importadas por windows.exe — funciones de criptografía y ejecución de procesos resaltadas](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727178234246.png)

**APIs clave identificadas:**

| API / Función | Lo que indica |
|---|---|
| `set_UseShellExecute` | Permite al proceso ejecutar otros procesos usando el shell del OS — común en malware que spawna procesos adicionales |
| `CryptoStream` | Uso de funciones criptográficas — cifrado de comunicaciones o archivos |
| `RijndaelManaged` | Implementa cifrado AES (Rijndael) — puede indicar ransomware o comunicación cifrada con C2 |
| `CipherMode` | Relacionado con el modo de operación del cifrado |
| `CreateDecryptor` | Crea un objeto desencriptador — el malware puede estar descifrando payloads en memoria |

> 🔑 **Por qué importa la IAT:** Las funciones importadas revelan *qué puede hacer* el binario antes de ejecutarlo. Si importa funciones de cifrado + ejecución de procesos + red, la combinación es altamente sospechosa.

---

### Step 2 — Análisis con FLOSS

**Objetivo:** Extraer strings del binario para encontrar URLs, IPs, y otras cadenas hardcodeadas.

```powershell
# Navegar al directorio del sample
cd C:\Users\Administrator\Desktop\Sample

# Correr FLOSS y redirigir output a un archivo
FLOSS.exe .\windows.exe > windows.txt
```

**Output esperado en terminal:**
```
WARNING: floss: .NET language-specific string extraction is not supported yet
WARNING: floss: FLOSS does NOT attempt to deobfuscate any strings from .NET binaries
INFO: floss: disabled string deobfuscation
INFO: floss: extracting static strings
INFO: floss: finished execution after 0.34 seconds
INFO: floss: rendering results
```

> ⚠️ Los warnings de `.NET` son normales — indican que el binario es .NET y FLOSS tiene limitaciones con esos. No es un error.

**Revisar el resultado:** Abrir `windows.txt` e ir al **final del archivo** donde aparecen los strings más relevantes.

![Resultado de FLOSS en windows.txt mostrando los strings extraídos del binario — al final aparecen las funciones criptográficas que ya vimos en PEStudio](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727179931028.png)

> **Correlación importante:** Los strings que FLOSS extrae al final corresponden a las mismas APIs que PEStudio mostró en la IAT. Esto **confirma** los hallazgos previos usando una segunda herramienta independiente — buena práctica de análisis.

---

### Step 3 — Análisis de Conectividad con Process Explorer

**Objetivo:** Determinar si `cobaltstrike.exe` hace conexiones a servidores externos (C2).

**Archivo:** `cobaltstrike.exe` — ubicado en `C:\Users\Administrator\Desktop\Sample`

#### Paso a paso:

**1.** Ejecutar `cobaltstrike.exe` manualmente (doble click).

**2.** Abrir **Process Explorer** desde el desktop o taskbar.

**3.** Localizar el proceso `cobaltstrike.exe` en la lista — verificar la jerarquía padre-hijo.

![Process Explorer mostrando cobaltstrike.exe como proceso hijo de explorer.exe con su PID visible](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727191613610.png)

> Si se ejecutó manualmente haciendo doble click, el proceso padre es `Explorer.exe`. Esto es el comportamiento esperado — confirma que el proceso hijo es `cobaltstrike.exe`.

**4.** Anotar el **Process ID (PID)** — en el lab es `4756` (puede variar en tu máquina).

**5.** Right-click en el proceso → **Properties** → tab **TCP/IP**.

![Propiedades de cobaltstrike.exe en la pestaña TCP/IP mostrando la IP de destino, puerto, y estado de la conexión](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727191613592.png)

**Hallazgo:** El proceso está haciendo una conexión a una IP externa — se puede ver la **IP de destino**, el **puerto**, y el **estado** de la conexión.

---

### Step 4 — Confirmación con Process Monitor (Procmon)

**Objetivo:** Verificar y confirmar la conectividad de red encontrada con Process Explorer usando una segunda herramienta.

> 🔑 **Principio clave:** En análisis de malware nunca te fíes de una sola tool. Siempre confirma los hallazgos con al menos una segunda herramienta independiente.

#### Paso a paso:

**1.** Terminar `cobaltstrike.exe` y volver a ejecutarlo.

**2.** Abrir **Process Monitor (Procmon)** desde el desktop o taskbar.

**3.** Procmon captura TODO — la lista es enorme. Necesitamos filtrar. Abrir el filtro con `Ctrl + L` o el ícono de filtro:

![Procmon mostrando el botón de filtro en la barra de herramientas para acceder al panel de filtros](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727192184037.png)

**4.** Configurar el filtro en estos pasos exactos:

![Procmon mostrando el panel de filtros con los 6 pasos para configurar el filtro por nombre de proceso](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727193011175.png)

| # | Campo | Valor | Acción |
|---|---|---|---|
| 1 | Primera dropdown | **Process Name** | Seleccionar |
| 2 | Segunda dropdown | **contains** | Seleccionar |
| 3 | Campo de texto | **cobalt** | Escribir (parte del nombre del proceso) |
| 4 | Tercera dropdown | **Include** | Seleccionar |
| 5 | Botón | **Add** | Click |
| 6 | Botón | **Apply** | Click |

**5.** Revisar los resultados filtrados:

![Procmon mostrando resultados filtrados para cobaltstrike.exe — se confirma la conexión a la IP 47.120.46.210](https://tryhackme-images.s3.amazonaws.com/user-uploads/5e6bbe59a46ee9407fd65bbe/room-content/5e6bbe59a46ee9407fd65bbe-1727193407393.png)

**Resultado confirmado:** `cobaltstrike.exe` está haciendo una conexión de red a la IP `47[.]120[.]46[.]210` (defanged).

> ✅ **Análisis completo:** Process Explorer identificó la conexión → Procmon la confirmó. Dos herramientas, mismo resultado. El hallazgo es confiable.

---

### Resumen del Análisis — windows.exe y cobaltstrike.exe

| Indicador | Herramienta que lo detectó | Nivel de Sospecha |
|---|---|---|
| Descripción falsa como REGEDIT | PEStudio | 🔴 Alto |
| Texto en ruso en metadata | PEStudio | 🔴 Alto |
| Rich Header ausente | PEStudio | 🟡 Medio-Alto |
| Entropy elevada | PEStudio | 🟡 Medio-Alto |
| APIs de criptografía (AES/Rijndael) | PEStudio / FLOSS | 🔴 Alto |
| `set_UseShellExecute` importado | PEStudio / FLOSS | 🔴 Alto |
| Conexión a IP externa desconocida | Process Explorer + Procmon | 🔴 Alto |

---

## 5. Quick Reference Cheatsheet

### Flujo de trabajo de análisis estático

```
1. PEStudio → hashes, entropy, strings, IAT (API calls)
2. FLOSS    → strings extraídos y deofuscados
3. CFF Explorer → PE structure, DOS header, imports
4. HxD     → bytes raw, verificar magic bytes del archivo
```

### Flujo de trabajo de análisis dinámico

```
1. Ejecutar el binario en entorno aislado
2. Process Explorer → jerarquía padre-hijo + TCP/IP tab
3. Process Monitor  → filtrar por proceso → confirmar red/archivos/registro
4. Wireshark → captura completa del tráfico de red
```

### Comandos FLOSS

```powershell
# Extraer strings (output a pantalla)
FLOSS.exe .\malware.exe

# Guardar output para análisis posterior
FLOSS.exe .\malware.exe > resultado.txt

# Revisar la parte más relevante (bottom del archivo)
Get-Content .\resultado.txt | Select-Object -Last 100
```

### Configurar filtro en Procmon

```
Filtro → Process Name | contains | [nombre] | Include → Add → Apply
Shortcut: Ctrl + L para abrir el panel de filtros
```

### Red Flags en Static Analysis

| Indicador | Qué significa |
|---|---|
| Entropy > 7.0 | Archivo probablemente empaquetado o cifrado |
| Rich Header ausente | Posible packing u ofuscación |
| Descripción falsa (ej: REGEDIT) | Masquerading — malware disfrazado de herramienta legítima |
| Metadata en idioma inesperado | Posible origen del threat actor |
| Magic bytes `4D 5A` en archivo `.txt` | Ejecutable disfrazado con extensión falsa |
| APIs de criptografía + red + procesos | Combinación típica de malware avanzado |

### Magic Bytes de referencia

```
4D 5A          → PE Executable (Windows .exe/.dll)
50 4B 03 04    → ZIP / Office OOXML
25 50 44 46    → PDF (%PDF)
FF D8 FF       → JPEG
89 50 4E 47    → PNG
```

---

## 6. Key Terminology

| Término | Definición |
|---|---|
| **FlareVM** | VM Windows con herramientas pre-instaladas para malware analysis y reverse engineering — desarrollada por FLARE Team de FireEye |
| **Static Analysis** | Analizar un archivo sin ejecutarlo — extraer información del binario en reposo |
| **Dynamic Analysis** | Analizar un archivo ejecutándolo en un entorno controlado — observar su comportamiento real |
| **PE File** | Portable Executable — formato estándar de ejecutables en Windows (.exe, .dll, .sys) |
| **IAT** | Import Address Table — tabla de funciones (APIs de Windows) que un ejecutable importa |
| **Entropy** | Medida de aleatoriedad del archivo — valores altos (~8.0) sugieren packing o cifrado |
| **Rich Header** | Sección del PE file que contiene información sobre el compilador — su ausencia puede indicar manipulación |
| **Magic Bytes** | Primeros bytes de un archivo que identifican su tipo real (independiente de la extensión) |
| **Masquerading** | Técnica donde el malware se hace pasar por software legítimo (ej: renombrarse como REGEDIT) |
| **C2 / Command & Control** | Servidor remoto al que el malware se conecta para recibir instrucciones del atacante |
| **Defanged IP** | Formato de IP donde los puntos se reemplazan con `[.]` para evitar que sea clickeable — ej: `47[.]120[.]46[.]210` |
| **PEStudio** | Tool de static analysis para PE files — muestra hashes, entropy, imports, strings sin ejecutar el archivo |
| **FLOSS** | FLARE Obfuscated String Solver — extrae strings incluyendo los ofuscados (stack strings, tight strings) |
| **Process Explorer** | Tool de Sysinternals que muestra jerarquía de procesos, DLLs cargadas, y conexiones de red |
| **Procmon** | Process Monitor — registra en tiempo real toda la actividad de procesos, archivos, registro, y red |
| **CFF Explorer** | PE editor y visor — análisis profundo de estructura PE, hashes, headers, e imports |
| **HxD** | Hex editor — permite ver y editar el contenido binary raw de cualquier archivo |
| **Rijndael / AES** | Algoritmo de cifrado simétrico — su presencia en malware puede indicar ransomware o cifrado de C2 |
| **Credential Dumping** | Técnica para extraer credenciales de memoria — frecuentemente targeta LSASS en Windows |
| **Parent-Child Process** | Relación entre el proceso que inicia otro (padre) y el proceso iniciado (hijo) — clave para detectar ejecución anómala |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | FlareVM — Malware Analysis Environment*
