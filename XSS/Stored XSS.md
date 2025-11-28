

Sometimes the XSS context is into a type of HTML tag attribute that itself can create a scriptable context. Here, you can execute JavaScript without needing to terminate the attribute value. For example, if the XSS context is into the `href` attribute of an anchor tag, you can use the `javascript` pseudo-protocol to execute script. For example:

`<a href="javascript:alert(document.domain)">`

# LAB :Stored XSS into anchor `href` attribute with double quotes HTML-encoded


In this lab , there is vulnerability where the web page will ask the name, website , e-mail . In this page, they say that # Stored XSS into anchor `href` attribute with double quotes HTML-encoded
for this payload we can enter the payload in the email space -> javascript:alert(1) , the lab get solves


--------------------------------------------------------------------------

### Making use of HTML-encoding

When the XSS context is some existing JavaScript within a quoted tag attribute, such as an event handler, it is possible to make use of HTML-encoding to work around some input filters.

When the browser has parsed out the HTML tags and attributes within a response, it will perform HTML-decoding of tag attribute values before they are processed any further. If the server-side application blocks or sanitizes certain characters that are needed for a successful XSS exploit, you can often bypass the input validation by HTML-encoding those characters.

For example, if the XSS context is as follows:

`<a href="#" onclick="... var input='controllable data here'; ...">`

and the application blocks or escapes single quote characters, you can use the following payload to break out of the JavaScript string and execute your own script:

`&apos;-alert(document.domain)-&apos;`

The `&apos;` sequence is an HTML entity representing an apostrophe or single quote. Because the browser HTML-decodes the value of the `onclick` attribute before the JavaScript is interpreted, the entities are decoded as quotes, which become string delimiters, and so the attack succeeds.


--------------------------------------------------------------------------


