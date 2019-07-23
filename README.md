<div id="page">
<div id="main" class="aui-page-panel">

# Mobile ID (MID) REST API

<div id="content" class="view">
<div id="main-content" class="wiki-content group">


*   [1. Introduction](#1-introduction)
    *   [1.1\. Terminology](#11-terminology)
*   [2\. General description](#2-general-description)
    *   [2.1\. relyingPartyName](#21-relyingpartyname)
    *   [2.2\. relyingPartyUUID](#22-relyingpartyuuid)
    *   [2.3\. Creating the hash](#23-creating-the-hash)
         *   [2.3.1\. Supported hashing algorithms](#231-supported-hashing-algorithms)
    *  [2.4\. Verification code](#24-verification-code)
         *   [2.4.1\. Verification code calculation algorithm](#241-verification-code-calculation-algorithm)
    *  [2.5\. HTTP status code usage](#25-http-status-code-usage)
    *  [2.6\. Session management](#26-session-management)
    *  [2.7. Backwards compatibility](#27-backwards-compatibility)
*   [3\. REST API flows](#3-rest-api-flows)
    *   [3.1\. Certificate request](#31-certificate-request)
        *   [3.1.1\. Pre-Conditions ](#311-Pre-Conditions)
        *   [3.1.2\. Post-Conditions](#312-Post-Conditions)
        *   [3.1.3\. Request parameters](#313-request-parameters)
        *   [3.1.4\. Example request](#314-example-request)
        *   [3.1.5\. Example response](#315-example-response)
        *   [3.1.6\. Response structure ](#316-response-structure)
        *   [3.1.7\. Possible result values](#317-possible-result-values)
        *   [3.1.8\. Error conditions](#318-error-conditions)
    *   [3.2\. Initiating signing and authentication](#32-initiating-signing-and-authentication)
        *   [3.2.1\. Pre-Conditions](#321-Pre-Conditions)
        *   [3.2.2\. Post-Conditions](#322-Post-Conditions)
        *   [3.2.3\. Request parameters](#323-request-parameters)
        *   [3.2.4\. Example request](#324-example-request)
        *   [3.2.5\. Example response](#325-example-response)
        *   [3.2.6\. Error codes](#326-error-conditions)
    *   [3.3\. Status of signing and authentication](#33-status-of-signing-and-authentication)
        *   [3.3.1\. Pre-Conditions](#331-Pre-Conditions)   
        *   [3.3.2\. Post-Conditions](#332-Post-Conditions)
        *   [3.3.3. Request parameters](#333-request-parameters)
        *   [3.3.4. Long polling](#334-long-polling)
        *   [3.3.5. Response structure](#335-response-structure)
        *   [3.3.6. Verifying the authentication response](#336-verifying-the-authentication-response)
        *   [3.3.7. Example responses](#337-example-responses)
        *   [3.3.8. Session end result codes](#338-session-end-result-codes)
        *   [3.3.9. HTTP error codes](#339-http-error-codes)
    *   [3.4. API version](#34-api-version)
        *   [3.4.1. Example response](#341-example-response)
        *   [3.4.2. Response structure](#342-response-structure)
        *   [3.4.3. Public demo environment version number](#343-public-demo-environment-version-number)
*   [4\. Helper libraries and demo applications](#4-helper-libraries-and-demo-applications)
    *   [4.1\. Java](#41-java)
    *   [4.2\. PHP](#42-php)
*   [5\. Comparison with DigiDocService](#5-comparison-with-digidocservice)

<div>  

# <span class="numhead-number">1\.</span> Introduction

Mobile-ID (MID) REST interface offers the entry point to main use cases for Mobile-ID:
* digital signing
* authentication
* pulling an End Users's signing certificate

## <span class="numhead-number">1.1\.</span> Terminology

*   **Application Provider** - provider of the MID service ([SK ID Solutions AS](https://www.sk.ee/en))
*   **Relying Party (RP)** - e-service provider - client for the MID REST API. Authenticates users via MID REST service and/or uses MID REST for users to sign documents inside the e-service. 
*   **Session** - A process initiated by Relying Party, which contains authentication or signing operation.
*   **End User** - Person that has a mobile phone with Mobile-ID SIM and who initiates authentication or signing in e-service.
*   **Mobile Signing** - A process where (besides other operations) the hash value of document to be signed is encrypted using secret signing key (stored on SIM-card, protected by 5-digit PIN) 
*   **Mobile Authentication** - A process where generated hash is encrypted using secret authentication key (stored on SIM-card, protected by 4-digit PIN)
*   **Verification Code** - A 4-digit number displayed both in e-service and in cellphone screen during authentication and signing. See paragraph 2.4 for more info.

# <span class="numhead-number">2\.</span> General description

Mobile-ID API is exposed over REST interface as described below.
All messages are encoded using UTF-8. 

## <span class="numhead-number">2.1\.</span> relyingPartyName

Possible string values of RelyingPartyName are agreed between Relying Party and Application Provider during registration.
The value from request is checked during authorization and is displayed to the End User on cell phone screen during authentication and signing.
This field is case insensitive.

E-Service provider can have more than one relyingPartyName for different e-services.

## <span class="numhead-number">2.2\.</span> relyingPartyUUID

relyingPartyUuid is a shared secret that is handed to Relying Party by Application Provider during registration.

This value contains hexadecimal digits in canonical 8-4-4-4-12 format, for example: 
<span style="color: rgb(0,0,0);"> </span> `de305d54-75b4-431b-adb2-eb6b9e546014 `

E-Service provider can have more than one relyingPartyUUID for different e-services.

## <span class="numhead-number">2.3\.</span> Creating the hash

Relying Party creates the hash using one of the supported hashing algorithms.
For signing the document to be signed is the input for the chosen hash algorithm.

For authentication the hash can be random HEX string with correct length.
The length has to be either 32 characters (if hashType is SHA-256), 48 characters (SHA-384) or 64 characters (SHA-512).

### <span class="numhead-number">2.3.1\.</span> Supported hashing algorithms

MID REST supports signature operations based on SHA-2 family of hash algorithms, namely SHA-256, SHA-384 and SHA-512\.
Their corresponding identifiers in API are "SHA256", "SHA384" and "SHA512".

## <span class="numhead-number">2.4\.</span> Verification code

Verification code is a 4-digit number used in mobile authentication and mobile signing.
Verification code is cryptographically linked with hash value to be signed.
Verification code is displayed both in mobile phone and in e-service provider application in order to provide authenticity of the signing request
and enable end user to verify what is exactly being signed.

During Mobile-ID authentication and signing it is required that e-service provider calculates verification code from the hash what will be signed 
and displays it to the user.

### <span class="numhead-number">2.4.1\.</span> Verification code calculation algorithm

6 bits from the beginning of hash and 7 bits from the end of hash are taken. 
The resulting 13 bits are transformed into decimal number and printed out.
The Verification code is a decimal 4-digits number in range 0000...8192, always 4 digits are displayed (e.g. 0041).

**Example:**
* Hash value: 2f665f6a6999e0ef0752e00ec9f453adf59d8cb6
* Binary representation of hash: **0010 11**11 0110 0110 1111 .... 1000 1100 1**011 0110**
* Verification code – binary value: 0010110110110
* Verification code – decimal value (displayed for the user): 1462


## <span class="numhead-number">2.5\.</span> HTTP status code usage

All positive responses are given using HTTP status code "200 OK".
Cases where end user is not a Mobile ID customer or the certificate of End User is not active
are also considered as positive responses and HTTP status code 200 is used.

In some cases, 4xx series error codes are used, those cases are described per request.
All 5xx series error codes indicate some kind of fatal server error. 

## <span class="numhead-number">2.6\.</span> Session management 

Main flows of MID REST API - authentication and signing - include involvement from End User who has to enter PIN on the cellphone.
As this can take time - these processes are split in two parts:

* First request initiates the process and immediately returns session id to the Relying Party.
* Relying Party has to then periodically make status check requests until the process has finished.

## <span class="numhead-number">2.7.</span> Backwards compatibility

MID-REST API-s remain backwards compatible with following exceptions:

* new request fields may be added over time
* new fields may be added to JSON responses. Developers need to take this into account when
de-serializing JSON fields into objects. For example when using Jackson to de-serialize JSON
objects into Java objects the Java classes should be annotated with
`@JsonIgnoreProperties(ignoreUnknown = true)` 
or the configuration parameter
[FAIL_ON_UNKNOWN_PROPERTIES](https://github.com/FasterXML/jackson-databind/wiki/Deserialization-Features)
should be set to false. Otherwise Jackson starts throwing
`com.fasterxml.jackson.databind.exc.UnrecognizedPropertyException`
when a new field is added to a MID-REST API response.

See [chapter 3.4.](#34-api-version) for fetching current API version.

# <span class="numhead-number">3.</span> REST API flows

<span class="inline-comment-marker" data-ref="37ce9f19-f57b-4c9f-922a-36e89b61c972">BASE: mid-api</span>

## <span class="numhead-number">3.1\.</span> Certificate request

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

**Method**

</td><td class="confluenceTd">

**URL**

</td></tr>

<tr>
    <td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">POST</span></td>   
    <td colspan="1" class="confluenceTd"><span class="nolink">BASE/certificate</span></td>
</tr>

</tbody>
</table>
</div>

This method retrieves the signing certificate.

This method is necessary for *AdES-styled digital signatures which require knowledge of the certificate before creating the signature.
For other types of digital signatures knowledge of the certificate is not needed.

This endpoint can be used to test if end user is a Mobile ID customer.

If end user has two pairs of certificates (RSA and Elliptic Curve Cryptography (ECC)) then system returns preferred certificate (ECC).


### <span class="numhead-number">3.1.1\.</span> Pre-Conditions 

*   User identified in the request (by relyingPartyName, relyingPartyUUID and IP-address)

### <span class="numhead-number">3.1.2\.</span> Post-Conditions

*   Request result has been returned to caller. 

### <span class="numhead-number">3.1.3\.</span> Request parameters

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

<td colspan="1" class="confluenceTd"><span>relyingPartyName</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span>Name of the relying party – previously agreed with Application Provider and DigiDocService operator.</span></td>

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

<td colspan="1" class="confluenceTd"><span>Phone number of the signer with the country code in the format of +xxxxxxxxx</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">nationalIdentityNumber</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span>Identification number of the signer (personal national ID number). For example 38412319871</span></td>

</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">3.1.4\.</span> Example request


```json
{
   "relyingPartyUUID": "de305d54-75b4-431b-adb2-eb6b9e546014" ,  
   "relyingPartyName": "BANK123",  
   "phoneNumber": "+3726234566" ,  
   "nationalIdentityNumber": "38412319871"
}
```

### <span class="numhead-number">3.1.5\.</span> Example response

```json
{  
    "result": "OK",  
    "cert": "MIIHhjCCBW6gAwIBAgIQDNYLtVwrKURYStrYApYViTANBgkqhkiG9w0B..."  
}
```

### <span class="numhead-number">3.1.6\.</span> Response structure 

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

### <span class="numhead-number">3.1.7\.</span> Possible result values

For all the result values HTTP status code 200 is used.

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Result</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">OK</td>

<td class="confluenceTd"><span>An active certificate was found</span></td>

</tr>

<tr>

<td class="confluenceTd">NOT_FOUND</td>

<td class="confluenceTd"><span>No certificate for the user was found</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">NOT_ACTIVE</td>

<td colspan="1" class="confluenceTd"><span>Certificate was found but is not active</span></td>

</tr>


</tbody>

</table>

</div>


### <span class="numhead-number">3.1.8\.</span> Error conditions

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">HTTP status code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>



<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">{parameterName} cannot be null.</td>

<td colspan="1" class="confluenceTd">Required parameter in request is missing on has incorrect format/value</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">401</td>

<td colspan="1" class="confluenceTd">Failed to authorize user</td>

<td colspan="1" class="confluenceTd">User authorization by relyingPartyName, relyingPartyUUID and IP-address fails</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">405</td>

<td colspan="1" class="confluenceTd">Method Not Allowed</td>

<td colspan="1" class="confluenceTd">Only POST and OPTIONS methods are allowed.  
</td>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Internal error</td>

<td class="confluenceTd">MID-REST internal error. Retry the operation.</td>

</tr>

</tbody>

</table>

</div>



## <span class="numhead-number">3.2\.</span> Initiating signing and authentication

Different URL-s need to be used to initiate signing or authentication:

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<td class="confluenceTd">

**Process**

</td>
<td class="confluenceTd">

**Method**

</td>
<td class="confluenceTd">

**URL**

</td>
</tr>

<tr>


<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">Signing</span></td>

<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">POST</span></td>

<td colspan="1" class="confluenceTd"><span class="nolink">BASE/signature</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">Authenticate</span></td>

<td class="confluenceTd"><span style="color: rgb(0,0,0);">POST</span></td>

<td class="confluenceTd"><span class="nolink">BASE/authentication</span></td>

</tr>


</tbody>

</table>

</div>




### <span class="numhead-number">3.2.1\.</span> Pre-Conditions

*   End User in the request is identified.

### <span class="numhead-number">3.2.2\.</span> Post-Conditions

*   A new session with ID is returned in response. 

### <span class="numhead-number">3.2.3\.</span> <span class="inline-comment-marker" data-ref="54f344ad-e80a-41ba-98cb-8e8d899d26f0">Request parameters</span>

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
    <td colspan="1" class="confluenceTd"><span>relyingPartyName</span></td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">+</td>
    <td colspan="1" class="confluenceTd"><span>Name of the Relying Party, previously agreed with Application Provider. Displayed together with displayText and Verification Code on cellphone screen before End User can insert PIN.</span></td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd">relyingPartyUUID</td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">+</td>
    <td colspan="1" class="confluenceTd"><span>UUID of the Relying Party - previously agreed with Application Provider.</span></td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd">phoneNumber</td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">+</td>
    <td colspan="1" class="confluenceTd"><span>Phone number of the signer with the country code in the format of +xxxxxxxxx</span></td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd">nationalIdentityNumber</td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">+</td>
    <td colspan="1" class="confluenceTd"><span>Identification number of the signer (personal national ID number)</span></td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd">hash</td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">+</td>
    <td colspan="1" class="confluenceTd"><span>Base64 encoded hash function output to be signed.</span></td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd">hashType</td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">+</td>
    <td colspan="1" class="confluenceTd">Hash algorithm used to create the hash.</td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd"><span>language</span></td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">+</td>
    <td colspan="1" class="confluenceTd"><span>Language for user dialog in mobile phone. 3-letters capitalized acronyms are used. Possible values: EST, ENG, RUS, LIT.
    NB! If you use language="LIT" to send to Estonian number (+372...) or you use language="EST" to send to Lithuanian number (+370...) then internally language is replaced with "ENG".
    </span></td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd"><span>displayText</span></td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd"></td>
    <td colspan="1" class="confluenceTd"><span>Text displayed in addition to relyingPartyName and Verification Code before asking authentication PIN. Maximum length is 40 bytes that is either 20 or 40 characters depending on the encoding - see displayTextFormat. 
    If you set displayTextFormat="GSM-7" then all characters not beloning to this alphabet are replaced with spaces.
    </span></td>
</tr>

<tr>
    <td colspan="1" class="confluenceTd"><span>displayTextFormat</span></td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd"></td>
    <td colspan="1" class="confluenceTd"> Specifies which characters and how many can be used in "displayText". Possible values are "GSM-7" and "UCS-2”, if nothing is specified then defaults to "GSM-7". GSM-7 allows displayText to contain up to 40 characters from standard GSM 7-bit alpabet including up to 5 characters from extension table ( €[]^|{}\ ). UCS-2 allows up to 20 characters from UCS-2 alpabet (this has all Cyrillic characters, ÕŠŽ šžõ and ĄČĘĖĮŠŲŪŽ ąčęėįšųūž). [More info about encoding](https://en.wikipedia.org/wiki/GSM_03.38).</span></td>
</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">3.2.4\.</span> Example request

```json
{
    "relyingPartyUUID": "00000000-0000-0000-0000-000000000000",
    "relyingPartyName": "DEMO",
    "phoneNumber": "+3726234566",
    "nationalIdentityNumber": "38412319871",
    "hash": "0nbgC2fVdLVQFZJdBbmG7oPoElpCYsQMtrY0c0wKYRg=",
    "hashType": "SHA256",
    "language": "ENG",
    "displayText": "This is display text.",
    "displayTextFormat": "GSM-7"
}
```

Following dialogues are displayed on end user cellphone screen for authentication and signing.
Actual design varies on different phone models.


<div class="table-wrap">
<table class="confluenceTable"><colgroup><col> <col> <col> <col></colgroup> 
<thead>
<tr>
<th class="confluenceTh">
Prompt for signing
</th>

<th class="confluenceTh">
Prompt of authentication 
</th>
</tr>
</thead>
<tbody>
<tr><td colspan="1" class="confluenceTd">
    
![Sign prompt on cellphone screen](images/phone_screen_sign.png?raw=true "Sign prompt on cellphone screen")

</td><td colspan="1" class="confluenceTd">

![Auth prompt on cellphone screen](images/phone_screen_auth.png?raw=true "Authprompt on cellphone screen")

</td>
</tr>
</tbody>
</table>
</div>

Note that when the process is signing the prompt to End User has "Sign?" in the end and authentication has "Enter?".




### <span class="numhead-number">3.2.5\.</span> Example response

```json
{
  "sessionID": "de305d54-75b4-431b-adb2-eb6b9e546015"  
}
```

### <span class="numhead-number">3.2.6\.</span> Error conditions

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">HTTP status code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>


<tr>
    <td colspan="1" class="confluenceTd">400</td>
    <td colspan="1" class="confluenceTd">Required {parameterName} is missing.</td>
    <td colspan="1" class="confluenceTd">Mandatory parameter in request is missing on has incorrect format/value</td>
</tr>


<tr>
    <td colspan="1" class="confluenceTd">400</td>
    <td colspan="1" class="confluenceTd">The length of the hash must match the type of hash</td>
    <td colspan="1" class="confluenceTd">Hash length does not match the type of hash. For example base64 decoded value of SHA256 hash has to be (256bits divided with 8bits) = 32 bytes.</td>
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

<td colspan="1" class="confluenceTd">Only HTTP methods POST and OPTIONS are allowed</td>

</tr>

<tr>

<td class="confluenceTd">500</td>

<td class="confluenceTd">Internal error</td>

<td class="confluenceTd">MID-REST internal error. Try start the process again from the beginning.</td>

</tr>
</tbody>
</table>
</div>




## <span class="numhead-number">3.3\.</span> Status of signing and authentication

Polling of the authentication and signing status differs from endpoint name to be used.


<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>
<td class="confluenceTd">

**Process**

</td><td class="confluenceTd">

**Method**

</td><td class="confluenceTd">

**URL**

</td>
</tr>

<tr>
<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">Signing</span></td>
<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">GET</span></td>

<td colspan="1" class="confluenceTd"><span class="nolink">BASE/signature/session/:sessionId?timeoutMs=:timeoutMs</span></td>
</tr>

<tr>
<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">Authentication</span></td>
<td colspan="1" class="confluenceTd"><span style="color: rgb(0,0,0);">GET</span></td>
<td colspan="1" class="confluenceTd"><span class="nolink">BASE/authentication/session/:sessionId?timeoutMs=:timeoutMs</span></td>

</tr>
</tbody>
</table>
</div>



### <span class="numhead-number">3.3.1\.</span> Pre-Conditions

*   Session is present in the system and the request is either running or has been completed recently. 

### <span class="numhead-number">3.3.2\.</span> Post-Conditions

*   Current session state has been returned to user.

### <span class="numhead-number">3.3.3.</span> Request parameters


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

<td colspan="1" class="confluenceTd"><span>sessionId</span></td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">+</td>

<td colspan="1" class="confluenceTd"><span>Session ID</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">timeoutMs</td>

<td colspan="1" class="confluenceTd">integer</td>

<td colspan="1" class="confluenceTd"></td>

<td colspan="1" class="confluenceTd">Maximum time in milliseconds the server is allowed to wait before returning a response. See next chapter for more info.</td>
</tr>



</tbody>

</table>

</div>



### <span class="numhead-number">3.3.4.</span> Long polling

In order to avoid making many requests towards Application Provider the E-Service provider is recommended to use long polling
by setting parameter timeoutMs. 

If the session is in RUNNING state (meaning waiting for user to enter the PIN to the cellphone and the response
to arrive) the server waits this amount of time before responding.

If this parameter is not provided, a default is used (can change, value around 1000ms).
For very large values the service silently reverts to configuration specific maximum value (can change, value around 60000-12000ms).
For very low values the service silently reverts to configuration specific minimum value (can change, value around 1000ms)

If the state of session changes during the wait time then the response is sent out immediately.
If the wait time is over but the response has not yet arrived from phone then the service responds back that the session is RUNNING and the caller is
encouraged to immediately create a new long polling request.

The E-Service provider should not make new requests for given session before the previous request gets a response back.
However, if Application Provider detects a new request with the same session ID then the previous request is discarded and 
response with state=RUNNING is replied back to the previous request.

NB! E-Service provider should set its own internal request timeout to a slightly higher value (add additional ~1500ms).
For example if E-Service provider makes a request with ?timeoutMs=60000 then it should set a request timeout of 61500ms 
(and not use the same value of 60000ms) as it takes additional time to transfer the request and response over the network.


### <span class="numhead-number">3.3.5\.</span> Response structure

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

Present

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

<td class="confluenceTd">State of request. One of "RUNNING", "COMPLETE".</td>

</tr>

<tr>

<td class="confluenceTd">result</td>

<td class="confluenceTd">string</td>
  
<td class="confluenceTd">Only if state is COMPLETE</td>

<td class="confluenceTd">Result of the transaction. See paragraph 3.3.7 for possible values.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">Only if state is COMPLETE</td>

<td colspan="1" class="confluenceTd">Structure describing the signature result, if any.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature.value</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">Only if state is COMPLETE</td>

<td colspan="1" class="confluenceTd">Signature value, base64 encoded.</td>

</tr>

<tr>

<td colspan="1" class="confluenceTd">signature.algorithm</td>

<td colspan="1" class="confluenceTd">string</td>

<td colspan="1" class="confluenceTd">Only if state is COMPLETE</td>

<td colspan="1" class="confluenceTd">Signature algorithm, in the form of sha256WithRSAEncryption</td>

</tr>
<tr>
    <td colspan="1" class="confluenceTd">cert</td>
    <td colspan="1" class="confluenceTd">string</td>
    <td colspan="1" class="confluenceTd">Only if process was authentication and signature is present.</td>
    <td colspan="1" class="confluenceTd">Authentication certificate used. DER + Base64 encoded. Signing process doesn't return this value (need to pull separately). 
    From the certificate it is possible to obtain end user name, national identity number and country. See [mid-rest-java-client](https://github.com/SK-EID/mid-rest-java-client) or [mid-rest-php-client](https://github.com/SK-EID/mid-rest-php-client) for examples how to parse the certificate.</td>
</tr>

</tbody>

</table>

</div>

### <span class="numhead-number">3.3.6\.</span> Verifying the authentication response

For authentication additional steps must be followed to verify that the authentication
result is trustworthy and identity of the End User is confirmed:

* "result" has the value "OK"
* "signature.value" is a valid signature over the same "hash", which was submitted by the Relying Party.
* "signature.value" is a valid signature. This can be verified by using the public key taken from the certificate returned on the "cert" field.
* The person's certificate given in the "cert" is valid. This means it is: 
   * not expired
   * signed by trusted certificate authority 
* The identity of the authenticated person is in the "subject" field of the X.509 certificate included in "cert" field.

After successful authentication, the Relying Party must also invalidate user's browser or API session identifier and generate a new one.



### <span class="numhead-number">3.3.7\.</span> Example responses


Response when server is still waiting for user's response to arrive back from cellphone:

```json
{
    "state":"RUNNING"  
}
```


Signing response after successful completion:


```json
{
    "state": "COMPLETE",
    "result": "OK",
    "signature": {
        "value": "B+C9XVjIAZnCHH9vfBSv...",
        "algorithm": "SHA256WithECEncryption"
    }  
}
```


Authentication response after successful completion (note that unlike signature response it also includes authentication certificate):

```json
{  
    "state": "COMPLETE",
    "result": "OK",
    "signature": {
        "value": "B+C9XVjIAZnCHH9vfBSv...",
        "algorithm": "SHA256WithECEncryption"  
    },
    "cert": "MIIFxjCCA66gAwIBAgIQZ6v2ut9..."
}
```

If user cancelled the operation:

```json
{  
    "state": "COMPLETE",
    "result": "USER_CANCELLED"
}
```

When response from end user's cell phone has not arrived within a timeout period set by Application Provider.
The Application Provider has given up waiting for it to arrive and responds with:

```json
{
    "state": "COMPLETE",
    "result": "TIMEOUT"  
}
```


### <span class="numhead-number">3.3.8\.</span> Session end result codes

The following is a complete list of possible result codes returned by the service.
For all of them a HTTP 200 status code and a JSON string is returned with "state": "COMPLETE"
and "result" with one of the following values:

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Result</th>

<th class="confluenceTh">Reason</th>

</tr>

<tr>

<td class="confluenceTd">OK</td>

<td class="confluenceTd"><span>Session was completed successfully</span></td>

</tr>

<tr>

<td class="confluenceTd">TIMEOUT</td>

<td class="confluenceTd"><span>There was a timeout, i.e. end user did not confirm or refuse the operation within maximum time frame allowed (can change, around two minutes).</span></td>

</tr>

<tr>

<td class="confluenceTd">NOT_MID_CLIENT</td>

<td class="confluenceTd"> <span>Given user has no active certificates and is not MID client.</span></td>

</tr>


<tr>

<td colspan="1" class="confluenceTd">USER_CANCELLED</td>

<td colspan="1" class="confluenceTd"><span>User cancelled the operation</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>SIGNATURE_HASH_MISMATCH</span></td>

<td colspan="1" class="confluenceTd"><span>Mobile-ID configuration on user's SIM card differs from what is configured on service provider's side. User needs to contact his/her mobile operator.</span></td>

</tr>



<tr>

<td colspan="1" class="confluenceTd"><span>PHONE_ABSENT</span></td>

<td colspan="1" class="confluenceTd"><span>Sim not available</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>DELIVERY_ERROR</span></td>

<td colspan="1" class="confluenceTd"><span>SMS sending error</span></td>

</tr>

<tr>

<td colspan="1" class="confluenceTd"><span>SIM_ERROR</span></td>

<td colspan="1" class="confluenceTd"><span>Invalid response from card</span></td>

</tr>


</tbody>

</table>

</div>

### <span class="numhead-number">3.3.9\.</span> HTTP error codes

<div class="table-wrap">

<table class="confluenceTable"><colgroup><col> <col> <col></colgroup> 

<tbody>

<tr>

<th class="confluenceTh">Error code</th>

<th class="confluenceTh">Error message</th>

<th class="confluenceTh">Reason</th>

</tr>



<tr>

<td colspan="1" class="confluenceTd">400</td>

<td colspan="1" class="confluenceTd">Required sessionId is missing</td>

<td colspan="1" class="confluenceTd">Required paramter sessionId is missing.</td>

</tr>


<tr>

<td colspan="1" class="confluenceTd">401</td>

<td colspan="1" class="confluenceTd">Failed to authorize user</td>

<td colspan="1" class="confluenceTd">User authorization by sessionId and IP-address fails</td>

</tr>

<tr>
<td class="confluenceTd">404</td>

<td class="confluenceTd">SessionID not found</td>

<td class="confluenceTd">Sessions expire within 5 minutes.</td>
</tr>

<tr>
<td colspan="1" class="confluenceTd">405</td>
<td colspan="1" class="confluenceTd">Method Not Allowed</td>
<td colspan="1" class="confluenceTd">Only GET and OPTIONS are allowed methods.</td>
</tr>

<tr>
<td class="confluenceTd">500</td>
<td class="confluenceTd">Internal error</td>
<td class="confluenceTd">MID-REST internal error. Try start the process again from the beginning.</td>
</tr>



</tbody>

</table>

</div>

## <span class="numhead-number">3.4.</span> API version 

| Method | URL          |
| ------ | -------------|
| GET    | BASE/version |

### 3.4.1. Example response

```
    Version: 5.1.1. Built: 19.06.2019 21:06  
```

### 3.4.2. Response structure 

```
    Version: MAJOR.MINOR.PATCH. Built: dd.MM.yyyy hh:mm
```

Version numbering:

* MAJOR version is incremented when a new API is released or if there have been major internal changes. See chapter [2.7.](#27-backwards-compatibility) for notes about API backwards compatibility.
* MINOR version is incremented when there are smaller internal changes, new request parameters or new response fields are added.
* PATCH version is incremented with backwards-compatible bug fixes. No fields are added with bug fixes.

Built timestamp refers when the release was built from source code. 

### 3.4.3. Public demo environment version number

Open link: <https://tsp.demo.sk.ee/mid-api/version>


# <span class="numhead-number">4.</span> Helper libraries and demo applications

There are client libraries provided for easier integration for Java and PHP and also
demo applications that demonstrate usage of the client libraries.

## <span class="numhead-number">4.1\.</span> Java

Java Clint allows using all of the MID-REST functionality.

* [Java Client](https://github.com/SK-EID/mid-rest-java-client)
* [Java Demo Application](https://github.com/SK-EID/mid-rest-java-demo)

## <span class="numhead-number">4.2\.</span> PHP

Provided PHP functionality only supports authentication and fetching the signing certificate.

* [PHP Client](https://github.com/SK-EID/mid-rest-php-client)
* [PHP Demo Application](https://github.com/SK-EID/mid-rest-php-demo)

# <span class="numhead-number">5.</span> Comparison with DigiDocService

Previous generation MID-REST integration has been provided with SOAP-based DigiDocService (DDS).
Following page lists the main differences between DDS and new MID-REST (MID) and provides
hints for migration from DDS to MID-

* [MID-REST compared to DDS](https://github.com/SK-EID/MID/blob/master/DDS-to-MID-migration/README.md)


</div>
</div>
</div>
</div>
</div>
