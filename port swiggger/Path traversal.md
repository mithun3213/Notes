## What is path traversal?

Path traversal is also known as directory traversal. These vulnerabilities enable an attacker to read arbitrary files on the server that is running an application. This might include:

- Application code and data.
- Credentials for back-end systems.
- Sensitive operating system files.

In some cases, an attacker might be able to write to arbitrary files on the server, allowing them to modify application data or behavior, and ultimately take full control of the server.

## Reading arbitrary files via path traversal

Imagine a shopping application that displays images of items for sale. This might load an image using the following HTML:

`<img src="/loadImage?filename=218.png">`

The `loadImage` URL takes a `filename` parameter and returns the contents of the specified file. The image files are stored on disk in the location `/var/www/images/`. To return an image, the application appends the requested filename to this base directory and uses a filesystem API to read the contents of the file. In other words, the application reads from the following file path:

`/var/www/images/218.png`

This application implements no defenses against path traversal attacks. As a result, an attacker can request the following URL to retrieve the `/etc/passwd` file from the server's filesystem:

`https://insecure-website.com/loadImage?filename=../../../etc/passwd`

This causes the application to read from the following file path:

`/var/www/images/../../../etc/passwd`


## What is a Path Traversal Attack?

A **path traversal attack** (a.k.a. directory traversal) lets an attacker read **arbitrary files** on a server by manipulating the **file path** input, such as using `../` to move up the directory tree.

app.get('/image', (req, res) => {
    const file = req.query.filename;
    res.sendFile('/var/www/images/' + file);
});
### 🧪 Example of Vulnerable Code (in Express.js)

If a user visits:

`https://example.com/image?filename=cat.png`

`/var/www/images/cat.png`---

## 🚨 Basic Path Traversal Attack

If there's **no input validation**, an attacker can supply:


`filename=../../../../etc/passwd`

Then the server will try to read:

`/var/www/images/../../../../etc/passwd`

After resolving `../`, it become

`/etc/passwd`

That file will be read and returned if accessible.
## 🛡️ Common Defense — Strip `../`

Some apps try to prevent traversal by removing the `../` from the input.


`const file = req.query.filename.replace(/\.\.\//g, ''); res.sendFile('/var/www/images/' + file);`

So if an attacker tries:

`filename=../../../../etc/passwd`

It becomes:

`etc/passwd`

Then:

CopyEdit

`/var/www/images/etc/passwd  ❌ (likely a 404)`
## 🚨 Bypass Using Absolute Path

But — if the server allows **absolute paths**, then even with `../` stripped, an attacker can supply:

`filename=/etc/passwd`

Which directly maps to:

`/etc/passwd ✅`

No `../` is needed — this **bypasses** the defense completely.
### ❗ Example Attack URL:

`https://example.com/image?filename=/etc/passwd`


You might be able to use nested traversal sequences, such as `....//` or `....\/`. These revert to simple traversal sequences when the inner sequence is stripped.


**Attackers can sneak in traversal patterns in a disguised form**, so that when the application tries to "sanitize" them by removing parts like `../`, the result **still behaves like `../`** and leads to a path traversal.


This only removes **exactly** `../`.
....//secret.txt

### What Happens?

- `....//` looks like: `..` + `../`
- If `../` is stripped, you're left with:
../secret.txt
Which is **still a valid path traversal**!

but instead of using this ../ we can use the ....// -> for one .../


In some contexts, such as in a URL path or the `filename` parameter of a `multipart/form-data` request, web servers may strip any directory traversal sequences before passing your input to the application. You can sometimes bypass this kind of sanitization by URL encoding, or even double URL encoding, the `../` characters. This results in `%2e%2e%2f` and `%252e%252e%252f` respectively. Various non-standard encodings, such as `..%c0%af` or `..%ef%bc%8f`, may also work.

For Burp Suite Professional users, Burp Intruder provides the predefined payload list **Fuzzing - path traversal**. This contains some encoded path traversal sequences that you can try.  

../ -> %2e%2e%2f ( single url encode)
%2e%2e%2f -> `%252e%252e%252f` ( double url encoding)

final is %252e%252e%252f%252e%252e%252f%252e%252e%252fetc%2fpasswd

An application may require the user-supplied filename to start with the expected base folder, such as `/var/www/images`. In this case, it might be possible to include the required base folder followed by suitable traversal sequences. For example: `filename=/var/www/images/../../../etc/passwd`.

An application may require the user-supplied filename to end with an expected file extension, such as `.png`. In this case, it might be possible to use a null byte to effectively terminate the file path before the required extension. For example: `filename=../../../etc/passwd%00.png`.

## How to prevent a path traversal attack

The most effective way to prevent path traversal vulnerabilities is to avoid passing user-supplied input to filesystem APIs altogether. Many application functions that do this can be rewritten to deliver the same behavior in a safer way.

If you can't avoid passing user-supplied input to filesystem APIs, we recommend using two layers of defense to prevent attacks:

- Validate the user input before processing it. Ideally, compare the user input with a whitelist of permitted values. If that isn't possible, verify that the input contains only permitted content, such as alphanumeric characters only.
- After validating the supplied input, append the input to the base directory and use a platform filesystem API to canonicalize the path. Verify that the canonicalized path starts with the expected base directory.

Below is an example of some simple Java code to validate the canonical path of a file based on user input:

`File file = new File(BASE_DIRECTORY, userInput); if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) { // process file }`   



this to be remember 

1) the path direction ../../../etc/passwd ------------ the ../../../ stands for the /var/www/images
2) sometimes the path ../../../ can be blocked at that time /etc/passwd can be enough 
3) we can use the recursive path traversal ....// for one ../
4) the path traversal can single time url encoded or double url encoded 
5) sometimes the application may require the full path traversal like `filename=/var/www/images/../../../etc/passwd`.
6) sometimes the application may require the extension like ../../../etc/passwd%00.pnng


/view_bill?filename=../../../../proc/self/cwd
most important payload