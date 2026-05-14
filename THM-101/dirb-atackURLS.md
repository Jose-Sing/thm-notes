# Comando dirb

> este es un comando similar a John de Ripper pero para paginas web que lo que hace es usar diccionarios como `common.txt` para adivinar carpetas y archivos ocultos del servidor web.

Imagina que estás auditando la página `http://www.empresa.com`. A simple vista, solo ves la página de inicio, "Quiénes somos" y "Contacto".

Pero los servidores web suelen tener carpetas escondidas que los administradores olvidan proteger, como paneles de control, respaldos o versiones viejas de la página. Como no hay botones que te lleven ahí, tienes que adivinar cómo se llaman.

Ahí es donde entra `dirb`. Si ejecutas el comando básico: `dirb http://www.empresa.com`

La herramienta agarra su propia lista de palabras y empieza a bombardear al servidor preguntándole a la velocidad de la luz:

* _¿Tienes una carpeta que se llame /admin?_ (El servidor dice: Error 404 No existe)

* _¿Tienes una carpeta que se llame /login?_ (El servidor dice: Error 404 No existe)

* _¿Tienes una carpeta que se llame /backups?_ (**El servidor dice: Código 200 OK!**)

¡Bingo! `dirb` te avisa que encontró una ruta secreta a la que ahora puedes acceder desde tu navegador.

> Es como caminar por el pasillo de un hotel probando todas las manijas de las puertas hasta encontrar una que el personal de limpieza dejó sin seguro.



Este comando puede correr en conexiones HTTPS

> Nota: no apuntar este tipo de herramientas en URLs o servidores a los que no tengo permiso testear.
> 
> 
