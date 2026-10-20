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
area: AREA
workgroup: WG Working Group
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

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

This document aims to describe the changes to CA software and clients required. 

# Changes Overview

## getCACaps

The GetCACaps is modified by the CA adding the ML-KEM algorithm name (i.e. ML-KEM-768) to the GetCACaps response to indicate to clients that it's expecting requests to be encrypted using an ephemeral symmetric key, with key encapsulation done through ML-KEM.

# Workflow

## SCEP Client Create Request



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
