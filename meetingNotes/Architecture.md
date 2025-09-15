# Architecture

How do we want to add KEM support to SCEP?

MikeO: I suggest that we mirror as closely as we can the flow that is in CMPv3 (RFC9810) since that has already gone through several years of review.

KEMs will need some sort of handshake -- or "interactive PoP".
I'm doing this from memory, but I would expect it to look something like this:

1. --> Client submits CSR to CA for a KEM key.
2. <-- CA does an Encaps for that KEM public key.
3. --> Client does a Decaps and uses the resulting shared secret to MAC a conf message.
3a. CA uses their copy of the session shared secret to validate the MAC'd conf message.
