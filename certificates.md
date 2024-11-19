
Part 0 

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
TLS
TLS handshake

Part 6
Key exchange

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