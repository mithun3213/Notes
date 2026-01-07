
## What is the HTTP Host header?

The HTTP Post Header is that , the mandatory header that specify the host the user need to visit , for example when the user visit the `https://portswigger.net/web-security`, the header will be

```
GET /web-security HTTP/1.1 Host: portswigger.net
```

## What is the purpose of the HTTP Host header?

The purpose of the HTTP Host header is to help identify which back-end component the client wants to communicate with. If requests didn't contain Host headers, or if the Host header was malformed in some way, this could lead to issues when routing incoming requests to the intended application.

Historically, this ambiguity didn't exist because each IP address would only host content for a single domain. Nowadays, largely due to the ever-growing trend for cloud-based solutions and outsourcing much of the related architecture, it is common for multiple websites and applications to be accessible at the same IP address. This approach has also increased in popularity partly as a result of IPv4 address exhaustion.

When multiple applications are accessible via the same IP address, this is most commonly a result of one of the following scenarios.


### Virtual hosting

One possible scenario is when a single web server hosts multiple websites or applications. This could be multiple websites with a single owner, but it is also possible for websites with different owners to be hosted on a single, shared platform. This is less common than it used to be, but still occurs with some cloud-based SaaS solutions.

In either case, although each of these distinct websites will have a different domain name, they all share a common IP address with the server. Websites hosted in this way on a single server are known as "virtual hosts".

To a normal user accessing the website, a virtual host is often indistinguishable from a website being hosted on its own dedicated server.


### Routing traffic via an intermediary ?

When the website or the application are hosted in different server , the request are transfer via the one intermediate (Single ip address) via Content delivery network . So the intermediate server must know the which request should transfer to which server 

### How does the HTTP Host header solve this problem?

In both of these scenarios, the Host header is relied on to specify the intended recipient. A common analogy is the process of sending a letter to somebody who lives in an apartment building. The entire building has the same street address, but behind this street address there are many different apartments that each need to receive the correct mail somehow. One solution to this problem is simply to include the apartment number or the recipient's name in the address. In the case of HTTP messages, the Host header serves a similar purpose.

When a browser sends the request, the target URL will resolve to the IP address of a particular server. When this server receives the request, it refers to the Host header to determine the intended back-end and forwards the request accordingly.

-----

Some intercepting proxies derive the target IP address from the Host header directly, which makes this kind of testing all but impossible; any changes you made to the header would just cause the request to be sent to a completely different IP address.

------

## What is a Host Header Attack? (Very simple)

> A **Host Header Attack** happens when a web application **trusts the `Host` header**, and an attacker **changes it** to do something malicious.

---

## 🔹 First: What is the Host header?

Every HTTP request contains this:

`Host: example.com`

It tells the server:

> “Hey, I want the website called `example.com`”

⚠️ **Important:**  
The **client controls this value** — anyone can change it.

---

## 🔹 Why trusting Host header is dangerous

Some applications use the Host header to:

- Generate password reset links
    
- Build email links
    
- Redirect users
    
- Decide which backend to use
    
- Create absolute URLs
    

Example (bad code):

`$link = "https://" . $_SERVER['HTTP_HOST'] . "/reset";`

---

## 🔥 What the attacker does (core idea)

Instead of:

`Host: example.com`

Attacker sends:

`Host: attacker.com`

The application **believes it**.

---

## 🧪 Simple real example (step by step)

### 1️⃣ Normal user request

`GET /forgot-password HTTP/1.1 Host: example.com`

App sends email:

`https://example.com/reset?token=ABC`

✅ Safe

---

### 2️⃣ Attacker request

`GET /forgot-password HTTP/1.1 Host: attacker.com`

App sends email:

`https://attacker.com/reset?token=ABC`

❌ VERY dangerous

----------
### When you change (tamper with) the Host header, two things can happen:

1️⃣ The website **still works**  
2️⃣ The website **breaks / blocks you**

Both behaviors are normal.
## 🔹 Case 1: Website still works even with a wrong Host header

### What does this mean?

Sometimes, the server is configured like this:

> “If I don’t recognize the Host name, just send the request to a **default website**.”

This is called a **default / fallback virtual host**.

---

### 🔧 Simple server example

Imagine a server with this setup:

`IP: 203.0.113.10  example.com      → main website admin.example.com → admin panel (default)        → example.com`

Now you send:

`Host: random123.com`

The server thinks:

> “I don’t know `random123.com`, so I’ll send them to my default site.”

And that default site happens to be **example.com**.

## Case 2: Website breaks when Host header is changed

This is **very common**, especially with modern setups.

---

### What happens here?

Instead of a default site, the server says:

> “If I don’t recognize the Host, I refuse the request.”

So when you send:

`Host: evil.com`

You get:

`Invalid Host header`

or

`400 Bad Request`

---------

> Even when a website checks the Host header for security, **the checking logic can be weak**, and attackers try to **bypass it** using tricks in how the Host header is parsed.

---

## 🔹 Why you may not see “Invalid Host header”

Sometimes the site **doesn’t just reject** bad Host headers.  
Instead, it applies **security checks**.

Example:

- Checks Host header
    
- Compares it with TLS information
    
- Blocks only if they don’t match
    

This sounds strong — but **logic mistakes still happen**.

---

## 🔐 What is SNI (very simple)

When using HTTPS:

- Browser tells the server **which domain it wants**
    
- This happens during TLS handshake
    
- That value is called **SNI (Server Name Indication)**
    

Some sites check:

`SNI domain == Host header ?`

If they match → allow  
If not → block

---

## 🔹 Why this still can be bypassed

Because **how the Host header is parsed** matters.

Different systems may:

- Read it differently
    
- Ignore parts of it
    
- Only check part of the value
    

---

## 🧪 Trick 1: Port confusion

Example request:

`Host: vulnerable-website.com:bad-stuff-here`

What happens:

- Security check:
    
    - Looks only at `vulnerable-website.com`
        
    - Ignores the port part
        
- Application:
    
    - Uses the **full value**
        
    - Includes `:bad-stuff-here`
        

➡️ Validation passes  
➡️ Payload sneaks through

---

## 🧪 Trick 2: Subdomain matching logic

Some sites allow:

`*.vulnerable-website.com`

They check:

> “Does Host end with `vulnerable-website.com`?”

Then this passes:

`Host: notvulnerable-website.com`

Because it **ends with** the same text.

❌ Weak string matching  
❌ No real domain validation

---

## 🧪 Trick 3: Using a compromised subdomain

If you control:

`hacked-subdomain.vulnerable-website.com`

Then this is valid:

`Host: hacked-subdomain.vulnerable-website.com`

Security thinks:  
✔ Trusted domain

But attacker controls it → dangerous.

----------

> Different parts of a website may read the **Host header differently**, and attackers exploit this mismatch to confuse the system and sneak in malicious behavior.

---

## 🔹 Why this situation exists at all

Modern websites are **not one single program**.

They are usually made of **multiple components**, for example:

- CDN / Load balancer
    
- Reverse proxy (Nginx)
    
- Application server (PHP / Node / Python)
    
- Email service
    
- Cache server
    

👉 These components **do not share the same code**  
👉 Each one may **parse the Host header differently**

---

## 🔍 Two types of code involved

### 1️⃣ Host validation code (security side)

- Checks if Host is allowed
    
- Blocks bad domains
    
- Often runs on:
    
    - CDN
        
    - Load balancer
        
    - Reverse proxy
        

### 2️⃣ Vulnerable code (business logic side)

- Uses Host to:
    
    - Build links
        
    - Generate emails
        
    - Redirect users
        
- Runs inside:
    
    - Application code
        

⚠️ These two **often live in different places**

---

## 🧠 The key problem

> The **security check** and the **dangerous usage** of the Host header do **not see the same value**.

This creates a **discrepancy**.

---

## 🧪 What is an “ambiguous request”?

An **ambiguous request** is one where:

- One component sees:
    

- `Host: safe-domain.com`
    
- Another component sees:
    

- `Host: attacker-controlled-value`
    

Both are looking at the **same request**, but **interpreting it differently**.

---

## 🔹 How can this happen? (beginner examples)

### Example 1: Port confusion

`Host: vulnerable-site.com:evil`

- Security check:
    
    - Reads only `vulnerable-site.com`
        
    - ✅ Allowed
        
- Application code:
    
    - Uses full string
        
    - ❌ `vulnerable-site.com:evil`
        

Result → attacker injects data.

---

### Example 2: Multiple Host headers

`Host: vulnerable-site.com Host: attacker.com`

- Front-end server:
    
    - Uses first Host
        
    - ✅ Allowed
        
- App server:
    
    - Uses last Host
        
    - ❌ attacker.com
        

Boom 💥 ambiguity.

---

### Example 3: X-Forwarded-Host confusion

`Host: vulnerable-site.com X-Forwarded-Host: attacker.com`

- Proxy validates `Host`
    
- App trusts `X-Forwarded-Host`
    

Attacker wins.