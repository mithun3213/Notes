
## What is computer network?

A Computer Network is a system where two or more devices are linked together to share data, resources and information.These networks can range from simple setups, like connecting two devices in your home, to massive global systems, like the Internet. Below are some uses of computer networks

## Difference between the HTTP and HTTPS protocal:

when we are the the browser , we see the url that either starts with the http:// or https:// , which is nothing the HYPER TEXT TRASFER PROTOCAL AND S for the SECURE Verison that's it..
 HTTP and HTTPS are both protocols for communication but they are different in several ways and the main difference is security


## What is HTTP?

HTTP stands for HyperText Transfer Protocol. It is invented by Tim Berner. HyperText is the type of text that is specially coded with the help of some standard coding language called [HyperText Markup Language (HTML)](https://www.geeksforgeeks.org/html/html-introduction/). HTTP provides a standard between a web browser and a web server to establish communication. It is a set of rules for transferring data from one computer to another. Data such as text, images, and other multimedia files are shared on the World Wide Web.

### Characteristics of HTTP

- HTTP is IP based communication protocol that is used to deliver data from server to client or vice-versa.
- Any type of content can be exchanged as long as the server and client are compatible with it. 
- It is a request and response protocol based on client and server requirements. 

### HTTP Request

HTTP request is a kind of message a client (in most cases, a web browser) sends to the server demanding some specific resources. It comprises of several elements like the request method (GET, POST and etc. ), the headers and occasionally the body carrying the data. This is the request part where the client outlines a request to the server and what they want.

### HTTP Response

Http response is a message sent by the server to the client in response to an Http request. In simple terms, it has status code that describes the result of a request; header, which is information about the response; and the body, which is the actual response or an error message.

## What is HTTPS protocal ?

https is the hyper-text-transfer-protocal in the secure version . https is the combination of the http and then SSL/TCP version . Why we treat the https is a secure version is because , it is certified by the SSL(Secure-socket-layer).

### How Does the HTTPS Protocol Work?

HTTPS can be said to be similar to the HTTP only that it also provides a level of security. It first creates a connection between the client and server over SSL/TLS, which enhances security by encrypting the Client and server communication. When a client makes a request for a resource using the https then the server and the client agree on the encryption keys that will be used in encrypting the data that will be transmitted in that particular session.


## what is SSL ?

the term SSL is nothing but the Secure-Socket layer which is used in the internet security to keep the user information safe . It will encrypt the information which provides data -integrity, encryption and the authentication. mostly the SSL is used in the HTTPs protocal .

## Working of SSL

- ****Encryption****: SSL encrypts data transmitted over the web, ensuring privacy. If someone intercepts the data, they will see only a jumble of characters that is nearly impossible to decode.
- ****Authentication****: SSL starts an authentication process called a handshake between two devices to confirm their identities, making sure both parties are who they claim to be.
- ****Data Integrity****: SSL [digitally signs](https://www.geeksforgeeks.org/computer-networks/difference-between-electronic-signature-and-digital-signature/) data to ensure it hasn't been tampered with, verifying that the data received is exactly what was sent by the sender.

## why the SSL comes ?

before the HTTPs protocal , the http protocal were used , so the data between the user and server can be viewed by anyone , because theu were no encryption at that time , So the SSL were introduced . it encrypt the message , so no one can see the message , if they intercep the message  , they can only see the jumble of characters 

## if we see the what are the protocols of the SSL ??

-->SSL Record Protocol
-->handshake Protocol
-->change-cipher Spec protocol
-->Alert protocol

**SSL Record Protocol :**

in the SSL record protocols the data is divided into segments , first the fragment is spilt into two types , at first it is compressed and then it is encrypted by MAC(Message Authentication Code) and algorithm using the MD5 and SHA 

**Hand Shake protocol :**

This protocol is used for the communication between the server and the client . It involves four cycles,
--> At first , both the client and the server sends hello-packets and the their protocols version will be shared
--> and then server sends the server certificate and the server-exchange-key and the phase 2 will end by server sending the server-hello-end-packets
--> and then the client send the certificate and the client-exchange-key 
--> In Phase-4 Change Cipher Spec occurs and after this the Handshake Protocol ends.

### Change-Cipher Protocol

This protocol uses the SSL record protocol. Unless [Handshake](https://www.geeksforgeeks.org/computer-networks/tcp-3-way-handshake-process/) Protocol is completed, the SSL record Output will be in a pending state. After the handshake protocol, the Pending state is converted into the current state.   
Change-cipher protocol consists of a single message which is 1 byte in length and can have only one value. This protocol's purpose is to cause the pending state to be copied into the current state.

### Alert Protocol

This protocol is used to convey SSL-related alerts to the peer entity. Each message in this protocol contains 2 bytes.

****Warning (level = 1)****  
This Alert has no impact on the connection between sender and receiver. Some of them are:

- ****Bad Certificate:**** When the received certificate is corrupt.
- ****No Certificate:**** When an appropriate certificate is not available.
- ****Certificate Expired:**** When a certificate has expired.
- 
****Fatal Error (level = 2):**** 

This Alert breaks the connection between sender and receiver. The connection will be stopped, cannot be resumed but can be restarted. Some of them are :

- ****Handshake Failure:**** When the sender is unable to negotiate an acceptable set of security parameters given the options available.
- ****Decompression Failure****: When the decompression function receives improper input.
- ****Illegal Parameters:**** When a field is out of range or inconsistent with other fields.
- ****Bad Record MAC:**** When an incorrect MAC was received.


## What is Transport Layer Security (TLS)?

Transport Layer Security, or TLS, is a widely adopted security [protocol](https://www.cloudflare.com/learning/network-layer/what-is-a-protocol/) designed to facilitate privacy and data security for communications over the Internet. A primary use case of TLS is encrypting the communication between web applications and servers, such as web browsers loading a website. TLS can also be used to encrypt other communications such as email, messaging, and [voice over IP (VoIP)](https://www.cloudflare.com/learning/video/what-is-voip/). In this article we will focus on the role of TLS in [web application security](https://www.cloudflare.com/learning/security/what-is-web-application-security/).


**Because of the POODLE with the SSL 3.0 , the more TSL comes to use**

## What Can an Attacker Do with POODLE?

The POODLE vulnerability lets the attacker eavesdrop on encrypted communication. This means that the attacker can steal confidential data that is transmitted, for example, passwords or session cookies, and then impersonate the user. This can have very serious consequences, including losing control over the web application (for example, if the attacker impersonates an admin).

The attack is not very easy because it needs to be successful in three stages:

1. In the first stage, the attacker must perform a successful [man-in-the-middle attack (MITM)](https://www.acunetix.com/blog/articles/man-in-the-middle-attacks/). The attacker can now listen to all communication between the client and the server as well as add to this communication (impersonate the client or the server). However, if this is a secure connection, communication is encrypted using SSL/TLS, so the attacker cannot understand what is being sent.
2. In the second stage, the attacker must convince the server to use the old SSL 3.0 protocol. The attacker can do this by dropping connections – after a number of such drop-outs, the server will try an older protocol, thinking that the client cannot use a newer protocol such as TLS 1.2. This is called a protocol downgrade attack or downgrade dance.
3. In the third stage, when the client and the server are communicating using SSL 3.0, the attacker can use the POODLE attack to decrypt selected parts of the communication and steal confidential information.
4. To make sure that the POODLE attack succeeds, the attacker should also be able to trick the user browser into running JavaScript, for example, using social engineering.


### What Is MAC-Then-Encrypt?

To understand POODLE, you must first understand MAC and MAC-then-Encrypt.

The SSL/TLS protocol not only guarantees that the data is confidential. It also guarantees that the data has not been tampered with. For example, you do not want someone to be able to inject their own account number when you try to transfer money between accounts in an online bank.

To guarantee that the data is not corrupt, every encrypted fragment of data has a checksum – a MAC (message authentication code). The MAC can only be calculated if you have the encryption key. If the MAC is wrong, it means that someone has tampered with the message.

The SSL 3.0 protocol uses the MAC-then-Encrypt approach. This means that first, the algorithm calculates the MAC value, then it adds that MAC value at the end of the data, and then it encrypts the whole thing, including padding.


# What is CBC (Cyber block chaining) ?

CBC = **Cipher Block Chaining** → a common mode of operation for block ciphers (like AES, DES, 3DES).

Instead of encrypting each block **independently**, CBC **chains** them together.

### How it works:

- Each plaintext block is **XORed** with the **previous ciphertext block** before encryption.
    
- This way, each block’s encryption **depends on all previous blocks**.
    
- For the **first block**, since there’s no “previous ciphertext”, an **IV (Initialization Vector)** is used.


### CBC Encryption Formula

'C[i] = Encrypt( P[i] XOR C[i-1] )'

Where:

- `P[i]` = plaintext block i
    
- `C[i]` = ciphertext block i
    
- `C[i-1]` = previous ciphertext block
    
- `IV` = used for the very first block (instead of `C[-1]`)


## Visual Example

Block size = 8 bytes  
Message = `HELLOWORLD!` (11 bytes → needs 16 with padding)

`Plaintext blocks: P1 = "HELLOWOR" P2 = "LD!....."   (padded with 5 bytes → 05 05 05 05 05)`

Encryption:

`C1 = Encrypt( P1 XOR IV ) C2 = Encrypt( P2 XOR C1 )`

Decryption:

`P1 = Decrypt(C1) XOR IV P2 = Decrypt(C2) XOR C1`

## what is cipher suites ??

SSL/TLS lets the server and the browser use sets of different algorithms to encrypt communication 
– these are called _cipher suites_.
## What is Cipher?

A cipher is a technique that is used in transforming the readable data (plaintext) into coded data (ciphertext) and the other way round. The first step in converting regular text into an unrecognizable form is encryption and the process of converting the encoded text back into regular text is decryption. Ciphers are able to perform these transformation using keys; specific pieces of information. It guarantees that only the right person can get to the primary data.

**---->For the encryption of the message in AES the length should be 16 bytes and for the DES the length should be 8 bytes**

**---->The method used by the SSL to encrypt the message is cipher block chain mode or simply CBC**

## How does the POODLE attack works :


First we need to know how the SSL encryption works , so if we are sending the data to server , first the  data is converted into  8 or 16 bytes depending upon the AES or DES algorithm . And the last bytes contain the padding length , so the padding length is that if the any one of the block is missed by the length eg 8 or 16 , at the last the padding added at the last padding length is mentioned at the last , if the attacker need to see what the information is b/w the server and the client , the attacker couldn't see nothing because all the character is encrypted . So the attacker will copy the block and paste to the last byte and send the server , and the MAC is valid and the attacker able to read the 1 byte of the information . By using the same method the attacker could able to read all the information b/w the server and client..


## 1. Messages are structured

When your browser sends a request with a cookie, the encrypted message looks like this (in blocks of 8 or 16 bytes):

`Block 1:  GET /inde  Block 2:  x.html HT  Block 3:  TP/1.1\r\n  Block  4:  Cookie: s  Block 5:  essionid=  Block 6:  SECRETVA  Block 7:  LUE12345\r  Block 8:  \nPadding`

Here:

- The cookie is `"sessionid=SECRETVALUE12345"`.
    
- It starts in Block 5, continues in Block 6 and 7.
    

So **the attacker doesn’t guess blindly**. They already know:

- The fixed HTTP parts (`GET ...`, `Cookie:` ) are predictable.
    
- So the **only unknown part is the cookie value**.

## 1. Think of blocks like boxes

Imagine each block = **a box that can hold exactly 8 letters** (for simplicity, block size = 8).  
If you have a message longer than 8 letters, it spills into the next box.

---

## 2. Structure of a request

When your browser sends a cookie, the request might look like this:

`Cookie: SECRET`

Now let’s put it into 8-letter blocks:

`Block 1: Cookie:  Block 2: SECRET..`

(`..` are padding so that the block fills to 8)

So `"SECRET"` is in Block 2.

---

## 3. Attacker’s trick: shifting

The attacker can add **extra characters** to the request before the cookie, for example:

`GET /?A  Cookie: SECRET`

Now let’s put this into 8-letter blocks:

`Block 1: GET /?A   Block 2: Cookie:  Block 3: SECRET..`

See what happened? By adding `"?A"` (2 characters), the cookie `"SECRET"` moved down into **Block 3** instead of Block 2.


why the attacker add the padding , because the server only valid the last bytes whether it is valid or invalid , so the attacker add one-byte and then move the information to the last and read the letter by letter...

--------------------------------------------------------------------------

  
****Port:**** A port can be referred to as a logical channel through which data can be sent/received to an application. Any host may have multiple applications running, and each of these applications is identified using the port number on which they are running. 

A port number is a 16-bit integer, hence, we have 216 ports available which are categorized as shown below: 

|****Port Types****|****Range****|
|---|---|
|Well known Ports|0 – 1023|
|Registered Ports|1024 – 49151|
|Ephemeral Ports|49152 - 65535|

Number of ports: 65,536

****ARP:**** [ARP](https://www.geeksforgeeks.org/ethical-hacking/how-address-resolution-protocol-arp-works/) stands for ****Address Resolution Protocol****. It is used to convert an IP address to its corresponding physical address(i.e., MAC Address). ARP is used by the Data Link Layer to identify the MAC address of the Receiver’s machine.   
  
****RARP:**** [RARP](https://www.geeksforgeeks.org/computer-networks/what-is-rarp/) stands for ****Reverse Address Resolution Protocol****. As the name suggests, it provides the IP address of the device given a physical address as input. But RARP has become obsolete since the time DHCP has come into the picture.


## how the DNS system works :

- ****User Input:**** When a user enters a domain name in a browser, the system needs to find its [IP address](https://www.geeksforgeeks.org/computer-science-fundamentals/what-is-an-ip-address/).
- ****DNS Query:**** The user's device sends a DNS query to the DNS resolver.
- ****Resolver Request:**** The DNS resolver checks its cache for the IP address. If not found, it forwards the request to the root DNS server.
- ****Root DNS Server:**** The root DNS server provides the address of the TLD (Top-Level Domain) server for the specific domain extension (e.g., .com).
- ****TLD DNS Server:**** The TLD server directs the resolver to the authoritative DNS server for the actual domain.
- ****Authoritative DNS Server:**** The authoritative DNS server knows the IP address for the domain and provides it to the resolver.
- ****Response to User:**** The resolver stores the IP address in its cache and sends it to the user's device.
- ****Access Website****: With the IP address, the user's device can access the desired website.


## Access Point

An [access point](https://www.geeksforgeeks.org/computer-networks/what-is-a-wireless-access-point/) in networking is a device that allows wireless devices, like smartphones and laptops, to connect to a wired network. It creates a Wi-Fi network that lets wireless devices communicate with the internet or other devices on the network. Access points are used to extend the range of a network or provide Wi-Fi in areas that do not have it. They are commonly found in homes, offices, and public places to provide wireless internet access.

## Firewalls

A [firewall](https://www.geeksforgeeks.org/computer-networks/introduction-of-firewall-in-computer-network/) is a network security device that monitors and controls the flow of data between your computer or network and the internet. It acts as a barrier, blocking unauthorized access while allowing trusted data to pass through. Firewalls help protect your network from hackers, viruses, and other online [threats](https://www.geeksforgeeks.org/computer-science-fundamentals/computer-security-threats/) by filtering traffic based on security rules. Firewalls can be physical devices (hardware), programs (software), or even cloud-based services, which can be offered as [SaaS](https://www.geeksforgeeks.org/software-engineering/software-as-a-service-saas/), through public clouds, or private virtual clouds.

## what is SMTP protocol?

****Simple Mail Transfer Protocol (SMTP)**** is an application layer protocol used for exchanging email messages between servers. It is essential in the email communication process and operates at the application layer of the TCP/IP stack.

To send an email, the client opens a TCP connection to the SMTP server. The server, which is always listening on port 25, initiates the connection as soon as it detects a client. Once the TCP connection is established, the client sends the email across the connection.

## ****Types of SMTP Protocol****

The ****SMTP model**** supports two types of email delivery methods: ****end-to-end**** and ****store-and-forward****.

- ****End-to-end**** delivery is used between organizations. In this method, the email is sent directly from the sender's SMTP client to the recipient's SMTP server without passing through intermediate servers.
- ****Store-and-forward**** is used within organizations that have TCP/IP and SMTP-based networks. In this method, the email may pass through sellveral intermediate servers (Message Transfer Agents, or MTAs) before reaching the recipient.

# Dynamic Host Configuration Protocol (DHCP)

Dynamic Host Configuration Protocol is a network protocol used to automate the process of assigning IP addresses and other network configuration parameters to devices (such as computers, smartphones and printers) on a network.

Instead of manually configuring each device with an IP address, DHCP allows devices to connect to a network and receive all necessary network information, like IP address, subnet mask, default gateway and DNS server addresses, automatically from a DHCP server.


- ****DHCP Server:**** DHCP Server is a server that holds IP Addresses and other information related to configuration.
- ****DHCP Client:**** It is a device that receives configuration information from the server. It can be a mobile, laptop, computer or any other electronic device that requires a connection.
- ****DHCP Relay:**** DHCP relays basically work as a communication channel between DHCP Client and Server.
- ****IP Address Pool:**** It is the pool or container of IP Addresses possessed by the DHCP Server. It has a range of addresses that can be allocated to devices.
- ****Subnets:**** Subnets are smaller portions of the IP network partitioned to keep networks under control.
- ****Lease:**** It is simply the time that how long the information received from the server is valid, in case of expiration of the lease, the tenant must have to re-assign the lease.
- ****DNS Servers:**** DHCP servers can also provide DNS (Domain Name System) server information to DHCP clients, allowing them to resolve domain names to IP addresses.



## What are the 7 layers of the OSI Model?
#### 7. The application layer

This is the only layer that directly interacts with data from the user. Software applications like web browsers and email clients rely on the application layer to initiate communications. But it should be made clear that client software applications are not part of the application layer; rather the application layer is responsible for the protocols and data manipulation that the software relies on to present meaningful data to the user.

Application layer protocols include [HTTP](https://www.cloudflare.com/learning/ddos/glossary/hypertext-transfer-protocol-http/) as well as [SMTP](https://www.cloudflare.com/learning/email-security/what-is-smtp/) (Simple Mail Transfer Protocol is one of the protocols that enables [email](https://www.cloudflare.com/learning/email-security/what-is-email/) communications).


#### 6. The presentation layer

This layer is primarily responsible for preparing data so that it can be used by the application layer; in other words, layer 6 makes the data presentable for applications to consume. The presentation layer is responsible for translation, [encryption](https://www.cloudflare.com/learning/ssl/what-is-encryption/), and compression of data.

Two communicating devices communicating may be using different encoding methods, so layer 6 is responsible for translating incoming data into a syntax that the application layer of the receiving device can understand.

If the devices are communicating over an encrypted connection, layer 6 is responsible for adding the encryption on the sender’s end as well as decoding the encryption on the receiver's end so that it can present the application layer with unencrypted, readable data.

Finally the presentation layer is also responsible for compressing data it receives from the application layer before delivering it to layer 5. This helps improve the speed and efficiency of communication by minimizing the amount of data that will be transferred.

#### 5. The session layer

This is the layer responsible for opening and closing communication between the two devices. The time between when the communication is opened and closed is known as the session. The session layer ensures that the session stays open long enough to transfer all the data being exchanged, and then promptly closes the session in order to avoid wasting resources.

The session layer also synchronizes data transfer with checkpoints. For example, if a 100 megabyte file is being transferred, the session layer could set a checkpoint every 5 megabytes. In the case of a disconnect or a crash after 52 megabytes have been transferred, the session could be resumed from the last checkpoint, meaning only 50 more megabytes of data need to be transferred. Without the checkpoints, the entire transfer would have to begin again from scratch.

#### 4. The transport layer

Layer 4 is responsible for end-to-end communication between the two devices. This includes taking data from the session layer and breaking it up into chunks called segments before sending it to layer 3. The transport layer on the receiving device is responsible for reassembling the segments into data the session layer can consume.

The transport layer is also responsible for flow control and error control. Flow control determines an optimal speed of transmission to ensure that a sender with a fast connection does not overwhelm a receiver with a slow connection. The transport layer performs error control on the receiving end by ensuring that the data received is complete, and requesting a retransmission if it isn’t.

Transport layer protocols include the [Transmission Control Protocol (TCP)](https://www.cloudflare.com/learning/ddos/glossary/tcp-ip/) and the [User Datagram Protocol (UDP)](https://www.cloudflare.com/learning/ddos/glossary/user-datagram-protocol-udp/).

#### 3. The network layer


The [network layer](https://www.cloudflare.com/learning/network-layer/what-is-the-network-layer/) is responsible for facilitating data transfer between two different networks. If the two devices communicating are on the same network, then the network layer is unnecessary. The network layer breaks up segments from the transport layer into smaller units, called [packets](https://www.cloudflare.com/learning/network-layer/what-is-a-packet/), on the sender’s device, and reassembling these packets on the receiving device. The network layer also finds the best physical path for the data to reach its destination; this is known as [routing](https://www.cloudflare.com/learning/network-layer/what-is-routing/).

Network layer protocols include IP, the [Internet Control Message Protocol (ICMP)](https://www.cloudflare.com/learning/ddos/glossary/internet-control-message-protocol-icmp/), the [Internet Group Message Protocol (IGMP)](https://www.cloudflare.com/learning/network-layer/what-is-igmp/), and the [IPsec](https://www.cloudflare.com/learning/network-layer/what-is-ipsec/) suite.

#### 2. The data link layer

The data link layer is very similar to the network layer, except the data link layer facilitates data transfer between two devices on the _same_ network. The data link layer takes packets from the network layer and breaks them into smaller pieces called frames. Like the network layer, the data link layer is also responsible for flow control and error control in intra-network communication (The transport layer only does flow control and error control for inter-network communications).

#### 1. The physical layer

This layer includes the physical equipment involved in the data transfer, such as the cables and [switches](https://www.cloudflare.com/learning/network-layer/what-is-a-network-switch/). This is also the layer where the data gets converted into a bit stream, which is a string of 1s and 0s. The physical layer of both devices must also agree on a signal convention so that the 1s can be distinguished from the 0s on both devices.


## What are IP & TCP?

The [Internet Protocol (IP)](https://www.cloudflare.com/learning/network-layer/internet-protocol/) is the address system of the Internet and has the core function of delivering [packets](https://www.cloudflare.com/learning/network-layer/what-is-a-packet/) of information from a source device to a target device. IP is the primary way in which network connections are made, and it establishes the basis of the Internet. IP does not handle packet ordering or error checking. Such functionality requires another protocol, often the Transmission Control Protocol (TCP).

The TCP/IP relationship is similar to sending someone a message written on a puzzle through the mail. The message is written down and the puzzle is broken into pieces. Each piece then can travel through a different postal route, some of which take longer than others. When the puzzle pieces arrive after traversing their different paths, the pieces may be out of order. IP makes sure the pieces arrive at their destination address. TCP can be thought of as the puzzle assembler on the other side who puts the pieces together in the right order, asks for missing pieces to be resent, and lets the sender know the puzzle has been received. TCP maintains the connection with the sender from before the first puzzle piece is sent to after the final piece is sent.

IP is a connectionless protocol, which means that each unit of data is individually addressed and routed from the source device to the target device, and the target does not send an acknowledgement back to the source. That’s where protocols such as TCP come in. TCP is used in conjunction with IP in order to maintain a connection between the sender and the target and to ensure packet order.

For example, when an [email](https://www.cloudflare.com/learning/email-security/what-is-email/) is sent over TCP, a connection is established and a 3-way handshake is made. First, the source sends an SYN “initial request” packet to the target server in order to start the dialogue. Then the target server sends a SYN-ACK packet to agree to the process. Lastly, the source sends an ACK packet to the target to confirm the process, after which the message contents can be sent. The email message is ultimately broken down into packets before each packet is sent out into the Internet, where it traverses a series of gateways before arriving at the target device where the group of packets are reassembled by TCP into the original contents of the email.



## What is the User Datagram Protocol (UDP/IP)?

The User Datagram Protocol, or UDP, is a communication protocol used across the Internet for especially time-sensitive transmissions such as [video playback](https://www.cloudflare.com/learning/video/what-is-streaming/) or [DNS](https://www.cloudflare.com/learning/dns/what-is-dns/) lookups. It speeds up communications by not formally establishing a connection before data is transferred. This allows data to be transferred very quickly, but it can also cause [packets](https://www.cloudflare.com/learning/network-layer/what-is-a-packet/) to become lost in transit — and create opportunities for exploitation in the form of [DDoS attacks](https://www.cloudflare.com/learning/ddos/what-is-a-ddos-attack/).



#### TCP vs. UDP

UDP is faster but less reliable than [TCP](https://www.cloudflare.com/learning/ddos/glossary/tcp-ip/), another common transport protocol. In a TCP communication, the two computers begin by establishing a connection via an automated process called a ‘handshake.’ Only once this handshake has been completed will one computer actually transfer data packets to the other.

UDP communications do not go through this process. Instead, one computer can simply begin sending data to the other:

In addition, TCP communications indicate the order in which data packets should be received and confirm that packets arrive as intended. If a packet does not arrive — e.g. due to congestion in intermediary networks — TCP requires that it be re-sent. UDP communications do not include any of this functionality.

These differences create some advantages. Because UDP does not require a ‘handshake’ or check whether data arrives properly, it is able to transfer data much faster than TCP.

However, this speed creates tradeoffs. If a UDP datagram is lost in transit, it will not be re-sent. As a result, applications that use UDP must be able to tolerate errors, loss, and duplication.

(Technically, such packet loss is less a flaw in UDP than a consequence of how the Internet is built. Most network [routers](https://www.cloudflare.com/learning/network-layer/what-is-a-router/) do not perform packet ordering and arrival confirmation by design, because doing so would require an unfeasible amount of additional memory. TCP is a way of filling this gap when an application requires it.)


## What is the Internet Control Message Protocol (ICMP)?

The Internet Control Message Protocol (ICMP) is a [network layer](https://www.cloudflare.com/learning/network-layer/what-is-the-network-layer/) protocol used by network devices to diagnose network communication issues. ICMP is mainly used to determine whether or not data is reaching its intended destination in a timely manner. Commonly, the ICMP [protocol](https://www.cloudflare.com/learning/network-layer/what-is-a-protocol/) is used on network devices, such as [routers](https://www.cloudflare.com/learning/network-layer/what-is-a-router/). ICMP is crucial for error reporting and testing, but it can also be used in [distributed denial-of-service (DDoS) attacks](https://www.cloudflare.com/learning/ddos/what-is-a-ddos-attack/).


## What is ICMP used for?

The primary purpose of ICMP is for error reporting. When two devices connect over the Internet, the ICMP generates errors to share with the sending device in the event that any of the data did not get to its intended destination. For example, if a [packet](https://www.cloudflare.com/learning/network-layer/what-is-a-packet/) of data is too large for a router, the router will drop the packet and send an ICMP message back to the original source for the data.


## What is the Internet Group Management Protocol (IGMP)?

The Internet Group Management Protocol (IGMP) is a protocol that allows several devices to share one IP address so they can all receive the same data. IGMP is a network layer [protocol](https://www.cloudflare.com/learning/network-layer/what-is-a-protocol/) used to set up multicasting on networks that use the [Internet Protocol](https://www.cloudflare.com/learning/network-layer/internet-protocol/) version 4 (IPv4). Specifically, IGMP allows devices to join a multicasting group.


## How does IGMP work?

Computers and other devices connected to a network use IGMP when they want to join a multicast group. A router that supports IGMP listens to IGMP transmissions from devices in order to figure out which devices belong to which multicast groups.

IGMP uses IP addresses that are set aside for multicasting. Multicast IP addresses are in the range between 224.0.0.0 and 239.255.255.255. (In contrast, anycast networks can use any regular IP address.) Each multicast group shares one of these IP addresses. When a router receives a series of packets directed at the shared IP address, it will duplicate those packets, sending copies to all members of the multicast group.

IGMP multicast groups can change at any time. A device can send an IGMP "join group" or "leave group" message at any point.

IGMP works directly on top of the Internet Protocol (IP). Each IGMP packet has both an IGMP header and an IP header. Just like [ICMP](https://www.cloudflare.com/learning/ddos/glossary/internet-control-message-protocol-icmp/), IGMP does not use a transport layer protocol such as [TCP](https://www.cloudflare.com/learning/ddos/glossary/tcp-ip/) or [UDP](https://www.cloudflare.com/learning/ddos/glossary/user-datagram-protocol-udp/).


