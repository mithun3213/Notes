
 
## What are JWTs?

JSON web tokens (JWTs) are a standardized format for sending cryptographically signed JSON data between systems. They can theoretically contain any kind of data, but are most commonly used to send information ("claims") about users as part of authentication, session handling, and access control mechanisms.

Unlike with classic session tokens, all of the data that a server needs is stored client-side within the JWT itself. This makes JWTs a popular choice for highly distributed websites where users need to interact seamlessly with multiple back-end servers.

### JWT format

A JWT consists of 3 parts: a header, a payload, and a signature. These are each separated by a dot, as shown in the following example:

`eyJraWQiOiI5MTM2ZGRiMy1jYjBhLTRhMTktYTA3ZS1lYWRmNWE0NGM4YjUiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTY0ODAzNzE2NCwibmFtZSI6IkNhcmxvcyBNb250b3lhIiwic3ViIjoiY2FybG9zIiwicm9sZSI6ImJsb2dfYXV0aG9yIiwiZW1haWwiOiJjYXJsb3NAY2FybG9zLW1vbnRveWEubmV0IiwiaWF0IjoxNTE2MjM5MDIyfQ.SYZBPIBg2CRjXAJ8vCER0LA_ENjII1JakvNQoP-Hw6GG1zfl4JyngsZReIfqRvIAEi5L4HV0q7_9qGhQZvy9ZdxEJbwTxRs_6Lb-fZTDpW6lKYNdMyjw45_alSCZ1fypsMWz_2mTpQzil0lOtps5Ei_z7mM7M8gCwe_AGpI53JxduQOaB5HkT5gVrv9cKu9CsW5MS6ZbqYXpGyOG5ehoxqm8DL5tFYaW3lB50ELxi0KsuTKEbD0t5BCl0aCR2MBJWAbN-xeLwEenaqBiwPVvKixYleeDQiBEIylFdNNIMviKRgXiYuAvMziVPbwSgkZVHeEdF5MQP1Oe2Spac-6IfA`

The header and payload parts of a JWT are just base64url-encoded JSON objects. The header contains metadata about the token itself, while the payload contains the actual "claims" about the user. For example, you can decode the payload from the token above to reveal the following claims:

`{ "iss": "portswigger", "exp": 1648037164, "name": "Carlos Montoya", "sub": "carlos", "role": "blog_author", "email": "carlos@carlos-montoya.net", "iat": 1516239022 }`

In most cases, this data can be easily read or modified by anyone with access to the token. Therefore, the security of any JWT-based mechanism is heavily reliant on the cryptographic signature.


### JWT signature

The server that issues the token typically generates the signature by hashing the header and payload. In some cases, they also encrypt the resulting hash. Either way, this process involves a secret signing key. This mechanism provides a way for servers to verify that none of the data within the token has been tampered with since it was issued:

- As the signature is directly derived from the rest of the token, changing a single byte of the header or payload results in a mismatched signature.
    
- Without knowing the server's secret signing key, it shouldn't be possible to generate the correct signature for a given header or payload.

### Accepting tokens with no signature

Among other things, the JWT header contains an `alg` parameter. This tells the server which algorithm was used to sign the token and, therefore, which algorithm it needs to use when verifying the signature.

`{ "alg": "HS256", "typ": "JWT" }`

This is inherently flawed because the server has no option but to implicitly trust user-controllable input from the token which, at this point, hasn't been verified at all. In other words, an attacker can directly influence how the server checks whether the token is trustworthy.

JWTs can be signed using a range of different algorithms, but can also be left unsigned. In this case, the `alg` parameter is set to `none`, which indicates a so-called "unsecured JWT". Due to the obvious dangers of this, servers usually reject tokens with no signature. However, as this kind of filtering relies on string parsing, you can sometimes bypass these filters using classic obfuscation techniques, such as mixed capitalization and unexpected encodings.

## Brute-forcing secret keys

Some signing algorithms, such as HS256 (HMAC + SHA-256), use an arbitrary, standalone string as the secret key. Just like a password, it's crucial that this secret can't be easily guessed or brute-forced by an attacker. Otherwise, they may be able to create JWTs with any header and payload values they like, then use the key to re-sign the token with a valid signature.

When implementing JWT applications, developers sometimes make mistakes like forgetting to change default or placeholder secrets. They may even copy and paste code snippets they find online, then forget to change a hardcoded secret that's provided as an example. In this case, it can be trivial for an attacker to brute-force a server's secret using a [wordlist of well-known secrets](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list).

### Brute-forcing secret keys using hashcat

We recommend using hashcat to brute-force secret keys. You can [install hashcat manually](https://hashcat.net/wiki/doku.php?id=frequently_asked_questions#how_do_i_install_hashcat), but it also comes pre-installed and ready to use on Kali Linux.

#### Note

If you're using the pre-built VirtualBox image for Kali rather than the bare metal installer version, this may not have enough memory allocated to run hashcat.

You just need a valid, signed JWT from the target server and a [wordlist of well-known secrets](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list). You can then run the following command, passing in the JWT and wordlist as arguments:

`hashcat -a 0 -m 16500 <jwt> <wordlist>`

Hashcat signs the header and payload from the JWT using each secret in the wordlist, then compares the resulting signature with the original one from the server. If any of the signatures match, hashcat outputs the identified secret in the following format, along with various other details:

`<jwt>:<identified-secret>`

#### Note

If you run the command more than once, you need to include the `--show` flag to output the results.

Normally, **JWTs (JSON Web Tokens)** are signed by the **server** using its **private key**, and the server verifies them using the **public key** (for RS256).

However, some **misconfigured servers** allow attackers to **inject a key into the JWT header** using the `jwk` parameter.  
If the server **uses this injected key** to verify the JWT **instead of its own key**, then you can:

> ➤ **Sign your own JWT with your private key**,  
> ➤ **Embed the matching public key** in the JWT’s `jwk` header,  
> ➤ **Send it to the server**,  
> ➤ And the server will believe it’s a valid token.

## 🧠 Key Terms:

|Header Param|Purpose|
|---|---|
|`alg`|Algorithm used (e.g., RS256)|
|`kid`|Key ID to help the server identify which key to use|
|`jwk`|JSON Web Key (public key) embedded directly in the header|
|`jku`|URL from which server can fetch a JSON Web Key Set (key set)|
