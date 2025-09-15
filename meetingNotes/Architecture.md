# Architecture of the draft

# How to add KEM PoP?

How do we want to add KEM support to SCEP?

MikeO: I suggest that we mirror as closely as we can the flow that is in CMPv3 (RFC9810) since that has already gone through several years of review.

KEMs will need some sort of handshake -- or "interactive PoP".
I'm doing this from memory, but I would expect it to look something like this:

1. --> Client submits CSR to CA for a KEM key.
2. <-- CA does an Encaps for that KEM public key.
3. --> Client does a Decaps and uses the resulting shared secret to MAC a conf message.
3a. CA uses their copy of the session shared secret to validate the MAC'd conf message.


# Supplant RFC8894 or create an addendum?

MikeO: Having recently done RFC9810 that fully replaces CMPv2 (RFC4210), that is a ton of work, and it opens you up to needing to incorporate all the erata ever filed against the original doc, and whatever other junk people decide absolutely has to be fixed. I vote to do an UPDATE RFC that simply adds a new self-contained functionality to RFC8894. At least, we should aim to do that, unless we run into a reason that this update won't work in that kind of document format.
