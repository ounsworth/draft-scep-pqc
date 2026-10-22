---
title: "TODO - Your title"
abbrev: "TODO - Abbreviation"
category: info

docname: draft-scep-pqc-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "ounsworth/draft-scep-pqc"
  latest: "https://ounsworth.github.io/draft-scep-pqc/draft-scep-pqc.html"

author:
 -
    fullname: Mike Ounsworth
    organization:
    email:

    fullname: Mike Agrenius Kushner
    organization: Keyfactor
    email: mike.kushner@keyfactor.com

normative:

informative:

...

--- abstract

TODO Abstract


--- middle

# Introduction

SCEP is one of the earliest enrollment protocols and predates widespread use of SSL/TLS, so thus relies on its own encryption mechanisms to protect the payload. While RFC 8894 allows for the encapsulation of payloads using the challengePassword, in modern practice it's desirable to use the server's and client's respective keys for encryption. As SCEP also predates widespread use of elliptic curves, the restriction to RSA keys was considered an acceptable limitation at the time.

With the global migration of existing ciphers to quantum safe ones, this limitation becomes far more dire, and there is an existing need to amend SCEP to be able to handle post quantum ciphers. Fortunately, there is an update of CMS (https://datatracker.ietf.org/doc/draft-ietf-lamps-pq-composite-kem/) which allows ML-KEM keys to be used to facilitate enrollment of PQC ciphers over SCEP.

This document describes the procedure needed by CAs and clients in order to utilize SCEP to issue PQC Certificates from a PQC Certificate Authority. 

# Operational Changes

## getCACaps

The GetCACaps is modified by the CA adding the ML-KEM algorithm name (i.e. ML-KEM-768) to the GetCACaps response to indicate to clients that it's expecting requests to be encrypted using an ephemeral symmetric key, with key encapsulation done through ML-KEM.

## PKCSREQ

# Workflow Example: PKCSREQ

This workflow will omit some SCEP-related actions for the sake of brevity, unless they directly affect the encapsulation operation

## SCEP Client Request

The client:

1. performs the getCACaps operation, verifying that the CA is capable of performing KEM operations.
2. perform the getCACerts operation, which returns the CA's signing certificate, encryption certificate and CA chain. 
3. generates a standard PKCS#10 CSR
4. generates a transaction ID as per RFC 8894.
5. generates the Recipient Info with the RFC 9936 key wrap (AES-128 for ML-KEM-512, AES-256 otherwise) and HKDF-SHA256. The recipient is identified by issuer and serial number.
6. generates a symmetric Content Encryption Key (CEK), presumably AES, as per RFC 5652.
8. encrypts the CSR using the CEK
9. uses the CA's public encryption key to derive:
    1. a shared secret
    2. a kemct capsule
10. turns the shared secret into a Key Encryption Key (KEK)
11. encrypts the CEK using the KEK
12. constructs the envelope using the encrypted CEK, the encrypted CSR and the kemct, adds all required labels, including the kemResponse attribute
13. derives the response key from the CEK with HMAC-based Key Derivation Function (HKDF) and the transaction ID and caches it for later
14. signs the envelope using the public key which is to be signed
15. sends the request to the CA

## CA Issuance and Response

In order to verify and decrypt the request:

1. receives the SCEP Request, notes the transaction ID, nonce, message type and the kemResponse attribute
2. verifies the signature on the request
3. identifies the correct decapsulation key based on the Recipient Info and opens the kemct capsule
4. derives the wrapping key by performing HKDF on the shared secret, and then uses the wrapping key to decrypt the CEK
5. decrypts the CSR using the CEK

The CA then issues the certificate, and prepares the response by:
   
1. deriving the response key the same way as the client does above
2. generating a new AES key to act as a CEK for the response
3. preparing the envelope as per RFC 8894 and encrypts it with the new CEK
4. encrypting the CEK using the response key and label it with the transaction ID
5. putting the the encrypted response and the encrypted CEK into the response envelope and signs it using the RA signing key
6. transmitting the response back to the client

## SCEP Client Receipt 

Upon receiving the response, the client:

1. tbd

# Differences to RFC 8894 

This section documents changes to RFC 8894 that are not explicitly mentioned above.

## Use of RA Certificates 

While RFC 8894 is ambiguous about whether a CA must provide an encryption and signing certificate on the chain returned by getCaCerts or can use the key in its own certificate for both signing and encryption, this RFC mandates the use of a specific signing and encryption certificate, containing only the key usages Digital Signature and Key Encryption respectively. 

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

## Key Material Handling 

The ephemeral symmetric keys generated for a SCEP transactions (the wrapping key, the response key and the two CEKs) should never be used more than once, stored or logged, and implementers should make effort to scrub them from memory as soon as they've been used. 


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
