---
title: Klient kontroll i Microsoft Authentication Library för .NET
titleSuffix: Microsoft identity platform
description: Läs om stöd för signerade klienter för konfidentiella klient program i Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcf11ac8dc39dcb1d70b932dbe870687f5446a52
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802845"
---
# <a name="confidential-client-assertions"></a>Konfidentiell klient kontroll
För att bevisa sin identitet utbyter konfidentiella klient program en hemlighet med Azure AD. Hemligheten kan vara:
- En klient hemlighet (program lösen ord).
- Ett certifikat som används för att bygga en signerad försäkran som innehåller standard anspråk.

Den här hemligheten kan också vara en signerad kontroll direkt.

MSAL.NET har fyra metoder för att ange autentiseringsuppgifter eller intyg för den konfidentiella klient appen:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

### <a name="signed-assertions"></a>Signerade kontroller

En signerad klient kontroll tar formen av en signerad JWT med nytto lasten som innehåller de autentiseringsuppgifter som krävs av Azure AD, Base64-kodat. Så här använder du den:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

De anspråk som förväntas av Azure AD är:

Anspråks typ | Värde | Beskrivning
---------- | ---------- | ----------
AUD | https://login.microsoftonline.com/{tenantId}/v2.0 | Anspråket "AUD" (Audience) identifierar mottagarna som JWT är avsett för (här Azure AD) se [RFC 7519, section 4.1.3]
EXP | Tor Jun 27 2019 15:04:17 GMT + 0200 (romantik, sommar tid) | Anspråket "EXP" (förfallo tid) anger förfallo tid för eller efter vilken JWT inte får godkännas för bearbetning. Se [RFC 7519, section 4.1.4]
ISS | ClientID | Anspråket "ISS" (utfärdare) identifierar det huvud konto som utfärdade JWT. Bearbetningen av detta påstående är programspecifik. Värdet "ISS" är en Skift läges känslig sträng som innehåller ett StringOrURI-värde. [RFC 7519, avsnitt 4.1.1]
jti | (ett GUID) | Anspråket "JTI" (JWT ID) tillhandahåller en unik identifierare för JWT. Identifier-värdet måste tilldelas på ett sätt som garanterar att det är en försumbar sannolikhet att samma värde har tilldelats av misstag till ett annat data objekt. om programmet använder flera utfärdare, måste kollisioner förhindras mellan värden som skapas av olika utfärdare. JTI-anspråket kan användas för att förhindra att JWT spelas upp. Värdet "JTI" är en Skift läges känslig sträng. [RFC 7519, section 4.1.7]
NBF | Tor Jun 27 2019 14:54:17 GMT + 0200 (romantik, sommar tid) | Anspråket "NBF" (inte före) anger hur lång tid som JWT inte får godkännas för bearbetning. [RFC 7519, avsnitt 4.1.5]
Build | ClientID | Anspråket "sub" (subject) identifierar ämnet för JWT. Anspråken i en JWT-sats är normalt uttryck för ämnet. Subject-värdet måste antingen vara lokalt unikt i kontexten för utfärdaren eller vara globalt unikt. Se [RFC 7519, avsnitt 4.1.2]

Här är ett exempel på hur du kan hantverka dessa anspråk:

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Så här kan du skapa en signerad klient kontroll:

```CSharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` som standard skapas en signerad kontroll som innehåller de anspråk som förväntas av Azure AD plus ytterligare klient anspråk som du vill skicka. Här är ett kodfragment om hur du gör det.

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Om något av anspråken i ord listan som du skickar in är samma som ett av de obligatoriska anspråken, tas det ytterligare anspråkets värde med i beräkningen. Den åsidosätter de anspråk som beräknas av MSAL.NET.

Om du vill tillhandahålla egna anspråk, inklusive obligatoriska anspråk som förväntas av Azure AD, måste du skicka `false` för `mergeWithDefaultClaims`-parametern.
