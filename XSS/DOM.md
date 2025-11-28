
## DOM XSS and `document.write()`

### The Vulnerability Flow

DOM XSS occurs when a website uses client-side script (like JavaScript) to take data from a **source** (like `location.search`, `document.URL`, or `location.hash`) and passes it to a dangerous **sink** (like `innerHTML`, `document.write()`, or `eval()`) without proper sanitization.

|Component|Description|
|---|---|
|**Source**|A JavaScript property that can hold user-controllable data (e.g., `document.URL`).|
|**Sink**|A dangerous JavaScript function or property that can execute or render data (e.g., `document.write()`).|

Export to Sheets

In this scenario, if an attacker can control the data that is passed to the `document.write()` function, they can inject malicious code.

---

### The `document.write()` Sink

The `document.write()` method is a **highly dangerous sink** because it directly writes HTML markup to the document. When the browser parses this new content, it treats it as part of the page's structure, including any embedded `<script>` tags, and executes them immediately.

---

### Exploiting with the Payload

The example payload is: `document.write('... <script>alert(document.domain)</script> ...');`

1. **Attacker's Goal:** The attacker's aim is to construct an input (e.g., in the URL fragment or query string) that, when processed by the vulnerable script, causes the browser to execute the attacker-supplied JavaScript.
    
2. **The Code Injection:** Assuming a vulnerable script like this exists:
    
    JavaScript
    
    ```
    let data = new URLSearchParams(window.location.search).get('input'); // Source
    document.write('Welcome, ' + data + '!'); // Sink
    ```
    
    The attacker would craft a URL that makes the `data` variable contain something that closes the single quote and injects a script tag.
    
3. **The Execution:** When the malicious data (which includes the full string `'... <script>alert(document.domain)</script> ...'`) is passed to `document.write()`, the browser does the following:
    
    - It inserts the new string into the HTML document.
        
    - It parses the injected content: `... <script>alert(document.domain)</script> ...`
        
    - Because the string contains a properly formed **`<script>` element**, the browser executes the code inside it: `alert(document.domain)`


