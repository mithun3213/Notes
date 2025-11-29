

## What is reflected cross-site scripting?

Reflected cross-site scripting (or XSS) arises when an application receives data in an HTTP request and includes that data within the immediate response in an unsafe way.

Suppose a website has a search function which receives the user-supplied search term in a URL parameter:

`https://insecure-website.com/search?term=gift`

The application echoes the supplied search term in the response to this URL:

`<p>You searched for: gift</p>`

Assuming the application doesn't perform any other processing of the data, an attacker can construct an attack like this:

`https://insecure-website.com/search?term=<script>/*+Bad+stuff+here...+*/</script>`

This URL results in the following response:

`<p>You searched for: <script>/* Bad stuff here... */</script></p>`

If another user of the application requests the attacker's URL, then the script supplied by the attacker will execute in the victim user's browser, in the context of their session with the application.

--------------------------------------------------------------------------
## What is a `<link rel="canonical">` tag?

The **canonical tag** is an HTML element used to tell **search engines** which version of a webpage is the **original or "main" version** when there are **multiple URLs** with similar or identical content.

It looks like this:
<link rel="canonical" href="https://example.com/my-page">
it is always placed in the head tag...

if the user press any the buttons like 
ALT+SHIFT+X
CTRL+ALT+X
Alt+X

the payload ?'accesskey='x'onclick='alert(1) will be executed 


|Fragment|Type|Explanation|
|---|---|---|
|`accesskey='x'`|**HTML Attribute**|This attribute assigns the shortcut key **'x'** to the element it is attached to. When a user presses the appropriate modifier keys along with 'x' (e.g., Alt+x in some browsers), the element's default action (like clicking a button or focusing a link) is triggered.|
|`onclick='alert(1)`|**HTML Event Handler**|This is a **JavaScript event handler** that executes the script `alert(1)` when the element is **clicked**. The `alert(1)` script simply opens a pop-up box with the number '1' inside it|

it will work in the link tag ..

--------------------------------------------------------------------------


### Terminating the existing script:


`<script> ... var input = 'controllable data here'; ... </script>`

then you can use the following payload to break out of the existing JavaScript and execute your own:

`</script><img src=1 onerror=alert(document.domain)>`

## Breaking Out with `</script>`

To successfully execute your payload, you must use `</script>` to signal the browser that the JavaScript context is over.

**Payload:** `</script><img src=1 onerror=alert(document.domain)>`

When the browser parses the resulting code:

HTML

```
<script>
...
var input = '</script><img src=1 onerror=alert(document.domain)>'; // The full code
...
</script>
```

1. **Original Script (Start):** The browser sees the opening `<script>` tag.
    
2. **String Termination:** Your payload begins with a single quote, which terminates the existing `var input = '` string.
    
    - Result: `var input = ''`
        
3. **Script Termination:** Immediately following the string is the `</script>` tag. The **HTML parser** finds this tag and immediately **closes the current `<script>` block**, returning the browser to the **general HTML parsing context**.
    
4. **New HTML Injection:** The browser is now in the HTML context and encounters the injected code: `<img src=1 onerror=alert(document.domain)>`. This is interpreted as a **new, valid HTML tag**.
    
5. **Execution:** The `onerror` event triggers because the image source `src=1` is invalid, executing the JavaScript payload `alert(document.domain)`


![[Pasted image 20251014192243.png]]

--------------------------------------------------------------------------

### Breaking out of a JavaScript string

In cases where the XSS context is inside a quoted string literal, it is often possible to break out of the string and execute JavaScript directly. It is essential to repair the script following the XSS context, because any syntax errors there will prevent the whole script from executing.

Some useful ways of breaking out of a string literal are:

`'-alert(document.domain)-' ';alert(document.domain)//`

# Reflected XSS into a JavaScript string with angle brackets HTML encoded

in the above lab , the payload `'-alert(document.domain)-` , is used to execute the js script in the script , this payload is `</script><img src=1 onerror=alert(document.domain)>` is used for terminating the script and run the html ..


--------------------------------------------------------------------------

## 1. The Application's Defense (and Its Flaw)

1. **Goal of the Defense:** The application attempts to convert a potentially malicious single quote (`'`), which could terminate a JavaScript string, into a literal single quote (`\'`). The **backslash** (`\`) is a special character in JavaScript that tells the parser to interpret the _next_ character literally, not as a special control character (like a string terminator).
    
2. **Example of the Defense:** If a user inputs `';alert(document.domain)//`, the application converts the first quote to a backslash-quote sequence, resulting in `\';alert(document.domain)//`. The script is _intended_ to fail because the quote is now literal and doesn't terminate the string.
    
3. **The Flaw (The Backslash Vulnerability):** The application fails to escape the backslash character (`\`) itself.
    

---

## 2. The Attacker's Strategy (The Successful Exploit)

1. **The Alternative Payload:** The attacker uses a payload that begins with their own backslash: `\';alert(document.domain)//`.
    
2. **The Conversion:** When the application processes this input, it adds a backslash _only_ before the single quote.
    
    - Attacker Input: \′;alert(document.domain)//
        
    - Application Output: \\′;alert(document.domain)//
        
3. **The Result (Successful String Breakout):** The resulting string starts with two backslashes: `\\`.
    
    - The **first backslash** (`\`) acts as the escape character for the second backslash. It tells the parser to interpret the character immediately following it (the second backslash) literally. The two backslashes essentially cancel each other out, resulting in a single literal backslash (`\`) being placed in the code.
        
    - This leaves the single quote (`'`) _unescaped_. It is now interpreted as a **string terminator**, successfully breaking out of the application's original JavaScript string.
        
    - The remaining code, `;alert(document.domain)//`, is executed by the browser, and the attack succeeds

--------------------------------------------------------------------------

### XSS in JavaScript template literals

JavaScript template literals are string literals that allow embedded JavaScript expressions. The embedded expressions are evaluated and are normally concatenated into the surrounding text. Template literals are encapsulated in backticks instead of normal quotation marks, and embedded expressions are identified using the `${...}` syntax.

For example, the following script will print a welcome message that includes the user's display name:

``document.getElementById('message').innerText = `Welcome, ${user.displayName}.`;``

When the XSS context is into a JavaScript template literal, there is no need to terminate the literal. Instead, you simply need to use the `${...}` syntax to embed a JavaScript expression that will be executed when the literal is processed. For example, if the XSS context is as follows:

``<script> ... var input = `controllable data here`; ... </script>``

then you can use the following payload to execute JavaScript without terminating the template literal:

`${alert(document.domain)}`



