# 🛡️ TryHackMe — Cybersecurity 101

## Módulo: SQL Fundamentals | Study Summary

> **Keywords:** SQL · Relational Databases · CRUD · Clauses · Operators · Functions · MySQL.

---

## 📚 Tabla de Contenidos

1. [Databases 101](#1-databases-101)
2. [SQL & DBMS](#2-sql--dbms)
3. [Database & Table Statements](#3-database--table-statements)
4. [CRUD Operations](#4-crud-operations)
5. [Clauses](#5-clauses)
6. [Operators](#6-operators)
7. [Functions](#7-functions)
8. [Quick Reference Cheatsheet](#8-quick-reference-cheatsheet)

---

## 1. Databases 101

### ¿Qué es una base de datos?

Una base de datos es una **colección organizada de información estructurada** que puede ser fácilmente accedida, manipulada y analizada. Se usan en casi todo: autenticación de usuarios, redes sociales, streaming, e-commerce, etc.

---

### Tipos de Bases de Datos

| Tipo              | También llamada | Cómo almacena datos         | Cuándo usarla                                       |
| ----------------- | --------------- | --------------------------- | --------------------------------------------------- |
| **Relacional**    | SQL             | Tablas con filas y columnas | Datos consistentes y estructurados (ej: e-commerce) |
| **No relacional** | NoSQL           | Documentos, key-value pairs | Datos variables y flexibles (ej: redes sociales)    |

**Ejemplo de un documento NoSQL:**

```json
{
  "_id": "4556712cd2b2397ce1b47661",
  "name": { "first": "Thomas", "last": "Anderson" },
  "occupation": ["The One"]
}
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Tablas, Filas y Columnas

- **Tabla:** El contenedor que guarda un conjunto de datos (ej: `books`).
- **Columnas:** Definen los campos de cada registro y su tipo de dato (ej: `id`, `name`, `published_date`).
- **Filas:** Cada registro individual dentro de la tabla.
- **Tipos de dato comunes:** `STRING`, `INTEGER`, `FLOAT/DECIMAL`, `DATE/TIME`

---

### Primary Key vs Foreign Key

| Concepto        | ¿Qué hace?                                           | Regla                                |
| --------------- | ---------------------------------------------------- | ------------------------------------ |
| **Primary Key** | Identifica **únicamente** cada registro en una tabla | Solo puede haber **una** por tabla   |
| **Foreign Key** | Referencia la PK de otra tabla, creando una relación | Puede haber **más de una** por tabla |

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 2. SQL & DBMS

### ¿Qué es un DBMS?

Un **Database Management System** es el software que actúa como interfaz entre el usuario y la base de datos. Ejemplos: `MySQL`, `MongoDB`, `Oracle Database`, `MariaDB`.

**SQL (Structured Query Language)** es el lenguaje que se usa para interactuar con bases de datos relacionales: consultar, definir y manipular datos.

### Beneficios de SQL

| Beneficio                | Descripción                                              |
| ------------------------ | -------------------------------------------------------- |
| ⚡ **Rápido**             | Retorna grandes volúmenes de datos casi instantáneamente |
| 📖 **Fácil de aprender** | Escrito en inglés plano y legible                        |
| ✅ **Confiable**          | La estructura rígida garantiza precisión en los datos    |
| 🔍 **Flexible**          | Permite queries complejas para análisis de datos         |

### Conectarse a MySQL

```bash
# Iniciar sesión en MySQL
mysql -u root -p

# Ingresar la contraseña cuando se solicite:
# Password: tryhackme
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 3. Database & Table Statements

### 3.1 Database Statements

| Statement           | Sintaxis                  | Qué hace                               |
| ------------------- | ------------------------- | -------------------------------------- |
| `CREATE DATABASE`   | `CREATE DATABASE nombre;` | Crea una nueva base de datos           |
| `SHOW DATABASES`    | `SHOW DATABASES;`         | Lista todas las bases de datos         |
| `USE`               | `USE nombre;`             | Selecciona la base de datos activa     |
| `SELECT DATABASE()` | `SELECT DATABASE();`      | Confirma cuál DB está activa *(extra)* |
| `DROP DATABASE`     | `DROP DATABASE nombre;`   | Elimina una base de datos              |

```sql
-- Ejemplo completo del flujo
CREATE DATABASE thm_bookmarket_db;
SHOW DATABASES;
USE thm_bookmarket_db;
SELECT DATABASE();
```

---

### 3.2 Table Statements

| Statement           | Qué hace                                                   |
| ------------------- | ---------------------------------------------------------- |
| `CREATE TABLE`      | Crea una nueva tabla con sus columnas y tipos de dato      |
| `SHOW TABLES`       | Lista todas las tablas en la DB activa                     |
| `DESCRIBE` / `DESC` | Muestra la estructura de una tabla (columnas, tipos, keys) |
| `ALTER TABLE`       | Modifica la estructura de una tabla existente              |
| `DROP TABLE`        | Elimina una tabla permanentemente                          |

**Crear una tabla:**

```sql
CREATE TABLE book_inventory (
    book_id    INT AUTO_INCREMENT PRIMARY KEY,
    book_name  VARCHAR(255) NOT NULL,
    publication_date DATE
);
```

**Ver estructura:**

```sql
DESCRIBE book_inventory;
```

```
+------------------+--------------+------+-----+---------+----------------+
| Field            | Type         | Null | Key | Default | Extra          |
+------------------+--------------+------+-----+---------+----------------+
| book_id          | int          | NO   | PRI | NULL    | auto_increment |
| book_name        | varchar(255) | NO   |     | NULL    |                |
| publication_date | date         | YES  |     | NULL    |                |
+------------------+--------------+------+-----+---------+----------------+
```

**Text String / Text types**

| Type          | Description                                                                                        | Range / Limit       |
| ------------- | -------------------------------------------------------------------------------------------------- | ------------------- |
| CHAR(n)       | Fixed-length string. Always occupies n bytes, padded with spaces.CHAR(10) → always stores 10 chars | 1–255 chars         |
| VARCHAR(n)    | Variable-length string. Only uses the space actually needed.VARCHAR(255) → up to 255 chars         | 1–65,535 bytes      |
| TEXT          | Long text with no defined max. Good for descriptions, articles.blog posts, comments, notes         | up to 65,535 bytes  |
| TINYTEXT      | Very short text strings.                                                                           | up to 255 bytes     |
| MEDIUMTEXT    | Medium-length text. Good for longer documents.                                                     | up to 16 MB         |
| LONGTEXT      | Largest text type. For very large documents or logs.                                               | up to 4 GB          |
| ENUM('a','b') | A column that can only hold one value from a predefined list.ENUM('admin','user','guest')          | up to 65,535 values |
| SET('a','b')  | Like ENUM but can hold multiple values simultaneously.SET('read','write','exec')                   | up to 64 members    |

Numeric Integer types

| Type      | Description                                                                                | Range (signed)    |
| --------- | ------------------------------------------------------------------------------------------ | ----------------- |
| TINYINT   | Very small integer. Often used for flags (0/1).                                            | -128 to 127       |
| SMALLINT  | Small integer for compact storage.                                                         | -32,768 to 32,767 |
| MEDIUMINT | Medium integer. Saves space vs INT for mid-range values.                                   | -8.3M to 8.3M     |
| INT       | Standard integer. Default choice for IDs and counts.book_id INT AUTO_INCREMENT PRIMARY KEY | -2.1B to 2.1B     |
| BIGINT    | Large integer for very big numbers (user IDs at scale, timestamps).                        | ±9.2 quintillion  |

Numeric Decimal / Floating-point types

| Type         | Description                                                                                   | Precision  |
| ------------ | --------------------------------------------------------------------------------------------- | ---------- |
| DECIMAL(p,s) | Exact decimal. Use for money — no floating-point rounding errors.DECIMAL(10,2) → 99999999.99  | Exact      |
| NUMERIC(p,s) | Alias for DECIMAL in most databases.                                                          | Exact      |
| FLOAT        | Approximate decimal. Faster but imprecise. Good for scientific data.coordinates, measurements | ~7 digits  |
| DOUBLE       | Double-precision float. More accurate than FLOAT but still approximate.                       | ~15 digits |

Date / Time Date and time types

| Type      | Description                                                                                                                                      | Format / Range           |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------ |
| DATE      | Calendar date only. No time component.publication_date DATE → '2024-03-15'                                                                       | 1000-01-01 to 9999-12-31 |
| TIME      | Time of day only. No date component.start_time TIME → '14:30:00'                                                                                 | -838:59:59 to 838:59:59  |
| DATETIME  | Date + time combined. Not timezone-aware.created_at DATETIME → '2024-03-15 14:30:00'                                                             | 1000-01-01 to 9999-12-31 |
| TIMESTAMP | Date + time stored as UTC. Converts to local timezone on retrieval. Auto-updates on INSERT/UPDATE.updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP | 1970-01-01 to 2038-01-19 |
| YEAR      | 4-digit year only.model_year YEAR → 2024                                                                                                         | 1901 to 2155             |

Boolean Boolean type

| Type           | Description                                                                                          | Values               |
| -------------- | ---------------------------------------------------------------------------------------------------- | -------------------- |
| BOOLEAN / BOOL | Logical true/false. In MySQL it's stored as TINYINT(1) under the hood.is_active BOOLEAN DEFAULT TRUE | TRUE (1) / FALSE (0) |

Binary Binary / Blob types

| Type         | Description                                                                | Limit          |
| ------------ | -------------------------------------------------------------------------- | -------------- |
| BINARY(n)    | Fixed-length binary data. Like CHAR but stores raw bytes.                  | 1–255 bytes    |
| VARBINARY(n) | Variable-length binary. Like VARCHAR but for bytes.                        | 1–65,535 bytes |
| BLOB         | Binary Large Object. Stores raw binary data — images, files, keys.         | up to 65 KB    |
| MEDIUMBLOB   | Medium binary data. For larger files.                                      | up to 16 MB    |
| LONGBLOB     | Largest binary type. For very large files. Prefer external storage though. | up to 4 GB     |

Special Spatial & JSON types

| Type       | Description                                                                                                                                      | Use case             |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------- |
| JSON       | Stores a JSON document natively. Allows querying inside the JSON with functions like JSON_EXTRACT().metadata JSON → {"theme":"dark","lang":"es"} | Flexible/nested data |
| GEOMETRY   | Base spatial type. Can store points, lines, polygons.                                                                                            | Maps, GIS data       |
| POINT      | A single geographic point (lat, long).location POINT                                                                                             | GPS coordinates      |
| LINESTRING | A sequence of points forming a line or route.                                                                                                    | Roads, paths         |
| POLYGON    | A closed spatial area (a region on a map).                                                                                                       | Borders, zones       |

> -- Números: sin comillas 
> 
> -- Fechas, strings: con comillas simples

---



**Key Constraints de clave**

| Constraint  | Qué hace                                                                                                                                                                                              |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PRIMARY KEY | Identifica de forma única cada fila. No puede ser NULL ni repetirse. Solo puede haber una por tabla. Crea un índice automáticamente.id INT PRIMARY KEY                                                |
| FOREIGN KEY | Referencia la PRIMARY KEY de otra tabla. Garantiza integridad referencial — no puedes insertar un valor que no exista en la tabla padre.author_id INT, FOREIGN KEY (author_id) REFERENCES authors(id) |
| UNIQUE      | Garantiza que todos los valores en esa columna sean distintos. A diferencia de PRIMARY KEY, acepta NULL (y puede haber varios NULL).email VARCHAR(100) UNIQUE                                         |

Value Constraints de valor

| Constraint | Qué hace                                                                                                                                       |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| NOT NULL   | El campo es obligatorio. La DB rechaza cualquier INSERT o UPDATE que intente dejar ese campo vacío.name VARCHAR(255) NOT NULL                  |
| NULL       | El campo es opcional (comportamiento por defecto si no se especifica nada). Permite que el campo quede sin valor.middle_name VARCHAR(100) NULL |
| CHECK      | Valida que el valor cumpla una condición lógica antes de insertarlo. Disponible en MySQL 8.0.16+.age INT CHECK (age >= 0 AND age <= 120)       |
| UNSIGNED   | Solo para tipos numéricos. Elimina los negativos y duplica el rango positivo. Muy común en IDs.stock INT UNSIGNED → rango: 0 a 4,294,967,295   |

Default Constraints de valor por defecto y auto-generación

| Constraint     | Qué hace                                                                                                                                                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| DEFAULT        | Define el valor que se usará si el INSERT no especifica nada para esa columna.status VARCHAR(20) DEFAULT 'active'<br>created_at DATETIME DEFAULT CURRENT_TIMESTAMP                                                             |
| AUTO_INCREMENT | MySQL genera automáticamente un número incremental cada vez que se inserta una fila. Solo se puede usar en columnas INT y debe ser una clave (generalmente PRIMARY KEY).id INT AUTO_INCREMENT PRIMARY KEY                      |
| ON UPDATE      | Actualiza automáticamente el valor cuando la fila se modifica. Muy usado con TIMESTAMP para llevar un registro de cuándo fue la última modificación.updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP |

Index Constraints de índice y almacenamiento

| Constraint  | Qué hace                                                                                                                                                   |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| INDEX / KEY | Crea un índice en esa columna para acelerar búsquedas (SELECT con WHERE). No restringe valores, solo mejora el rendimiento.INDEX idx_email (email)         |
| ZEROFILL    | Rellena el número con ceros a la izquierda hasta alcanzar el ancho definido. Deprecado en MySQL 8.0.code INT(5) ZEROFILL → 7 se guarda como 00007          |
| INVISIBLE   | La columna existe pero no aparece en SELECT *. Disponible desde MySQL 8.0. Útil para columnas internas o de auditoría.internal_token VARCHAR(64) INVISIBLE |

En un mismo `CREATE TABLE`, los constraints se pueden combinar libremente en una sola columna:  
`id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY`

---

### 3.3 ALTER TABLE — Modificaciones de columnas *(Extra)*

> 💡 **Tip de Oro:** Antes de usar `ALTER TABLE` en producción, **siempre haz un backup**. Un error puede causar una caída del servicio.

| Operación                  | Comando         | Ejemplo                                       |
| -------------------------- | --------------- | --------------------------------------------- |
| **Agregar** columna        | `ADD`           | `ALTER TABLE users ADD email VARCHAR(100);`   |
| **Modificar** tipo de dato | `MODIFY`        | `ALTER TABLE users MODIFY name VARCHAR(100);` |
| **Renombrar** columna      | `RENAME COLUMN` | `ALTER TABLE users RENAME COLUMN old TO new;` |
| **Eliminar** columna       | `DROP COLUMN`   | `ALTER TABLE users DROP COLUMN col_name;`     |

```sql
-- Agregar columna page_count
ALTER TABLE book_inventory ADD page_count INT;

-- Cambiar nombre de columna (MySQL moderno)
ALTER TABLE book_inventory RENAME COLUMN book_name TO title;

-- Eliminar columna (¡CUIDADO, irreversible!)
ALTER TABLE book_inventory DROP COLUMN page_count;
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 4. CRUD Operations

**CRUD** = **C**reate · **R**ead · **U**pdate · **D**elete — las operaciones fundamentales de cualquier sistema de datos.

> La DB de práctica para esta sección es `thm_books`. Actívala con `USE thm_books;`

---

### C — Create: `INSERT INTO`

```sql
INSERT INTO books (id, name, published_date, description)
VALUES (1, 'Android Security Internals', '2014-10-14', 'An In-Depth Guide to Android Security Architecture');
```

- Especifica la tabla y las columnas.
- `VALUES` provee los datos en el mismo orden que las columnas.

---

### R — Read: `SELECT`

```sql
-- Traer todas las columnas
SELECT * FROM books;

-- Traer columnas específicas
SELECT name, description FROM books;
```

- `*` = todas las columnas.
- `FROM` especifica la tabla fuente.

---

### U — Update: `UPDATE`

```sql
UPDATE books
SET description = 'An In-Depth Guide to Android Security Architecture.'
WHERE id = 1;
```

> ⚠️ **Siempre usa `WHERE`** en un `UPDATE`. Sin él, actualizas **todos** los registros.

---

### D — Delete: `DELETE`

```sql
DELETE FROM books WHERE id = 1;
```

> ⚠️ **Siempre usa `WHERE`** en un `DELETE`. Sin él, eliminas **toda** la tabla.

---

### Resumen CRUD

| Operación  | Statement        | Función                        |
| ---------- | ---------------- | ------------------------------ |
| **Create** | `INSERT INTO`    | Agrega un nuevo registro       |
| **Read**   | `SELECT`         | Lee/recupera registros         |
| **Update** | `UPDATE ... SET` | Modifica un registro existente |
| **Delete** | `DELETE FROM`    | Elimina un registro            |

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 5. Clauses

Las **clauses** especifican criterios sobre los datos que se manipulan. Se usan en conjunto con los statements de CRUD.

> La DB de práctica para esta sección es `thm_books`. Actívala con `USE thm_books;`

---

### `WHERE` — Filtrar registros

Filtra qué registros se ven afectados por la query.

```sql
SELECT * FROM books WHERE id = 1;
```

---

### `DISTINCT` — Evitar duplicados

Retorna solo valores únicos en el resultado.

```sql
SELECT DISTINCT name FROM books;
```

> Sin `DISTINCT`, si "Ethical Hacking" aparece 2 veces en la tabla, se muestra 2 veces. Con `DISTINCT`, solo una.

---

### `GROUP BY` — Agrupar resultados

Agrupa filas con valores iguales en una columna. Muy útil con funciones de agregación.

```sql
SELECT name, COUNT(*)
FROM books
GROUP BY name;
```

```
+----------------------------+----------+
| name                       | COUNT(*) |
+----------------------------+----------+
| Ethical Hacking            |        2 |
| Android Security Internals |        1 |
+----------------------------+----------+
```

---

### `ORDER BY` — Ordenar resultados

Ordena los resultados en forma ascendente (`ASC`) o descendente (`DESC`).

```sql
-- Ascendente (default)
SELECT * FROM books ORDER BY published_date ASC;

-- Descendente
SELECT * FROM books ORDER BY published_date DESC;
```

---

### `HAVING` — Filtrar después de agrupar

Similar a `WHERE`, pero se aplica **después** de que `GROUP BY` ha agregado los datos.

|           | `WHERE`                | `HAVING`                 |
| --------- | ---------------------- | ------------------------ |
| Filtra    | Antes de la agregación | Después de la agregación |
| Usado con | Registros individuales | Grupos (`GROUP BY`)      |

```sql
SELECT name, COUNT(*)
FROM books
GROUP BY name
HAVING name LIKE '%Hack%';
```

```
+-----------------------+----------+
| name                  | COUNT(*) |
+-----------------------+----------+
| Car Hacker's Handbook |        1 |
| Ethical Hacking       |        2 |
+-----------------------+----------+
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 6. Operators

Los operadores filtran y manipulan datos en las queries. Se dividen en **lógicos** y de **comparación**.

> La DB de práctica para esta sección es `thm_books2`. Actívala con `USE thm_books2;`

---

### 6.1 Logical Operators

| Operador  | Función                  | Retorna TRUE si...                         |
| --------- | ------------------------ | ------------------------------------------ |
| `LIKE`    | Busca un patrón en texto | El valor coincide con el patrón            |
| `AND`     | Múltiples condiciones    | **Todas** las condiciones son verdaderas   |
| `OR`      | Múltiples condiciones    | **Al menos una** condición es verdadera    |
| `NOT`     | Invierte una condición   | La condición **no** se cumple              |
| `BETWEEN` | Rango de valores         | El valor está dentro del rango (inclusivo) |

**`LIKE` — Wildcards:**

- `%` → Cero o más caracteres. Ej: `'%guide%'` encuentra "A Guide for Developers"
- `_` → Exactamente un carácter.

```sql
-- LIKE: descripción que contenga "guide"
SELECT * FROM books WHERE description LIKE '%guide%';

-- AND: ambas condiciones deben cumplirse
SELECT * FROM books
WHERE category = 'Offensive Security' AND name = 'Bug Bounty Bootcamp';

-- OR: al menos una condición se cumple
SELECT * FROM books
WHERE name LIKE '%Android%' OR name LIKE '%iOS%';

-- NOT: excluye el patrón
SELECT * FROM books WHERE NOT description LIKE '%guide%';

-- BETWEEN: id entre 2 y 4 (inclusivo)
SELECT * FROM books WHERE id BETWEEN 2 AND 4;
```

---

### 6.2 Comparison Operators

| Operador | Significado   | Ejemplo                                  |
| -------- | ------------- | ---------------------------------------- |
| `=`      | Igual a       | `WHERE name = 'Ethical Hacking'`         |
| `!=`     | No igual a    | `WHERE category != 'Offensive Security'` |
| `<`      | Menor que     | `WHERE published_date < '2020-01-01'`    |
| `>`      | Mayor que     | `WHERE published_date > '2020-01-01'`    |
| `<=`     | Menor o igual | `WHERE published_date <= '2021-11-15'`   |
| `>=`     | Mayor o igual | `WHERE published_date >= '2021-11-02'`   |

```sql
-- Igual: nombre exacto
SELECT * FROM books WHERE name = 'Designing Secure Software';

-- No igual: excluir una categoría
SELECT * FROM books WHERE category != 'Offensive Security';

-- Menor que: publicados antes del 2020
SELECT * FROM books WHERE published_date < '2020-01-01';

-- Mayor o igual: publicados desde Nov 2021
SELECT * FROM books WHERE published_date >= '2021-11-02';
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 7. Functions

Las funciones de SQL manipulan y transforman datos dentro de las queries. Se dividen en **String Functions** y **Aggregate Functions**.

---

### 7.1 String Functions

Operan sobre cadenas de texto y retornan un valor modificado.

| Función          | Qué hace                                        | Ejemplo de uso                                |
| ---------------- | ----------------------------------------------- | --------------------------------------------- |
| `CONCAT()`       | Une dos o más strings                           | Combinar nombre y categoría en una frase      |
| `GROUP_CONCAT()` | Une valores de múltiples filas en un solo campo | Listar libros por categoría en una sola celda |
| `SUBSTRING()`    | Extrae una parte de un string                   | Obtener solo el año de una fecha              |
| `LENGTH()`       | Retorna el número de caracteres                 | Saber cuántos caracteres tiene un nombre      |

```sql
-- CONCAT: combinar columnas en una oración
SELECT CONCAT(name, ' is a type of ', category, ' book.') AS book_info
FROM books;

-- GROUP_CONCAT: agrupar libros por categoría
SELECT category, GROUP_CONCAT(name SEPARATOR ', ') AS books
FROM books
GROUP BY category;

-- SUBSTRING: extraer el año de una fecha (posición 1, largo 4)
SELECT SUBSTRING(published_date, 1, 4) AS published_year
FROM books;

-- LENGTH: largo del nombre en caracteres
SELECT name, LENGTH(name) AS name_length
FROM books;
```

**Output `GROUP_CONCAT`:**

```
+--------------------+-------------------------------------------------------------+
| category           | books                                                       |
+--------------------+-------------------------------------------------------------+
| Defensive Security | Android Security Internals, Designing Secure Software       |
| Offensive Security | Bug Bounty Bootcamp, Car Hacker's Handbook, Ethical Hacking |
+--------------------+-------------------------------------------------------------+
```

---

### 7.2 Aggregate Functions

Combinan múltiples valores de filas en un único resultado. Se usan generalmente con `GROUP BY`.

| Función   | Qué hace                              | Ejemplo                                        |
| --------- | ------------------------------------- | ---------------------------------------------- |
| `COUNT()` | Cuenta el número de registros         | ¿Cuántos libros hay en total?                  |
| `SUM()`   | Suma todos los valores de una columna | ¿Cuánto suma el precio de todos los libros?    |
| `MAX()`   | Retorna el valor máximo               | ¿Cuál es la fecha de publicación más reciente? |
| `MIN()`   | Retorna el valor mínimo               | ¿Cuál es la fecha de publicación más antigua?  |

```sql
-- COUNT: total de libros
SELECT COUNT(*) AS total_books FROM books;
-- Resultado: 5

-- SUM: suma total de precios
SELECT SUM(price) AS total_price FROM books;
-- Resultado: 249.95

-- MAX: libro publicado más recientemente
SELECT MAX(published_date) AS latest_book FROM books;
-- Resultado: 2021-12-21

-- MIN: libro publicado más antiguo
SELECT MIN(published_date) AS earliest_book FROM books;
-- Resultado: 2014-10-14
```

---

### 7.3 Ejercicios Prácticos — `tools_db`

```sql
-- ¿Cuál herramienta tiene el nombre más largo?
SELECT NAME, LENGTH(NAME) AS name_length
FROM hacking_tools
ORDER BY name_length DESC;
-- Resultado: USB Rubber Ducky (16 caracteres)

-- ¿Cuál es la suma total de todas las herramientas?
SELECT SUM(amount) FROM hacking_tools;
-- Resultado: 1444

-- ¿Qué herramientas tienen un amount que NO termina en 0?
-- (Usar % como operador módulo/residuo, no como wildcard)
SELECT GROUP_CONCAT(name SEPARATOR ' & ') AS tools_list
FROM hacking_tools
WHERE amount % 10 != 0;
-- Resultado: Flipper Zero & iCopy-XS
-- Nota: amount % 10 != 0 filtra los que NO son múltiplos de 10, Si el residuo % es 0 quiere decir el numero dividido entre 10 termina en 0.
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 8. Quick Reference Cheatsheet

### 🗂️ Navegación de DBs

```sql
SHOW DATABASES;          -- Ver todas las bases de datos
CREATE DATABASE nombre;  -- Crear nueva base de datos
USE nombre;              -- Seleccionar base de datos activa
SELECT DATABASE();       -- Confirmar cuál DB está activa
DROP DATABASE nombre;    -- Eliminar base de datos ⚠️
```

### 🗃️ Gestión de Tablas

```sql
SHOW TABLES;                       -- Ver tablas en la DB activa
DESCRIBE tabla;   -- o DESC tabla  -- Ver estructura de la tabla
CREATE TABLE tabla (col tipo, ...);
ALTER TABLE tabla ADD col tipo;
ALTER TABLE tabla MODIFY col tipo;
ALTER TABLE tabla RENAME COLUMN old TO new;
ALTER TABLE tabla DROP COLUMN col;  -- ⚠️ irreversible
DROP TABLE tabla;                   -- ⚠️ elimina toda la tabla
```

### 📝 CRUD

```sql
INSERT INTO tabla (col1, col2) VALUES (val1, val2);  -- Create
SELECT col1, col2 FROM tabla WHERE condicion;         -- Read
UPDATE tabla SET col = valor WHERE condicion;         -- Update ⚠️ siempre con WHERE
DELETE FROM tabla WHERE condicion;                    -- Delete ⚠️ siempre con WHERE
```

### 🔍 Clauses & Operators

```sql
WHERE col = 'valor'         -- Filtrar registros
DISTINCT col                -- Sin duplicados
GROUP BY col                -- Agrupar por columna
ORDER BY col ASC/DESC       -- Ordenar resultados
HAVING condicion            -- Filtrar grupos (post-agregación)

-- Lógicos
LIKE '%patron%'             -- Patrón de texto
AND / OR / NOT              -- Combinar condiciones
BETWEEN val1 AND val2       -- Rango inclusivo

-- Comparación
= != < > <= >=
```

### ⚙️ Functions

```sql
-- String
CONCAT(a, b, ...)           -- Une strings
GROUP_CONCAT(col SEPARATOR ',')  -- Une filas en un string
SUBSTRING(col, inicio, largo)    -- Extrae parte de un string
LENGTH(col)                 -- Longitud en caracteres

-- Aggregate
COUNT(*) / COUNT(col)       -- Contar registros
SUM(col)                    -- Sumar valores
MAX(col)                    -- Valor máximo
MIN(col)                    -- Valor mínimo
```

---

## 📝 Mis Notas & Preguntas

> *[ Escribe aquí tus dudas, insights y conexiones con otros conceptos ]*

---

*TryHackMe — Cybersecurity 101 | SQL Fundamentals*
