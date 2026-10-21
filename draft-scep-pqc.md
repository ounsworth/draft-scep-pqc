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

# Changes Overview

## getCACaps

The GetCACaps is modified by the CA adding the ML-KEM algorithm name (i.e. ML-KEM-768) to the GetCACaps response to indicate to clients that it's expecting requests to be encrypted using an ephemeral symmetric key, with key encapsulation done through ML-KEM.

# Workflow Example: PKCSREQ

## SCEP Client Request

The client:

1. performs the getCACaps operation, verifying that the CA is capable of performing KEM operations.
2. perform the getCACerts operation, which returns the CA's signing certificate, encryption certificate and CA chain. 
3. generates a standard PKCS#10 CSR
4. generates a transaction ID and a sender NONCE as per RFC 8894.
5. generates the Recipient Info with the RFC 9936 key wrap (AES-128 for ML-KEM-512, AES-256 otherwise) and HKDF-SHA256. The recipient is identified by issuer and serial number.
6. generates a symmetric Content Encryption Key (CEK), presumably AES, as per RFC 5652.
8. encrypts the CSR using the CEK
9. uses the CA's public encryption key to derive:
    1. a shared secret
    2. a kemct capsule
10. turns the shared secret into a Key Encryption Key (KEK)
11. encrypts the CEK using the KEK
12. constructs the envelope using the encrypted CEK, the encrypted CSR and the kemct, adds all required labels, including the kemResponse attribute
13. derives the response key from the CEK with HKDF and the transaction ID and caches it for later
14. Signs the envelope using the public key which is to be signed
15. Sends the request to the CA

## CA Issuance and Response

## SCEP Client Receipt 

# Differences to RFC 8894 

## Use of RA Certificates 

While RFC 8894 is ambiguous about whether a CA must provide an encryption and signing certificate on the chain returned by getCaCerts or can use the key in its own certificate for both signing and encryption, this RFC mandates the use of a specific signing and encryption certificate, containing only the key usages Digital Signature and Key Encryption respectively. 

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
