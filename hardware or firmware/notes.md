

## What is SSL pinning ?

SSL pinning stands for Secure Socket Layer. SSL certificate creates a foundation of trust by establishing a secure connection.

This connection ensures that all data passed between the web server and browsers remain private and integral. SSL certificates have a key pair, which is a Public and Private key.

These keys work together to establish an encrypted connection. The certificate also contains “**Subject**“ which is the identity of the certificate/website owner.

==SSL Certificate Pinning==, or pinning for short, is the process of associating a host with its certificate or public key. Once you know a host’s certificate or public key, you **pin** it to that host.

Reading this I have one question in my mind, what is the certificate and what other informations this holds?

![](https://miro.medium.com/v2/resize:fit:581/1*j6-CMxHfNWqDX_YKTHPafw.png)

SSL Pinning

### **Digital Certificate**

A certificate is a file that encapsulates information about the server that owns the certificate. It’s similar to an identification card, such as a passport or a driver license. The structure of certificate uses **X.509** standards. X.509 is defined by the **International Telecommunication Unions’s** standardisation sector. A **Certificate Authority(CA)** can issue a certificate or it can be self-signed

A Digital Certificates holds many informations -

1. **Subject**: Provides the name of the entity (computer, user, network device, etc.) that the CA issued the certificate to.
2. **Serial Number**: Provides a unique identifier for each certificate that a CA issues.
3. **Issuer**: Provides a unique name for the CA that issued the certificate.
4. **Valid From**: Provides the date and time when the certificate becomes valid.
5. **Valid To**: Provides the date and time when the certificate is no longer considered valid.
6. **Public Key**: Contains the public key of the key pair that goes with the certificate.
7. **Algorithm Identifier**: Indicates the algorithm used to sign the certificate.
8. **Digital Signature:** A bit string used to verify the authenticity of the certificate.
9. **Version**: Version number of certificate
10. **TimeStamp**: This shows the time when certificate was created

There are several commonly used filename extensions for digital certificates (**X.509**) certificates-

**A. PEM(Privacy Enhanced Mail)**: A **Base-64** encoding, whose file extension is **.pem**. The certificate information is enclosed between “ — — -BEGIN CERTIFICATE — — -” and “ — — -END CERTIFICATE — — -”

**B. PKCS(Public-key cryptography standards )**: Used to exchange public and private objects in a single file. Its extensions are **.p7b**, **.p7c**, **.p12** etc.

**C. DER(Distinguished Encoding Rules)**: A binary encoding, whose file extensions are **.cer**, **.der** and **.crt**.

### **Why Do You Need SSL Certificate Pinning?**

**SSL pinning** allows the application to only trust the valid or pre-defined **certificate** or **Public Key.** The application developer uses **SSL pinning** technique as an additional security layer for application traffic. As normally, the application trusts custom **certificate** and allows the application to intercept the traffic.

Restricting the set of trusted certificates through pinning prevents attackers from analyzing the functionality of the app and the way it communicates with the server.

### **How SSL works?**

1. Client machine sends a connection request to server, server listens the request.
2. Server gives response including public key and certificate.
3. Client checks the certificate and sends a encrypted key to server.
4. Server decrypt the key and sends encrypted data back to the client machine.
5. Client receives and decrypt the encrypted data.

--------------------------------------------------------------------------

## What is CND (content network delivery) ?

HTTP CDNs (Content Delivery Networks) are ==global networks of servers that cache website content (like images, videos, scripts) closer to users==, drastically reducing latency and speeding up loading times by serving data from the nearest "edge" server instead of the main "origin" server, enhancing performance, reliability, and security for web traffic. They are crucial for modern web performance, handling most internet traffic and improving user experience globally


