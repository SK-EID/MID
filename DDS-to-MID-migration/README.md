## Why a new service MID-REST (MID) was built to replace DigidocService (DDS)

Since launching the DDS service a lot has happened:

* REST has gained popularity and adoption over SOAP
* Digital signature container formats have evolved from *.ddoc, to *.bdoc (internally two separate formats) until *.asice. Due to these (and other internal) changes, DDS has accumulated a lot of complexity and a lot of it is no longer used.
* The movement towards micro-services architecture - DDS is a monolith and thus morally outdated, MID is built using easily scalable micro-services architecture.
* Launching of Smart-ID proved that authentication/signing functionality should be separated from containers (to provide dedicated micro-services). Majority of customers use Mobile-ID and Smart-ID only for authentication (and not for signing) so to reduce integration costs Smart-ID and MID services are built as similar as possible and the complexity of creating signed containers has been left out.

## Main differences

|               | DDS | MID |
| ---           | --- | --- |
| Documentation | http://sk-eid.github.io/dds-documentation/ | https://github.com/SK-EID/MID |
| Technology                                          | SOAP | REST
| Public libraries available to ease integration work | no | yes ([java](https://github.com/SK-EID/mid-rest-java-client)) |
| Demo applications available                         | no | yes ([java](https://github.com/SK-EID/mid-rest-java-demo)) | 
| Who generates the authentication hash               | partly DDS (10 bytes), partly Relying Party (another 10 bytes) | Relying Party |
| Who needs to calculate 4-digit verification code from the hash | done by DDS | Relying Party (libraries provide this as a method) | 
| Long-polling support (client makes a request and sending back the response is delayed until the customer has input PIN code (or a timeout is reached) | yes | yes | 
| Can be used to check if the end user has a Mobile-ID | yes | yes | 
| Provides authentication with Mobile-ID | yes | yes | 
| Provides hash signing with Mobile-ID.  | yes | yes | 
| Can be used for digital signing of DigiDoc/BDOC with ID card (and other smartcards | yes | no | 
| Can be used to verify a certificate's validity (including any smartcard) | yes | no | 
| Can be used to create DigiDoc/BDOC containers (even when signing with smart-card) | yes | no | 
| Can be used to verify digitally signed files (DigiDoc/BDOC) and validity of signatures | yes | no | 
| OSCP during authentication | yes | no | 


## How to create and validate containers with MID

Use [DigiDoc4J](https://github.com/open-eid/digidoc4j) or [libdigidocpp](https://github.com/open-eid/libdigidocpp) libraries.

# Main differences between Smart-ID and MID

MID verification codes are always in range 0000...8192, but Smart-ID verification codes spread out more widely - 0000...9999.