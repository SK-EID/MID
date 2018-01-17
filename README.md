<div id="page">
<div id="main" class="aui-page-panel">

<div id="main-header">

# <span id="title-text">MID API</span>
</div>

<div id="content" class="view">

<div id="main-content" class="wiki-content group">


*   [1. Introduction](#MIDRESTAPI-Introduction)
    *   [1.1\. Terminology](#MIDRESTAPI-Terminology)
*   [2\. General description](#MIDRESTAPI-Generaldescription)
    *   [2.1\. UUID encoding](#MIDRESTAPI-UUIDencoding)
    *   [2.2\.  relyingPartyName handling](#MIDRESTAPI-relyingPartyNamehandling)
    *   [2.3\. Hash algorithms](#MIDRESTAPI-Hashalgorithms)
*   [3\. REST API](#MIDRESTAPI-RESTAPI)
    *   [3.1\. Session management ](#MIDRESTAPI-Sessionmanagement)
    *   [3.2\. HTTP status code usage](#MIDRESTAPI-HTTPstatuscodeusage)
*   [4\. REST API main flows](#MIDRESTAPI-RESTAPImainflows)
    *   [4.1\. Certificate initiation](#MIDRESTAPI-Certificateinitiation)
        *   [4.1.1\. Preconditions ](#MIDRESTAPI-Preconditions)
        *   [4.1.2\. Postconditions](#MIDRESTAPI-Postconditions)
        *   [4.1.3\. Request parameters](#MIDRESTAPI-Requestparameters)
        *   [4.1.4\. Example request](#MIDRESTAPI-Examplerequest)
        *   [4.1.5\. Example response](#MIDRESTAPI-Exampleresponse)
        *   [4.1.6\. Error conditions](#MIDRESTAPI-Errorconditions)
    *   [4.2\. Certificate status](#MIDRESTAPI-Certificatestatus)
        *   [4.2.1\. Preconditions](#MIDRESTAPI-Preconditions.1)
        *   [4.2.2\. Postconditions](#MIDRESTAPI-Postconditions.1)
        *   [4.2.3\. Response structure ](#MIDRESTAPI-Responsestructure)
        *   [4.2.4\. Error codes](#MIDRESTAPI-Errorcodes)
    *   [4.3\. Signature session initiation](#MIDRESTAPI-Signaturesessioninitiation)
        *   [4.3.1\. Preconditions](#MIDRESTAPI-Preconditions.2)
        *   [4.3.2\. Postconditions](#MIDRESTAPI-Postconditions.2)
        *   [4.3.3\. Request parameters](#MIDRESTAPI-Requestparameters.1)
        *   [4.3.4\. Example request](#MIDRESTAPI-Examplerequest.1)
        *   [4.3.5\. Example response](#MIDRESTAPI-Exampleresponse.1)
        *   [4.3.6\. Error codes](#MIDRESTAPI-Errorcodes.1)
    *   [4.4\. Signature status](#MIDRESTAPI-Signaturestatus)
        *   [4.4.1\. Preconditions](#MIDRESTAPI-Preconditions.3)
        *   [4.4.2\. Postconditions](#MIDRESTAPI-Postconditions.3)
        *   [4.4.3\. Response structure ](#MIDRESTAPI-Responsestructure.1)
        *   [4.4.4\. Error codes](#MIDRESTAPI-Errorcodes.2)
    *   [4.5\. Authentication session initiation](#MIDRESTAPI-Authenticationsessioninitiation)
        *   [4.5.1\. Preconditions](#MIDRESTAPI-Preconditions.4)
        *   [4.5.2\. Preconditions](#MIDRESTAPI-Preconditions.5)
        *   [4.5.3\. Authentication request parameters](#MIDRESTAPI-Authenticationrequestparameters)
        *   [4.5.4\. Example request](#MIDRESTAPI-Examplerequest.2)
        *   [4.5.5\. Example response](#MIDRESTAPI-Exampleresponse.2)
        *   [4.5.6\. Error codes](#MIDRESTAPI-Errorcodes.3)
    *   [4.6\.  status](#MIDRESTAPI-Authenticationstatus)
        *   [4.6.1\. Preconditions](#MIDRESTAPI-Preconditions.6)
        *   [4.6.2\. Postconditions](#MIDRESTAPI-Postconditions.4)
        *   [4.6.3\. Response structure ](#MIDRESTAPI-Responsestructure.2)
        *   [4.6.4\. Error codes](#MIDRESTAPI-Errorcodes.4)
*   [5\. Session end result codes](#MIDRESTAPI-Sessionendresultcodes)

<div>  

# <span class="numhead-number">1\.</span> Introduction

MID REST interface offers the entry point to main use cases for Mobile-ID, i.e. certificate choice, creating signature and authentication.

## <span class="numhead-number">1.1\.</span> Terminology

*   **Relying Party (RP)** - a provider of some kind of e-service. Relying Party authenticates users via MID REST service. 
*   **Session**- A proccess initated by Relying Party, which contains a single certificate choice, authentication or signing operation.

# <span class="numhead-number">2\.</span> General description

Mobile-ID API is exposed over a REST interface as described below. All messages are encoded using UTF-8. 

## <span class="numhead-number">2.1\.</span> UUID encoding

UUID values are encoded as strings containing hexadecimal digits, in canonical 8-4-4-4-12 format, for example: 
<span style="color: rgb(0,0,0);"> </span> `de305d54-75b4-431b-adb2-eb6b9e546014 `

## <span class="numhead-number">2.2\.</span>  relyingPartyName handling

relyingPartyName request field is case insensitive. The string is passed to end user as sent in via API.

## <span class="numhead-number">2.3\.</span> Hash algorithms

MID REST supports signature operations based on SHA-2 family of hash algorithms, namely SHA-256, SHA-384 and SHA-512\. Their corresponding identifiers in API are "SHA256", "SHA384" and "SHA512".

# <span class="numhead-number">3\.</span> REST API

## <span class="numhead-number">3.1\.</span> Session management 

Session is created using one of the POST requests and it ends when a result gets created or when session ends with an error. Session result can be obtained using GET request described below. 

## <span class="numhead-number">3.2\.</span> HTTP status code usage

Normally, all positive responses are given using HTTP status code "200 OK". 

In some cases, 4xx series error codes are used, those cases are described per request. All 5xx series error codes indicate some kind of fatal server error. 

Response on successful session creation 

This response is returned from all POST method calls that create a new session. 

<div class="table-wrap">

<table class="relative-table confluenceTable" style="width: 55.1034%;"><colgroup><col style="width: 10.9091%;"> <col style="width: 6.56566%;"> <col style="width: 11.1111%;"> <col style="width: 71.3131%;"></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Parameter</th>

<th class="confluenceTh">Type</th>

<th class="confluenceTh">Mandatory</th>

<th class="confluenceTh">Description</th>

</tr>

<tr>

<td class="confluenceTd">sessionId</td>

<td class="confluenceTd">string</td>

<td class="confluenceTd">+</td>

<td class="confluenceTd">A string that can be used to request operation result, see below.</td>

</tr>

</tbody>

</table>

</div>

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_  
_`"sessionId" : "de305d54-75b4-431b-adb2-eb6b9e546015"`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

# <span class="numhead-number">4\.</span> REST API main flows

<span class="inline-comment-marker" data-ref="37ce9f19-f57b-4c9f-922a-36e89b61c972">BASE: mid-api</span>

## <span class="numhead-number">4.1\.</span> Certificate initiation

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">**Method**</td>

<td class="confluenceTd">**URL**</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">POST</span></td>

<td colspan="1" class="confluenceTd"><span class="nolink">BASE/certificate</span></td>

</tr>

</tbody>

</table>

</div>

This method retrieves the signing certificate.

This method is necessary for *AdES-styled digital signatures which require knowledge of the certificate before creating the signature.

### <span class="numhead-number">4.1.1\.</span> Preconditions 

*   User identified in the request (by relyingPartyName, relyingPartyUUID and IP-address)

### <span class="numhead-number">4.1.2\.</span> Postconditions

*   New session has been created in the system and its ID returned in response. 

### <span class="numhead-number">4.1.3\.</span> Request parameters

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col> <col></colgroup> 

<thead>

<tr>

<th class="confluenceTh">

Parameter

</th>

<th class="confluenceTh">

Type

</th>

<th class="confluenceTh">

Mandatory

</th>

<th class="confluenceTh">

Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">relyingPartyName</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Name of the relying party – previously agreed with Application Provider and DigiDocService operator.</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">relyingPartyUUID</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">UUID of the relying party</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">phoneNumber</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Phone number of the signer with the country code in the format of +xxxxxxxxx</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">nationalIdentityNumber</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Identification number of the signer (personal national ID number)</span></td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.1.4\.</span> Example request

<div class="table-wrap">

<table class="relative-table confluenceTable" style="width: 64.25%;"><colgroup><col style="width: 100.0%;"></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_

_`"relyingPartyUUID" : "de305d54-75b4-431b-adb2-eb6b9e546014" ,`_  
_`"relyingPartyName" : "BANK123",`_  
_`"phoneNumber" : "+3726234566" `,_  
_`"nationalIdentityNumber" : "31111111111"`_  

_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.1.5\.</span> Example response

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_  
_`"sessionID": "de305d54-75b4-431b-adb2-eb6b9e546015"`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.1.6\.</span> Error conditions

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Error code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Error retrieving certificate response</td>

<td class="confluenceTd">Getting sessionId from cert-store fails. Cert-store throws internal server error</td>

</tr>

<tr>

<td class="confluenceTd">404</td>

<td class="confluenceTd">Something went wrong, response not found</td>

<td class="confluenceTd">Cert-store cannot find response for given request.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">RelyingParty name cannot be null.</td>

<td colspan="1" class="confluenceTd">Parameter in request is missing on has incorrect format/value</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">IP-address in request is invalid</td>

<td colspan="1" class="confluenceTd"><span class="inline-comment-marker" data-ref="d24f7c05-430a-4300-98d3-3eeb970eae06">IP-address in X-Forwarded-For header is incorrect</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">401</td>

<td colspan="1" class="confluenceTd">Failed to authorize user</td>

<td colspan="1" class="confluenceTd">User authorization by relyingPartyName, relyingPartyUUID and IP-address fails</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">405</td>

<td colspan="1" class="confluenceTd">Method Not Allowed</td>

<td colspan="1" class="confluenceTd">  
</td>

</tr>

</tbody>

</table>

</div>

## <span class="numhead-number">4.2\.</span> Certificate status

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">**Method**</td>

<td class="confluenceTd">**URL**</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">GET</span></td>

<td colspan="1" class="confluenceTd"><span class="nolink">BASE/certificate/session/:sessionId</span></td>

</tr>

</tbody>

</table>

</div>

This method can be used to retrieve session result for certificate from MID.

### <span class="numhead-number">4.2.1\.</span> Preconditions

*   Session is present in the system and the request is either running or has been completed less than x minutes ago. 

### <span class="numhead-number">4.2.2\.</span> Postconditions

*   Request result has been returned to user. 

### <span class="numhead-number">4.2.3\.</span> Response structure 

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col> <col></colgroup> 

<thead>

<tr>

<th class="confluenceTh">

Parameter

</th>

<th class="confluenceTh">

Type

</th>

<th class="confluenceTh">

Mandatory

</th>

<th class="confluenceTh">

Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td class="confluenceTd">state</td>

<td class="confluenceTd">string</td>

<td class="confluenceTd">+</td>

<td class="confluenceTd">State of request. "RUNNING"/"COMPLETE".</td>

</tr>

<tr>

<td class="confluenceTd">result</td>

<td class="confluenceTd">string</td>

<td class="confluenceTd">+</td>

<td class="confluenceTd">End result of the transaction.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">cert</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">for OK result</td>

<td colspan="1" class="confluenceTd">Certificate value, DER + Base64 encoded.</td>

</tr>

</tbody>

</table>

</div>

**successful response when still waiting for user's response**

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_  
_`"state"` `: ` `"RUNNING"` `,`_  
_`"result"` `: {}`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

<span style="color: rgb(0,0,0);"> </span>  
**successful response after completion**

<div class="table-wrap">

<table class="relative-table confluenceTable" style="width: 38.75%;"><colgroup><col style="width: 100.0%;"></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_

_``"state"` `: ` `"COMPLETE";``_  
_`"result"` `: "OK"` `,`_  
_`"cert"` `:` `"B+C9XVjIAZnCHH9vfBSv..."`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.2.4\.</span> Error codes

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Error code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Error retrieving certificate response</td>

<td class="confluenceTd">Happens when getting session status from cert-store fails. Cert-store throws internal server error</td>

</tr>

<tr>

<td class="confluenceTd">404</td>

<td class="confluenceTd">SessionId not found :sessionID</td>

<td class="confluenceTd">SessionID in request is not found</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">Required sessionId is missing :sessionID</td>

<td colspan="1" class="confluenceTd">Happens when sessionID is missing.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">IP-address in request is invalid</td>

<td colspan="1" class="confluenceTd">Happens when IP-address in X-Forwarded-For header is incorrect</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">401</td>

<td colspan="1" class="confluenceTd">Failed to authorize user</td>

<td colspan="1" class="confluenceTd">User authorization by sessionID and IP-address fails</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">405</td>

<td colspan="1" class="confluenceTd">Method Not Allowed</td>

<td colspan="1" class="confluenceTd">  
</td>

</tr>

</tbody>

</table>

</div>

## <span class="numhead-number">4.3\.</span> Signature session initiation

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">**Method**</td>

<td class="confluenceTd">**URL**</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">POST</span></td>

<td colspan="1" class="confluenceTd"><span class="nolink">BASE/signature</span></td>

</tr>

</tbody>

</table>

</div>

<span style="color: rgb(0,0,0);">  
</span>

This method is the main entry point to signing logic.

### <span class="numhead-number">4.3.1\.</span> Preconditions

*   User identified in the request.

### <span class="numhead-number">4.3.2\.</span> Postconditions

*   a new session with ID is returned in response. 

### <span class="numhead-number">4.3.3\.</span> <span class="inline-comment-marker" data-ref="54f344ad-e80a-41ba-98cb-8e8d899d26f0">Request parameters</span>

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col> <col></colgroup> 

<thead>

<tr>

<th class="confluenceTh">

Parameter

</th>

<th class="confluenceTh">

Type

</th>

<th class="confluenceTh">

Mandatory

</th>

<th class="confluenceTh">

Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">relyingPartyName</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Name of the relying party – previously agreed with Application Provider and DigiDocService operator.</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">relyingPartyUUID</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">UUID of the relying party</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">phoneNumber</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Phone number of the signer with the country code in the format of +xxxxxxxxx</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">nationalIdentityNumber</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Identification number of the signer (personal national ID number)</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">hash</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Base64 encoded hash function output to be signed.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">hashType</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Hash algorithm.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">language</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Language for user dialog in mobile phone. 3-letters capitalized acronyms are used. Possible values: EST, ENG, RUS, LIT</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">displayText</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">-</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Text displayed in addition to ServiceName and before asking authentication PIN. Maximum length is 40 bytes. In case of Latin letters, this means also a 40 character long text, but Cyrillic characters may be encoded by two bytes and you will not be able to send more than 20 symbols.</span></td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.3.4\.</span> Example request

<div class="table-wrap">

<table class="relative-table confluenceTable" style="width: 62.166668%;"><colgroup><col style="width: 100.0%;"></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_

_`"relyingPartyUUID"` `: ` `"de305d54-75b4-431b-adb2-eb6b9e546014"` `, `_  
_`"relyingPartyName"` `: ` `"BANK123"` `, `_  
_`"phoneNumber"` `: ` `"+3726234566"` `, `_  
_`"nationalIdentityNumber"` `: ` `"31111111111"` `, `_  
_`"hash"` `: ` `"ZHNmYmhkZmdoZGcgZmRmMTM0NTM..."` `, `_  
_`"hashType"` `: ` `"SHA256"` `, `_  
_`"language"` `: ` `"EST"` `, `_  
_`"displayText"` `: ` `"Sign contract" `_  

_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.3.5\.</span> Example response

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{   `_  
_`"sessionId"` `: ` `"de305d54-75b4-431b-adb2-eb6b9e546015"`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.3.6\.</span> Error codes

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Error code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Error retrieving certificate/mssp response</td>

<td class="confluenceTd">Happens when getting sessionId from cert-store/mssp fails. Cert-store/MSSP throws internal server error</td>

</tr>

<tr>

<td class="confluenceTd">404</td>

<td class="confluenceTd">Something went wrong, response not found</td>

<td class="confluenceTd">Happens when cert-store/mssp cannot find response for given request.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">  
</td>

<td colspan="1" class="confluenceTd">Happens when parameter in request is missing on has incorrect format/value</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">IP-address in request is invalid</td>

<td colspan="1" class="confluenceTd">Happens when IP-address in X-Forwarded-For header is incorrect</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">Required sessionId is missing :sessionID</td>

<td colspan="1" class="confluenceTd">Happens when sessionID is missing.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">The length of the hash must match the type of hash</td>

<td colspan="1" class="confluenceTd">Hash length does not match the type of hash. For example: hash: SHA256, hashtype:SHA384</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">Hash must be Base64 encoded</td>

<td colspan="1" class="confluenceTd">Hash is not base64 encoded.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">401</td>

<td colspan="1" class="confluenceTd">Failed to authorize user</td>

<td colspan="1" class="confluenceTd">User authorization by relyingPartyName, relyingPartyUUID and IP-address fails</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">405</td>

<td colspan="1" class="confluenceTd">Method Not Allowed</td>

<td colspan="1" class="confluenceTd">  
</td>

</tr>

</tbody>

</table>

</div>

## <span class="numhead-number">4.4\.</span> Signature status

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">**Method**</td>

<td class="confluenceTd">**URL**</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">GET</span></td>

<td colspan="1" class="confluenceTd"><span class="nolink">BASE/signature/session/:sessionId</span></td>

</tr>

</tbody>

</table>

</div>

This method can be used to retrieve session result for authentication from MID.

### <span class="numhead-number">4.4.1\.</span> Preconditions

*   Session is present in the system and the request is either running or has been completed less than x minutes ago. 

### <span class="numhead-number">4.4.2\.</span> Postconditions

*   Request result has been returned to user. 

### <span class="numhead-number">4.4.3\.</span> Response structure 

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col> <col></colgroup> 

<thead>

<tr>

<th class="confluenceTh">

Parameter

</th>

<th class="confluenceTh">

Type

</th>

<th class="confluenceTh">

Mandatory

</th>

<th class="confluenceTh">

Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td class="confluenceTd">state</td>

<td class="confluenceTd">string</td>

<td class="confluenceTd">+</td>

<td class="confluenceTd">State of request. "RUNNING"/"COMPLETE".</td>

</tr>

<tr>

<td class="confluenceTd">result</td>

<td class="confluenceTd">string</td>

<td class="confluenceTd">+</td>

<td class="confluenceTd">End result of the transaction.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Structure describing the signature result, if any.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature.value</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Signature value, base64 encoded.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature.algorithm</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Signature algorithm, in the form of sha256WithRSAEncryption</td>

</tr>

</tbody>

</table>

</div>

**successful response when still waiting for user's response**

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_  
_`"state"` `: ` `"RUNNING"` `,`_  
_`"result"` `: {}`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

<span style="color: rgb(0,0,0);"> </span>  
**successful response after completion**

<div class="table-wrap">

<table class="relative-table confluenceTable" style="width: 44.833332%;"><colgroup><col style="width: 100.0%;"></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_  
_`"state": "COMPLETE",`_  
_`"result": "OK",`_  
_`"signature": {`_  
_`"value": "B+C9XVjIAZnCHH9vfBSv...",`_  
_`"algorithm": "sha512WithRSAEncryption"`_  
_`}`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.4.4\.</span> Error codes

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Error code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Error retrieving mssp response</td>

<td class="confluenceTd">Happens when getting session status from mssp fails. MSSP throws internal server error</td>

</tr>

<tr>

<td class="confluenceTd">404</td>

<td class="confluenceTd">SessionId not found :sessionID</td>

<td class="confluenceTd">SessionID in request is not found</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">Required sessionId is missing :sessionID</td>

<td colspan="1" class="confluenceTd">Happens when sessionID is missing.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">IP-address in request is invalid</td>

<td colspan="1" class="confluenceTd">

Happens when IP-address in X-Forwarded-For header is incorrect

</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">401</td>

<td colspan="1" class="confluenceTd">Failed to authorize user</td>

<td colspan="1" class="confluenceTd">User authorization by sessionID and IP-address fails</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">405</td>

<td colspan="1" class="confluenceTd">Method Not Allowed</td>

<td colspan="1" class="confluenceTd">  
</td>

</tr>

</tbody>

</table>

</div>

## <span class="numhead-number">4.5\.</span> Authentication session initiation

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td colspan="1" class="confluenceTd">**Method**</td>

<td colspan="1" class="confluenceTd">**URL**</td>

</tr>

<tr>

<td class="confluenceTd"><span style="color: rgb(0,0,0);">POST</span></td>

<td class="confluenceTd"><span class="nolink">BASE/authentication</span></td>

</tr>

</tbody>

</table>

</div>

This method is the main entry point to authentication logic.

### <span class="numhead-number">4.5.1\.</span> Preconditions

*   User identified in the request. 

### <span class="numhead-number">4.5.2\.</span> Preconditions

*   New session has been created in the system and its ID returned in response. 

### <span class="numhead-number">4.5.3\.</span> Authentication request parameters

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col> <col></colgroup> 

<thead>

<tr>

<th class="confluenceTh">

Parameter

</th>

<th class="confluenceTh">

Type

</th>

<th class="confluenceTh">

Mandatory

</th>

<th class="confluenceTh">

Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">relyingPartyName</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Name of the relying party – previously agreed with Application Provider and DigiDocService operator.</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">relyingPartyUUID</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">UUID of the relying party</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">phoneNumber</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Phone number of the signer with the country code in the format of +xxxxxxxxx</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">nationalIdentityNumber</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Identification number of the signer (personal national ID number)</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">hash</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Base64 encoded hash function output to be signed.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">hashType</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Hash algorithm.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">language</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Language for user dialog in mobile phone. 3-letters capitalized acronyms are used. Possible values: EST, ENG, RUS, LIT</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">displayText</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">-</td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(52,56,56);">Text displayed in addition to ServiceName and before asking authentication PIN. Maximum length is 40 bytes. In case of Latin letters, this means also a 40 character long text, but Cyrillic characters may be encoded by two bytes and you will not be able to send more than 20 symbols.</span></td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.5.4\.</span> Example request

<div class="table-wrap">

<table class="relative-table confluenceTable" style="width: 64.833336%;"><colgroup><col style="width: 100.0%;"></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_

_`"relyingPartyUUID": "de305d54-75b4-431b-adb2-eb6b9e546014",`_  
_`"relyingPartyName": "BANK123",`_  
_`"phoneNumber": "+3726234566",`_ 
_`"nationalIdentityNumber": "31111111111",`_  
_`"hash": "ZHNmYmhkZmdoZGcgZmRmMTM0NTM...",`_  
_`"hashType": "SHA256",`_  
_`"language": "EST",`_  
_`"displayText": "Log into internet banking system"`_

_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.5.5\.</span> Example response

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{   `_  
_`"sessionId"` `: ` `"de305d54-75b4-431b-adb2-eb6b9e546015"`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.5.6\.</span> Error codes

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Error code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Error retrieving certificate/mssp response</td>

<td class="confluenceTd">Happens when getting sessionId from cert-store/mssp fails. Cert-store/MSSP throws internal server error</td>

</tr>

<tr>

<td class="confluenceTd">404</td>

<td class="confluenceTd">Something went wrong, response not found</td>

<td class="confluenceTd">Happens when cert-store/mssp cannot find response for given request.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">  
</td>

<td colspan="1" class="confluenceTd">Happens when parameter in request is missing on has incorrect format/value</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">IP-address in request is invalid</td>

<td colspan="1" class="confluenceTd">Happens when IP-address in X-Forwarded-For header is incorrect</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">Required sessionId is missing :sessionID</td>

<td colspan="1" class="confluenceTd">Happens when sessionID is missing.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">The length of the hash must match the type of hash</td>

<td colspan="1" class="confluenceTd">Hash length does not match the type of hash. For example: hash: SHA256, hashtype:SHA384</td>

</tr>

<tr>

<td class="confluenceTd">400</td>

<td class="confluenceTd">Hash must be Base64 encoded</td>

<td class="confluenceTd">Hash is not base64 encoded.</td>

</tr>

<tr>

<td class="confluenceTd">401</td>

<td class="confluenceTd">Failed to authorize user</td>

<td class="confluenceTd">User authorization by relyingPartyName, relyingPartyUUID and IP-address fails</td>

</tr>

</tbody>

</table>

</div>

## <span class="numhead-number">4.6\.</span>  status

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">**Method**</td>

<td class="confluenceTd">**URL**</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">GET</span></td>

<td colspan="1" class="confluenceTd"><span class="nolink">BASE/authentication/session/:sessionId</span></td>

</tr>

</tbody>

</table>

</div>

This method can be used to retrieve session result for authentication from MID.

### <span class="numhead-number">4.6.1\.</span> Preconditions

*   Session is present in the system and the request is either running or has been completed less than x minutes ago. 

### <span class="numhead-number">4.6.2\.</span> Postconditions

*   Request result has been returned to user. 

### <span class="numhead-number">4.6.3\.</span> Response structure 

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col> <col></colgroup> 

<thead>

<tr>

<th class="confluenceTh">

Parameter

</th>

<th class="confluenceTh">

Type

</th>

<th class="confluenceTh">

Mandatory

</th>

<th class="confluenceTh">

Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td class="confluenceTd">state</td>

<td class="confluenceTd">string</td>

<td class="confluenceTd">+</td>

<td class="confluenceTd">State of request. "RUNNING"/"COMPLETE".</td>

</tr>

<tr>

<td class="confluenceTd">result</td>

<td class="confluenceTd">string</td>

<td class="confluenceTd">+</td>

<td class="confluenceTd"><span>End result of the transaction.</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Structure describing the signature result, if any.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature.value</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Signature value, base64 encoded.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature.algorithm</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd">Signature algorithm, in the form of sha256WithRSAEncryption</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">cert</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">for OK</td>

<td colspan="1" class="confluenceTd"><span>Certificate value, DER + Base64 encoded.</span></td>

</tr>

</tbody>

</table>

</div>

**successful response when still waiting for user's response**

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_  
_`"state"` `: ` `"RUNNING"` `,`_  
_`"result"` `: {}`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

<span style="color: rgb(0,0,0);"> </span>  
**successful response after completion**

<div class="table-wrap">

<table class="relative-table confluenceTable" style="width: 40.416668%;"><colgroup><col style="width: 100.0%;"></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

_`{`_  
&nbsp;&nbsp;&nbsp;_`"state"` `: ` `"COMPLETE"` `,`_  
&nbsp;&nbsp;&nbsp;_`"result"` `: "OK"` `,`_  
&nbsp;&nbsp;&nbsp;_`"signature"` `: {`_  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;_` "value"` `: ` `"B+C9XVjIAZnCHH9vfBSv..."` `,`_  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;_` "algorithm"` `: ` `"sha512WithRSAEncryption"`_  
&nbsp;&nbsp;&nbsp;_`},`_  
&nbsp;&nbsp;&nbsp;_`"cert"` `:` `"B+C9XVjIAZnCHH9vfBSv..."`_  
_`}`_

</td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">4.6.4\.</span> Error codes

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Error code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Error retrieving signature response</td>

<td class="confluenceTd">Happens when getting session status from mssp fails. MSSP throws internal server error</td>

</tr>

<tr>

<td class="confluenceTd">404</td>

<td class="confluenceTd">SessionId not found :sessionID</td>

<td class="confluenceTd">SessionID in request is not found</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">Required sessionId is missing :sessionID</td>

<td colspan="1" class="confluenceTd">Happens when sessionID is missing.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">IP-address in request is invalid</td>

<td colspan="1" class="confluenceTd">Happens when IP-address in X-Forwarded-For header is incorrect</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">401</td>

<td colspan="1" class="confluenceTd">Failed to authorize user</td>

<td colspan="1" class="confluenceTd">User authorization by sessionID and IP-address fails</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">405</td>

<td colspan="1" class="confluenceTd">Method Not Allowed</td>

<td colspan="1" class="confluenceTd">  
</td>

</tr>

</tbody>

</table>

</div>

# <span class="numhead-number">5\.</span> Session end result codes

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Response</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">OK</td>

<td class="confluenceTd"><span>Session was completed successfully</span></td>

</tr>

<tr>

<td class="confluenceTd">TIMEOUT</td>

<td class="confluenceTd"><span>There was a timeout, i.e. end user did not confirm or refuse the operation within given timeframe. </span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">ERROR</td>

<td colspan="1" class="confluenceTd"><span>There was error getting response from MSSP/certificate service</span></td>

</tr>

<tr>

<td class="confluenceTd">NOT_MID_CLIENT</td>

<td class="confluenceTd"> <span>Given user has no active certificates and is not M-ID client.</span></td>

</tr>

<tr>

<td class="confluenceTd">EXPIRED_TRANSACTION</td>

<td class="confluenceTd"><span>MSSP transaction timed out.</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">USER_CANCELLED</td>

<td colspan="1" class="confluenceTd"><span>User cancelled the operation</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>MID_NOT_READY</span></td>

<td colspan="1" class="confluenceTd"><span>Mobile-ID not ready</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>PHONE_ABSENT</span></td>

<td colspan="1" class="confluenceTd"><span> Sim not available</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>DELIVERY_ERROR</span></td>

<td colspan="1" class="confluenceTd"><span>SMS sending error</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>SIM_ERROR</span></td>

<td colspan="1" class="confluenceTd"><span>Invalid response from card</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>SIGNATURE_HASH_MISMATCH</span></td>

<td colspan="1" class="confluenceTd"><span>Hash does not match with certificate type</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>INTERNAL_ERROR</span></td>

<td colspan="1" class="confluenceTd"><span>All other technical errors</span></td>

</tr>

</tbody>

</table>

</div>

</div>

</div>

</div>

</div>

</div>
