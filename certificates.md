
Part 0 
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

X.509 Certificate
The X.509 certificate standard is one of the most prolithic certificate standards. It is what is used all over the web and many other places.

CRL (Certificate Revocation List)
A list of certificates that have been revoked (for whatever reason, comprimise, just because the organizaion felt like it, etc.). The point being is that they are no longer valid. So if you were to visit a website with an X.509 certificate, your computer would first check the CRL, if it was there, it would mark the website as having a bad certificate.

PFX File (Personal Exchange File)
A PFX file is a file that contains X.509 Public Key Certificates, X.509 Private Keys, X.509 CRLs, and other generic data.



Part 1
Anatomy of X.509/RFC 5820
Anatomy of PFX file

Part 2
CSR

Part 3
CRL/OCSP

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