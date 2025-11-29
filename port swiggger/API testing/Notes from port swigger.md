

## API recon

To start API testing, you first need to find out as much information about the API as possible, to discover its attack surface.

To begin, you should identify API endpoints. These are locations where an API receives requests about a specific resource on its server. For example, consider the following `GET` request:

`GET /api/books HTTP/1.1 Host: example.com`

The API endpoint for this request is `/api/books`. This results in an interaction with the API to retrieve a list of books from a library. Another API endpoint might be, for example, `/api/books/mystery`, which would retrieve a list of mystery books.

Once you have identified the endpoints, you need to determine how to interact with them. This enables you to construct valid HTTP requests to test the API. For example, you should find out information about the following:

- The input data the API processes, including both compulsory and optional parameters.
- The types of requests the API accepts, including supported HTTP methods and media formats.
- Rate limits and authentication mechanisms.

## API documentation

APIs are usually documented so that developers know how to use and integrate with them.

Documentation can be in both human-readable and machine-readable forms. Human-readable documentation is designed for developers to understand how to use the API. It may include detailed explanations, examples, and usage scenarios. Machine-readable documentation is designed to be processed by software for automating tasks like API integration and validation. It's written in structured formats like JSON or XML.

API documentation is often publicly available, particularly if the API is intended for use by external developers. If this is the case, always start your recon by reviewing the documentation.

### Discovering API documentation

Even if API documentation isn't openly available, you may still be able to access it by browsing applications that use the API.

To do this, you can use Burp Scanner to crawl the API. You can also browse applications manually using Burp's browser. Look for endpoints that may refer to API documentation, for example:

- `/api`
- `/swagger/index.html`
- `/openapi.json`

If you identify an endpoint for a resource, make sure to investigate the base path. For example, if you identify the resource endpoint `/api/swagger/v1/users/123`, then you should investigate the following paths:

- `/api/swagger/v1`
- `/api/swagger`
- `/api` 

## lab 1 :

in this lab , weare suppose to delete the carlos acc, for that we have to find the endpoint . For that I change the email 
![[Pasted image 20250630113527.png]]
and capture the request using the burp , and then the request is PATCH /api/user/wiener HTTP/2 and trying the /api/user/carlos no result and then /api/user no result and then api/ it shows 302 found , then I opened in the browser and then it shows the delete strings , in that type carlos..

### What is the `OPTIONS` HTTP Method?

The `OPTIONS` method is used to **ask the server what HTTP methods are supported** for a specific **resource (URL)**.

OPTIONS /api/users HTTP/1.1
Host: example.com

HTTP/1.1 204 No Content
Allow: GET, POST, OPTIONS

### Interacting with API endpoints

Once you've identified API endpoints, interact with them using Burp Repeater and Burp Intruder. This enables you to observe the API's behavior and discover additional attack surface. For example, you could investigate how the API responds to changing the HTTP method and media type.

As you interact with the API endpoints, review error messages and other responses closely. Sometimes these include information that you can use to construct a valid HTTP request.

#### Identifying supported HTTP methods

The HTTP method specifies the action to be performed on a resource. For example:

- `GET` - Retrieves data from a resource.
- `PATCH` - Applies partial changes to a resource.
- `OPTIONS` - Retrieves information on the types of request methods that can be used on a resource.

An API endpoint may support different HTTP methods. It's therefore important to test all potential methods when you're investigating API endpoints. This may enable you to identify additional endpoint functionality, opening up more attack surface.

For example, the endpoint `/api/tasks` may support the following methods:

- `GET /api/tasks` - Retrieves a list of tasks.
- `POST /api/tasks` - Creates a new task.
- `DELETE /api/tasks/1` - Deletes a task.

You can use the built-in **HTTP verbs** list in Burp Intruder to automatically cycle through a range of methods.  

#### Identifying supported content types

API endpoints often expect data in a specific format. They may therefore behave differently depending on the content type of the data provided in a request. Changing the content type may enable you to:

- Trigger errors that disclose useful information.
- Bypass flawed defenses.
- Take advantage of differences in processing logic. For example, an API may be secure when handling JSON data but susceptible to injection attacks when dealing with XML.

To change the content type, modify the `Content-Type` header, then reformat the request body accordingly. You can use the [Content type converter](https://portswigger.net/bappstore/db57ecbe2cb7446292a94aa6181c9278) BApp to automatically convert data submitted within requests between XML and JSON.    

## lab 2 :

in this lab we are suppose to buy the jacket , which is the somthing around $2k , we have only $0 . we have to capture the request of the -->GET /api/products/1/price HTTP/2 and then change the method to OPTIONS it wil show the PATCH method allowed , and then it ask the content-type: application/json and then give the dummy json 
{
   "price":0
}
and send the request , and then show response in browser , the price of the jacket is $0 and then buy the jacket , the lab solved......


### **Concept Recap: Mass Assignment**

**Mass assignment** is when an application automatically maps input fields (like in a JSON body) to internal object properties **without filtering** or **validating** them.

If internal object fields (like `isAdmin`, `id`, etc.) are not meant to be user-editable but **are still bound**, a malicious user might change them by simply including them in their request.

### Application Behavior:

- The app allows users to update their **profile** via:
-PATCH /api/users/123
The normal user (let's say `wiener`) sees this form:
{
  "username": "wiener",
  "email": "wiener@example.com"
}

When the frontend sends a PATCH request, it only includes:
{
  "username": "wiener",
  "email": "wiener@example.com"
}
### Recon Phase: Hidden Fields Found

You try to fetch your own user data:

GET /api/users/123
it returns :

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "isAdmin": false
}
### Step-by-Step Exploit

#### ✅ Step 1: Try to Set `isAdmin`

PATCH /api/users/123
Content-Type: application/json

{
  "username": "wiener",
  "email": "wiener@example.com",
  "isAdmin": true
}

If the backend **blindly maps** this JSON to the user object, now `wiener` might be admin!

## lab 3 :

in the lab , we need to buy the jacket as same as the lab 2 , but  in this lab , we need to apply the discount coupen , when we open the jacket , and add in the cart and then go the the cart and capture the request in the burp . After that we may see the GET /api/checkout HTTP/2 request in that there may the JSON code for the discount . we need to use this and send via POST request 
{"chosen_discount":{"percentage":0},"chosen_products":[{"product_id":"1","name":"Lightweight \"l33t\" Leather Jacket","quantity":1,"item_price":133700}]} and change the discount to 100%.........




## Preventing vulnerabilities in APIs

When designing APIs, make sure that security is a consideration from the beginning. In particular, make sure that you:

- Secure your documentation if you don't intend your API to be publicly accessible.
- Ensure your documentation is kept up to date so that legitimate testers have full visibility of the API's attack surface.
- Apply an allowlist of permitted HTTP methods.
- Validate that the content type is expected for each request or response.
- Use generic error messages to avoid giving away information that may be useful for an attacker.
- Use protective measures on all versions of your API, not just the current production version.

To prevent mass assignment vulnerabilities, allowlist the properties that can be updated by the user, and blocklist sensitive properties that shouldn't be updated by the user.


 ## What is Server-Side Parameter Pollution (SSPP)?

Server-Side Parameter Pollution occurs when:

- A **user-controllable input** (like a URL parameter or header) is **included in a server-side request** (typically to an internal API).

- The application **does not sanitize or validate** that input properly.

- The input **pollutes or overrides** the original parameters in the backend request.
 

This is different from **Client-Side Parameter Pollution (CSPP)**, where parameters are manipulated on the frontend.


## How Does SSPP Work?

Imagine this:

1. You send a request to a **frontend web application**.

2. That app **forwards your input** to a **backend API** (often an internal/private API that’s not publicly accessible).

3. Your input gets **embedded** in the backend API request — maybe as a query parameter, header, or JSON body.

4. If you can inject additional parameters **that override or add new ones**, you can:

 - Change the backend logic.

 - Retrieve sensitive data.

 - Perform actions as another user.


## Example Scenario

Let’s say a web app sends a request like this to its internal API:

GET /profile?user_id=123

Internally, the frontend sends this to the backend API:
GET http://internal-api.local/get_user?user_id=123

Suppose the app builds this internal request like:
internal_url = "http://internal-api.local/get_user?" + request.query_string

If you craft your request like this:
GET /profile?user_id=123&user_id=999

The internal request becomes:
GET http://internal-api.local/get_user?user_id=123&user_id=999

Depending on how the backend processes duplicate parameters, it may:

- Use the **last** `user_id=999` → attacker sees another user's profile.
    
- Merge or override internal logic → access unauthorized info.

### Overriding existing parameters

To confirm whether the application is vulnerable to server-side parameter pollution, you could try to override the original parameter. Do this by injecting a second parameter with the same name.

For example, you could modify the query string to the following:

`GET /userSearch?name=peter%26name=carlos&back=/home`

This results in the following server-side request to the internal API:

`GET /users/search?name=peter&name=carlos&publicProfile=true`

The internal API interprets two `name` parameters. The impact of this depends on how the application processes the second parameter. This varies across different web technologies. For example:

- PHP parses the last parameter only. This would result in a user search for `carlos`.
- ASP.NET combines both parameters. This would result in a user search for `peter,carlos`, which might result in an `Invalid username` error message.
- Node.js / express parses the first parameter only. This would result in a user search for `peter`, giving an unchanged result.

If you're able to override the original parameter, you may be able to conduct an exploit. For example, you could add `name=administrator` to the request. This may enable you to log in as the administrator user. 


## lab 4 :

in this lab , we need to log in as administrator ,but there is no password to log in as administrator , for  that first we need to capture the request of the forgot password , and the change the csrf=6NpxWzNG2556Axsb9L317cdDO5P1Ddjn&username=administrator%26field=reset_token it will give the reset_token , and then change the url  to https://0ac9000f0398420980673acf0034007a.web-security-academy.net/forgot-password/reset_token=xyz
it the solve is solved...


## Testing for server-side parameter pollution in REST paths

A RESTful API may place parameter names and values in the URL path, rather than the query string. For example, consider the following path:

`/api/users/123`

The URL path might be broken down as follows:

- `/api` is the root API endpoint.
- `/users` represents a resource, in this case `users`.
- `/123`represents a parameter, here an identifier for the specific user.

Consider an application that enables you to edit user profiles based on their username. Requests are sent to the following endpoint:

`GET /edit_profile.php?name=peter`

This results in the following server-side request:

`GET /api/private/users/peter`

An attacker may be able to manipulate server-side URL path parameters to exploit the API. To test for this vulnerability, add path traversal sequences to modify parameters and observe how the application responds.

You could submit URL-encoded `peter/../admin` as the value of the `name` parameter:

`GET /edit_profile.php?name=peter%2f..%2fadmin`

This may result in the following server-side request:

`GET /api/private/users/peter/../admin`

If the server-side client or back-end API normalize this path, it may be resolved to `/api/private/users/admin`.
## lab  5:

in this lab , the stuff is same as the lab 4 , but there is a small different in the reset_token process, we we need to capture the forgot/password request , and then username=`../../../../openapi.json%23`
, after that it returns the response like this `/api/internal/v1/users/{username}/field/{field}`
and then change the username=administrator/field/foo%23 , it says  that the field part is email. and then changing the username=administrator/field/email and then changing that administrator/field/passwordreset and then it will  throw  an error . after changing the username =../../v1/users/administrator/field/passwordResetToken%23 and then it will give the reset_token using that we can change the password and delete the carlos ..............


## Testing for server-side parameter pollution in structured data formats

An attacker may be able to manipulate parameters to exploit vulnerabilities in the server's processing of other structured data formats, such as a JSON or XML. To test for this, inject unexpected structured data into user inputs and see how the server responds.

Consider an application that enables users to edit their profile, then applies their changes with a request to a server-side API. When you edit your name, your browser makes the following request:

`POST /myaccount name=peter`

This results in the following server-side request:

`PATCH /users/7312/update {"name":"peter"}`

You can attempt to add the `access_level` parameter to the request as follows:

`POST /myaccount name=peter","access_level":"administrator`

If the user input is added to the server-side JSON data without adequate validation or sanitization, this results in the following server-side request:

`PATCH /users/7312/update {name="peter","access_level":"administrator"}`

This may result in the user `peter` being given administrator access.

#### Related pages

For information on how to identify parameters that you can inject into the query string, see the [Finding hidden parameters](https://portswigger.net/web-security/api-testing#finding-hidden-parameters) section.

Consider a similar example, but where the client-side user input is in JSON data. When you edit your name, your browser makes the following request:

`POST /myaccount {"name": "peter"}`

This results in the following server-side request:

`PATCH /users/7312/update {"name":"peter"}`

You can attempt to add the `access_level` parameter to the request as follows:

`POST /myaccount {"name": "peter\",\"access_level\":\"administrator"}`

If the user input is decoded, then added to the server-side JSON data without adequate encoding, this results in the following server-side request:

`PATCH /users/7312/update {"name":"peter","access_level":"administrator"}`

Again, this may result in the user `peter` being given administrator access.

Structured format injection can also occur in responses. For example, this can occur if user input is stored securely in a database, then embedded into a JSON response from a back-end API without adequate encoding. You can usually detect and exploit structured format injection in responses in the same way you can in requests. 


