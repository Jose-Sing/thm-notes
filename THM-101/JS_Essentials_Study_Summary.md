# 🛡️ TryHackMe — Cybersecurity 101
## Module: JavaScript Essentials | Study Summary
> **Keywords:** JS · Browser Scripting · DOM · Dialogue Functions · Obfuscation · Security

---

## 📚 Table of Contents

1. [JavaScript Fundamentals](#1-javascript-fundamentals)
2. [Integrating JavaScript into HTML](#2-integrating-javascript-into-html)
3. [Dialogue Functions & Abuse](#3-dialogue-functions--abuse)
4. [Control Flow & Bypassing](#4-control-flow--bypassing)
5. [Minification & Obfuscation](#5-minification--obfuscation)
6. [Best Practices](#6-best-practices)
7. [Quick Reference & Links](#7-quick-reference--links)

---

## 1. JavaScript Fundamentals

### What is JavaScript?

**JavaScript (JS)** is a popular **scripting language** that adds interactivity to web pages built with HTML and CSS. It runs primarily on the **client side** and is **interpreted** — code executes directly in the browser without prior compilation.

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Variables

Variables are named containers that store data values so they can be referenced later.

| Keyword | Scope | Notes |
|---|---|---|
| `var` | Function-scoped | Older style, less control over visibility |
| `let` | Block-scoped | Preferred for mutable variables |
| `const` | Block-scoped | Value cannot be reassigned after declaration |

```javascript
// Open Chrome DevTools Console (Ctrl + Shift + I) and try:

// var — function-scoped, can be redeclared
var city = "San Jose";
var city = "Heredia";    // no error — var allows redeclaration
console.log(city);       // Output: Heredia

// let — block-scoped, cannot be redeclared in same scope
let age = 25;
age = 30;                // OK — reassignment is allowed
console.log(age);        // Output: 30

// const — block-scoped, value is fixed after declaration
const PI = 3.14159;
// PI = 3;              // ❌ TypeError: Assignment to constant variable
console.log(PI);         // Output: 3.14159
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Data Types

Data types define what kind of value a variable can hold:

| Type | Description |
|---|---|
| `string` | Text values: `"hello"`, `'world'` |
| `number` | Numeric values: `25`, `3.14` |
| `boolean` | Logical values: `true` or `false` |
| `null` | Intentional absence of value |
| `undefined` | Variable declared but not yet assigned |
| `object` | Complex data — arrays, objects, etc. |

```javascript
// Try these in the Chrome DevTools Console:

let username = "hacker42";        // string
let score    = 100;               // number
let isAdmin  = false;             // boolean
let session  = null;              // null  — intentionally empty
let token;                        // undefined — declared, not assigned yet
let profile  = {                  // object
    name: "Alice",
    role: "pentester"
};

console.log(typeof username);     // Output: string
console.log(typeof score);        // Output: number
console.log(typeof isAdmin);      // Output: boolean
console.log(typeof session);      // Output: object  ← known JS quirk, null returns "object"
console.log(typeof token);        // Output: undefined
console.log(typeof profile);      // Output: object
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Functions

A **function** is a named, reusable block of code designed to perform a specific task. Functions avoid repetition (DRY principle) and accept **arguments** as input.

```javascript
// Basic function declaration and call:
function PrintResult(rollNum) {
    alert("Roll number " + rollNum + " has passed the exam");
}
PrintResult(101);        // Calls the function — triggers the alert

// Function with a return value:
function add(a, b) {
    return a + b;
}
let result = add(5, 10);
console.log(result);     // Output: 15

// Function with a default argument:
function greet(name = "stranger") {
    console.log("Hello, " + name + "!");
}
greet("Alice");          // Output: Hello, Alice!
greet();                 // Output: Hello, stranger!
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Loops

Loops repeat a block of code while a condition is `true`. Common loop types:

- `for` — iterate a fixed number of times
- `while` — repeat while condition holds
- `do...while` — run at least once, then check condition

```javascript
// --- FOR loop ---
// Syntax: for (initialization; condition; increment)
for (let i = 0; i < 5; i++) {
    console.log("Iteration number: " + i);
}
// Output:
// Iteration number: 0
// Iteration number: 1
// Iteration number: 2
// Iteration number: 3
// Iteration number: 4


// --- WHILE loop ---
// Runs as long as the condition is true
let count = 0;
while (count < 3) {
    console.log("Count is: " + count);
    count++;
}
// Output:
// Count is: 0
// Count is: 1
// Count is: 2


// --- DO...WHILE loop ---
// Always runs at least once, even if condition is false from the start
let attempts = 0;
do {
    console.log("Attempt: " + attempts);
    attempts++;
} while (attempts < 3);
// Output:
// Attempt: 0
// Attempt: 1
// Attempt: 2


// --- Loop with an array (practical example from the module) ---
const rollNumbers = [101, 102, 103];
for (let i = 0; i < rollNumbers.length; i++) {
    PrintResult(rollNumbers[i]);
}
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Request-Response Cycle

When a user's browser (**client**) sends a request to a **web server**, the server responds with the requested resources (webpage, data, etc.). JS operates within this cycle to make pages dynamic.

🔗 [How Websites Work — TryHackMe](https://tryhackme.com/r/room/howwebsiteswork)

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 2. Integrating JavaScript into HTML

### Internal JavaScript

JS written directly inside an HTML file, between `<script>` tags. Best for small projects or quick demos.

| Placement | Behavior |
|---|---|
| `<script>` in `<head>` | Loaded before page content renders |
| `<script>` in `<body>` | Runs as page elements are loaded — most common |

```html
<!-- internal.html — save this file and open with Chrome -->
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Internal JS</title>
</head>
<body>
    <h1>Addition of Two Numbers</h1>
    <p id="result"></p>

    <script>
        let x = 5;
        let y = 10;
        let result = x + y;
        document.getElementById("result").innerHTML = "The result is: " + result;
    </script>
</body>
</html>
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### External JavaScript

JS stored in a separate `.js` file, linked from HTML using the `src` attribute. Keeps code clean and reusable.

```html
<!-- external.html -->
<!DOCTYPE html>
<html lang="en">
<body>
    <h1>Addition of Two Numbers</h1>
    <p id="result"></p>

    <!-- Link to the external JS file -->
    <script src="script.js"></script>
</body>
</html>
```

```javascript
// script.js — separate file, same folder as external.html
let x = 5;
let y = 10;
let result = x + y;
document.getElementById("result").innerHTML = "The result is: " + result;
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Identifying Internal vs External JS *(Pentesting Tip)*

When pentesting a web app, inspect the page source to determine where JS is coming from:

- **No `src` attribute** → `<script>` tag contains inline/internal JS
- **`src` attribute present** → JS is loaded from an external file

```html
<!-- How to check: right-click → View Page Source (Ctrl + U) -->

<script> /* code lives here */ </script>                        <!-- Internal JS -->
<script src="app.js"></script>                                  <!-- External JS (same server) -->
<script src="https://cdn.example.com/lib.js"></script>          <!-- External JS (CDN) -->
```

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 3. Dialogue Functions & Abuse

JS provides built-in dialogue functions for user interaction. If unsanitized, attackers can exploit them for **XSS** attacks.

| Function | What it does | Returns |
|---|---|---|
| `alert(msg)` | Displays a message with an OK button | `undefined` |
| `prompt(msg)` | Asks the user for text input | The entered string, or `null` if cancelled |
| `confirm(msg)` | Shows an OK / Cancel dialog | `true` (OK) or `false` (Cancel) |

```javascript
// --- alert() ---
// Paste in Chrome DevTools Console:
alert("Hello THM");
// → A pop-up appears with the message. No return value.


// --- prompt() ---
let userName = prompt("What is your name?");
alert("Hello " + userName + "!");
// → First pop-up asks for input.
// → Second pop-up greets the user with whatever they typed.
// → If the user clicks Cancel, userName = null


// --- confirm() ---
let proceed = confirm("Do you want to continue?");
console.log(proceed);
// → If user clicks OK     → Output: true
// → If user clicks Cancel → Output: false


// --- Combining all three (practical flow) ---
let age = prompt("How old are you?");
if (confirm("Are you sure you are " + age + " years old?")) {
    alert("Access granted!");
} else {
    alert("Please try again.");
}
```

### How Attackers Exploit Dialogue Functions

A malicious HTML file sent by email can abuse loops with `alert()` to flood the user with hundreds of pop-ups, causing disruption or worse.

```html
<!-- invoice.html — simple dialogue spam attack -->
<!DOCTYPE html>
<html>
<body>
    <script>
        for (let i = 0; i < 500; i++) {
            alert("Hacked");   // forces the user to close 500 pop-ups
        }
    </script>
</body>
</html>
```

> ⚠️ **Only execute JS files from TRUSTED sources. Even a simple `.html` attachment can be weaponized.**

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 4. Control Flow & Bypassing

### Conditional Statements (if-else)

Execute different code blocks based on whether a condition is `true` or `false`.

```javascript
// Basic if-else — from the module:
age = prompt("What is your age?");
if (age >= 18) {
    document.getElementById("message").innerHTML = "You are an adult.";
} else {
    document.getElementById("message").innerHTML = "You are a minor.";
}

// Chaining conditions with else if:
let score = 75;
if (score >= 90) {
    console.log("Grade: A");
} else if (score >= 75) {
    console.log("Grade: B");
} else if (score >= 60) {
    console.log("Grade: C");
} else {
    console.log("Grade: F");
}
// Output: Grade: B
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Bypassing Login Forms *(Security Perspective)*

When authentication logic is implemented purely in client-side JS, it is visible and manipulatable by anyone inspecting the source. An attacker can:

- Read the expected credentials directly from the source code
- Modify variable values in the browser console to bypass checks
- Intercept and alter JS before it executes

```html
<!-- ⚠️ UNSAFE — client-side only authentication (login.html) -->
<script>
    let username = prompt("Enter username:");
    let password = prompt("Enter password:");

    if (username === "admin" && password === "secret123") {
        alert("Welcome, admin!");
    } else {
        alert("Access denied.");
    }
</script>

<!-- Problem: anyone can View Source (Ctrl + U) and read
     "admin" and "secret123" in plain text.
     No guessing needed — the answer is right there. -->
```

> ⚠️ **NEVER rely solely on client-side JS for authentication. Server-side validation is mandatory.**

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 5. Minification & Obfuscation

### Minification

**Minification** compresses JS files by removing whitespace, line breaks, comments, and shortening variable names. It **reduces file size and improves load time** without changing functionality.

```javascript
// Original readable code (hello.js):
function hi() {
    alert("Welcome to THM");
}
hi();

// After minification — same logic, all whitespace removed:
function hi(){alert("Welcome to THM")}hi();
```

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Obfuscation

**Obfuscation** makes JS intentionally difficult to read by renaming variables/functions to meaningless names, inserting dummy code, and encoding strings. The code remains fully **functional but is not human-readable**.

```javascript
// Original code:
function hi() {
    alert("Welcome to THM");
}
hi();

// After obfuscation — same behavior, completely unreadable:
(function(_0x114713,_0x2246f2){var _0x51a830=_0x33bf,_0x4ce60b=_0x114713();
while(!![]){try{var _0x51ecd3=-parseInt(_0x51a830(0x88))...
// → The browser executes this perfectly. Humans cannot read it at a glance.
```

```javascript
// Workflow to obfuscate / deobfuscate:

// To OBFUSCATE:
// 1. Write your JS (hello.js)
// 2. Paste into → https://codebeautify.org/javascript-obfuscator
// 3. Copy the output back into hello.js
// 4. Reload hello.html in Chrome → same behavior, unreadable source

// To DEOBFUSCATE (reverse it):
// 1. Copy the obfuscated code
// 2. Paste into → https://obf-io.deobfuscate.io/
// 3. Get back the human-readable version
```

| Tool | Link |
|---|---|
| Obfuscator | [codebeautify.org/javascript-obfuscator](https://codebeautify.org/javascript-obfuscator) |
| Deobfuscator | [obf-io.deobfuscate.io](https://obf-io.deobfuscate.io/) |

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

### Why it matters in Cybersecurity

- Minified/obfuscated code is harder to audit during a code review or pentest
- Malicious scripts are often obfuscated to evade detection by security tools
- **Deobfuscation** is a key skill — recognize that obfuscated code is still fully executable

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 6. Best Practices

> ⚠️ *This section was listed in the module but had no content — completed based on the module's topics.*

### Writing secure and maintainable code

- Use `let` and `const` instead of `var` for predictable, block-scoped variables
- Never implement authentication logic **only** on the client side
- Always **validate and sanitize** user input to prevent XSS via dialogue functions
- Keep JS in external `.js` files for medium/large projects — easier to maintain and audit

### When Pentesting / doing a Code Review

- Inspect page source (`Ctrl + U`) to identify whether JS is **internal or external**
- Check if authentication or critical logic is exposed on the client side
- Look for unsafe patterns: `eval()`, `innerHTML`, `document.write()` without sanitization — common XSS vectors
- Learn to **deobfuscate JS** — attackers use obfuscation to hide malicious code

### Operational Security

- Never open `.html` or `.js` email attachments unless you fully trust the source
- Remember: **minification ≠ security** — obfuscated code is still fully reversible
- When in doubt about a JS file, paste it into a deobfuscator before executing it

> **In other words:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

> **If it's missing:** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

---

## 7. Quick Reference & Links

### Key Concepts at a Glance

| Concept | Description |
|---|---|
| **Interpreted language** | Executes directly in browser, no compilation needed |
| **Client-side execution** | Code is visible and inspectable by anyone |
| **DOM manipulation** | `document.getElementById("id").innerHTML = "value"` |
| **`console.log()`** | Prints output to the browser developer console |
| **Internal JS tag** | `<script> ... </script>` inside the HTML file |
| **External JS tag** | `<script src="file.js"></script>` |
| **Open DevTools** | `Ctrl + Shift + I` in Chrome |
| **View Page Source** | `Ctrl + U` in Chrome |

### Useful Links

| Resource | URL |
|---|---|
| How Websites Work (THM Room) | [tryhackme.com/r/room/howwebsiteswork](https://tryhackme.com/r/room/howwebsiteswork) |
| JS Obfuscator | [codebeautify.org/javascript-obfuscator](https://codebeautify.org/javascript-obfuscator) |
| JS Deobfuscator | [obf-io.deobfuscate.io](https://obf-io.deobfuscate.io/) |

---

## 📝 My Notes & Questions

> *[ Write your questions, insights, and connections to other concepts here ]*

---

*TryHackMe — Cybersecurity 101 | JavaScript Essentials*
