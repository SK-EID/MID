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
| Public libraries available to ease integration work | no | yes ([java](https://github.com/SK-EID/mid-rest-java-client) and [php](https://github.com/SK-EID/mid-rest-php-client)) |
| Demo applications available                         | no | yes ([java](https://github.com/SK-EID/mid-rest-java-demo) and [php](https://github.com/SK-EID/mid-rest-php-demo)) | 
| Who generates the authentication hash               | partly DDS (10 bytes), partly Relying Party (another 10 bytes) | Relying Party |
| Who needs to calculate 4-digit verification code from the hash | done by DDS | Relying Party (libraries provide this as a method) | 
| Relying Party can display Verification Code immediately after getting End User's details (phone number and national identity number) | no, it has to make a request to DDS first | yes | 
| Long-polling support (client makes a request and sending back the response is delayed until the customer has input PIN code (or a timeout is reached) | yes | yes | 
| Can be used to check if the end user has a Mobile-ID | yes | yes | 
| Provides authentication with Mobile-ID | yes | yes | 
| Provides hash signing with Mobile-ID.  | yes | yes | 
| Can authenticate Estonian users with providing phone number only (or only national ID-code)  | yes| no| 
| Can authenticate Lithuanian users with providing phone number only (or only national ID-code)| no | no|  
| Can be used for digital signing of DigiDoc/BDOC with ID card (and other smartcards | yes | no | 
| Can be used to verify a certificate's validity (including any smartcard) | yes | no | 
| Can be used to create DigiDoc/BDOC containers (even when signing with smart-card) | yes | no | 
| Can be used to verify digitally signed files (DigiDoc/BDOC) and validity of signatures | yes | no | 
| OCSP during authentication | yes | no | 
| Relying Party can get end user's authentication certificate| yes | only after successful authentication|
| Relying Party can pull end user's signing certificate| yes | yes|
| Relying Party can decide if it wants to use RSA or ECC certificate for signing | yes | no, MID decides which certificate to return|


## How to create and validate containers with MID

Use [DigiDoc4J](https://github.com/open-eid/digidoc4j) or [libdigidocpp](https://github.com/open-eid/libdigidocpp) libraries.

# Main differences between Smart-ID and MID

## Multiple accounts

Single End User can have more than one device with Smart-ID installed on it (multiple accounts, each account has its
own authentication and signing certificate) and (during first interaction) it is not possible for Relying Party to
target the notification to specific device. If End User has multiple accounts then at first interaction
 (usually the first interaction in any session is authentication) all these devices
get a notification and user can choose which device it wants to use for the current session.

With MID the combination of phone number and national identification code always results with single pair of authentication
and signing certificates. In Estonia any End User can have only one MID account.
In Lithuania it is possible for End User to have multiple MID accounts (each account with a different cell phone number).

This is the reason why in Smart-ID pulling signing certificate is designed to be a two step process but with MID
it is possible to pull the certificate with a single request.
If Relying Party only has End User's (with multiple Smart-ID accounts) natinal identity number and it wants to pull signing certificate
then all the devices get a notification and End User has to click on the device it wants to use for signing.
However this is used rarely as End User usually has to authenticate first and during authentication the user 
selects the device and further operations within this session (i.e. signing) the same device is used by Relying Party.


## Verification code

MID verification codes are always in range 0000...8192, but Smart-ID verification codes spread out more widely - 0000...9999.

