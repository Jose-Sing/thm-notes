# API REST notes



Vas bien encaminado, pero hay algunas cosas importantes que agregar. Te lo explico completo:

* * *

Estructura de una API REST
--------------------------

La estructura general es:
    http://IP:PORT/[prefijo]/[recurso]/[identificador]/[sub-recurso]

El `/api/` que viste es simplemente una convención — no es obligatorio. También puedes ver `/v1/`, `/api/v2/`, o nada. Lo que importa son las partes que siguen.

* * *

Los 4 componentes reales de una petición API
--------------------------------------------

Cuando te comunicas con una API, no solo importa la URL. Son cuatro cosas juntas:

### 1. El método HTTP (el verbo)

Esto es lo más importante que probablemente te faltaba:

| Método   | Propósito                       | Analogía                                 |
| -------- | ------------------------------- | ---------------------------------------- |
| `GET`    | Pedir/leer datos                | "Dame la lista de usuarios"              |
| `POST`   | Crear algo nuevo / enviar datos | "Aquí está mi login, procésalo"          |
| `PUT`    | Reemplazar un recurso completo  | "Reemplaza todo el perfil del usuario 5" |
| `PATCH`  | Modificar parte de un recurso   | "Solo cambia el email del usuario 5"     |
| `DELETE` | Eliminar                        | "Borra el usuario 5"                     |

Cuando escribes algo en el navegador, **siempre** estás haciendo un `GET`. Para `POST`, `DELETE`, etc., necesitas curl, Burp Suite, o código.

### 2. La URL / endpoint

    http://10.67.185.189:5002/api/users/5/messages
                              │    │      │    │
                        prefijo  recurso  ID  sub-recurso

No hay magia aquí — es solo una dirección. El servidor define qué endpoints existen.

### 3. Los Headers

Información extra que va junto a la petición, pero fuera del body. Los más importantes:
    Content-Type: application/json   # Le dices al servidor en qué formato mandas datos
    Authorization: Bearer eyJ...     # Token de autenticación
    Cookie: session=abc123           # Sesión
    Accept: application/json         # Qué formato quieres de respuesta

### 4. El Body (solo en POST, PUT, PATCH)

Los datos que envías. Generalmente en JSON:
    {
      "username": "admin",
      "password": "1234"
    }

* * *

Cómo se ve todo junto en curl
-----------------------------

    # GET — solo URL, sin body
    curl http://10.67.185.189:5002/api/users
    
    # GET con autenticación
    curl http://10.67.185.189:5002/api/users \
      -H "Authorization: Bearer eyJhbGciOiJ..."
    
    # POST con body JSON
    curl -X POST http://10.67.185.189:5002/api/login \
      -H "Content-Type: application/json" \
      -d '{"username": "admin", "password": "1234"}'
    
    # DELETE
    curl -X DELETE http://10.67.185.189:5002/api/users/5 \
      -H "Authorization: Bearer eyJhbGciOiJ..."

* * *

Lo que normalmente explora un analista/pentester
------------------------------------------------

Cuando llegas a una API sin documentación (como en los labs), la lógica es:
    1. ¿Qué recursos existen?
       GET /api/users          → lista de usuarios
       GET /api/messages       → mensajes
       GET /api/admin          → panel admin?

    2. ¿Puedo acceder a un recurso por ID?
       GET /api/users/1
       GET /api/users/2        → IDOR si no hay control de acceso

    3. ¿Hay sub-recursos?
       GET /api/users/1/messages
       GET /api/users/admin/messages   ← lo que hiciste en el lab

    4. ¿Qué métodos acepta cada endpoint?
       POST /api/users         → crear usuario?
       DELETE /api/users/1     → borrar usuario?

* * *

Lo que debería quedarte claro ahora
-----------------------------------

Lo que tenías bien: la URL con `/api/recurso/id` es correcto.

Lo que faltaba: **el método HTTP es la otra mitad de la ecuación**. La misma URL con `GET` puede devolver datos, con `POST` puede crear algo, con `DELETE` puede borrar. Son peticiones completamente distintas aunque la URL sea idéntica.

En el lab de AS03 por ejemplo, la URL `/api/process` con `GET` probablemente devolvía un error, pero con `POST` y el body `{"data": "debug"}` activó el código vulnerable. La URL sola no era suficiente — el método y el body eran la clave.
