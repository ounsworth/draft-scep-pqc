# Architecture of the draft

# How to add KEM PoP?

How do we want to add KEM support to SCEP?

MikeO: I suggest that we mirror as closely as we can the flow that is in CMPv3 (RFC9810) since that has already gone through several years of review.
MikeK: Agree with the above. 

MikeO: KEMs will need some sort of handshake -- or "interactive PoP".
I'm doing this from memory, but I would expect it to look something like this:

1. --> Client submits CSR to CA for a KEM key.
2. <-- CA does an Encaps for that KEM public key.
3. --> Client does a Decaps and uses the resulting shared secret to MAC a conf message.
3a. CA uses their copy of the session shared secret to validate the MAC'd conf message.

MikeK: 

Staying in the theme of how SCEP likes to do things, I have the following thoughts:

* The getCaCaps operation can be extended to indicate support for ML-KEM for encryption
* Normally the CA signing keys (which doesn't need to be the same as the CA's signing key, i.e. an RA key can be used, though that's very badly defined in the draft) and encryption keys (same as before) can be retrieved using the getCaCerts operation. At least in Intune, the signing and encryption keys shouldn't be ephemeral, since they need to be manually set on the Intune side. I suggest we use that mechanism to share the KEM-key.

So given that, the symmetric key would have to be generated client side and the shared secret transmitted to the CA and cached there. Should we in that case allow reuse of the client's symmetric key or require that the CA only allow a single PKCSReq per key?

# Supplant RFC8894 or create an addendum?

MikeO: Having recently done RFC9810 that fully replaces CMPv2 (RFC4210), that is a ton of work, and it opens you up to needing to incorporate all the erata ever filed against the original doc, and whatever other junk people decide absolutely has to be fixed. I vote to do an UPDATE RFC that simply adds a new self-contained functionality to RFC8894. At least, we should aim to do that, unless we run into a reason that this update won't work in that kind of document format.

MikeK: Having thought about if for a few days, I agree with you. A full replacement party feels like it would meet more resistence to pass, and when it reaches the IETF I can see a scenario where it gets modified beyond the scope of what we're discussing here. An Update RFC is more attractive for the reasons your stated above. 


# Also update symmetric ciphers?

As we add PQC algs to SCEP, we might find that we also need to add, like SHA2, AES-GCM, PBKDF2-MAC, etc.


# Which SCEP Nourse or Gutmann?

RFC8894 has some weird history where it started as draft-nourse, but then in 2015 it switched to draft-gutmann, which is, like, actually a different protocol.

If the goal here is to update whatever underpins Microsoft InTune, we should check whether that is Nourse or Gutmann SCEP.

Alternatively, maybe we can do a tight little new mechanism draft that will fit into either version of SCEP, so that this question doesn't matter?

MikeK: the major change between the two (in my subjective) experience is the change to renewals (to the more sane) in the RFC. I'm pretty sure Microsoft holds true to the RFC, and most SCEP questions we've gotten over the last few years over here at EBJCA seem to pertain to Gutmann's version. 
