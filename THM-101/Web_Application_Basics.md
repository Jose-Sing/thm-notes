---

---

# Web Application Basics

## Web application Overview

### Fron end

> As analogy this can be considered as the surface of a planet, the pasts that an astronaut can see and interact with based on the laws of nature.

* HTML (Hypertext Markup Language): This is a set of instructions or code that intruscts a web browser on what to display and how to display it. 

* CSS (Cascadig Style Sheets): This describes a standard appearance, such as certain colours, type of text, and layouts.

* JS (JAvaScript): This is more advance set of instruction than HTML, that allows choices and decisions to be made on what to display. So this enables more complex activity in the wen browser.

### Back End

> Back end of a web application are things we don't see within a web browser but are important for the web application to work. As analogy, this can be the non-visual structures that keep a building standing, the air, and the gravity that keeps feet on the ground.

* Database: is where infomation can be stored, modified and retrieved. And example of this type of information can be the visitor's preferences on what to show or now.

* Infrastructure: Web servers, appliccation servers, storage, various network devices, and other software that supports the web application.

* WAF (Web Application Firewall): this is an optional component for web applications, it helps filter out dangerous requets and provide an element of protection.

---

## Uniform Resource Locator URL

This is the web address that lets us access all kinds of online content [webpage, a video, a photo, etc]. This guides our browser to the right place on the internet.



### Anatomy of a URL

> ![Illustration depicting the various parts of a URL, including the protocol, domain name, path, query parameters, and fragment. Each component is labeled and visually distinct, demonstrating how they work together to form a complete web address.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5c549500924ec576f953d9fc/room-content/34ad66d8b90aaaa35f9536d3b152ea97.png)



* **Scheme**: this is the protocol used to access the website. The most common are **HTTP** (HyperText Transfer Protocol) and **HTTPS** (HyperText Transfer Protocol secure).

* **User**: This can include for some sites authentication but rare nowaday becuase isn't safe.

* **Host/Domain**: the most important part of the URL because it tells which website we are accessing.
  
  > domain names that appear almost like real ones but have small differences (this is called **typosquatting**). These fake domains are often used in phishing attacks to trick people into giving up sensitive info.

* **Ports**: helps direct the browser to the right service on the web server
  
  > Port numbers range from 1 to 65,535, but the most common are **80** for HTTP and **443** for HTTPS.

* **Path**: points the specific file or page on the server that we want to access. It's like a roadmap

* **Query String**: it's the part of a URL that start with ? mark, often used to search terms or form inputs.
  
  > Since users can modify these query strings, it's important to handle them securely to prevent attacks like **injections**, where malicious code could be added.

* **Fragment**: starts with the symbol # and helps point to a specific section of a webpage like jumping directly to a particular heading or table.
  
  > Like with query strings, it's important to check and clean up any data here to avoid issues like injection attacks.
  
  

---

## HTTP Messages



There are 2 types of HTTP messages:

* HTTP request: Client 

* HTTP response: Server
  
  

Parts of the HTTP message:

```bash
POST /login HTTP/1.1                                    #Start Line: Introduction of the message, this tells if "request/response" and give info about how the message should be handle.
Host: tryhackme.com                                    #Headers:  give instructions to both the client and the server handling the request or response. These headers cover all sorts of things, like security, content types, and more, making sure everything goes smoothly in the communication.
Content-Type: application/x-www-form-urlencoded
Content-Length: 43

                                                       #Empty line separates the header from the body. without this can cause errors
username-aleksandra&password=securepassword            # Where the sata is stored might include data the user wants to send to the server (like form data). In a response, it's where the server puts the content that the user requested (like a webpage or API data).
```

![Illustration showing a login POST request and a login successful response.](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1728786920770.png)



---

## Http Request [lines and methods]

> this is what a user [client] sends to a web server to interact with a web application.

![Illustration showing a GET request and all of the different headers that are used.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1730445201524.png)



### Request Line:

tells the server what kind of request it’s dealing with. It has three main parts: the **HTTP method**, the **URL path**, and the **HTTP version**.

### HTTP Method

tell what action the user wants to perform: 

* GET: Get data from the server without making any changes. [never put sensitive info like tokes or PW since this can show up as plaintext]

* POST: Sends data to the server to create or update [ Always validate and clean the input to avoid attacks like SQL injection or XSS.]

* PUT: Replace or updates something on the server. [make sure only authorise users can delete resources]

* DELETE: Removes something from the server [make sure only authorise users can delete resources]

* PATCH: Updates part of a resource. useful to make small changes without replacing the whole thing.

* HEAD: works like GET but only retrives headers, not the full content. Usefull for checking metadata without dowloading the full response.

* OPTIONS: Tells you what methods are available for a specific resource, helping clients understand what they can do with the server.

* TRACE: Similar to OPTIONS, it shows which methods are allowed, often for debugging. Many servers disable it for security reasons.

* CONNET: Used to create a secure connection, like for HTTPS. It's not as common but is critical for encrypted communication.

> Each of these methods has its own set of security rules. For example, PATCH requests should be validated to avoid inconsistencies, and OPTIONS and TRACE should be turned off if not needed to avoid possible security risks.



### URL PATH

Tells the server where to find the resource the user is asking for.

> Attackers often try to manipulate the URL path to exploit vulnerabilities, so it's crucial to:
> 
> * Validate the URL path to prevent unauthorised access
> * Sanitise the path to avoid injection attacks
> * Protect sensitive data by conducting privacy and risk assessments

### HTTP Version

Shows the protocol version used to communicate between the client and server

> **HTTP/0.9** (1991) The first version, only supported GET requests.
> 
> **HTTP/1.0** (1996) Added headers and better support for different types of content, improving caching.
> 
> **HTTP/1.1** (1997) Brought persistent connections, chunked transfer encoding, and better caching. It's still widely used today.
> 
> **HTTP/2** (2015) Introduced features like multiplexing, header compression, and prioritisation for faster performance.
> 
> **HTTP/3** (2022) Built on HTTP/2, but uses a new protocol (QUIC) for quicker and more secure connections



Although HTTP/2 and HTTP/3 offer better speed and security, many systems still use **HTTP/1.1** because it's well-supported and works with most existing setups. However, upgrading to HTTP/2 or HTTP/3 can provide significant performance and security improvements as more systems adopt them.



---

## Http Request [Headers and Body]

Headers are extra information to be conveyed to the web server Some common headers are as follows:  

### Common Request Headers

| **Request Header** | **Example**                                                                      | **Description**                                                                          |
| ------------------ | -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Host               | `Host: tryhackme.com`                                                            | Specifies the name of the web server the request is for.                                 |
| User-Agent         | `User-Agent: Mozilla/5.0`                                                        | Shares information about the web browser the request is coming from.                     |
| Referer            | `Referer: https://www.google.com/`                                               | Indicates the URL from which the request came from.                                      |
| Cookie             | `Cookie: user_type=student; room=introtowebapplication; room_status=in_progress` | Information the web server previously asked the web browser to store is held in cookies. |
| Content-Type       | `Content-Type: application/json`                                                 | Describes what type or format of data is in the request.                                 |



### Request Body

> Extra information to be conveyed to the web server. Common body forms are: `URL Encoded`, `Form Data`, `JSON`, or `XML`



**URL Encoded (application/x-www-form-urlencoded)**  
A format where data is structured in pairs of key and value where (`key=value`). Multiple pairs are separated by an (`&`) symbol, such as `key1=value1&key2=value2`. Special characters are percent-encoded.

```bash
POST /profile HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 33

name=Aleksandra&age=27&country=US   #here you can see how seach value is separated with &
```



**Form Data (multipart/form-data)**



Allows multiple data blocks to be sent where each block is separated by a boundary string. The boundary string is the defined header of the request itself. This type of formatting can be used to send binary data, such as when uploading files or images to a web server.



```bash
POST /upload HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="username"

aleksandra
----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="profile_pic"; filename="aleksandra.jpg"
Content-Type: image/jpeg

[Binary Data Here representing the image]
----WebKitFormBoundary7MA4YWxkTrZu0gW--
```



**JSON (application/json)**  
In this format, the data can be sent using the JSON (JavaScript Object Notation) structure. Data is formatted in pairs of name : value. Multiple pairs are separated by commas, all contained within curly braces { }.



```
POST /api/user HTTP/1.1\r\n
Host: tryhackme.com\r\n
User-Agent: Mozilla/5.0\r\n
Content-Type: application/json\r\n
Content-Length: 62\r\n
\r\n
{
    "name": "Aleksandra",
    "age": 27,
    "country": "US"
}
```



**XML (application/xml)**  
In XML formatting, data is structured inside labels called tags, which have an opening and closing. These labels can be nested within each other. You can see in the example below the opening and closing of the tags to send details about a user called Aleksandra.



```bash
POST /api/user HTTP/1.1\r\n
Host: tryhackme.com\r\n
User-Agent: Mozilla/5.0\r\n
Content-Type: application/xml\r\n
Content-Length: 124\r\n
\r\n
<user>
    <n>Aleksandra</n>
    <age>27</age>
    <country>US</country>
</user>
```



---

## Http Request Status line and status codes



### Status line

Every HTTP response has the first line calls "Status line" it gives:

1. HTTP version

2. Status code: 3 digit number

3. Reason phrase: short message explanning the status code
   
   

### Status codes and reasons phrases

Status code tells if the request succeeded or failed. there are 5 categories:

**Informational Responses (100-199)**  

> These codes mean the server has received part of the request and is waiting for the rest. It's a "keep going" signal.

**Successful Responses (200-299)**  

> These codes mean everything worked as expected. The server processed the request and sent back the requested data.

**Redirection Messages (300-399)**  

> These codes tell you that the resource you requested has moved to a different location, usually providing the new URL.

**Client Error Responses (400-499)**  

> These codes indicate a problem with the request. Maybe the URL is wrong, or you're missing some required info, like authentication.

**Server Error Responses (500-599)**  

> These codes mean the server encountered an error while trying to fulfil the request. These are usually server-side issues and not the client's fault.



### Common Status Codes

* **100 (Continue)**  
  The server got the first part of the request and is ready for the rest.

* **200 (OK)**  
  The request was successful, and the server is sending back the requested resource.

* **301 (Moved Permanently)**  
  The resource you're requesting has been permanently moved to a new URL. Use the new URL from now on.

* **404 (Not Found)**  
  The server couldn't find the resource at the given URL. Double-check that you've got the right address.

* **500 (Internal Server Error)**  
  Something went wrong on the server's end, and it couldn't process your request.
  
  

---

## Http Response Headers and body



### Headers

> These headers provide important info about the response and tell the client (usually the browser) how to handle it.



Key headers like `Content-Type`, `Content-Length`, and `Date` give us important details about the response the server sends back.

![HTTP response header](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1741854427607.svg)



* **Date**:  
  Example: `Date: Fri, 23 Aug 2024 10:43:21 GMT`  
  Shows exact date and time when the response was generated by the server.

* **Content-Type**:  
  Example: `Content-Type: text/html; charset=utf-8`  
  It tells the client what kind of content it's getting, like whether it's HTML, JSON, or something else. It also includes the character set (like UTF-8) to help the browser display it properly.

* **Server**:  
  Example: `Server: nginx`  
  This header shows what kind of server software is handling the request. It's good for debugging, but it can also reveal server information that might be useful for attackers, so many people remove or obscure this one.
  
  

#### Other common response headers.

> other common headers that give additional instructions to the client or browser and help control how the response should be handled.



* **Set-Cookie**:  
  Example: `Set-Cookie: sessionId=38af1337es7a8`  
  This one sends cookies from the server to the client, which the client then stores and sends back with future requests. To keep things secure, make sure cookies are set with the `HttpOnly` flag (so they can't be accessed by JavaScript) and the `Secure` flag (so they're only sent over HTTPS).

* **Cache-Control**:  
  Example: `Cache-Control: max-age=600`  
  This header tells the client how long it can cache the response before checking with the server again. It can also prevent sensitive info from being cached if needed (using `no-cache`).

* **Location**:  
  Example: `Location: /index.html`  
  This one's used in redirection (3xx) responses. It tells the client where to go next if the resource has moved. If users can modify this header during requests, be careful to validate and sanitise it—otherwise, you could end up with open redirect vulnerabilities, where attackers can redirect users to harmful sites.
  
  

### Response Body

> is where the actual data lives—things like HTML, JSON, images, etc., that the server sends back to the client. To prevent **injection attacks** like Cross-Site Scripting (XSS), always sanitise and escape any data (especially user-generated content) before including it in the response.



---

## Security Headers

HTTP Security Headers help improve the overall security of the web application by providing mitigations against attacks like Cross-Site Scripting (XSS), clickjacking, and others.

> In other words: these are extra instructions the server sends to the browser saying "here's how you're allowed to behave on this page." Without them, the browser operates with no rules — which is exactly what attackers exploit.

* Content-Security-Policy (CSP)
* Strict-Transport-Security (HSTS)
* X-Content-Type-Options
* Referrer-Policy

> You can use [https://securityheaders.io/](https://securityheaders.io/) to analyse the security headers of any website and see which ones are missing or misconfigured.

---

### Content-Security-Policy (CSP)

A CSP header is an additional security layer that helps mitigate against common attacks like **Cross-Site Scripting (XSS)**. Malicious code could be hosted on a separate website or domain and injected into the vulnerable website. A CSP provides a way for administrators to define what domains or sources are considered safe.

> In other words: CSP is a whitelist the server sends to the browser. It says "only load scripts, styles, and images from these approved places." If an attacker injects a script from an unknown domain, the browser refuses to run it.

> **If it's missing:** The browser will load and execute resources from anywhere — including attacker-controlled domains. XSS attacks become much easier to pull off.

The `self` keyword is a special value that means "the same domain this page is hosted on."

**Example:**

```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.tryhackme.com; style-src 'self'
```

| Directive     | Value                              | Meaning                                             |
| ------------- | ---------------------------------- | --------------------------------------------------- |
| `default-src` | `'self'`                           | Default policy: only load from the current domain   |
| `script-src`  | `'self' https://cdn.tryhackme.com` | Scripts allowed from current domain + TryHackMe CDN |
| `style-src`   | `'self'`                           | CSS stylesheets only from current domain            |

---

### Strict-Transport-Security (HSTS)

The HSTS header ensures that web browsers will **always connect over HTTPS**, even if a user types `http://` manually or clicks an old HTTP link.

> In other words: HSTS tells the browser "never talk to this site over plain HTTP again." Once received, the browser enforces this rule on its own — no need to rely on the server to redirect.

> **If it's missing:** Attackers can perform **SSL stripping attacks** — intercepting the connection before HTTPS is established and downgrading it to plain HTTP, where traffic is visible.

**Example:**

```http
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
```

| Directive           | Description                                                                                        |
| ------------------- | -------------------------------------------------------------------------------------------------- |
| `max-age=63072000`  | Browser enforces HTTPS for ~2 years (value in seconds)                                             |
| `includeSubDomains` | Applies the rule to all subdomains as well                                                         |
| `preload`           | Allows the site to be added to browser preload lists — HTTPS enforced even on the very first visit |

---

### X-Content-Type-Options

This header instructs browsers **not to guess (sniff) the MIME type** of a resource. The browser must use only what the server declares in the `Content-Type` header.

> In other words: without this header, browsers try to be "helpful" and guess what type of file they're receiving — even if the server says it's plain text. Attackers abuse this by uploading files disguised as innocent content that the browser then executes as JavaScript or HTML.

> **If it's missing:** An attacker could upload a file with a harmless-looking extension (like `.txt`) but containing malicious script. The browser might sniff it, detect it as JavaScript, and execute it.

**Example:**

```http
X-Content-Type-Options: nosniff
```

| Directive | Description                                                                      |
| --------- | -------------------------------------------------------------------------------- |
| `nosniff` | Browser must not guess the content type — only trust the declared `Content-Type` |

---

### Referrer-Policy

This header controls **how much information about the origin page is shared** when a user navigates away — for example, when clicking a link to an external site.

> In other words: every time you click a link, your browser can tell the destination site where you came from (the "referrer"). This can leak internal URLs, session identifiers, or private paths. Referrer-Policy lets you control exactly how much of that gets shared.

> **If it's missing:** Internal URLs, query parameters, and sensitive path information can leak to third-party sites every time a user clicks an outbound link.

**Examples:**

```http
Referrer-Policy: no-referrer
Referrer-Policy: same-origin
Referrer-Policy: strict-origin
Referrer-Policy: strict-origin-when-cross-origin
```

| Directive                         | Behaviour                                                                       | When to use                                         |
| --------------------------------- | ------------------------------------------------------------------------------- | --------------------------------------------------- |
| `no-referrer`                     | No referrer information is ever sent                                            | Maximum privacy, no external sharing needed         |
| `same-origin`                     | Referrer sent only within the same domain                                       | Internal navigation needs context, external doesn't |
| `strict-origin`                   | Only sends the domain (not the path) when protocol stays the same (HTTPS→HTTPS) | Balance between usability and privacy               |
| `strict-origin-when-cross-origin` | Full URL for same-origin requests; only domain for cross-origin                 | Good default for most modern web apps               |

---

### Quick Reference — Security Headers at a Glance

| Header                      | Protects Against                          | Risk if Missing                                  |
| --------------------------- | ----------------------------------------- | ------------------------------------------------ |
| `Content-Security-Policy`   | XSS, code injection from external sources | Browser executes scripts from any domain         |
| `Strict-Transport-Security` | SSL stripping, HTTP downgrade attacks     | Connection can be intercepted before HTTPS       |
| `X-Content-Type-Options`    | MIME sniffing, disguised file execution   | Browser may execute malicious uploaded files     |
| `Referrer-Policy`           | Information leakage via referrer headers  | Internal URLs and paths exposed to third parties |

---

### Lab / Practice Notes

> *(Update this section after completing the TryHackMe lab for this module)*

- Tool used: [https://securityheaders.io/](https://securityheaders.io/)
- Site analysed:
- Headers present:
- Headers missing:
- Key observation:


