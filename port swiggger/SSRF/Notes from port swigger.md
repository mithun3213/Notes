

**topic:**
What is SSRF
Common SSRF attacks
Circumventing common SSRF defenses
What is Blind SSRF?


## What is SSRF?

Server-side request forgery is a web security vulnerability that allows an attacker to cause the server-side application to make requests to an unintended location.

In a typical SSRF attack, the attacker might cause the server to make a connection to internal-only services within the organization's infrastructure. In other cases, they may be able to force the server to connect to arbitrary external systems. This could leak sensitive data, such as authorization credentials

## Common SSRF attacks


In an SSRF attack against the server, the attacker causes the application to make an HTTP request back to the server that is hosting the application, via its loopback network interface. This typically involves supplying a URL with a hostname like `127.0.0.1` (a reserved IP address that points to the loopback adapter) or `localhost` (a commonly used name for the same adapter).

For example, imagine a shopping application that lets the user view whether an item is in stock in a particular store. To provide the stock information, the application must query various back-end REST APIs. It does this by passing the URL to the relevant back-end API endpoint via a front-end HTTP request. When a user views the stock status for an item, their browser makes the following request:

`POST /product/stock HTTP/1.0 Content-Type: application/x-www-form-urlencoded Content-Length: 118 stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1`

This causes the server to make a request to the specified URL, retrieve the stock status, and return this to the user.

In this example, an attacker can modify the request to specify a URL local to the server:

`POST /product/stock HTTP/1.0 Content-Type: application/x-www-form-urlencoded Content-Length: 118 stockApi=http://localhost/admin`

The server fetches the contents of the `/admin` URL and returns it to the user.

An attacker can visit the `/admin` URL, but the administrative functionality is normally only accessible to authenticated users. This means an attacker won't see anything of interest. However, if the request to the `/admin` URL comes from the local machine, the normal access controls are bypassed. The application grants full access to the administrative functionality, because the request appears to originate from a trusted location.


**Lab : 1**

# Lab: Basic SSRF against the local server

APPRENTICE

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.

**Solution :**

when visiting the stocks , there is the option called check stock , where I captured the requested via the burp  suite and change the url to the http://localhost/admin where i can see the two account called weiner and carlos , the work is to delete the username carlos and i found the url to delete the username carlos /admin/delete?username=carlos , the full url is http://localhost/admin/delete/username?=carlos , the lab solved


**Lab 2 :**

# Lab: Basic SSRF against another back-end system

APPRENTICE

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, use the stock check functionality to scan the internal `192.168.0.X` range for an admin interface on port `8080`, then use it to delete the user `carlos`.

**Solution :**

, by visiting the page there is a check functionality as same in previous lab . I captured the request and in this lab , the ip we don't know , 192.168.0.?:8080 , we need to find the ip by brute-forcing the ip we came to know that the ip is 192.168.0.101:8080 , so by previously the url is 
http://192.168.0.101:8080/admin/delete?username=carlos
the lab solved......


## Circumventing common SSRF defenses:


### SSRF with blacklist-based input filters

Some applications block input containing hostnames like `127.0.0.1` and `localhost`, or sensitive URLs like `/admin`. In this situation, you can often circumvent the filter using the following techniques:

- Use an alternative IP representation of `127.0.0.1`, such as `2130706433`, `017700000001`, or `127.1`.
- Register your own domain name that resolves to `127.0.0.1`. You can use `spoofed.burpcollaborator.net` for this purpose.
- Obfuscate blocked strings using URL encoding or case variation.
- Provide a URL that you control, which redirects to the target URL. Try using different redirect codes, as well as different protocols for the target URL. For example, switching from an `http:` to `https:` URL during the redirect has been shown to bypass some anti-SSRF filters.


**Lab 3:**

# Lab: SSRF with blacklist-based input filter

PRACTITIONER

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.

The developer has deployed two weak anti-SSRF defenses that you will need to bypass.

**Solution :**

in this lab we are going to done the same thing as done in previous lab , by certain character are being blocked , so we need bypass that . 

usually visiting the page and then changing the url to http://localhost/admin , but here the localhost or 127.0.0.1 and the word admin are being blocked , so instead of the word admin we are using aDmIn and for localhost -->127.1 

stockapi=http://127.1/aDmIn/delete?username=carlos


### SSRF with whitelist-based input filters

Some applications only allow inputs that match, a whitelist of permitted values. The filter may look for a match at the beginning of the input, or contained within in it. You may be able to bypass this filter by exploiting inconsistencies in URL parsing.

The URL specification contains a number of features that are likely to be overlooked when URLs implement ad-hoc parsing and validation using this method:

- You can embed credentials in a URL before the hostname, using the `@` character. For example:
    
    `https://expected-host:fakepassword@evil-host`
- You can use the `#` character to indicate a URL fragment. For example:
    
    `https://evil-host#expected-host`
- You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example:
    
    `https://expected-host.evil-host`
- You can URL-encode characters to confuse the URL-parsing code. This is particularly useful if the code that implements the filter handles URL-encoded characters differently than the code that performs the back-end HTTP request. You can also try [double-encoding](https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings#obfuscation-via-double-url-encoding) characters; some servers recursively URL-decode the input they receive, which can lead to further discrepancies.
- You can use combinations of these techniques together.

## i)Core Function: Client-Side Navigation

The most common and original use of the `#` is to allow the browser to automatically scroll to a particular element within a document _after_ the document has loaded.

###  Example

If a webpage has a long FAQ section, and one question has the HTML attribute `id="returns-policy"`, the URL might look like this:

`https://example.com/faq#returns-policy`

1. **Request:** The browser sends a request to the server only for `https://example.com/faq`.
    
2. **Server Action:** The server ignores everything after the `#` and sends the entire `/faq` page content back to the browser.
    
3. **Client Action:** Once the browser receives the page, it reads the fragment identifier (`returns-policy`) and immediately scrolls the page down to the element with that matching ID.
    

### Key Characteristics of the Fragment

- **Not Sent to Server:** The fragment portion of the URL is **not** included in the HTTP request sent to the web server. This is the main difference between the `#` and the `?` (query parameters), which _are_ sent to the server.
    
- **Client-Side Processing:** It is used only by the browser to manipulate the displayed content of the page it has already loaded.


## ii) Core Function: Embedding Credentials

The general format for a URL that includes credentials is:

$$\text{scheme}://\text{username}:\text{password}@\text{host}:\text{port}/\text{path}$$

###  Example

If a server requires a user named `reader` with the password `secret` to access a resource, the URL might be constructed as:

`ftp://**reader:secret**@ftp.example.com/file.txt`

In this structure:

1. **`reader:secret`** is the **user information** (username and password).
    
2. **`@`** acts as the delimiter, clearly separating the user information from the host.
    
3. **`ftp.example.com`** is the **host** (server) to connect to.
    

### Historical Context

This syntax was historically common for protocols like **FTP** (File Transfer Protocol) and was also supported by early web browsers for **HTTP Basic Authentication**.

**However, this practice is now strongly discouraged and rarely used in modern web browsing** for several security reasons:

- **Security Risk:** Credentials embedded in a URL are often visible in browser history, server logs, and can be exposed if the link is shared.
    
- **Browser Removal:** Modern browsers actively discourage or even block this use of the `@` symbol in the URL for security purposes. The preferred and secure method for sending credentials is via **HTTP headers** (like the `Authorization` header), not in the URL itself.
    

**Lab 4:**

# Lab: SSRF with whitelist-based input filter


This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.

The developer has deployed an anti-SSRF defense you will need to bypass.


**Solution:**

, when we visiting the page , usually we need the change the url to /admin/delete?username=carlos

but there is certain restriction and we need to bypass using the whitelisted-based input filter
, so the url is `http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos`

double-encoded of # is %2523 , what the url will do is at first via the localhost and port will send to the server and then after the browser will see the # symbol and then after the # symbol the browser will see and then do the stuff after the # symbol and it will render , we user the # as the bypass symbol 


### iii) Bypassing SSRF filters via open redirection

It is sometimes possible to bypass filter-based defenses by exploiting an open redirection vulnerability.

In the previous example, imagine the user-submitted URL is strictly validated to prevent malicious exploitation of the SSRF behavior. However, the application whose URLs are allowed contains an open redirection vulnerability. Provided the API used to make the back-end HTTP request supports redirections, you can construct a URL that satisfies the filter and results in a redirected request to the desired back-end target.

For example, the application contains an open redirection vulnerability in which the following URL:

`/product/nextProduct?currentProductId=6&path=http://evil-user.net`

returns a redirection to:

`http://evil-user.net`

You can leverage the open redirection vulnerability to bypass the URL filter, and exploit the SSRF vulnerability as follows:

`POST /product/stock HTTP/1.0 Content-Type: application/x-www-form-urlencoded Content-Length: 118 stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin`

This SSRF exploit works because the application first validates that the supplied `stockAPI` URL is on an allowed domain, which it is. The application then requests the supplied URL, which triggers the open redirection. It follows the redirection, and makes a request to the internal URL of the attacker's choosing.

**Lab 5:**

# Lab: SSRF with filter bypass via open redirection vulnerability

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://192.168.0.12:8080/admin` and delete the user `carlos`.

The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first.

**Solution :**

, in this lab we need to find the redirecting page first and then change the path to the `http://192.168.0.12:8080/admin`

, so When we lick the next product , I notice that /product/nextProduct?currentProductId=1&path=/product?productId=2 
and putting the **/product/nextProduct?&path=/product?productId=2** in the stockapi , the product page is showing so this our redirecting page and then putting the full url in the 
stockapi=**/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos**
the lab solved


## What is Blind SSRF?

**Blind SSRF (Server-Side Request Forgery)** happens when an attacker can force a server to make a request to a URL they choose, **but the attacker cannot see the response**.

So the server _does_ send the request, but the output is **not shown** to the attacker

## How do we find Blind SSRF?

Since you cannot see the server’s response, you must detect it **out-of-band**.

### The common method:

 **Use an external listener**  
(Example: Burp Collaborator, Interactsh, webhook.site)

You send a payload like:

`http://your-collaborator-id.oast.com`

If the server makes a request to this URL, the Collaborator server logs it.

**If you see a callback → the server is vulnerable to Blind SSRF.**


