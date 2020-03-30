---
title: Kundpåståenden (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om stöd för signerade klientpåståenden för konfidentiella klientprogram i Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050296"
---
# <a name="confidential-client-assertions"></a>Konfidentiella klientpåståenden

För att bevisa sin identitet utbyter konfidentiella klientprogram en hemlighet med Azure AD. Hemligheten kan vara:
- En klienthemlighet (programlösenord).
- Ett certifikat som används för att skapa ett signerat påstående som innehåller standardanspråk.

Denna hemlighet kan också vara ett undertecknat påstående direkt.

MSAL.NET har fyra metoder för att ange autentiseringsuppgifter eller påståenden till den konfidentiella klientappen:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Även om det är `WithClientAssertion()` möjligt att använda API för att hämta token för den konfidentiella klienten, rekommenderar vi inte att du använder det som standard eftersom det är mer avancerat och är utformat för att hantera mycket specifika scenarier som inte är vanliga. Om `.WithCertificate()` du använder API:et kan MSAL.NET hantera detta åt dig. Med det här api:et kan du anpassa din `.WithCertificate()` autentiseringsbegäran om det behövs, men standardpåståendet som skapas av räcker för de flesta autentiseringsscenarier. Det här API:et kan också användas som en lösning i vissa scenarier där MSAL.NET misslyckas med att utföra signeringsåtgärden internt.

### <a name="signed-assertions"></a>Signerade påståenden

Ett signerat klientpåstående har formen av en signerad JWT med nyttolasten som innehåller de autentiseringsanspråk som krävs på uppdrag av Azure AD, Base64-kodade. Så här använder du den:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

De anspråk som förväntas av Azure AD är:

Anspråkstyp | Värde | Beskrivning
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Påståendet "aud" (målgrupp) identifierar de mottagare som JWT är avsett för (här Azure AD) Se [RFC 7519, Avsnitt 4.1.3]
exp | Tor Jun 27 2019 15:04:17 GMT+0200 (Romantik Daylight Time) | Anspråket "exp" (utgångstid) identifierar utgångstiden på eller efter vilken JWT INTE FÅR accepteras för bearbetning. Se [RFC 7519, avsnitt 4.1.4]
Iss | {ClientID} | Påståendet "iss" (emittent) identifierar det huvudman som utfärdade JVT. Behandlingen av detta anspråk är programspecifik. Iss-värdet är en skiftlägeskänslig sträng som innehåller ett StringOrURI-värde. [RFC 7519, avsnitt 4.1.1]
jti (på ett sätt) | (en Guid) | Påståendet "jti" (JWT ID) ger en unik identifierare för JWT. Identifieringsvärdet MÅSTE tilldelas på ett sätt som säkerställer att det finns en försumbar sannolikhet för att samma värde av misstag tilldelas ett annat dataobjekt. Om programmet använder flera emittenter måste kollisioner förhindras bland värden som produceras av olika emittenter. Påståendet "jti" kan användas för att förhindra att JWT spelas upp igen. Värdet "jti" är en skiftlägeskänslig sträng. [RFC 7519, avsnitt 4.1.7]
Nbf | Tor Jun 27 2019 14:54:17 GMT+0200 (Romantik Daylight Time) | I påståendet "nbf" (ej före) anges den tid före vilken JWT INTE FÅR godkännas för behandling. [RFC 7519, avsnitt 4.1.5]
Sub | {ClientID} | I påståendet "sub" (ämne) anges ämnet för den gemensamma tillsyns- och Påståendena i en gemensamma tillsynspost är normalt uttalanden om ämnet. Ämnesvärdet MÅSTE antingen begränsas för att vara lokalt unikt i samband med utfärdaren eller vara globalt unikt. Se [RFC 7519, avsnitt 4.1.2]

Här är ett exempel på hur man hantverk dessa påståenden:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
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

Så här skapar du ett signerat klientpåstående:

```csharp
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

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

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
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternativ metod

Du kan också använda [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) för att skapa påståendet åt dig. Koden kommer att vara en mer elegant som visas i exemplet nedan:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

När du har din signerade klient påstående, kan du använda den med MSAL apis som visas nedan.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`som standard kommer att producera ett signerat påstående som innehåller de anspråk som förväntas av Azure AD plus ytterligare klientanspråk som du vill skicka. Här är ett kodavsnitt om hur man gör det.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Om en av anspråken i ordlistan som du skickar in är samma som en av de obligatoriska anspråken, kommer det ytterligare anspråkets värde att beaktas. Det åsidosätter anspråk som beräknas av MSAL.NET.

Om du vill ange dina egna anspråk, inklusive de obligatoriska `false` anspråk `mergeWithDefaultClaims` som förväntas av Azure AD, går du in för parametern.
