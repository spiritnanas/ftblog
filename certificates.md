
Part 0 

Preface: I am not an expert on this topic. I am writing this blog as I go, and as I feel comfortable with the information I have learned. I found it very challenging to locate all of the information needed to get a detailed undertanding of PKI, TLS, and 801.x in one place. Honestly, I did not even find PKI in one place, not even close. My goal is to provide this as a starting point for others wanting to learn about the topic. 

The below section is a series of vocabulary and short descriptions. They seem mostly unrelated but will be referenced many times later on. It is helpful to familiarize yourself with some of these terms. 

Digital signature vs encryption 
A digital signature's goal is to prove that the signer has access to the private key used to sign the content.
Encryptions goal is to ensure the confidentiality of the data. 

A digital signature is created with the private key and verified with the public key.
Encryption is completed with the public key and decrypted with the private key

While not a dificult topic to understand, it is key to keep in mind later on.
High level of PKI

PKI is a system that uses Digital Certificates to verify identity.

High level of TLS

TLS is a transport layer encryption standard that uses certificates to protect the worlds web traffic in many ways. 
High level of TLS handshake
The TLS handshake is the way in which TLS TLS valdiates identity and completes an exchange of keys for the purposes of encrypting further traffic that takes place after the handshake.

#High level of TLS Key Exchange
802.1x
802.1x is a set of standards that authenticate a device onto a port that is part of a network. I.e. Wi-Fi, Switches, Printers, etc., allowing them onto the LAN.

High level of EAPoL
EAPOL is the standard responsible for the intial setup of communication between a suplicant (device seeking access via 802.1x) and the authenticator (the network device, switch, access point, etc.) to support further 802.1x communications.
High level of PEAP
PEAP is a relatively insecure 802.1x authenticatin meathod that uses a certificate on the authenticator to encrypt a username and password exchange for authentication.

High level of EAP-TLS
EAP-TLS is the gold standard for 802.1x authentication. It uses a certificate on the suplicant as well as the authenticator to authenticate to the network. 
https://www.rfc-editor.org/rfc/rfc5216.html

X.509 Certificate
The X.509 certificate standard is one of the most prolithic certificate standards. It is what is used all over the web and many other places.
https://datatracker.ietf.org/doc/html/rfc5280#section-4.1.1.3

CRL (Certificate Revocation List)
A list of certificates that have been revoked (for whatever reason, comprimise, just because the organizaion felt like it, etc.). The point being is that they are no longer valid. So if you were to visit a website with an X.509 certificate, your computer would first check the CRL, if it was there, it would mark the website as having a bad certificate.
https://datatracker.ietf.org/doc/html/rfc5280#section-5.1

PFX File (Personal Exchange File)
A PFX file is a file that contains X.509 Public Key Certificates, X.509 Private Keys, X.509 CRLs, and other generic data.
https://datatracker.ietf.org/doc/html/rfc7292

CSR (Certificate Signing Request)
A certificate signing request is the proccess of an applicant requesting a certificate from a signing CA.
https://datatracker.ietf.org/doc/html/rfc2986#section-4


With that out of the way, prepare for more seemingly out of order topics. There is a point to this. Without the knowledge of what we are going to discuss first, the later parts will not make as much sense. 

The first item we are going to discuss is the X.509 Public Key Infrastructure Certificate

Part 1

Anatomy of X.509/RFC 5820

The anatomy of the X.509 cert is important, some parts being more critical to others for understanding later proccesses. I am going to note those in this description.

Basic fields:
- version: This is the version of X.509 being used. Most these days are version 3.
- serialNumber: A unique number assigned by the CA that created this certificate. Each certificate that CA creates MUST have a unique serialNumber. 
- signature: This field contains the algorithm used by the signing CA that is used to sign the certificate. You will notice another similar field, signatureAlgothim below. The reason for having it twice. This is to prevent agains algorithim substitution attacks. From what I can tell, it was expected that this type of attack to be much more common than it was. Here is some further reading on it. The big thing to know is that this field must match the later one. 
https://security.stackexchange.com/questions/114746/why-is-the-signature-algorithm-listed-twice-in-an-x509-certificate
- issuer: The enttity that signed and issued the certificate. IMPORTANT
- validity: The time/date for which this certificate is valid. IMPORTANT
- subject: This is the identity accocisated with the public key of the certificate. More plainly, who the certificate is assigned to. I.e. *.reddit.com, webhost.contoso.local, etc. IMPORTANT
- subjectPublicKeyInfo: This field contains the public key as well as the algotithm the key is used with.
- issuerUniqueId: This field is used to handle the possibility of certificate reuse, and is meant to uniquely identify the issuer. More to come soon, but for all intents and purposes this field should not be used. It is heavily discouraged in the X.509 standard. 
- subjectUniqueId: This field is used to handle the possibility of certificate reuse, and is meant to uniquely identify the subject. More to come soon, but for all intents and purposes this field should not be used. It is heavily discouraged in the X.509 standard. 
- extensions: These are going to be explained in a later topic, but for now understand that they require X.509 Version 3, and greatly extend the amount of fields to accomodate various things. 
- signatureAlgorithm: Contains the algorithim used by the Signing CA in the digital signature of the certificate. 
- signatureValue: This field contains the digital signature of the certificate. This proccess is done in the following way:
1. The data in the certificate (everything above this) is formatted according to the ASN.1 DER standard. The reason for this encoding is just to standardize how the data is formatted before signing. Otherwise certs would fail validation all the time because someone added an extra space or something. 
https://datatracker.ietf.org/doc/html/rfc6025
2. The encoded data is then digitally signed with the Signing CA's private key using the algorithim called out in the signatureValue field(s).


Anatomy of PFX file
TBC

Part 2

The next major concept to take on is the CSR, or the Certificate Signing Request. This is the proccess in which an applicant requests a certificate from a certificate authority. The following goes over that proccess at a high level.
1. The applicant creates a public/private key pair. The private key is kept secret. 
2. The applicant creates a request with the following fields:
2.1 X.509 Version
2.2 subject: This is the same subject as from Part 1.
2.3 subjectPKInfo: The algorithm the public key is to be used with, along with the public key. This is the same as the subjectPublicKeyInfo from Part 1.
2.4 attributes
3. The request is self signed by the applicant, to prove they have the private key.
4. The request is sent to the signing CA with proof of origin (i.e. providing the CA whatever they have defined to ensure you are who you say you are. For internal CA's this is determined by company policy.)
5. The Signing CA, assuming everything is correct, will send back a complete X.509 Certificate signed with its private key. The policies on the Singing CA determine what fields are included past that. 


Part 3
CRL/OCSP

CRLs or Certificate Revocation Lists are a means of keeping track of if certificates have been revoked or put on hold. This data is often stored in one of the X.509 Certificate Extensions. The extensions section was mentioned in Part 1, this is a field that would be stored there. The field in question being crlDistributionPoints (need to verify this is the actual field name). This field specifies how CRL information should be obtained. This tells the device how to find what certificates have been revoked. 

OCSP or Online Certificate Status Protocol was created to solve the problem of massive CRL's (for example, some devices may not even support CRL's that are as large as todays are, and would fail a check). It is essentially an API that is used in place of a CRL to check if a certificate is valid or not in near real time. 

Part 4
Example of accessing website up to TLS handshake. Include detail on how certs are verified.

Part 5

Okay, at this point, we have covered the 5000 foot view of PKI. The following sections are a couple of common technologies that rely on PKI. They are both great examples, and are excelent to understand. 

One thing to know about the TLS standard is that it is not a set standard in terms of what encryption alorithims are used, in that there are several to choose from. 

For example for TLS 1.2 there are 8 different 'cypher suites' to choose from. These each contain one of the following:

1. Key Exchange Method (Eliptical Curve Diffie-Hellman)
2. Authentication Method (Eliptical Curve DSA, RSA)
3. Encryption Standard (AES128, AES256)
4. Hashing Algorithm (SHA256, SHA384)

The first topic we are going to discuss is TLS, or Transport Layer Security. It is what secures the web traffic you are using to view this site right now. It works in the following way.

The following example is light on 'TLS' but is more intended to show how certificates are used. More TLS coming later as I am more comfortable with it.

Client accessing a website example:

1. Client sends a 'clientHello' containing the max TLS version the client supports, a random number to prevent replay attacks, and what cypher suites the client supports. 
2. Server responds with a 'ServerHello' containing the TLS version and cypher suite the server has chosen based on what the client supports and what the server supports, as well as a random number. Typically this is the highest version available, but can be configured on the server. If the server has no overlap with the client on what it supports, the proccess stops and an error is thrown.
3. Server presents its certificate to the client. 
4. The server sends the server key exchange message. This contains the parameters for the Diffie-Hellman key exchange. In addition it sends it Public Value (different than public key) for the Diffie-Hellman excahnge. In addition it sends a digital signature of all of the previous messages so far, that is signed with the servers private key (the same one that is on the certificate).
5. Server sends a 'serverHelloDone' message, indicating that is all of the data it is sending over for now.
6. The client is going to verify the certificate from the server, below is a detail of how the client validates that certificate. 
I am going to take this time to explain in detail how this validation occurs. This is absolutely critical go gaining understanding to PKI. 
1. The certificate the server sends to the client contains all certificates between that certificate and the root certificate. So in a two tier architectrure, it would include the web servers certificate, the singing CA's certificate, and the root CA's certificate. 
2. The certificates are checked against their CRL's
3. The client reads the signatureValue field of the Web Server's certificate.
4. The client then takes the subjectPublicKey value from the SIGNING CA'S CERTIFICATE and uses that pubic key to verify the web servers certificate.
5. This proccess is then repeated except this time the Signing CA's signatureValue is validated using the subjectPublicKey value from the Root CA's certificate. 
6. This proccess is done one more time, a little different now. The Root CA's certificate's signatureValue is read, but this time it is validated using the Root CA Certificates own subjectPublicKeyInfo value. This is to verify nothing has changed in the Root CA certificate. 
7. The client then checks to see if the Root CA's certificate is in its local repository of trusted root certificates. If it is, it can deduce the folowing:
Since the web servers certificate was signed by the issuing CA's certificate (we checked the digital signature on the Web Server's cert), and the Singing CA's certificate was indeed proven to be signed by the Root CA's cert (again proven by checking the digital signature on the Singing CAs cert agains the pubic key of the root certificate), and the Root Certificate was in that devices trusted root certificate store, we therefore trust the Web Servers certificate. 
7. Now, the client send the clientKeyExchange message. In this case, as this is a web server example, that is all that is sent (need to verify this step, I believe I am missing something)
#Note. There is a lot missing here, namely in the Diffe-Hellman key exchange section. There are several steps missing before this related to that. Suffice to say that the client and the server exchanged keys successfully and now have everything they need to talk securely with each other. These are primarily called out in steps 4 and 7 with the client and server key exchanges. 
8. The client sends a 'changeCypherSpec' message. This message basically says that since the key exchange is done, everything I send from here on out is going to be encrypted. 
9. The client sends a 'finish' message that contains a summary of all the messages sent back and forth so far. This message is encrypted. If the server can not read it, the proccess has failed. 
10. The server sends a 'finish' message containing the same thing. Note that the important part of these is the summary. This protects agains attackes where say for example someone was sitting in the middle and intercepting/modifying packets. Since the summaries being sent are from each devices point of view, if they don't match the proccess is aborted, as it means the proccess was tampered with. 

TLS established. 

Summary of what we get from this exchange.
1. Negotiation of connection terms. This is the proccess for which the client and server sent their respective hello messages, agreeing on how they will communicate. 
2. Encryption. Taking place by using Diffe-Hellman to exchange the AES keys (to be explained further in Part 6). RSA for example can only be used to encrypt a small amount of data, which is why this step is so important. 
3. Authentication. This is the use of the PKI certificates and their digital signatures. In this example we only authenticted the web server, but in other uses, such as in EAP-TLS, this proccess is done on both sides. 
4. Assurance of integrity of the TLS handshake proccess. This is contained in the server and client finish messages, as well as the random numbers. The summaries contained in the finish messages ensure there was no tampering, and the random numbers ensure that a replay attack cant be used. 

Part 6
Key exchange. This sectio will come later, but the Key Exchange is a key part of TLS. 

Part 7
EAPOL

Part 8
EAPOL
PEAP
EAP-TLS
802.1X Example

Part 9
Medium overview of PKI

Part 10
Exploring other uses 

Part 11
RSA

Part 12
ECE